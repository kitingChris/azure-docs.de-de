---
title: include file
description: include file
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 06/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 26a5baf07ee31bdf155629139e12ef1977ddca1d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457353"
---
GPU-optimierte VM-Größen sind für spezialisierte virtuelle Computer mit einzelnen oder mehreren NVIDIA-GPUs verfügbar. Diese Größen sind für rechenintensive, grafikintensive und visualisierungsorientierte Workloads vorgesehen. Dieser Artikel enthält Informationen über die Anzahlen und Typen von GPUs, vCPUs, Datenträgern und NICs. Der Speicherdurchsatz und die Netzwerkbandbreite sind für die jeweiligen Größen in dieser Gruppe ebenfalls enthalten.

* Die Größen **NC, NCv2 und NCv3** sind für rechen- und netzwerkintensive Anwendungen und Algorithmen optimiert. Einige Beispiele sind CUDA- und OpenCL-basierte Anwendungen und Simulationen, KI und Deep Learning. Die NCv3-Serie ist für High Performance Computing-Workloads, die mit der Tesla V100-GPU von NVIDIA ausgestattet sind, konzipiert. Virtuelle Computer der NC-Serie verwenden den Prozessor Intel Xeon E5-2690 v3 mit 2,60 GHz (Haswell) und virtuelle Computer der NCv2- und der NCv3-Serie den Prozessor Intel Xeon E5-2690 v4 (Broadwell).

* **ND und NDv2:** Die ND-Serie ist auf Trainings- und Rückschlussszenarien für Deep Learning spezialisiert. Für die Serie werden die NVIDIA Tesla P40-GPU und der Intel Xeon E5-2690 v4-Prozessor (Broadwell) verwendet. Für die NDv2-Serie wird der Intel Xeon Platinum 8168-Prozessor (Skylake) verwendet.

* Die Größen **NV und NVv3** sind für Remotevisualisierung, Streaming, Spiele, Codierung und VDI-Szenarien mit Frameworks wie OpenGL und DirectX optimiert und konzipiert.  Diese VMs werden von der NVIDIA Tesla M60-GPU unterstützt.

## <a name="nc-series"></a>NC-Serie

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

Virtuelle Computer der NC-Serie werden mit der [NVIDIA-Grafikkarte Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) und dem Prozessor Intel Xeon E5-2690 v3 (Haswell) betrieben. Benutzer können Daten schneller analysieren, indem sie CUDA für Anwendungen zur Energieuntersuchung, Absturzsimulationen, Rendering mit Raytracing, Deep Learning und mehr verwenden. Die NC24r-Konfiguration bietet eine Netzwerkschnittstelle mit geringer Wartezeit und hohem Durchsatz, die sich ideal für die Verarbeitung eng gekoppelter paralleler Computingworkloads eignet.

| Size | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1\.440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1\.440 | 4 | 48 | 64 | 4 |

1 GPU = halbe K80-Karte

*RDMA-fähig

## <a name="ncv2-series"></a>NCv2-Serie

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

NCv2-Serien-VMs werden mit [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/)-GPUs betrieben. Im Vergleich zur NC-Serie können diese GPUs eine mehr als doppelte Rechenleistung erzielen. Kunden können diese neuen GPUs für herkömmliche HPC-Workloads wie Modellierung von Lagerstätten, DNA-Sequenzierung, Proteinanalysen, Monte Carlo-Simulationen und Ähnliches nutzen. Zusätzlich zu den GPUs werden virtuelle Computer der NCv2-Serie mit Intel Xeon E5-2690 v4-CPUs (Broadwell) betrieben.

Die NC24rs v2-Konfiguration bietet eine Netzwerkschnittstelle mit geringer Wartezeit und hohem Durchsatz, die sich ideal für die Verarbeitung eng gekoppelter paralleler Computingworkloads eignet.

