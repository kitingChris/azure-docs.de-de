---
title: Diagnoseprotokollierung
titleSuffix: Azure Cognitive Services
description: Diese Anleitung enthält ausführliche Anweisungen zum Aktivieren der Diagnoseprotokollierung für Azure Cognitive Services. Diese Protokolle stellen umfassende, häufig verwendete Daten zu den Vorgängen einer Ressource bereit, die für die Ermittlung von Problemen und das Debuggen verwendet werden.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 3be912f053bf206999546678e1e407548af181bf
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657678"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Aktivieren der Diagnoseprotokollierung für Azure Cognitive Services

Diese Anleitung enthält ausführliche Anweisungen zum Aktivieren der Diagnoseprotokollierung für Azure Cognitive Services. Diese Protokolle stellen umfassende, häufig verwendete Daten zu den Vorgängen einer Ressource bereit, die für die Ermittlung von Problemen und das Debuggen verwendet werden. Bevor Sie fortfahren, benötigen Sie ein Azure-Konto mit einem Abonnement, das mindestens einen der Cognitive Services-Dienste enthält, z. B. [Bing-Websuche](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [Speech-Dienste](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview) oder [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen zum Aktivieren der Diagnoseprotokollierung einen Speicherort für Ihre Protokolldaten. In diesem Tutorial werden Azure Storage und Log Analytics verwendet.

* [Azure Storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) speichert die Diagnoseprotokolle für die Richtlinienüberwachung, statische Analysen oder als Sicherung. Das Speicherkonto muss sich nicht unter demselben Abonnement befinden wie die Ressource, die Protokolle ausgibt, sofern der Benutzer, der die Einstellung konfiguriert, den entsprechenden RBAC-Zugriff auf beide Abonnements hat.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) ist ein flexibles Tool für die Protokollsuche und -analyse, mit dem Sie die unformatierten Protokolle von einer Azure-Ressource analysieren können.

> [!NOTE]
> Es stehen noch weitere Konfigurationsoptionen zur Verfügung. Weitere Informationen finden Sie unter [Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Aktivieren der Diagnoseprotokollierung  

Beginnen Sie mit dem Aktivieren der Diagnoseprotokollierung im Azure-Portal.

> [!NOTE]
> Um dieses Feature mithilfe von PowerShell oder der Azure-Befehlszeilenschnittstelle zu aktivieren, gehen Sie anhand der Anweisungen unter [Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#diagnostic-settings) vor.

1. Navigieren Sie zum Azure-Portal. Suchen Sie anschließend eine Cognitive Services-Ressource, und wählen Sie sie aus. Sie können z. B. Ihr Abonnement für die Bing-Websuche verwenden.   
2. Suchen Sie danach im Navigationsmenü auf der linken Seite nach **Überwachung**, und wählen Sie **Diagnoseeinstellungen** aus. Dieser Bildschirm enthält alle zuvor erstellten Diagnoseeinstellungen für diese Ressource.
3. Wenn Sie eine zuvor erstellte Ressource verwenden möchten, können Sie diese nun auswählen. Wählen Sie andernfalls **+ Diagnoseeinstellung hinzufügen** aus.
4. Geben Sie einen Namen für die Einstellung ein. Wählen Sie **In einem Speicherkonto archivieren** und dann **An Log Analytics senden** aus.
5. Wenn Sie zur Konfiguration aufgefordert werden, wählen Sie das Speicherkonto und einen OMS-Arbeitsbereich zum Speichern der Diagnoseprotokolle aus. **Hinweis**: Wenn Sie nicht über ein Speicherkonto oder einen OMS-Arbeitsbereich verfügen, befolgen Sie die Anweisungen zum Erstellen.
6. Wählen Sie **Überwachung**, **RequestResponse** und **AllMetrics** aus. Legen Sie dann den Aufbewahrungszeitraum für Ihre Diagnoseprotokolldaten fest. Wird die Aufbewahrungsrichtlinie auf Null festgelegt, werden Ereignisse für diese Protokollkategorie unbegrenzt gespeichert.
7. Klicken Sie auf **Speichern**.

Es kann bis zu zwei Stunden dauern, bevor Protokollierungsdaten für Abfragen und Analysen zur Verfügung stehen. Sie müssen sich also keine Sorgen machen, wenn nicht sofort etwas angezeigt wird.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Anzeigen und Exportieren von Diagnosedaten aus Azure Storage

Azure Storage ist eine stabile Objektspeicherlösung, die für das Speichern großer Mengen unstrukturierter Daten optimiert ist. In diesem Abschnitt erfahren Sie, wie Sie Ihr Speicherkonto nach der Summe der Transaktionen über einen Zeitraum von 30 Tage abfragen und die Daten nach Excel exportieren.

1. Suchen Sie im Azure-Portal die Azure Storage-Ressource, die Sie im vorherigen Abschnitt erstellt haben.
2. Suchen Sie im Navigationsmenü auf der linken Seite nach **Überwachung**, und wählen Sie **Metriken** aus.
3. Verwenden Sie die verfügbaren Dropdownmenüs, um Ihre Abfrage zu konfigurieren. In diesem Beispiel legen Sie den Zeitbereich auf **Letzte 30 Tage** und die Metrik auf **Transaktion** fest.
4. Wenn die Abfrage abgeschlossen ist, wird eine Visualisierung der Transaktionen der letzten 30 Tage angezeigt. Verwenden Sie zum Exportieren dieser Daten die Schaltfläche **Exportieren in Excel** am oberen Rand der Seite.

Erfahren Sie mehr über die Verwendungsmöglichkeiten von Diagnosedaten in [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Anzeigen von Protokollen in Log Analytics

Gehen Sie anhand der folgenden Anweisungen vor, um die Log Analytics-Daten für Ihre Ressource zu untersuchen.

1. Suchen Sie im Azure-Portal im Navigationsmenü auf der linken Seite nach **Log Analytics**, und wählen Sie die Option aus.
2. Suchen Sie die Ressource, die Sie beim Aktivieren der Diagnose erstellt haben, und wählen Sie sie aus.
3. Wählen Sie unter **Allgemein** die Option **Protokolle** aus. Sie können auf dieser Seite Abfragen für Ihre Protokolle ausführen.

### <a name="sample-queries"></a>Beispielabfragen

Im Folgenden finden Sie einige grundlegende Kusto-Abfragen, mit denen Sie Ihre Protokolldaten untersuchen können.

Führen Sie diese Abfrage aus, um alle Diagnoseprotokolle von Azure Cognitive Services für einen angegebenen Zeitraum anzuzeigen:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Führen Sie diese Abfrage aus, um die letzten 10 Protokolle anzuzeigen:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Führen Sie diese Abfrage aus, um die Vorgänge nach **Ressource** zu gruppieren:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Führen Sie diese Abfrage aus, um die durchschnittliche Zeit für die Ausführung eines Vorgangs zu ermitteln:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Führen Sie diese Abfrage aus, um die Menge der Vorgänge im Laufe der Zeit, aufgeteilt nach OperationName und 10-Sekunden-Fenstern anzuzeigen:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Nächste Schritte

* Wenn Sie sich mit dem Aktivieren der Protokollierung vertraut machen und darüber hinaus ein Verständnis der von den verschiedenen Azure-Diensten unterstützten Metriken und Protokollkategorien erlangen möchten, lesen Sie die Artikel [Azure Monitor-Datenplattform](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) in Microsoft Azure und [Übersicht über Azure-Diagnoseprotokolle](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).
* Lesen Sie diese Artikel durch, um sich über Event Hubs zu informieren:
  * [Was ist Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Erste Schritte mit Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Lesen Sie [Herunterladen von Metrik- und Diagnoseprotokollen aus Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Lesen Sie [Grundlegendes zu Protokollsuchvorgängen in Azure Monitor-Protokolle](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
