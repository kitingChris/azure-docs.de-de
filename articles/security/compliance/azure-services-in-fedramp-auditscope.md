---
title: Azure-Dienste im FedRAMP- und DoD SRG-Prüfumfang
description: Dieser Artikel enthält Tabellen für öffentliche Azure-Instanzen und Azure Government, die veranschaulichen, welchen FedRAMP-Prüfumfang („Mittel“ gegenüber „Hoch“) und DoD SRG-Prüfumfang (Impact Level 2, 4 oder 5) ein bestimmter Dienst erreicht hat.
author: davib
ms.author: davib
ms.date: 5/17/2019
ms.topic: article
ms.service: security
ms.reviewer: rochiou
ms.openlocfilehash: 922098a67d148d5860145663ea1c5bf543c8aaec
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294824"
---
# <a name="azure-services-by-fedramp-and-dod-cc-srg-audit-scope"></a>Azure-Dienste nach FedRAMP- und DoD CC SRG-Prüfumfang

Microsoft Government-Clouddienste erfüllen die anspruchsvollen Vorgaben des Federal Risk and Authorization Management Program der USA (FedRAMP) sowie die des US-Verteidigungsministeriums, von Impact Level 2 bis 5. Durch die Bereitstellung geschützter Dienste, einschließlich Azure Government, Office 365 U.S. Government und Dynamics 365 Government, können Bundes- und Verteidigungsbehörden eine Vielzahl von konformen Diensten nutzen.

Dieser Artikel enthält eine detaillierte Liste der In-Scope-Clouddienste in Azure und Azure Government für FedRAMP- und DoD CC SRG-Complianceangebote.

#### <a name="terminologysymbols-used"></a>Verwendete Terminologie/Symbole

* DoD CC SRG = Department of Defense Cloud Computing Security Requirements Guide (Handbuch für Sicherheitsanforderungen für Cloud Computing des US-Verteidigungsministeriums)
* IIL = Information Impact Level
* FedRAMP = Federal Risk and Authorization Management Program (FedRAMP)  
* :heavy_check_mark: = Zeigt an, dass der Dienst diesen Prüfumfang erreicht hat.

## <a name="azure-public-services-by-audit-scope"></a>Azure Public-Dienste nach Prüfumfang
| _Letzte Aktualisierung: Juni 2019_ |

