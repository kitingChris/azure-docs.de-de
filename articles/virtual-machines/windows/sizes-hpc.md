---
title: Größen von virtuellen Azure Windows-Computern – HPC | Microsoft-Dokumentation
description: Auflistung der verschiedenen verfügbaren Größen für virtuelle Windows HPC-Computer (High Performance Computing) in Azure. Dieser Artikel listet Informationen zur Anzahl von vCPUs, Datenträgern und Netzwerkschnittstellenkarten sowie zum Speicherdurchsatz und zur Netzwerkbandbreite für Größen dieser Serie auf.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: 6fd08ca912c14a50064f4b6da18334d8bf9df0ca
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871588"
---
# <a name="high-performance-compute-vm-sizes"></a>Größen von virtuellen HPC-Computern (High Performance Computing)

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Betriebssystem**: Windows Server 2016 auf allen oben genannten virtuellen HPC-Computern. Windows Server 2012 R2 und Windows Server 2012 werden auch auf virtuellen Computern unterstützt, die nicht SR-IOV-fähig sind (und daher nicht zur HB- oder HC-Reihe gehören).

* **MPI**: Die SR-IOV-fähigen VM-Größen (HB, HC) in Azure gestatten die Verwendung nahezu jeder Variante von MPI mit Mellanox OFED.
Auf nicht SR-IOV-fähigen virtuellen Computern verwenden unterstützte MPI-Implementierungen die Microsoft Network Direct-Schnittstelle (ND) für die Kommunikation zwischen Instanzen. Daher werden nur die Versionen Microsoft MPI (MS-MPI) 2012 R2 oder höher und Intel MPI 5.x unterstützt. Höhere Versionen (2017, 2018) der Intel MPI-Laufzeitbibliothek sind möglicherweise mit den Azure-RDMA-Treibern kompatibel.

* **InfiniBandDriverWindows-VM-Erweiterung**: Fügen Sie auf RDMA-fähigen virtuellen Computern die Erweiterung „InfiniBandDriverWindows“ hinzu, um InfiniBand zu aktivieren. Diese Windows-Erweiterung für VMs installiert Windows Network Direct-Treiber (auf VMs ohne SR-IOV) oder Mellanox OFED-Treiber (auf VMs mit SR-IOV), um RDMA-Konnektivität herzustellen.
In bestimmten Bereitstellungen von A8- und A9-Instanzen wird die Erweiterung HpcVmDrivers automatisch hinzugefügt. Beachten Sie, dass die VM-Erweiterung „HpcVmDrivers“ eingestellt wird. Sie wird nicht mehr aktualisiert. Um die VM-Erweiterung einer VM hinzuzufügen, können Sie [Azure PowerShell](/powershell/azure/overview)-Cmdlets verwenden. 

  Der folgende Befehl installiert die neueste Version 1.0 der Erweiterung „InfiniBandDriverWindows“ auf einer vorhandenen, RDMA-fähigen VM mit dem Namen *myVM*, die in der Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *USA, Westen* bereitgestellt ist:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Alternativ können VM-Erweiterungen zur einfachen Bereitstellung mithilfe des folgenden JSON-Elements in Azure Resource Manager-Vorlagen aufgenommen werden:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Der folgende Befehl installiert die neueste Version 1.0 der Erweiterung „InfiniBandDriverWindows“ auf allen RDMA-fähigen VMs in einer vorhandenen VM-Skalierungsgruppe mit dem Namen *myVMss*, die in der Ressourcengruppe mit dem Namen *myResourceGroup* bereitgestellt ist:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Weitere Informationen finden Sie unter [Erweiterungen und Features für virtuelle Computer](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Erweiterungen können auch für virtuelle Computer verwendet werden, die mit dem [klassischen Bereitstellungsmodell](classic/manage-extensions.md) bereitgestellt wurden.

* **RDMA-Netzwerkadressbereich:** Für das RDMA-Netzwerk in Azure wird der Adressbereich 172.16.0.0/16 reserviert. Wenn Sie MPI-Anwendungen auf Instanzen ausführen möchten, die in einem virtuellen Azure-Netzwerk bereitgestellt wurden, vergewissern Sie sich, dass der Adressraum des virtuellen Netzwerks sich nicht mit dem RDMA-Netzwerk überschneidet.


### <a name="cluster-configuration-options"></a>Konfigurationsoptionen für Cluster

Azure bietet mehrere Optionen zum Erstellen von Clustern von Windows-HPC-VMs, die über das RDMA-Netzwerk kommunizieren können, einschließlich der folgenden: 

* **Virtuelle Computer**: Stellen Sie die RDMA-fähigen HPC-VMs in der gleichen Verfügbarkeitsgruppe bereit (wenn Sie das Azure Resource Manager-Bereitstellungsmodell verwenden). Stellen Sie die VMs bei Verwendung des klassischen Bereitstellungsmodells im gleichen Clouddienst bereit. 

* **VM-Skalierungsgruppen**: Stellen Sie bei Verwendung einer VM-Skalierungsgruppe sicher, dass Sie die Bereitstellung auf eine einzelne Platzierungsgruppe beschränken. Legen Sie z. B. in einer Resource Manager-Vorlage die Eigenschaft `singlePlacementGroup` auf `true` fest. 

* **MPI zwischen virtuellen Computern** – Wenn für die MPI-Kommunikation zwischen virtuellen Computern (VMs) erforderlich ist, dann sollten Sie sicherstellen, dass sich die VMs in der selben Verfügbarkeitsgruppe oder derselben VM-Skalierungsgruppe befinden.

* **Azure CycleCloud**: Erstellen Sie in [Azure CycleCloud](/azure/cyclecloud/) einen HPC-Cluster zum Ausführen von MPI-Aufträgen auf Windows-Knoten.

* **Azure Batch**: Erstellen Sie einen [Azure Batch](/azure/batch/)-Pool zum Ausführen von MPI-Workloads auf Windows Server-Computeknoten. Weitere Informationen finden Sie unter [Verwenden RDMA-fähiger oder GPU-fähiger Instanzen in Batch-Pools](../../batch/batch-pool-compute-intensive-sizes.md). Informationen zum Ausführen containerbasierter Workloads in Batch finden Sie im Projekt [Batch Shipyard](https://github.com/Azure/batch-shipyard).

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) enthält eine Laufzeitumgebung für MS-MPI, die das Azure RDMA-Netzwerk bei der Bereitstellung auf RDMA-fähigen Windows-VMs verwendet. Beispielbereitstellungen finden Sie unter [Einrichten eines Windows RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Andere Größen
- [Allgemeiner Zweck](sizes-general.md)
- [Computeoptimiert](sizes-compute.md)
- [Arbeitsspeicheroptimiert](../virtual-machines-windows-sizes-memory.md)
- [Speicheroptimiert](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte

- Prüflisten zur Verwendung rechenintensiver Instanzen mit HPC Pack unter Windows Server finden Sie unter [Einrichten eines Windows RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Informationen zur Verwendung rechenintensiver Instanzen zum Ausführen von MPI-Anwendungen mit Azure Batch finden Sie unter [Verwendung von Tasks mit mehreren Instanzen zum Ausführen von MPI-Anwendungen (Message Passing Interface) in Azure Batch](../../batch/batch-mpi.md).

- Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.
