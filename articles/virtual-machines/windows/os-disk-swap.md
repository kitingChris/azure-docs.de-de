---
title: Austauschen des Betriebssystem-Datenträgers für eine Azure-VM mit PowerShell | Microsoft-Dokumentation
description: Ändern Sie den von einem virtuellen Azure-Computer verwendeten Betriebssystem-Datenträger mithilfe von PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: ae3979f7ceae4a854df00b39d9c2b9673f65f987
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720135"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Ändern des von einem virtuellen Azure-Computer verwendeten Betriebssystem-Datenträgers mithilfe von PowerShell

Wenn Sie über einen virtuellen Computer verfügen, aber den Datenträger für einen Sicherungsdatenträger oder einem anderen Betriebssystem-Datenträger austauschen möchten, können Sie die Betriebssystem-Datenträger mit Azure PowerShell austauschen. Sie müssen den virtuellen Computer nicht löschen und neu erstellen. Sie können auch einen verwalteten Datenträger in einer anderen Ressourcengruppe verwenden, solange er nicht bereits in Gebrauch ist.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

Der virtuelle Computer muss sich im Zustand „Beendet (Zuordnung aufgehoben)“ befinden, und dann kann die Ressourcen-ID des verwalteten Datenträgers durch die Ressourcen-ID eines anderen verwalteten Datenträgers ersetzt werden.

Stellen Sie sicher, dass VM-Größe und Speichertyp mit dem Datenträger kompatibel sind, den Sie anfügen möchten. Wenn der Datenträger, den Sie verwenden möchten, sich z.B. im Storage Premium befindet, dann muss der virtuelle Computer für Storage Premium geeignet sein (z.B. eine Größe der DS-Serie). 

Abrufen einer Liste von Datenträgern in einer Ressourcengruppe mit [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Wenn Sie den Namen des Datenträgers kennen, den Sie verwenden möchten, legen Sie diesen als Betriebssystem-Datenträger für den virtuellen Computer fest. Dieses Beispiel beendet den virtuellen Computer mit dem Namen *myVM* (hebt seine Zuordnung auf) und weist den Datenträger namens *newDisk* als neuen Betriebssystem-Datenträger zu. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Nächste Schritte**

Wie Sie eine Kopie eines Datenträgers erstellen, erfahren Sie unter [Erstellen einer Momentaufnahme](snapshot-copy-managed-disk.md).