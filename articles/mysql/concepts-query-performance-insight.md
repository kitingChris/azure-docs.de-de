---
title: Query Performance Insight in Azure Database for MySQL
description: In diesem Artikel wird das Feature „Query Performance Insight“ in Azure Database for MySQL beschrieben.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 8f142933ebf955cbe3aa119f42779109fb6ef7db
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67589070"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Query Performance Insight in Azure Database for MySQL

**Gilt für:**  Azure Database for MySQL 5.7

> [!NOTE]
> Query Performance Insight befindet sich in der Vorschauphase.

Mithilfe von Query Performance Insight können Sie schnell die Abfragen mit den längsten Ausführungszeiten identifizieren, wie sie sich im Laufe der Zeit ändern und welche Wartezeiten sie beeinflussen.

## <a name="common-scenarios"></a>Häufige Szenarios

### <a name="long-running-queries"></a>Zeitintensive Abfragen

- Identifizieren der am längsten ausgeführten Abfragen in der vergangenen X Stunden
- Identifizieren der wichtigsten N Abfragen, die auf Ressourcen warten
 
### <a name="wait-statistics"></a>Wartestatistiken

- Verstehen der Natur des Wartens für eine Abfrage
- Verstehen von Trends für Ressourcenwartevorgänge und wo Ressourcenkonflikte vorhanden sind

## <a name="permissions"></a>Berechtigungen

Zum Anzeigen des Abfragetexts in Query Performance Insight sind die Berechtigungen **Besitzer** oder **Mitwirkender** erforderlich. Ein **Leser** kann Diagramme und Tabellen anzeigen, aber keinen Abfragetext.

## <a name="prerequisites"></a>Voraussetzungen

Damit Query Performance Insight funktioniert, müssen Daten im [Abfragespeicher](concepts-query-store.md) vorhanden sein.

## <a name="viewing-performance-insights"></a>Anzeigen von Einblicken in die Leistung

Die [Query Performance Insight](concepts-query-performance-insight.md)-Ansicht im Azure-Portal zeigt wichtige Informationen aus dem Abfragespeicher an.

Wählen Sie auf der Portalseite Ihres Azure Database for MySQL-Servers im Abschnitt **Intelligente Leistung** in der Menüleiste die Option **Query Performance Insight**.

### <a name="long-running-queries"></a>Zeitintensive Abfragen

Die Registerkarte **Abfragen mit langer Ausführungszeit** zeigt die ersten fünf Abfragen nach durchschnittlicher Dauer pro Ausführung an, zusammengefasst in Intervallen von 15 Minuten. Sie können mehr Abfragen anzeigen, indem Sie in der Dropdownliste **Anzahl der Abfragen** eine Auswahl treffen. Dabei ändern sich unter Umständen die Diagrammfarben für eine bestimmte Abfrage-ID.

Durch Klicken und Ziehen im Diagramm können Sie die Zeit auf ein bestimmtes Zeitfenster eingrenzen. Alternativ zeigen Sie mit den Symbolen zum Vergrößern oder Verkleinern einen kürzeren bzw. längeren Zeitraum an.

![Abfragen mit langer Ausführungszeit in Query Performance Insight](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Wartestatistiken

> [!NOTE]
> Wartestatistiken sind für die Problembehandlung von Leistungsproblemen bei Abfragen vorgesehen. Es wird empfohlen, diese nur zu Problembehandlungszwecken zu aktivieren.

Wartestatistiken bieten eine Ansicht der Warteereignisse, die während der Ausführung einer bestimmten Abfrage auftreten. Weitere Informationen zu den Warteereignistypen finden Sie in der [MySQL-Engine-Dokumentation](https://go.microsoft.com/fwlink/?linkid=2098206).

Wählen Sie die Registerkarte **Wartestatistik** aus, um die entsprechenden Visualisierungen zu Wartevorgängen auf dem Server anzuzeigen.

In der Ansicht der Wartestatistik angezeigte Abfragen werden nach den Abfragen gruppiert, die die längsten Wartezeiten während des angegebenen Zeitintervalls aufweisen.

![Query Performance Insight-Wartestatistiken](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich weiter über die [Überwachung und Optimierung](concepts-monitoring.md) in Azure Database for MySQL.