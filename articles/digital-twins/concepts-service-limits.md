---
title: Diensteinschränkungen der öffentlichen Vorschauversion von Azure Digital Twins | Microsoft-Dokumentation
description: Grundlegendes zu den Diensteinschränkungen der Public Preview von Azure Digital Twins.
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: dwalther
ms.openlocfilehash: f3239fa5d21078795e7b063cb0364e905cba25c1
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846624"
---
# <a name="public-preview-service-limits"></a>Diensteinschränkungen der öffentlichen Vorschauversion

Für die Public Preview von Azure Digital Twins gelten die nachfolgend beschriebenen temporären Abonnement-, Instanz- und Anzahlgrenzwerte.

Diese Einschränkungen gibt es, um das Kennenlernen des neuen Diensts und seiner zahlreichen Funktionen vereinfachen.

> [!NOTE]
> Die Grenzwerte werden gemäß der allgemeinen Verfügbarkeit erhöht oder entfernt.

## <a name="per-subscription-limits"></a>Grenzwerte pro Abonnement

In der Public Preview kann jedes Azure-Abonnement jeweils genau eine Azure Digital Twins-Instanz erstellen oder ausführen.

> [!TIP]
> Wenn Sie Ihre Instanz löschen, können Sie eine neue erstellen.

## <a name="per-instance-limits"></a>Grenzwerte pro Instanz

Der Reihe nach kann jede Azure Digital Twins-Instanz Folgendes haben:

- Genau eine eingebettete **IoTHub**-Ressource, die während der Dienstbereitstellung automatisch erstellt wird.
- Genau einen **EventHub**-Endpunkt für den Ereignistyp **DeviceMessage**
- Bis zu drei **EventHub**-, **ServiceBus**- oder **EventGrid**-Endpunkte der Ereignistypen **SensorChange**, **SpaceChange**, **TopologyOperation** oder **UdfCustom**

> [!NOTE]
> Einige Parameter, die normalerweise beim Erstellen der obigen Azure IoT-Entitäten definiert werden, sind während der Public Preview-Phase nicht erforderlich.
> - Die aktuellen API-Spezifikationen finden Sie in der [Swagger-Referenzdokumentation](./how-to-use-swagger.md).

## <a name="azure-digital-twins-management-api-limits"></a>Einschränkungen der Azure Digital Twins-Verwaltungs-API

Die Grenzwerte für die Anforderungsanzahl Ihrer Azure Digital Twins-Verwaltungs-API lauten wie folgt:

- 100 Anforderungen pro Sekunde an die Azure Digital Twins-Verwaltungs-API
- Bis zu 1.000 Objekte, die von einer einzelnen Azure Digital Twins-Verwaltungs-API-Abfrage zurückgegeben werden

> [!IMPORTANT]
> Beim Überschreiten des Grenzwerts von 1.000 Objekten tritt ein Fehler auf, und Sie müssen die Abfrage vereinfachen.

## <a name="user-defined-functions-rate-limits"></a>Ratenbegrenzungen für benutzerdefinierte Funktionen

Es gelten die folgenden Grenzwerte für die Gesamtanzahl der Aufrufe, die von benutzerdefinierten Funktionen an die Azure Digital Twins-Instanz gesendet werden:

- 400 Clientbibliotheksaufrufe pro Sekunde
- 100 **SendNotification**-Aufrufe pro Sekunde

> [!NOTE]
> Die folgenden Aktionen führen ggf. dazu, dass temporär zusätzliche Anzahlgrenzwerte angewendet werden:
> - Bearbeitungen an den Metadaten von Topologieobjekten
> - Aktualisierungen der Definition der benutzerdefinierten Funktion
> - Erstmaliges Senden von Telemetriedaten von Geräten

## <a name="device-telemetry-limits"></a>Grenzwerte für Gerätetelemetrie

Die folgenden Grenzwerte deckeln die Gesamtanzahl aller Nachrichten, die Ihre Geräte an Ihre Azure Digital Twins Instanz senden können:

- 100 Nachrichten pro Sekunde

## <a name="next-steps"></a>Nächste Schritte

- Um ein Azure Digital Twins-Beispiel auszuprobieren, wechseln Sie zu [Schnellstart: Suchen nach verfügbaren Räumen mithilfe von Azure Digital Twins](./quickstart-view-occupancy-dotnet.md).