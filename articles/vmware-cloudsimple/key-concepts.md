---
title: Wichtige Konzepte für die Verwaltung der Azure-VMware-Lösung von CloudSimple
description: Hier werden wichtige Konzepte für die Verwaltung der Azure-VMware-Lösung von CloudSimple beschrieben.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6d87871fe8faaaab2e56d4a0426cd5e5f0899c8f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595619"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Wichtige Konzepte für die Verwaltung der Azure-VMware-Lösung von CloudSimple

Für die Verwaltung der Azure-VMware-Lösung von CloudSimple müssen folgende Konzepte bekannt sein:

* CloudSimple-Dienst, angezeigt als: Azure-VMware-Lösung von CloudSimple – Dienst
* CloudSimple-Knoten, angezeigt als: Azure-VMware-Lösung von CloudSimple – Knoten
* Private CloudSimple-Cloud
* Dienstnetzwerk
* Virtueller CloudSimple-Computer, angezeigt als: Azure-VMware-Lösung von CloudSimple – virtueller Computer

## <a name="cloudsimple-service"></a>CloudSimple-Dienst

Mit dem CloudSimple-Dienst können Sie sämtliche Ressourcen für VMware-Lösungen von CloudSimple über das Azure-Portal erstellen und verwalten. Erstellen Sie in jeder Region, in der Sie den Dienst verwenden möchten, eine Dienstressource.

Weitere Informationen zum CloudSimple-Dienst finden Sie [hier](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>CloudSimple-Knoten

Bei einem CloudSimple-Knoten handelt es sich um einen dedizierten, hyperkonvergenten Bare-Metal-Compute- und Speicherhost, auf dem der VMware ESXi-Hypervisor bereitgestellt wird. Dieser Knoten wird dann in die VMware vSphere-, vCenter-, vSAN- und NSX-Plattform integriert. CloudSimple-Netzwerkdienste und Umkreisnetzwerkdienste werden ebenfalls aktiviert. Jeder Knoten fungiert als Compute- und Speicherkapazitätseinheit, die Sie zur Erstellung [privater CloudSimple-Clouds](cloudsimple-private-cloud.md) bereitstellen können. Sie stellen Knoten in einer Region bereit, in der der CloudSimple-Dienst verfügbar ist, oder reservieren sie.


Weitere Informationen zu CloudSimple-Knoten finden Sie [hier](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>Private CloudSimple-Cloud

Eine private CloudSimple-Cloud ist eine isolierte VMware-Stapelumgebung, die von einem vCenter-Server in einer eigenen Verwaltungsdomäne verwaltet wird. Der VMware-Stapel beinhaltet ESXi-Hosts, vSphere, vCenter, vSAN und NSX. Der Stapel wird auf dedizierten Knoten (dedizierte und isolierte Bare-Metal-Hardware) ausgeführt und von Benutzern über native VMware-Tools (einschließlich vCenter und NSX Manager) genutzt. Dedizierte Knoten werden an Azure-Standorten bereitgestellt und von Azure verwaltet. Jede private Cloud kann mithilfe von Netzwerkdiensten wie VLANs und Subnetzen sowie Firewalltabellen segmentiert und geschützt werden. Für die Verbindungsherstellung mit Ihrer lokalen Umgebung und dem Azure-Netzwerk werden ein sicheres, privates VPN sowie Azure ExpressRoute-Verbindungen verwendet.

Weitere Informationen zur privaten CloudSimple-Cloud finden Sie [hier](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Dienstnetzwerk

Der CloudSimple-Dienst stellt jeweils ein Netzwerk pro Region bereit, in der Ihr CloudSimple-Dienst bereitgestellt wird. Das Netzwerk ist ein einzelner TCP-Schicht-3-Adressraum mit standardmäßig aktiviertem Routing. Alle privaten Clouds und Subnetze, die in dieser Region erstellt werden, kommunizieren ohne zusätzliche Konfiguration miteinander. Verteilte Portgruppen in vCenter werden unter Verwendung von VLANs erstellt. Die Workloadressourcen in Ihrer privaten Cloud können mithilfe der folgenden Netzwerkfeatures konfiguriert und geschützt werden:

* [VLANs und Subnetze](cloudsimple-vlans-subnets.md)
* [Firewalltabellen](cloudsimple-firewall-tables.md)
* [VPN-Gateways](cloudsimple-vpn-gateways.md)
* [Öffentliche IP-Adresse](cloudsimple-public-ip-address.md)
* [Azure-Netzwerkverbindung](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Virtueller CloudSimple-Computer

Mit dem CloudSimple-Dienst können Sie virtuelle VMware-Computer über das Azure-Portal verwalten. Cluster oder Ressourcenpools aus Ihrer vSphere-Umgebung können dem Abonnement zugeordnet werden, für das der Dienst erstellt wird.

Weitere Informationen:

* [Virtuelle CloudSimple-Computer](cloudsimple-virtual-machines.md)
* [Azure-Abonnementzuordnung](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
