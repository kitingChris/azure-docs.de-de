---
title: 'Azure Batch: Ereignis zum Abschluss der Größenänderung von Pools | Microsoft-Dokumentation'
description: Referenz zum Batch-Ereignis zum Abschluss der Größenänderung von Pools.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: c2544bd2be683b731c3dac0bea651d4b64dff75e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323202"
---
# <a name="pool-resize-complete-event"></a>Ereignis zum Abschluss der Größenänderung von Pools

 Dieses Ereignis wird ausgegeben, wenn die Größenänderung eines Pools abgeschlossen wurde oder fehlgeschlagen ist.

 Das folgende Beispiel zeigt den Text eines Ereignisses zum Abschluss der Größenänderung von Pools für einen Pool, dessen Größe erfolgreich erhöht wurde.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|Element|Typ|Notizen|
|-------------|----------|-----------|
|id|Zeichenfolge|Die ID des Pools.|
|nodeDeallocationOption|Zeichenfolge|Gibt ab, ob Knoten ggf. aus dem Pool entfernt werden müssen, wenn sich die Poolgröße verringert.<br /><br /> Mögliche Werte:<br /><br /> **requeue**: Beendet ausgeführte Tasks, die erneut in die Warteschlange gestellt werden. Die Tasks werden erneut ausgeführt, sobald der Auftrag aktiviert ist. Entfernen Sie Knoten, sobald Tasks abgeschlossen wurden.<br /><br /> **terminate**: Beendet derzeit ausgeführte Tasks. Die Tasks werden nicht erneut ausgeführt. Entfernen Sie Knoten, sobald Tasks abgeschlossen wurden.<br /><br /> **taskcompletion**: Lässt das Abschließen aktuell ausgeführter Tasks zu. Planen Sie beim Warten keine neuen Tasks. Entfernen Sie Knoten, sobald alle Aufgaben abgeschlossen sind.<br /><br /> **Retaineddata**: Lässt das Abschließen aktuell ausgeführter Tasks zu und wartet dann, bis die Aufbewahrungszeiträume aller Taskdaten abgelaufen sind. Planen Sie beim Warten keine neuen Tasks. Entfernen Sie Knoten, sobald die Aufbewahrungszeiträume aller Tasks abgelaufen sind.<br /><br /> Der Standardwert ist „requeue“.<br /><br /> Wenn die Poolgröße zunimmt, wird der Wert auf **invalid** festgelegt.|
|currentDedicated|Int32|Die Anzahl der Computeknoten, die dem Pool derzeit zugewiesen sind.|
|targetDedicated|Int32|Die Anzahl der Computeknoten, die für den Pool angefordert werden.|
|enableAutoScale|Bool|Gibt an, ob die Poolgröße mit der Zeit automatisch angepasst wird.|
|isAutoPool|Bool|Gibt an, ob der Pool über den AutoPool-Mechanismus eines Auftrags erstellt wurde.|
|startTime|DateTime|Die Startzeit der Größenänderung des Pools.|
|endTime|DateTime|Die Uhrzeit des Abschlusses der Größenänderung des Pools.|
|resultCode|Zeichenfolge|Das Ergebnis der Größenänderung.|
|resultMessage|Zeichenfolge|Der Größenänderungsfehler enthält die Details des Ergebnisses.<br /><br /> Wenn die Größenänderung erfolgreich abgeschlossen wurde, wird angegeben, dass der Vorgang erfolgreich war.|