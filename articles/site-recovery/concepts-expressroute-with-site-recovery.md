---
title: Informationen zum Verwenden von Azure ExpressRoute mit Azure Site Recovery für Notfallwiederherstellung und Migration | Microsoft-Dokumentation
description: Beschreibt, wie Sie Azure ExpressRoute mit dem Azure Site Recovery-Dienst für Notfallwiederherstellung und Migration verwenden.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: mayg
ms.openlocfilehash: 35fa26112a6026ab05bd59b38621de7ee802c715
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491891"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute mit Azure Site Recovery

Mit Microsoft Azure ExpressRoute können Sie Ihre lokalen Netzwerke über eine private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, in die Microsoft Cloud erweitern. Mit ExpressRoute können Sie Verbindungen zu Microsoft-Clouddiensten herstellen, z.B. Microsoft Azure, Office 365 und Dynamics 365.

In diesem Artikel wird beschrieben, wie Sie Azure ExpressRoute mit Azure Site Recovery für die Notfallwiederherstellung und die Migration verwenden können.

## <a name="expressroute-circuits"></a>ExpressRoute-Verbindungen

Eine ExpressRoute-Verbindung stellt eine logische Verbindung zwischen der lokalen Infrastruktur und den Microsoft-Clouddiensten über einen Konnektivitätsanbieter dar. Sie können mehrere ExpressRoute-Verbindungen bestellen. Alle Verbindungen können sich in derselben Region oder in verschiedenen Regionen befinden und über verschiedene Konnektivitätsanbieter mit dem jeweiligen Standort verbunden sein. Weitere Informationen zu ExpressRoute-Verbindungen finden Sie [hier](../expressroute/expressroute-circuit-peerings.md).

