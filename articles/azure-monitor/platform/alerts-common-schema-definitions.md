---
title: Definitionen des allgemeinen Warnungsschemas für Webhooks/Logic Apps/Azure Functions/Automation Runbooks
description: Grundlagen der Definitionen des allgemeinen Warnungsschemas für Webhooks/Logic Apps/Azure Functions/Automation Runbooks
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: c37ecfbadd7345fea347ff488895f16ba505c818
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594380"
---
# <a name="common-alert-schema-definitions"></a>Definitionen des allgemeinen Warnungsschemas

In diesem Artikel werden die [Definitionen des allgemeinen Warnungsschemas](https://aka.ms/commonAlertSchemaDocs) für Webhooks/Logic Apps/Azure Functions/Automation Runbooks beschrieben. 

## <a name="overview"></a>Übersicht

Mit jeder Warnungsinstanz werden **die betroffene Ressource** und **die Ursache der Warnung** beschrieben. Diese Instanzen werden im allgemeinen Schema in den folgenden Abschnitten beschrieben:
* **Zusammenfassung**: Eine Gruppe **standardisierter Felder** (für alle Warnungstypen gleich), die beschreiben, auf **welcher Ressource** sich die Warnung befindet, sowie zusätzliche allgemeine Warnungsmetadaten (z. B. Schweregrad oder Beschreibung). 
* **Warnungskontext**: Eine Gruppe von Feldern, mit denen die **Ursache der Warnung** beschrieben wird. Die Felder variieren **basierend auf dem Warnungstyp**. Eine Metrikwarnung enthält im Warnungskontext beispielsweise Felder wie den Metriknamen und -wert, während eine Aktivitätsprotokollwarnung Informationen zum Ereignis enthält, von dem die Warnung generiert wurde. 

##### <a name="sample-alert-payload"></a>Beispielnutzlast einer Warnung
```json
{
  "schemaId": "azureMonitorCommonAlertSchema",
  "data": {
    "essentials": {
      "alertId": "/subscriptions/<subscription ID>/providers/Microsoft.AlertsManagement/alerts/b9569717-bc32-442f-add5-83a997729330",
      "alertRule": "WCUS-R2-Gen2",
      "severity": "Sev3",
      "signalType": "Metric",
      "monitorCondition": "Resolved",
      "monitoringService": "Platform",
      "alertTargetIDs": [
        "/subscriptions/<subscription ID>/resourcegroups/pipelinealertrg/providers/microsoft.compute/virtualmachines/wcus-r2-gen2"
      ],
      "originAlertId": "3f2d4487-b0fc-4125-8bd5-7ad17384221e_PipeLineAlertRG_microsoft.insights_metricAlerts_WCUS-R2-Gen2_-117781227",
      "firedDateTime": "2019-03-22T13:58:24.3713213Z",
      "resolvedDateTime": "2019-03-22T14:03:16.2246313Z",
      "description": "",
      "essentialsVersion": "1.0",
      "alertContextVersion": "1.0"
    },
    "alertContext": {
      "properties": null,
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Percentage CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "operator": "GreaterThan",
            "threshold": "25",
            "timeAggregation": "Average",
            "dimensions": [
              {
                "name": "ResourceId",
                "value": "3efad9dc-3d50-4eac-9c87-8b3fd6f97e4e"
              }
            ],
            "metricValue": 7.727
          }
        ]
      }
    }
  }
}
```

## <a name="essentials-fields"></a>Felder in „Essentials“

| Feld | BESCHREIBUNG|
|:---|:---|
| alertId | GUID, die diese Warnungsinstanz eindeutig identifiziert. |
| alertRule | Name der Warnungsregel, die die Warnungsinstanz generiert hat. |
| severity | Schweregrad der Warnung Mögliche Werte: Sev0, Sev1, Sev2, Sev3, Sev4 |
| signalType | Identifiziert das Signal, für das die Warnungsregel definiert wurde. Mögliche Werte: Metrik, Protokoll, Aktivitätsprotokoll |
| monitorCondition | Wenn eine Warnung ausgelöst wird, wird die Überwachungsbedingung der Warnung auf „Ausgelöst“ festgelegt. Wenn die zugrunde liegende Bedingung gelöscht wurde, die die Warnung ausgelöst hat, wird die Überwachungsbedingung auf „Behoben“ festgelegt.   |
| monitoringService | Der Überwachungsdienst oder die Lösung, von dem bzw. der die Warnung generiert wurde. Die Felder für den Warnungskontext werden vom Überwachungsdienst vorgegeben. |
| alertTargetIds | Liste der ARM-IDs aller betroffenen Ziele einer Warnung. Für eine Protokollwarnung, die in einem Log Analytics-Arbeitsbereich oder einer Application Insights-Instanz definiert ist, ist es der jeweilige Arbeitsbereich bzw. die jeweilige Anwendung. |
| originAlertId | ID der Warnungsinstanz, wie sie vom überwachenden Dienst generiert wird, der sie generiert. |
| firedDateTime | Datum/Uhrzeit der Auslösung der Warnungsinstanz in UTC |
| resolvedDateTime | Datum/Uhrzeit der Festlegung der Überwachungsbedingung für die Warnungsinstanz auf „Behoben“ in UTC. Gilt derzeit nur für Metrikwarnungen.|
| description | Die Beschreibung entsprechend der Definition in der Warnungsregel |
|essentialsVersion| Versionsnummer für den Abschnitt „essentials“|
|alertContextVersion | Versionsnummer für den Abschnitt „alertContext“ |

##### <a name="sample-values"></a>Beispielwerte
```json
{
  "essentials": {
    "alertId": "/subscriptions/<subscription ID>/providers/Microsoft.AlertsManagement/alerts/b9569717-bc32-442f-add5-83a997729330",
    "alertRule": "Contoso IT Metric Alert",
    "severity": "Sev3",
    "signalType": "Metric",
    "monitorCondition": "Fired",
    "monitoringService": "Platform",
    "alertTargetIDs": [
      "/subscriptions/<subscription ID>/resourceGroups/aimon-rg/providers/Microsoft.Insights/components/ai-orion-int-fe"
    ],
    "originAlertId": "74ff8faa0c79db6084969cf7c72b0710e51aec70b4f332c719ab5307227a984f",
    "firedDateTime": "2019-03-26T05:25:50.4994863Z",
    "description": "Test Metric alert",
    "essentialsVersion": "1.0",
    "alertContextVersion": "1.0"
  }
}
```

## <a name="alert-context-fields"></a>Warnungskontextfelder

### <a name="metric-alerts"></a>Metrikwarnungen

#### <a name="monitoringservice--platform"></a>monitoringService = 'Platform'

##### <a name="sample-values"></a>Beispielwerte
```json
{
  "alertContext": {
      "properties": null,
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Percentage CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "operator": "GreaterThan",
            "threshold": "25",
            "timeAggregation": "Average",
            "dimensions": [
              {
                "name": "ResourceId",
                "value": "3efad9dc-3d50-4eac-9c87-8b3fd6f97e4e"
              }
            ],
            "metricValue": 31.1105
          }
        ],
        "windowStartTime": "2019-03-22T13:40:03.064Z",
        "windowEndTime": "2019-03-22T13:45:03.064Z"
      }
    }
}
```

### <a name="log-alerts"></a>Protokollwarnungen

> [!NOTE]
> + Für Protokollwarnungen, für die eine benutzerdefinierte JSON-Nutzlast definiert wurde, wird das Nutzlastschema durch die Aktivierung des allgemeinen Schemas wie unten beschrieben zurückgesetzt.
> + Für Warnungen mit aktiviertem allgemeinem Schema gilt für die Größe ein oberer Grenzwert von 256 KB pro Warnung. **Suchergebnisse werden nicht in die Nutzlast der Protokollwarnungen eingebettet, wenn sie bewirken, dass die Warnungsgröße diesen Schwellenwert überschreitet.** Sie können dies anhand des Flags „IncludedSearchResults“ überprüfen. In Szenarien, in denen die Suchergebnisse nicht enthalten sind, ist es ratsam, die Suchabfrage in Verbindung mit der [Log Analytics-API](https://docs.microsoft.com/rest/api/loganalytics/query/get) zu verwenden. 

#### <a name="monitoringservice--log-analytics"></a>monitoringService = 'Log Analytics'

##### <a name="sample-values"></a>Beispielwerte
```json
{
  "alertContext": {
    "SearchQuery": "search * \n| where Type == \"Heartbeat\" \n| where Category == \"Direct Agent\" \n| where TimeGenerated > ago(30m) ",
    "SearchIntervalStartTimeUtc": "3/22/2019 1:36:31 PM",
    "SearchIntervalEndtimeUtc": "3/22/2019 1:51:31 PM",
    "ResultCount": 15,
    "LinkToSearchResults": "https://portal.azure.com#@72f988bf-86f1-41af-91ab-2d7cd011db47/blade/Microsoft_OperationsManagementSuite_Workspace/AnalyticsBlade/initiator/AnalyticsShareLinkToQuery/isQueryEditorVisible/true/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%<subscription ID>%2FresourceGroups%2Fpipelinealertrg%2Fproviders%2FMicrosoft.OperationalInsights%2Fworkspaces%2FINC-OmsAlertRunner%22%7D%5D%7D/query/search%20%2A%20%0A%7C%20where%20Type%20%3D%3D%20%22Heartbeat%22%20%0A%7C%20where%20Category%20%3D%3D%20%22Direct%20Agent%22%20%0A%7C%20where%20TimeGenerated%20%3E%20%28datetime%282019-03-22T13%3A51%3A31.0000000%29%20-%2030m%29%20%20/isQuerybase64Compressed/false/timespanInIsoFormat/2019-03-22T13%3a36%3a31.0000000Z%2f2019-03-22T13%3a51%3a31.0000000Z",
    "SeverityDescription": "Warning",
    "WorkspaceId": "2a1f50a7-ef97-420c-9d14-938e77c2a929",
    "SearchIntervalDurationMin": "15",
    "AffectedConfigurationItems": [
      "INC-Gen2Alert"
    ],
    "SearchIntervalInMinutes": "15",
    "Threshold": 10000,
    "Operator": "Less Than",
    "SearchResult": {
      "tables": [
        {
          "name": "PrimaryResult",
          "columns": [
            {
              "name": "$table",
              "type": "string"
            },
            {
              "name": "Id",
              "type": "string"
            },
            {
              "name": "TimeGenerated",
              "type": "datetime"
            }
          ],
          "rows": [
            [
              "Fabrikam",
              "33446677a",
              "2018-02-02T15:03:12.18Z"
            ],
            [
              "Contoso",
              "33445566b",
              "2018-02-02T15:16:53.932Z"
            ]
          ]
        }
      ],
      "dataSources": [
        {
          "resourceId": "/subscriptions/a5ea27e2-7482-49ba-90b3-60e7496dd873/resourcegroups/nrt-tip-kc/providers/microsoft.operationalinsights/workspaces/nrt-tip-kc",
          "tables": [
            "Heartbeat"
          ]
        }
      ]
    },
    "IncludedSearchResults": "True",
    "AlertType": "Number of results"
  }
}
```

#### <a name="monitoringservice--application-insights"></a>monitoringService = 'Application Insights'

##### <a name="sample-values"></a>Beispielwerte
```json
{
  "alertContext": {
    "SearchQuery": "search *",
    "SearchIntervalStartTimeUtc": "3/22/2019 1:36:33 PM",
    "SearchIntervalEndtimeUtc": "3/22/2019 1:51:33 PM",
    "ResultCount": 0,
    "LinkToSearchResults": "https://portal.azure.com#@72f988bf-86f1-41af-91ab-2d7cd011db47/blade/Microsoft_OperationsManagementSuite_Workspace/AnalyticsBlade/initiator/AnalyticsShareLinkToQuery/isQueryEditorVisible/true/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%<subscription ID>%2FresourceGroups%2FPipeLineAlertRG%2Fproviders%2Fmicrosoft.insights%2Fcomponents%2FWEU-AIRunner%22%7D%5D%7D/query/search%20%2A/isQuerybase64Compressed/false/timespanInIsoFormat/2019-03-22T13%3a36%3a33.0000000Z%2f2019-03-22T13%3a51%3a33.0000000Z",
    "SearchIntervalDurationMin": "15",
    "SearchIntervalInMinutes": "15",
    "Threshold": 10000,
    "Operator": "Less Than",
    "ApplicationId": "8e20151d-75b2-4d66-b965-153fb69d65a6",
    "SearchResult": {
      "tables": [
        {
          "name": "PrimaryResult",
          "columns": [
            {
              "name": "$table",
              "type": "string"
            },
            {
              "name": "Id",
              "type": "string"
            },
            {
              "name": "TimeGenerated",
              "type": "datetime"
            }
          ],
          "rows": [
            [
              "Fabrikam",
              "33446677a",
              "2018-02-02T15:03:12.18Z"
            ],
            [
              "Contoso",
              "33445566b",
              "2018-02-02T15:16:53.932Z"
            ]
          ]
        }
      ],
      "dataSources": [
        {
          "resourceId": "/subscriptions/a5ea27e2-7482-49ba-90b3-60e7496dd873/resourcegroups/nrt-tip-kc/providers/microsoft.operationalinsights/workspaces/nrt-tip-kc",
          "tables": [
            "Heartbeat"
          ]
        }
      ]
    },
    "IncludedSearchResults": "True",
    "AlertType": "Number of results"
  }
}
```

### <a name="activity-log-alerts"></a>Aktivitätsprotokollwarnungen

#### <a name="monitoringservice--activity-log---administrative"></a>monitoringService = 'Activity Log – Administrative'

##### <a name="sample-values"></a>Beispielwerte
```json
{
  "alertContext": {
      "authorization": {
        "action": "Microsoft.Compute/virtualMachines/restart/action",
        "scope": "/subscriptions/<subscription ID>/resourceGroups/PipeLineAlertRG/providers/Microsoft.Compute/virtualMachines/WCUS-R2-ActLog"
      },
      "channels": "Operation",
      "claims": "{\"aud\":\"https://management.core.windows.net/\",\"iss\":\"https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/\",\"iat\":\"1553260826\",\"nbf\":\"1553260826\",\"exp\":\"1553264726\",\"aio\":\"42JgYNjdt+rr+3j/dx68v018XhuFAwA=\",\"appid\":\"e9a02282-074f-45cf-93b0-50568e0e7e50\",\"appidacr\":\"2\",\"http://schemas.microsoft.com/identity/claims/identityprovider\":\"https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/\",\"http://schemas.microsoft.com/identity/claims/objectidentifier\":\"9778283b-b94c-4ac6-8a41-d5b493d03aa3\",\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\":\"9778283b-b94c-4ac6-8a41-d5b493d03aa3\",\"http://schemas.microsoft.com/identity/claims/tenantid\":\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\"uti\":\"v5wYC9t9ekuA2rkZSVZbAA\",\"ver\":\"1.0\"}",
      "caller": "9778283b-b94c-4ac6-8a41-d5b493d03aa3",
      "correlationId": "8ee9c32a-92a1-4a8f-989c-b0ba09292a91",
      "eventSource": "Administrative",
      "eventTimestamp": "2019-03-22T13:56:31.2917159+00:00",
      "eventDataId": "161fda7e-1cb4-4bc5-9c90-857c55a8f57b",
      "level": "Informational",
      "operationName": "Microsoft.Compute/virtualMachines/restart/action",
      "operationId": "310db69b-690f-436b-b740-6103ab6b0cba",
      "status": "Succeeded",
      "subStatus": "",
      "submissionTimestamp": "2019-03-22T13:56:54.067593+00:00"
    }
}
```

#### <a name="monitoringservice--servicehealth"></a>monitoringService = 'ServiceHealth'

##### <a name="sample-values"></a>Beispielwerte
```json
{
  "alertContext": {
    "authorization": null,
    "channels": "Admin",
    "claims": null,
    "caller": null,
    "correlationId": "f3cf2430-1ee3-4158-8e35-7a1d615acfc7",
    "eventSource": "ServiceHealth",
    "eventTimestamp": "2019-06-24T11:31:19.0312699+00:00",
    "httpRequest": null,
    "eventDataId": "<GUID>",
    "level": "Informational",
    "operationName": "Microsoft.ServiceHealth/maintenance/action",
    "operationId": "<GUID>",
    "properties": {
      "title": "Azure SQL DW Scheduled Maintenance Pending",
      "service": "SQL Data Warehouse",
      "region": "East US",
      "communication": "<MESSAGE>",
      "incidentType": "Maintenance",
      "trackingId": "<GUID>",
      "impactStartTime": "2019-06-26T04:00:00Z",
      "impactMitigationTime": "2019-06-26T12:00:00Z",
      "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"East US\"}],\"ServiceName\":\"SQL Data Warehouse\"}]",
      "impactedServicesTableRows": "<tr>\r\n<td align='center' style='padding: 5px 10px; border-right:1px solid black; border-bottom:1px solid black'>SQL Data Warehouse</td>\r\n<td align='center' style='padding: 5px 10px; border-bottom:1px solid black'>East US<br></td>\r\n</tr>\r\n",
      "defaultLanguageTitle": "Azure SQL DW Scheduled Maintenance Pending",
      "defaultLanguageContent": "<MESSAGE>",
      "stage": "Planned",
      "communicationId": "<GUID>",
      "maintenanceId": "<GUID>",
      "isHIR": "false",
      "version": "0.1.1"
    },
    "status": "Active",
    "subStatus": null,
    "submissionTimestamp": "2019-06-24T11:31:31.7147357+00:00"
  }
}
```
#### <a name="monitoringservice--resource-health"></a>monitoringService = 'Resource Health'

##### <a name="sample-values"></a>Beispielwerte
```json
{
  "alertContext": {
    "channels": "Admin, Operation",
    "correlationId": "<GUID>",
    "eventSource": "ResourceHealth",
    "eventTimestamp": "2019-06-24T15:42:54.074+00:00",
    "eventDataId": "<GUID>",
    "level": "Informational",
    "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
    "operationId": "<GUID>",
    "properties": {
      "title": "This virtual machine is stopping and deallocating as requested by an authorized user or process",
      "details": null,
      "currentHealthStatus": "Unavailable",
      "previousHealthStatus": "Available",
      "type": "Downtime",
      "cause": "UserInitiated"
    },
    "status": "Active",
    "submissionTimestamp": "2019-06-24T15:45:20.4488186+00:00"
  }
}
```


## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zum allgemeinen Warnungsschema](https://aka.ms/commonAlertSchemaDocs)
- [Erfahren Sie, wie Sie eine Logik-App erstellen, die das allgemeine Warnungsschema nutzt, um all Ihre Warnungen zu verarbeiten.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 

