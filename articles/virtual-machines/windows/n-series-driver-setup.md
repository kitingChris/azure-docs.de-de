---
title: Einrichtung von GPU-Treibern für die Azure N-Serie unter Windows | Microsoft-Dokumentation
description: Einrichten von NVIDIA-GPU-Treibern für virtuelle Computer der N-Serie unter Windows Server oder Windows in Azure
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 24189fa8e0f6c31d7fbd3779f666eb85e24dc8f7
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723115"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Installieren von NVIDIA GPU-Treibern für virtuelle Computer der Serie N mit Windows 

Um die GPU-Funktionen von virtuellen Azure-Computern der N-Serie unter Windows nutzen zu können, müssen NVIDIA-GPU-Treiber installiert werden. Mit der [NVIDIA-GPU-Treibererweiterung](../extensions/hpccompute-gpu-windows.md) werden entsprechende NVIDIA-CUDA- oder GRID-Treiber auf einem virtuellen Computer der N-Serie installiert. Installieren oder verwalten Sie die Erweiterung mithilfe des Azure-Portals oder mit Tools wie Azure PowerShell oder Azure Resource Manager-Vorlagen. Informationen zu unterstützten Betriebssystemen und Bereitstellungsschritten finden Sie in der [Dokumentation zur NVIDIA-GPU-Treibererweiterung](../extensions/hpccompute-gpu-windows.md).

Wenn Sie GPU-Treiber manuell installieren möchten, finden Sie in diesem Artikel Informationen zu unterstützten Betriebssystemen und Treibern sowie Schritte zur Installation und Überprüfung. Informationen zur manuellen Einrichtung von Treibern stehen auch für [Linux-VMs](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) zur Verfügung.

Informationen zu grundlegenden Spezifikationen, Speicherkapazitäten und Details zu den Datenträgern finden Sie unter [GPU-optimierte Größen von virtuellen Windows-Computern](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Treiberinstallation

1. Stellen Sie über Remotedesktop eine Verbindung mit den einzelnen virtuellen Computern der N-Serie her.

2. Laden Sie den unterstützten Treiber für Ihr Windows-Betriebssystem herunter, entpacken und installieren Sie ihn.

Nach der GRID-Treiberinstallation auf einer VM ist ein Neustart erforderlich. Nach der CUDA-Treiberinstallation ist kein Neustart erforderlich.

## <a name="verify-driver-installation"></a>Überprüfen der Treiberinstallation

Sie können die Treiberinstallation im Geräte-Manager überprüfen. Das folgende Beispiel zeigt die erfolgreiche Konfiguration der Tesla K80-Karte auf einem virtuellen Azure-NC-Computer.

![Eigenschaften des GPU-Treibers](./media/n-series-driver-setup/GPU_driver_properties.png)

Führen Sie zum Abfragen des GPU-Gerätestatus das mit dem Treiber installierte Befehlszeilenprogramm [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) aus.

1. Öffnen Sie eine Eingabeaufforderung, und wechseln Sie in das Verzeichnis **C:\Programme\NVIDIA Corporation\NVSMI**.

2. Führen Sie `nvidia-smi`aus. Wenn der Treiber installiert wurde, wird eine Ausgabe ähnlich der folgenden angezeigt. Für **GPU-Auslastung** wird **0 %** angezeigt, sofern gerade keine GPU-Workload auf dem virtuellen Computer ausgeführt wird. Die Treiberversion und die GPU-Informationen weichen möglicherweise von den angezeigten Informationen ab.

![NVIDIA-Gerätestatus](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA-Netzwerkkonnektivität

RDMA-Netzwerkkonnektivität kann auf virtuellen Computern der N-Serie (etwa NC24r) aktiviert werden, die RDMA-fähig sind und in der gleichen Verfügbarkeitsgruppe bzw. in einer einzelnen Platzierungsgruppe in einer VM-Skalierungsgruppe bereitgestellt werden. Die HpcVmDrivers-Erweiterung muss hinzugefügt werden, um Windows-Netzwerkgerätetreiber zu installieren, die für RDMA-Konnektivität benötigt werden. Verwenden Sie [Azure PowerShell](/powershell/azure/overview)-Cmdlets für Azure Resource Manager, um einem virtuellen RDMA-fähigen Computer der N-Serie die VM-Erweiterung hinzuzufügen.

So installieren Sie die neueste Version (1.1) der HpcVMDrivers-Erweiterung auf einem vorhandenen, RDMA-fähigen virtuellen Computer namens myVM in der Region USA, Westen:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Weitere Informationen finden Sie unter [Erweiterungen und Features für virtuelle Computer für Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Das RDMA-Netzwerk unterstützt Message Passing Interface-Datenverkehr (MPI) für Anwendungen mit [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) oder Intel MPI 5.x. 


## <a name="next-steps"></a>Nächste Schritte

* Entwickler, die GPU-beschleunigte Anwendungen für die NVIDIA Tesla-GPUs erstellen, können auch das neueste [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads) herunterladen und installieren. Weitere Informationen finden Sie im [CUDA-Installationshandbuch](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


