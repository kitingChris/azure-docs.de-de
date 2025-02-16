---
title: Knotenübersicht für VMware-Lösung von CloudSimple – Azure
description: Erfahren Sie mehr über CloudSimple-Knoten und -Konzepte.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb82e31d58d9955efc3b147eccf2b82b8768aeee
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165800"
---
# <a name="cloudsimple-nodes-overview"></a>Übersicht über CloudSimple-Knoten

Ein Knoten ist:

* Ein dedizierter Bare-Metal-Computehost, auf dem VMware ESXi-Hypervisor installiert ist  
* Eine Recheneinheit, die Sie bereitstellen oder reservieren können, um private Clouds zu erstellen  
* Verfügbar, um in einer Region bereitgestellt oder reserviert zu werden, in der der CloudSimple-Service verfügbar ist

Knoten sind die Bausteine einer privaten Cloud.  Um eine private Cloud zu erstellen, benötigen Sie mindestens drei Knoten derselben SKU.  Wenn Sie eine private Cloud erweitern möchten, fügen Sie zusätzliche Knoten hinzu.  Sie können Knoten zu einem vorhandenen Cluster hinzufügen. Alternativ können Sie einen neuen Cluster erstellen, indem Sie Knoten im Azure-Portal bereitstellen und diese mit dem CloudSimple-Dienst verknüpfen.  Alle bereitgestellten Knoten werden unter dem CloudSimple-Dienst angezeigt.  Sie erstellen eine private Cloud aus den bereitgestellten Knoten im CloudSimple Portal.

## <a name="provisioned-nodes"></a>Bereitgestellte Knoten

Bereitgestellte Knoten bieten Kapazität mit nutzungsbasierter Bezahlung. Durch das Bereitstellen von Knoten können Sie Ihren VMware-Cluster nach Bedarf schnell skalieren. Sie können Knoten bedarfsgerecht hinzufügen, oder Sie können einen bereitgestellten Knoten löschen, um Ihren VMware-Cluster herunterzuskalieren. Bereitgestellte Knoten werden monatlich in Rechnung gestellt und dem Abonnement belastet, in dem sie bereitgestellt werden:

* Wenn Sie Ihr Azure-Abonnement per Kreditkarte bezahlen, wird die Karte sofort belastet.
* Wenn Sie jeweils eine Rechnung erhalten, werden die Gebühren auf Ihrer nächsten Rechnung aufgeführt.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-Lösung entsprechend SKU für CloudSimple-Knoten

Es können Knoten der folgenden Typen bereitgestellt oder reserviert werden.

| SKU | CS28-Knoten | CS36-Knoten |
|-----|-------------|-------------|
| CPU | 2x2,2 GHz, 28 Kerne (56 HT) | 2x2,3 GHz, 36 Kerne (72 HT) |
| RAM | 256 GB | 512 GB |
| Cachedatenträger |  1,6 TB NVMe | 3,2 TB NVMe |
| Datenträgerkapazität | 5,625 TB unformatiert | 11,25 TB unformatiert |
| Speichertyp | Nur Flash | Nur Flash |

## <a name="limits"></a>Einschränkungen

Die folgenden Knotengrenzwerte gelten für private Clouds.

| Resource | Begrenzung |
|----------|-------|
| Mindestanzahl von Knoten, um eine private Cloud zu erstellen | 3 |
| Maximale Anzahl von Knoten in einem Cluster in einer privaten Cloud | 16 |
| Maximale Anzahl von Knoten in einer privaten Cloud | 64 |
| Mindestanzahl von Knoten in einem neuem Cluster | 3 |

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Knoten bereitstellen](create-nodes.md).
* Erfahren Sie mehr über [private Clouds](cloudsimple-private-cloud.md).