Einer ExpressRoute-Verbindung sind mehrere Routingdomänen zugeordnet. Weitere Informationen zu und eine Gegenüberstellung von ExpressRoute-Routingdomänen finden Sie [hier](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Replikation zwischen einem lokalen Standort und Azure mit ExpressRoute

Azure Site Recovery ermöglicht die Notfallwiederherstellung und Migration zu Azure für lokale [Hyper-V-Computer](hyper-v-azure-architecture.md), [virtuelle VMware-Computer](vmware-azure-architecture.md) und [physische Server](physical-azure-architecture.md). In allen Szenarien der Migration von einem lokalen Standort zu Azure werden die Replikationsdaten an ein Azure Storage-Konto gesendet und darin gespeichert. Während der Replikation zahlen Sie keine Gebühren für die VM. Wenn Sie einen Failover zu Azure ausführen, erstellt Site Recovery automatisch Azure-IaaS-VMs.

Site Recovery repliziert Daten über einen öffentlichen Endpunkt in ein Azure Storage-Konto oder in verwaltete Replikatdatenträger. Um ExpressRoute für den Site Recovery-Replikationsdatenverkehr zu verwenden, können Sie [Microsoft-Peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) oder ein vorhandenes [öffentliches Peering](../expressroute/expressroute-circuit-peerings.md#publicpeering) (veraltet, für neue Erstellungen nicht mehr verwenden) verwenden. Microsoft-Peering ist die empfohlene Routingdomäne für die Replikation. Bitte beachten Sie, dass Replikation über privates Peering nicht unterstützt wird.

Stellen Sie sicher, dass die [Netzwerkanforderungen](vmware-azure-configuration-server-requirements.md#network-requirements) auch für den Konfigurationsserver erfüllt sind. Der Konfigurationsserver muss für die Orchestrierung der Site Recovery-Replikation eine Verbindung mit bestimmten URLs aufnehmen. Für diese Verbindung können Sie ExpressRoute nicht nutzen. 

Wenn Sie den Proxy vor Ort verwenden und ExpressRoute für den Replikationsdatenverkehr verwenden möchten, müssen Sie die Proxyumgehungsliste auf dem Konfigurationsserver und den Prozessservern konfigurieren. Führen Sie die folgenden Schritte aus:

- Laden Sie das Tool PsExec [hier](https://aka.ms/PsExec) herunter, um auf den Systembenutzerkontext zuzugreifen.
- Öffnen Sie Internet Explorer im Systembenutzercontext, indem Sie die folgende Befehlszeile ausführen: psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Hinzufügen von Proxyeinstellungen in Internet Explorer
- Fügen Sie in der Umgehungsliste die Azure-Speicher-URL *.blob.core.windows.net hinzu.

Dadurch wird sichergestellt, dass nur Replikationsdatenverkehr über ExpressRoute fließt, während die Kommunikation über Proxy laufen kann.

Nach dem Failover virtueller Computer oder Server zu einem virtuellen Azure-Netzwerk können Sie mit [privatem Peering](../expressroute/expressroute-circuit-peerings.md#privatepeering) auf sie zugreifen. 

Dieses kombinierte Szenario ist im folgenden Diagramm dargestellt: ![Konnektivität zwischen lokalen Ressourcen und Azure mit ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikation von Azure zu Azure mit ExpressRoute

Azure Site Recovery ermöglicht die Notfallwiederherstellung für [Azure-VMs](azure-to-azure-architecture.md). Je nachdem, ob Ihre virtuellen Azure-Computer [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) verwenden, werden Replikationsdaten an ein Azure Storage-Konto oder einen verwalteten Replikatdatenträger in der Azure-Zielregion gesendet. Obwohl die Replikationsendpunkte öffentlich sind, läuft der Replikationsdatenverkehr für die Replikation von Azure VM nicht über das Internet, unabhängig davon, in welcher Azure-Region sich das virtuelle Quellnetzwerk befindet. Sie können die Standardsystemroute von Azure für das Adresspräfix 0.0.0.0/0 mit einer [benutzerdefinierten Route](../virtual-network/virtual-networks-udr-overview.md#custom-routes) überschreiben und VM-Datenverkehr auf ein lokales virtuelles Netzwerkgerät umleiten, aber diese Konfiguration wird für die Site Recovery-Replikation nicht empfohlen. Wenn Sie benutzerdefinierte Routen verwenden, sollten Sie [einen VNET-Dienstendpunkt](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) in Ihrem virtuellen Netzwerk für „Storage“ erstellen, damit der Replikationsdatenverkehr innerhalb der Azure-Begrenzung bleibt.

Für die Notfallwiederherstellung einer Azure-VM wird ExpressRoute standardmäßig nicht für die Replikation benötigt. Nachdem dem Failover der virtuellen Computer zur Azure-Zielregion können Sie darauf über [privates Peering](../expressroute/expressroute-circuit-peerings.md#privatepeering) zugreifen.

Wenn Sie für die Verbindung zwischen Ihrem lokalen Datencenter und den Azure-VMs in der Quellregion bereits ExpressRoute verwenden, können Sie die Wiederherstellung der ExpressRoute-Verbindungen in der Failover-Zielregion planen. Sie können für die Verbindung zur Zielregion über eine neue virtuelle Netzwerkverbindung die gleiche ExpressRoute-Verbindung verwenden oder eine separate ExpressRoute-Verbindung und eine Verbindung für die Notfallwiederherstellung nutzen. Die verschiedenen möglichen Szenarien werden [hier](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute) beschrieben.

Sie können virtuelle Azure-Computer zu einer beliebigen Azure-Region innerhalb des gleichen geografischen Clusters replizieren, wie [hier](../site-recovery/azure-to-azure-support-matrix.md#region-support) beschrieben. Wenn sich die ausgewählte Azure-Zielregion nicht innerhalb der gleichen geopolitischen Region wie die Quelle befindet, müssen Sie möglicherweise ExpressRoute Premium aktivieren. Weitere Informationen finden Sie unter [ExpressRoute-Standorte](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) und [ExpressRoute – Preise](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [ExpressRoute-Verbindungen](../expressroute/expressroute-circuit-peerings.md)
- Weitere Informationen zu [ExpressRoute-Routingdomänen](../expressroute/expressroute-circuit-peerings.md#peeringcompare)
- Weitere Informationen zu [ExpressRoute-Standorten](../expressroute/expressroute-locations.md)
- Weitere Informationen zur Notfallwiederherstellung von [virtuellen Azure-Computern mit ExpressRoute ](azure-vm-disaster-recovery-with-expressroute.md).
