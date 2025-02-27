---
title: Tipps zur Problembehandlung bei der kognitiven Suche – Azure Search
description: Tipps und Problembehandlung für die Einrichtung von kognitiven Suchpipelines in Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c0de4d2b9ad0d009b9cd363d19a2de3f29d810d4
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303451"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Tipps zur Problembehandlung bei der kognitiven Suche

Dieser Artikel enthält eine Liste mit Tipps und Tricks als Unterstützung bei Ihren ersten Schritten mit der kognitiven Suche in Azure Search. 

Sofern noch nicht geschehen, machen Sie sich zunächst unter [Schnellstart: Erstellen einer Pipeline für die kognitive Suche mithilfe von Qualifikationen und Beispieldaten](cognitive-search-quickstart-blob.md) damit vertraut, wie Sie mithilfe der kognitive Suche Datenanreicherungen in einer Blobdatenquelle anwenden.

## <a name="tip-1-start-with-a-small-dataset"></a>Tipp 1: Beginnen Sie mit einem kleinen Dataset.
Am besten lassen sich Probleme schnell finden, wenn Sie diese möglichst schnell beheben können. Die einfachste Möglichkeit, die Indizierungszeit zu verkürzen, besteht darin, die Anzahl der zu indizierenden Dokumente zu verringern. 

Beginnen Sie, indem Sie eine Datenquelle mit nur wenigen Dokumenten bzw. Datensätzen erstellen. Die zusammengestellten Dokumente sollten dabei möglichst die Vielfalt der zu indexierenden Dokumente widerspiegeln. 

Führen Sie Ihre Dokumentenzusammenstellung durch die End-to-End-Pipeline und prüfen Sie, ob die Ergebnisse Ihre Anforderungen erfüllen. Sobald Sie mit den Ergebnissen zufrieden sind, können Sie weitere Dateien zu Ihrer Datenquelle hinzufügen.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tipp 2: Vergewissern Sie sich, dass Ihre Anmeldeinformationen für die Datenquelle korrekt sind.
Die Datenquellenverbindung wird erst überprüft, wenn Sie einen Indexer definieren, der sie verwendet. Wenn Fehler angezeigt werden, die darauf hinweisen, dass der Indexer nicht auf die Daten zugreifen kann, stellen Sie Folgendes sicher:
- Die Verbindungszeichenfolge ist korrekt. Insbesondere beim Erstellen von SAS-Token müssen Sie das von Azure Search erwartete Format verwenden. Weitere Informationen zu den verschiedenen unterstützten Formaten finden Sie im Abschnitt [Angeben von Anmeldeinformationen](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials).
- Der Containername im Indexer ist korrekt.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tipp 3: Achten Sie darauf, was funktioniert, auch wenn Fehler auftreten.
Manchmal wird ein Indexer durch einen kleinen Fehler an der Ausführung gehindert. Das ist in Ordnung, wenn Sie beabsichtigen, ein Problem nach dem anderen zu beheben. Möglicherweise möchten Sie aber auch einen bestimmten Fehlertyp ignorieren, damit der Indexer fortfahren kann, sodass Sie sehen können, welche Flows tatsächlich funktionieren.

In diesem Fall sollten Sie den Indexer anweisen, Fehler zu ignorieren. Dazu setzen Sie *maxFailedItems* und *maxFailedItemsPerBatch* als Teil der Indexerdefinition auf -1.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Tipp 4: Sehen Sie sich angereicherte Dokumente genauer an. 
Angereicherte Dokumente sind temporäre Strukturen, die während der Anreicherung erstellt und nach Abschluss der Verarbeitung gelöscht werden.

Zum Erstellen einer Momentaufnahme des angereicherten Dokuments, das während der Indizierung erstellt wurde, fügen Sie ein Feld namens ```enriched``` zu Ihrem Index hinzu. Der Indexer gibt für alle Anreicherungen dieses Dokuments automatisch eine Zeichenfolgendarstellung im Feld aus.

Das Feld ```enriched``` enthält eine Zeichenfolge, die eine logische Darstellung des im Speicher angereicherten Dokuments im JSON-Format ist.  Der Feldwert ist jedoch ein gültiges JSON-Dokument. Anführungszeichen werden mit Escapezeichen versehen, sodass Sie `\"` durch `"` ersetzen müssen, um das Dokument als formatierten JSON-Code anzuzeigen. 

Das angereicherte Feld ist für Debugzwecke vorgesehen und soll Sie beim Verständnis der logischen Form der Inhalte unterstützen, für die Ausdrücke ausgewertet werden. Sie sollten bei der Indizierung nicht auf dieses Feld angewiesen sein.

Fügen Sie ein Feld ```enriched``` als Teil Ihrer Indexdefinition zu Debuzwecken hinzu:

#### <a name="request-body-syntax"></a>Syntax des Anforderungstexts
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-5-expected-content-fails-to-appear"></a>Tipp 5: Prüfen Sie, ob die erwarteten Inhalte angezeigt werden.