| Azure-Dienst| DoD CC SRG IIL 2 | FedRAMP Moderate | FedRAMP High | Geplant 2019 |
| ------------ |:---------------:|:----------------:|:------------:|:------------:|
| [API Management](https://azure.microsoft.com/services/api-management/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Application Gateway](https://azure.microsoft.com/services/application-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Automatisierung](https://azure.microsoft.com/services/automation/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Active Directory (Free und Basic)](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Active Directory (Premium P1 + P2)](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) |  |  |  | :heavy_check_mark: |
| [Azure Active Directory-Domänendienste](https://azure.microsoft.com/services/active-directory-ds/) | |  |  | :heavy_check_mark: |
| [Azure Advanced Threat Protection](https://azure.microsoft.com/features/azure-advanced-threat-protection/) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Advisor](https://azure.microsoft.com/services/advisor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure-Archivspeicher](https://azure.microsoft.com/services/storage/archive/) |  |  |  | :heavy_check_mark: |
| [Azure Container Service](https://docs.microsoft.com/azure/container-service/) |  |  |  | :heavy_check_mark: |
| [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Data Box](https://azure.microsoft.com/services/databox/) | |  |  | :heavy_check_mark: |
| [Azure Data Lake Storage Gen1](https://azure.microsoft.com/services/storage/data-lake-storage/) |  |  |  | :heavy_check_mark: |
| [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure-Datenbank für PostgreSQL](https://azure.microsoft.com/services/postgresql/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Database for MariaDB](https://azure.microsoft.com/services/mariadb/) |  |  |  | :heavy_check_mark: |
| [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) |  |  |  |  |
| [Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Dediziertes HSM von Azure](https://azure.microsoft.com/services/azure-dedicated-hsm/) |  |  |  | :heavy_check_mark: |
| [Azure DevOps (früher VSTS)](https://azure.microsoft.com/services/devops/) | |  |  | |
| [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) |  |  |  | :heavy_check_mark: |
| [Azure DNS](https://azure.microsoft.com/services/dns/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure-Dateisynchronisierung ](https://azure.microsoft.com/services/storage/files/) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Intune](https://docs.microsoft.com/intune/what-is-intune) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) | |  |  | :heavy_check_mark: |
| [Azure Maps](https://azure.microsoft.com/services/azure-maps/) |  |  |  | :heavy_check_mark: |
| [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Monitor](https://azure.microsoft.com/services/monitor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Policy](https://azure.microsoft.com/services/azure-policy/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Search](https://azure.microsoft.com/services/search/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Service Manager (RDFE)](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Sicherung](https://azure.microsoft.com/services/backup/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Batch](https://azure.microsoft.com/services/batch/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cloud Services](https://azure.microsoft.com/services/cloud-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cognitive Services: Maschinelles Sehen](https://azure.microsoft.com/services/cognitive-services/computer-vision/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cognitive Services: Content Moderator](https://azure.microsoft.com/services/cognitive-services/content-moderator/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cognitive Services: Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cognitive Services: Gesichtserkennung](https://azure.microsoft.com/services/cognitive-services/face/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cognitive Services: Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cognitive Services: QnA Maker](https://azure.microsoft.com/services/cognitive-services/qna-maker/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cognitive Services: Speech-Dienste](https://azure.microsoft.com/services/cognitive-services/directory/speech/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cognitive Services: Textanalyse](https://azure.microsoft.com/services/cognitive-services/text-analytics/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cognitive Services: Textübersetzung](https://azure.microsoft.com/services/cognitive-services/speech-translation/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cognitive Services: Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Containerinstanzen](https://azure.microsoft.com/services/container-instances/) |  |  |  | :heavy_check_mark: |
| [Containerregistrierung](https://azure.microsoft.com/services/container-registry/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Content Delivery Network](https://azure.microsoft.com/services/cdn/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Data Catalog](https://azure.microsoft.com/services/data-catalog/) |  |  |  | :heavy_check_mark: |
| [Data Factory](https://azure.microsoft.com/services/data-factory/) |  |  |  | :heavy_check_mark: |
| [Event Grid](https://azure.microsoft.com/services/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Event Hubs](https://azure.microsoft.com/services/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ablauf](https://docs.microsoft.com/flow/getting-started) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Funktionen](https://azure.microsoft.com/services/functions/) |  |  |  | :heavy_check_mark: |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Import/Export](https://azure.microsoft.com/services/storage/import-export/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [IoT Central](https://azure.microsoft.com/services/iot-central/) |  |  |  | :heavy_check_mark: |
| [IoT Hub](https://azure.microsoft.com/services/iot-hub/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Schlüsseltresor](https://azure.microsoft.com/services/key-vault/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Load Balancer](https://azure.microsoft.com/services/load-balancer/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Logik-Apps](https://azure.microsoft.com/services/logic-apps/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Machine Learning Services](https://azure.microsoft.com/services/machine-learning-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Machine Learning Studio](https://azure.microsoft.com/services/machine-learning-studio/) |  |  |  | :heavy_check_mark: |
| [Media Services](https://azure.microsoft.com/services/media-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Azure-Portal](https://azure.microsoft.com/features/azure-portal/)| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Genomics](https://azure.microsoft.com/services/genomics/) |  |  |  | :heavy_check_mark: |
| [Microsoft PowerApps](https://docs.microsoft.com/powerapps/powerapps-overview) | | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Stream](https://docs.microsoft.com/stream/overview) | | :heavy_check_mark: | :heavy_check_mark: |  |
| [Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Network Watcher](https://azure.microsoft.com/services/network-watcher/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Network Watcher Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Notification Hubs](https://azure.microsoft.com/services/notification-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Redis Cache](https://azure.microsoft.com/services/cache/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Scheduler](https://azure.microsoft.com/services/scheduler/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Security Center](https://azure.microsoft.com/services/security-center/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Service Bus](https://azure.microsoft.com/services/service-bus/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Service Fabric](https://azure.microsoft.com/services/service-fabric/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [SQL Server Stretch Database](https://azure.microsoft.com/services/sql-server-stretch-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Speicher: Blobs](https://azure.microsoft.com/services/storage/blobs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Speicher: Datenträger (inkl. verwalteter Datenträger)](https://azure.microsoft.com/services/storage/disks/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Speicher: Dateien](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Speicher: Warteschlangen](https://azure.microsoft.com/services/storage/queues/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Speicher: Tabellen](https://azure.microsoft.com/services/storage/tables/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [StorSimple](https://azure.microsoft.com/services/storsimple/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Skalierungsgruppen für virtuelle Computer](https://azure.microsoft.com/services/virtual-machine-scale-sets/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Virtuelle Computer (inkl. reservierter Instanzen)](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Virtual Network](https://azure.microsoft.com/services/virtual-network/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [VPN Gateway](https://azure.microsoft.com/services/vpn-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Web-Apps (App Service)](https://azure.microsoft.com/services/app-service/web/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="azure-government-services-by-audit-scope"></a>Azure Government-Dienste nach Prüfumfang
| _Letzte Aktualisierung: Juni 2019_ |

| Azure-Dienst | DoD CC SRG IIL 2 | DoD CC SRG IIL 4 | DoD CC SRG IIL 5 | FedRAMP High | Geplant 2019
| ------------- |:---------------:|:---------------:|:---------------:|:------------:|:------------:
| [API Management](https://azure.microsoft.com/services/api-management/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Application Gateway](https://azure.microsoft.com/services/application-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) |  |  |  |  | :heavy_check_mark:
| [Automatisierung](https://azure.microsoft.com/services/automation/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: |
| [Azure Active Directory (Free und Basic)](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Active Directory (Premium P1 + P2)](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Advisor](https://azure.microsoft.com/services/advisor/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/) |  |  |  |  | :heavy_check_mark:
| [Containerregistrierung](https://azure.microsoft.com/services/container-registry/) |  |  |  |  | :heavy_check_mark:
| [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Data Factory](https://azure.microsoft.com/services/data-factory/)  |  |  |  |  | :heavy_check_mark:
| [Azure-Datenbank für MySQL](https://azure.microsoft.com/services/mysql/)| :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure-Datenbank für PostgreSQL](https://azure.microsoft.com/services/postgresql/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure DB for MariaDB](https://azure.microsoft.com/services/mariadb/)  |  |  |  |  | :heavy_check_mark:
| [Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/) |  |  |  |  | :heavy_check_mark:
| [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/)  |  |  |  |  | :heavy_check_mark:
| [Azure DNS](https://azure.microsoft.com/services/dns/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)  |  |  |  |  | :heavy_check_mark:
| [Azure-Dateisynchronisierung ](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Intune](https://docs.microsoft.com/intune/what-is-intune) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Lab Services](https://azure.microsoft.com/services/lab-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Monitor](https://azure.microsoft.com/services/monitor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Policy](https://azure.microsoft.com/services/azure-policy/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: **&ast;** | :heavy_check_mark: |
| [Azure Security Center](https://azure.microsoft.com/services/security-center/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Service Manager (RDFE)](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Sicherung](https://azure.microsoft.com/services/backup/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Batch](https://azure.microsoft.com/services/batch/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Cloud Services](https://azure.microsoft.com/services/cloud-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Cognitive Services: Maschinelles Sehen](https://azure.microsoft.com/services/cognitive-services/computer-vision/)  |  |  |  |  | :heavy_check_mark:
| [Cognitive Services: Content Moderator](https://azure.microsoft.com/services/cognitive-services/content-moderator/)  |  |  |  |  | :heavy_check_mark:
| [Cognitive Services: Gesichtserkennung](https://azure.microsoft.com/services/cognitive-services/face/)  |  |  |  |  | :heavy_check_mark:
| [Cognitive Services: Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)  |  |  |  |  | :heavy_check_mark:
| [Cognitive Services: Textanalyse](https://azure.microsoft.com/services/cognitive-services/text-analytics/)  |  |  |  |  | :heavy_check_mark:
| [Cognitive Services: Textübersetzung](https://azure.microsoft.com/services/cognitive-services/speech-translation/) |  |  |  |  | :heavy_check_mark:
| [Event Hubs](https://azure.microsoft.com/services/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Ablauf](https://docs.microsoft.com/flow/getting-started) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Funktionen](https://azure.microsoft.com/services/functions/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Import/Export](https://azure.microsoft.com/services/storage/import-export/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [IoT Hub](https://azure.microsoft.com/services/iot-hub/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Schlüsseltresor](https://azure.microsoft.com/services/key-vault/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](https://azure.microsoft.com/services/load-balancer/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Logik-Apps](https://azure.microsoft.com/services/logic-apps/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Media Services](https://azure.microsoft.com/services/media-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Microsoft Azure-Portal](https://azure.microsoft.com/features/azure-portal/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: **&ast;** | :heavy_check_mark: |
| [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  |  |  |  |  | :heavy_check_mark:
| [Microsoft Graph](https://docs.microsoft.com/graph/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Microsoft PowerApps](https://docs.microsoft.com/powerapps/powerapps-overview) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Microsoft Stream](https://docs.microsoft.com/stream/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Network Watcher](https://azure.microsoft.com/services/network-watcher/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Notification Hubs](https://azure.microsoft.com/services/notification-hubs/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Redis Cache](https://azure.microsoft.com/services/cache/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Scheduler](https://azure.microsoft.com/services/scheduler/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](https://azure.microsoft.com/services/service-bus/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://azure.microsoft.com/services/service-fabric/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [SQL Server Stretch Database](https://azure.microsoft.com/services/sql-server-stretch-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Speicher: Blobs](https://azure.microsoft.com/services/storage/blobs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Speicher: Datenträger (inkl. verwalteter Datenträger)](https://azure.microsoft.com/services/storage/disks/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Speicher: Dateien](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Speicher: Warteschlangen](https://azure.microsoft.com/services/storage/queues/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Speicher: Tabellen](https://azure.microsoft.com/services/storage/tables/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [StorSimple](https://azure.microsoft.com/services/storsimple/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Skalierungsgruppen für virtuelle Computer](https://azure.microsoft.com/services/virtual-machine-scale-sets/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtuelle Computer](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](https://azure.microsoft.com/services/virtual-network/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](https://azure.microsoft.com/services/vpn-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Web-Apps (App Service)](https://azure.microsoft.com/services/app-service/web/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

**&ast;** Einige Dienste wie das Azure-Portal und Azure Resource Manager speichern oder verarbeiten keine DoD CC SRG IIL 5-Inhalte, erben aber den IIL5-Kontrollsatz.

