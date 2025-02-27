---
title: Azure Data Factory-Mapping-Datenfluss – Debugmodus
description: Starten einer interaktiven Debugsitzung beim Erstellen von Datenflüssen
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147393"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapping Data Flow – Debugmodus

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory Mapping Data Flow verfügt über einen Debugmodus, der mit der Schaltfläche „Data Flow Debug“ (Datenfluss debuggen) oben auf der Entwurfsoberfläche aktiviert werden kann. Wenn Sie beim Erstellen von Datenflüssen den Debugmodus aktivieren, können Sie interaktiv die Transformation der Datenform beobachten, während Sie Ihre Datenflüsse erstellen und debuggen. Die Debugsitzung kann sowohl in Datenfluss-Entwurfssitzungen sowie während der Ausführung der Pipeline zum Debuggen von Datenflüssen verwendet werden.

![Schaltfläche „Debuggen“](media/data-flow/debugbutton.png "Schaltfläche „Debuggen“")

## <a name="overview"></a>Übersicht
Wenn der Debugmodus eingeschaltet ist, erstellen Sie Ihren Datenfluss mit einem aktiven Spark-Cluster. Die Sitzung wird beendet, sobald Sie das Debuggen in Azure Data Factory deaktivieren. Beachten Sie, dass stündlich Gebühren durch Azure Databricks anfallen, solange die Debugsitzung aktiviert ist.

In den meisten Fällen ist es eine gute Vorgehensweise, Ihre Datenflüsse im Debugmodus zu erstellen, sodass Sie Ihre Geschäftslogik validieren und Ihre Datentransformationen anzeigen können, bevor Sie Ihre Arbeit in Azure Data Factory veröffentlichen. Verwenden Sie die Schaltfläche „Debuggen“ im Bereich „Pipeline“, um Ihren Datenfluss innerhalb einer Pipeline zu testen.

> [!NOTE]
> Während auf der Data Factory-Symbolleiste der grüne Indikator für den Debugmodus angezeigt wird, wird Ihnen die Datenfluss-Debugrate von acht Kernen pro Stunde (Compute allgemein mit einer Gültigkeitsdauer von 60 Minuten) in Rechnung gestellt. 

> [!NOTE]
>Wenn der Datenfluss im Debugmodus ausgeführt wird, werden Ihre Daten nicht in die Senkentransformation geschrieben. Eine Debugsitzung soll als Testumgebung für Ihre Transformationen dienen. Senken sind während des Debuggens nicht erforderlich und werden in Ihrem Datenfluss ignoriert. Wenn Sie das Schreiben der Daten in Ihre Senke testen möchten, führen Sie den Datenfluss aus einer Azure Data Factory-Pipeline aus, und verwenden Sie die Debugausführung aus einer Pipeline.

## <a name="debug-settings"></a>Debugeinstellungen
Debugeinstellungen können bearbeitet werden, indem Sie auf „Debugeinstellungen“ in der Symbolleiste der Datenflusscanvas klicken. Sie können hier die Grenzwerten und/oder die Dateiquelle auswählen, die für jede Ihrer Quelltransformationen verwendet werden soll. Die Zeilengrenzwerte in dieser Einstellung gelten nur für die aktuelle Debugsitzung. Sie können auch dem mit dem Stagingprozess verknüpften Dienst auswählen, der für eine SQL Data Warehouse-Quelle verwendet werden soll. 

![Debugeinstellungen](media/data-flow/debug-settings.png "Debugeinstellungen")

## <a name="cluster-status"></a>Clusterstatus
Oben in der Entwurfsoberfläche befindet sich eine Clusterstatusanzeige. Diese wird grün, wenn der Cluster bereit zum Debuggen ist. Wenn Ihr Cluster bereits betriebsbereit ist, wird die Anzeige nahezu sofort grün. Wenn Ihr Cluster noch nicht ausgeführt wird, wenn Sie in den Debugmodus wechseln, müssen Sie 5-7 Minuten warten, bis der Cluster gestartet ist. Der Indikator wechselt solange, bis er bereit ist.

Wenn Sie mit dem Debuggen fertig sind, deaktivieren Sie den Debugmodus, damit Ihr Azure Databricks-Cluster beendet werden kann. Danach werden Ihnen keine Debugaktivitäten mehr in Rechnung gestellt.

## <a name="data-preview"></a>Datenvorschau
Wenn das Debuggen aktiviert ist, wird im unteren Bereich die Registerkarte „Datenvorschau“ angezeigt. Wenn der Debugmodus deaktiviert ist, werden im Datenfluss auf der Registerkarte „Überprüfen“ nur die für Ihre Transformationen eingehenden und ausgehenden Metadaten angezeigt. Die Datenvorschau fragt nur die Anzahl der Zeilen ab, die Sie in Ihren Debugeinstellungen als Grenzwert festgelegt haben. Sie müssen auf „Daten abrufen“ klicken, um die Datenvorschau zu aktualisieren.

![Datenvorschau](media/data-flow/datapreview.png "Datenvorschau")

## <a name="data-profiles"></a>Datenprofile
Wenn Sie einzelne Spalten in der Datenvorschau auswählen, wird ganz rechts in Ihrem Datentabelle ein Diagramm mit detaillierten Statistiken zu jedem Feld angezeigt. Azure Data Factory wird basierend auf dem Datensampling bestimmen, welche Art von Diagramm für die Anzeige verwendet wird. Felder mit hoher Kardinalität werden standardmäßig mit NULL/NOT NULL-Diagrammen angezeigt, kategorische und numerische Daten mit niedriger Kardinalität werden in Balkendiagrammen mit Datenwerthäufigkeit dargestellt. Außerdem wird die maximale Länge der Zeichenkettenfelder, Min/Max-Werte in numerischen Feldern, Standardabweichung, Perzentile, Anzahl und Mittelwert angezeigt. 

![Spaltenstatistiken](media/data-flow/stats.png "Spaltenstatistiken")

## <a name="next-steps"></a>Nächste Schritte

Sobald Sie mit dem Erstellen und Debuggen Ihres Datenflusses fertig sind, [führen Sie ihn in einer Pipeline aus](control-flow-execute-data-flow-activity.md).

Wenn Sie Ihre Pipeline mit einem Datenfluss testen, verwenden Sie die Option zum [Ausführen des Debuggens](iterative-development-debugging.md) für die Pipeline.