> [!IMPORTANT]
> Für diese Größenfamilie ist das vCPU-Kontingent (Kernkontingent) in Ihrem Abonnement anfänglich in jeder Region auf „0“ festgelegt. Für diese Familie können Sie in einer [verfügbaren Region](https://azure.microsoft.com/regions/services/) eine [Anhebung des vCPU-Kontingents anfordern](../articles/azure-supportability/resource-manager-core-quotas-request.md).
>

| Size | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

Eine GPU entspricht einer P100-Karte.

*RDMA-fähig

## <a name="ncv3-series"></a>NCv3-Serie

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

NCv3-Serien-VMs werden mit [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/)-GPUs betrieben. Diese GPUs können eine 1,5-mal so hohe Rechenleistung erzielen wie die NCv2-Serie. Kunden können diese neuen GPUs für herkömmliche HPC-Workloads wie Modellierung von Lagerstätten, DNA-Sequenzierung, Proteinanalysen, Monte Carlo-Simulationen und Ähnliches nutzen. Die NC24rs v3-Konfiguration bietet eine Netzwerkschnittstelle mit geringer Wartezeit und hohem Durchsatz, die sich ideal für die Verarbeitung eng gekoppelter paralleler Computingworkloads eignet. Zusätzlich zu den GPUs werden virtuelle Computer der NCv3-Serie mit Intel Xeon E5-2690 v4-CPUs (Broadwell) betrieben.

> [!IMPORTANT]
> Für diese Größenfamilie ist das vCPU-Kontingent (Kernkontingent) in Ihrem Abonnement anfänglich in jeder Region auf „0“ festgelegt. Für diese Familie können Sie in einer [verfügbaren Region](https://azure.microsoft.com/regions/services/) eine [Anhebung des vCPU-Kontingents anfordern](../articles/azure-supportability/resource-manager-core-quotas-request.md).
>

| Size | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

Eine GPU entspricht einer V100-Karte.

*RDMA-fähig

## <a name="ndv2-series-preview"></a>NDv2-Serie (Vorschau)

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

InfiniBand: Nicht unterstützt

Die virtuellen Computer der NDv2-Serie sind neu in der Familie von GPUs, die auf die Anforderungen von HPC-, KI- und Machine Learning-Workloads ausgelegt sind. Die Serie umfasst acht miteinander verbundene NVIDIA Tesla V100 NVLINK-GPUs, 40 Intel Xeon Platinum 8168 (Skylake)-Kerne und 672 GiB Systemarbeitsspeicher. Eine NDv2-Instanz bietet herausragende FP32- und FP64-Leistung für HPC- und KI-Workloads und nutzt dabei CUDA, TensorFlow, Pytorch, Caffe und andere Frameworks.

[Registrieren Sie sich, um diese virtuellen Computer in der Vorschauversion zu nutzen](https://aka.ms/ndv2signup).
<br>

| Size | vCPU | GPU | Arbeitsspeicher | Netzwerkkarten (max.) | Temporärer Speicher (SSD) GiB | Maximal Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Max. Netzwerkbandbreite | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND40s_v2 | 40 | 8 V100 (NVLink) | 672 GiB | 8 | 2948 | 32 | 80000/800 | 24.000 MBit/s |

## <a name="nd-series"></a>ND-Serie

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

Die virtuellen Computer der ND-Serie sind eine neue Ergänzung der GPU-Familie und für Workloads in den Bereichen KI und Deep Learning konzipiert. Sie bieten eine ausgezeichnete Leistung für Training und Rückschluss. ND-Instanzen werden mit [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf)-GPUs und Intel Xeon E5-2690 v4 (Broadwell)-CPUs betrieben. Diese Instanzen bieten eine ausgezeichnete Leistung für Gleitkommavorgänge mit einfacher Genauigkeit, für KI-Workloads mit Microsoft Cognitive Toolkit sowie für TensorFlow, Caffe und andere Frameworks. Die ND-Serie bietet auch einen wesentlich größeren GPU-Arbeitsspeicher (24 GB) und eignet sich somit für deutlich umfangreichere neurale Netzmodelle. Genau wie die NC-Serie bietet auch die ND-Serie eine Konfiguration mit einem sekundären, RDMA-basierten Netzwerk mit geringer Wartezeit und hohem Durchsatz sowie InfiniBand-Konnektivität, sodass Sie umfangreiche Trainingsaufträge über mehrere GPUs hinweg ausführen können.

> [!IMPORTANT]
> Für diese Größenfamilie ist das regionsspezifische vCPU-Kontingent (Kernkontingent) in Ihrem Abonnement anfänglich auf „0“ festgelegt. Für diese Familie können Sie in einer [verfügbaren Region](https://azure.microsoft.com/regions/services/) eine [Anhebung des vCPU-Kontingents anfordern](../articles/azure-supportability/resource-manager-core-quotas-request.md).
>

| Size | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

Eine GPU entspricht einer P40-Karte.

*RDMA-fähig

## <a name="nv-series"></a>NV-Serie

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

Die virtuellen Computer der NV-Serie nutzen NVIDIA-GPUs vom Typ [Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) sowie NVIDIA GRID-Technologie und ermöglichen die Verwendung beschleunigter Desktopanwendungen und virtueller Desktops, mit denen Kunden ihre Daten oder Simulationen visualisieren können. Benutzer können ihre grafikintensiven Workflows mit den NV-Instanzen visualisieren, um überragende Grafikfunktionen zu erhalten, und darüber hinaus Workloads mit einfacher Genauigkeit ausführen (beispielsweise Codierung und Rendering). Virtuelle Computer der NV-Serie werden außerdem mit Intel Xeon E5-2690 v3 (Haswell)-CPUs betrieben.

Alle GPUs in NV-Instanzen beinhalten eine GRID-Lizenz. Diese Lizenz bietet Ihnen die erforderliche Flexibilität für die Verwendung einer NV-Instanz als virtuelle Arbeitsstation für einen einzelnen Benutzer. Außerdem besteht für ein Szenario mit einer virtuellen Anwendung die Möglichkeit, dass 25 Benutzer gleichzeitig eine Verbindung mit dem virtuellen Computer herstellen.

| Size | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs | Virtuelle Arbeitsstationen | Virtuelle Anwendungen |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1\.440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = halbe M60-Karte

## <a name="nvv3-series-preview-sup1sup"></a>NVv3-Serie (Vorschau) <sup>1</sup>

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

Die virtuellen Computer der NVv3-Serie verfügen über GPUs vom Typ [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) sowie NVIDIA GRID-Technologie mit Intel E5-2690 v4 (Broadwell)-CPUs. Diese virtuellen Computer wurden für GPU-beschleunigte Grafikanwendungen und virtuelle Desktops entwickelt, um Kunden die Datenvisualisierung, Ergebnissimulation, CAD oder das Rendering und Streaming von Inhalten zu erleichtern. Außerdem können diese virtuellen Computer Workloads mit einfacher Genauigkeit wie Codierung und Rendering ausführen. Virtuelle Computer der NVv3-Serie unterstützen Storage Premium und verfügen im Vergleich zur NV-Vorgängerserie über doppelt so viel Systemarbeitsspeicher (RAM).  

Alle GPUs in NVv3-Instanzen beinhalten eine GRID-Lizenz. Diese Lizenz bietet Ihnen die erforderliche Flexibilität für die Verwendung einer NV-Instanz als virtuelle Arbeitsstation für einen einzelnen Benutzer. Außerdem besteht für ein Szenario mit einer virtuellen Anwendung die Möglichkeit, dass 25 Benutzer gleichzeitig eine Verbindung mit dem virtuellen Computer herstellen.

| Size | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs | Virtuelle Arbeitsstationen | Virtuelle Anwendungen | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000/200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = halbe M60-Karte

<sup>1</sup> Virtuelle Computer der NVv3-Serie verfügen über Hyperthreading-Technologie von Intel.
