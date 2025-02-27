---
title: Erstellen eines virtuellen Windows-Computers in einer Zone – Azure PowerShell | Microsoft-Dokumentation
description: Erstellen eines virtuellen Windows-Computers in einer Verfügbarkeitszone mit Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 68b7db2664890f3175fcfb14e6970d79b68f693f
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718998"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-powershell"></a>Erstellen eines virtuellen Windows-Computers in einer Verfügbarkeitszone mit PowerShell

In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure PowerShell einen virtuellen Azure-Computer unter Windows Server 2016 in einer Azure-Verfügbarkeitszone erstellen. Eine [Verfügbarkeitszone](../../availability-zones/az-overview.md) ist eine physisch separate Zone in einer Azure-Region. Verwenden Sie Verfügbarkeitszonen, um Ihre Apps und Daten vor einem unwahrscheinlichen Fehler oder Ausfall eines gesamten Rechenzentrums zu schützen.

Um eine Verfügbarkeitszone verwenden zu können, muss der virtuelle Computer in einer [unterstützten Azure-Region](../../availability-zones/az-overview.md#services-support-by-region) erstellt werden.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

## <a name="check-vm-sku-availability"></a>Überprüfen der VM-SKU-Verfügbarkeit
Die Verfügbarkeit von VM-Größen bzw. SKUs kann je nach Region und Zone variieren. Als Hilfe bei der Planung zur Verwendung von Verfügbarkeitszonen können Sie die verfügbaren VM-SKUs nach Azure-Region und -Zone auflisten. Hierdurch wird sichergestellt, dass Sie eine geeignete VM-Größe wählen und die gewünschte zonenübergreifende Resilienz erzielen. Weitere Informationen zu den verschiedenen VM-Typen und -Größen finden Sie unter [Übersicht über VM-Größen](sizes.md).

Sie können die verfügbaren VM-SKUs mit dem Befehl [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) anzeigen. Im folgenden Beispiel werden die verfügbaren VM-SKUs in der Region *eastus2* aufgeführt:

```powershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

Die Ausgabe ähnelt dem folgenden verkürzten Beispiel, in dem die Verfügbarkeitszonen aufgeführt werden, in denen die einzelnen VM-Größen verfügbar sind:

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Erstellen Sie mit [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. In diesem Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *eastus2* erstellt. 

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>Erstellen von Netzwerkressourcen

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Erstellen eines virtuelles Netzwerks, eines Subnetzes und einer öffentlichen IP-Adresse 
Diese Ressourcen dienen dazu, dem virtuellen Computer Netzwerkkonnektivität bereitzustellen und ihn mit dem Internet zu verbinden. Erstellen Sie die IP-Adresse in einer Verfügbarkeitszone (*2* in diesem Beispiel). In einem späteren Schritt erstellen Sie den virtuellen Computer in der gleichen Zone, die auch zum Erstellen der IP-Adresse verwendet wurde.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Erstellen Sie eine Netzwerksicherheitsgruppe und eine Netzwerksicherheitsgruppen-Regel. 
Die Netzwerksicherheitsgruppe sichert den virtuellen Computer mithilfe von Regeln für eingehenden und ausgehenden Datenverkehr. In diesem Fall wird eine Regel für eingehenden Datenverkehr für Port 3389 erstellt, die eingehende Remotedesktopverbindungen zulässt. Wir möchten auch eine Eingangsregel für Port 80 erstellen, um eingehenden Webdatenverkehr zuzulassen.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Erstellen einer Netzwerkkarte für den virtuellen Computer 
Erstellen Sie mit [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) eine Netzwerkkarte für den virtuellen Computer. Die Netzwerkkarte verbindet die VM mit einem Subnetz, einer Netzwerksicherheitsgruppe und einer öffentlichen IP-Adresse.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie eine VM-Konfiguration. Diese Konfiguration umfasst die beim Bereitstellen der VM verwendeten Einstellungen, z.B. ein Image des virtuellen Computers, Größe und Authentifizierungskonfiguration. Die in diesem Beispiel verwendete Größe *Standard_DS1_v2* wird in Verfügbarkeitszonen unterstützt. Diese Konfiguration gibt auch die Verfügbarkeitszone an, die Sie beim Erstellen der IP-Adresse festgelegt haben. Wenn Sie diesen Schritt ausführen, werden Sie aufgefordert, Anmeldeinformationen einzugeben. Die Werte, die Sie eingeben, werden als Benutzername und Kennwort für den virtuellen Computer konfiguriert.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
```

Erstellen Sie mit [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) den virtuellen Computer.

```powershell
New-AzVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>Bestätigen der Zone für den verwalteten Datenträger

Sie haben die IP-Adressressource des virtuellen Computers in der gleichen Verfügbarkeitszone erstellt wie den virtuellen Computer. Die verwaltete Datenträgerressource für den virtuellen Computer wird in der gleichen Verfügbarkeitszone erstellt. Dies kann mit [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) überprüft werden:

```powershell
Get-AzDisk -ResourceGroupName myResourceGroup
```

Die Ausgabe zeigt, dass sich der verwaltete Datenträger in der gleichen Verfügbarkeitszone befindet wie der virtuelle Computer:

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie einen virtuellen Computer in einer Verfügbarkeitszone erstellen. Weitere Informationen zu Regionen und Verfügbarkeit für virtuelle Azure-Computer finden Sie [hier](regions-and-availability.md).