Fehlender Inhalt könnte das Ergebnis von Dokumenten sein, die während der Indizierung verworfen werden. Für die Preisstufen Free und Basic gibt es niedrige Grenzwerte für die Dokumentgröße. Jede Datei, die den Grenzwert überschreitet, wird während der Indizierung verworfen. Sie können im Azure-Portal nach verworfenen Dokumenten suchen. Doppelklicken Sie im Dashboard des Suchdiensts auf die Kachel des Indexers. Überprüfen Sie das Verhältnis der erfolgreichen indizierten Dokumente. Wenn es nicht 100% ist, können Sie auf darauf klicken, um mehr Details zu erhalten. 

Wenn das Problem mit der Dateigröße zusammenhängt, wird möglicherweise ein Fehler wie dieser angezeigt: „Das Blob "\<Dateiname>" ist \<Dateigröße> Bytes groß und überschreitet daher das Größenlimit für die Dokumentenextrahierung für Ihre aktuelle Dienstebene.“ Weitere Informationen zu Indexergrenzwerten finden Sie unter [Grenzwerte für den Azure Search-Dienst](search-limits-quotas-capacity.md).

Ein zweiter Grund dafür, dass Inhalte nicht angezeigt werden, können Zuordnungsfehler bei der Eingabe/Ausgabe sein, die zusammenhängen. Ein Beispiel hierfür wäre, wenn ein Ausgabezielname „Personen“ lautet, aber der Indexfeldname kleingeschrieben ist („personen“). Das System könnte 201 Erfolgsmeldungen für die gesamte Pipeline zurückgeben, sodass der Eindruck entsteht, dass die Indizierung erfolgreich war, obwohl tatsächlich ein Feld leer ist. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tipp 6: Verlängern Sie die Verarbeitung über die maximale Laufzeit hinaus (24-Stunden-Fenster).

Die Bildanalyse ist selbst für einfache Fälle rechenintensiv, sodass die Verarbeitungsdauer bei besonders großen oder komplexen Bildern die maximal zulässige Zeit überschreiten kann. 

Die maximale Laufzeit variiert je nach Tarif: mehrere Minuten beim Free-Tarif, 24-Stunden-Indizierung bei abrechenbaren Tarifen. Wenn die Verarbeitung nicht innerhalb eines 24-Stunden-Zeitraums für die bedarfsgesteuerte Verarbeitung abgeschlossen werden kann, wechseln Sie zu einem Zeitplan, damit der Indexer die Verarbeitung dort fortsetzt, wo sie unterbrochen wurde. 

Bei geplanten Indexern wird die Indizierung beim letzten erfolgreich verarbeiteten Dokument planmäßig fortgesetzt. Durch die Verwendung eines wiederkehrenden Zeitplans kann sich der Indexer über eine Reihe von Stunden oder Tagen durch den Rückstand der Bilder arbeiten, bis schließlich alle Bilder verarbeitet sind. Weitere Informationen zur Zeitplansyntax finden Sie unter [Schritt 3: Erstellen eines Indexers](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) oder [How to schedule indexers for Azure Search (Festlegen eines Zeitplans für Indexer in Azure Search)](search-howto-schedule-indexers.md).

> [!NOTE]
> Wenn ein Indexer auf einen bestimmten Zeitplan festgelegt ist, im gleichen Dokument bei erneuter Ausführung aber immer wieder ein Fehler auftritt, wird der Indexer in selteneren Intervallen ausgeführt (bis hin zum Maximum von mindestens einmal in 24 Stunden), bis die Ausführung fehlerfrei verläuft.  Wenn Sie der Meinung sind, dass Sie das Problem behoben haben, das dafür gesorgt hat, dass der Indexer an einem bestimmten Punkt hängengeblieben ist, können Sie eine bedarfsgesteuerte Ausführung des Indexers veranlassen. Wenn diese Ausführung erfolgreich verläuft, wird der Indexer wieder im festgelegten Zeitplanintervall ausgeführt.

Bei der portalbasierten Indizierung (wie im Schnellstart beschrieben) wird durch die Indexeroption „Einmal ausführen“ die Verarbeitung auf eine Stunde (`"maxRunTime": "PT1H"`) eingeschränkt. Sie können das Verarbeitungsfenster auch erweitern.

## <a name="tip-7-increase-indexing-throughput"></a>Tipp 7: Erhöhen Sie den Durchsatz der Indizierung.

Für eine [parallele Indizierung](search-howto-large-index.md) platzieren Sie Ihre Daten in mehrere Container oder mehrere virtuelle Ordner innerhalb desselben Containers. Erstellen Sie dann mehrere Datenquellen-Indexer-Paare. Alle Indexer können das gleiche Skillset verwenden und in den gleichen Zielsuchindex schreiben, sodass Ihre Such-App über diese Partitionierung nicht informiert sein muss.
Weitere Informationen finden Sie unter [Indizieren großer Datasets](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Weitere Informationen
+ [Schnellstart: Erstellen einer Pipeline für die kognitive Suche mithilfe von Qualifikationen und Beispieldaten](cognitive-search-quickstart-blob.md)
+ [Tutorial: Informationen zum Aufrufen von APIs der kognitiven Suche (Vorschau)](cognitive-search-tutorial-blob.md)
+ [Angeben von Anmeldeinformationen](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indizieren großer Datasets](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Zuordnen angereicherter Felder zu einem Index](cognitive-search-output-field-mapping.md)
