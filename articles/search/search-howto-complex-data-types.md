---
title: Modellieren komplexer Datentypen – Azure Search
description: Geschachtelte oder hierarchische Datenstrukturen lassen sich in einem Azure Search-Index mit den Datentypen „ComplexType“ und „Collections“ modellieren.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.custom: seodec2018
ms.openlocfilehash: e7e6ddefd13d669c949389bc4fad85fb6cff4d3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621375"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Gewusst wie: Modellieren komplexer Datentypen in Azure Search

Externe Datasets zum Auffüllen eines Azure Search-Index können in vielen Formen vorliegen. Manchmal enthalten sie hierarchische oder geschachtelte Unterstrukturen. Beispiele sind mehrere Adressen für einen einzelnen Kunden, mehrere Farben und Größen für eine einzelne SKU, mehrere Autoren für ein einzelnes Buch usw. In der Modelliersprache werden diese Strukturen bisweilen als *komplexe*, *zusammengesetzte*, *verbundene* oder *aggregierte* Datentypen bezeichnet. Bei Azure Search wird für dieses Konzept der Begriff **komplexer Typ** verwendet. Komplexe Typen werden in Azure Search mithilfe **komplexer Felder** modelliert. Ein komplexes Feld ist ein Feld, das untergeordnete Elemente (untergeordnete Felder) enthält, die einen beliebigen Datentyp aufweisen können – einschließlich anderer komplexer Typen. Dies funktioniert auf ähnliche Weise wie bei strukturierten Datentypen in einer Programmiersprache.

Komplexe Felder stellen je nach Datentyp entweder ein einzelnes Objekt im Dokument oder ein Array von Objekten dar. Felder vom Typ `Edm.ComplexType` stellen einzelne Objekte dar, während Felder vom Typ `Collection(Edm.ComplexType)` für Arrays von Objekten stehen.

In Azure Search werden komplexe Typen und Sammlungen nativ unterstützt. Mit diesen Typen können Sie nahezu jede JSON-Struktur in einem Azure Search-Index modellieren. In früheren Versionen von Azure Search-APIs konnten nur vereinfachte Rowsets importiert werden. In der neuesten Version kann der Index nun genauer den Quelldaten entsprechen. Mit anderen Worten: Wenn die Quelldaten komplexe Typen enthalten, kann auch der Index komplexe Typen enthalten.

Zum Einstieg empfiehlt sich das [Dataset „Hotels“](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), das Sie im Assistenten **Daten importieren** im Azure-Portal laden können. Im Assistenten werden komplexe Typen in der Quelle erkannt, und es wird basierend auf den erkannten Strukturen ein Indexschema vorgeschlagen.

> [!Note]
> Die Unterstützung für komplexe Typen ist in `api-version=2019-05-06` allgemein verfügbar. 
>
> Wenn Ihre Suchlösung auf früheren Problemumgehungen von vereinfachten Datasets in einer Sammlung aufbaut, sollten Sie den Index so ändern, dass er komplexe Typen enthält, wie sie in der neuesten API-Version unterstützt werden. Weitere Informationen zum Aktualisieren von API-Versionen finden Sie unter [Aktualisieren auf die neueste Version der REST-API](search-api-migration.md) oder [Aktualisieren auf die neueste Version des .NET SDK](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Beispiel für eine komplexe Struktur

Das folgende JSON-Dokument besteht aus einfachen und komplexen Feldern. Komplexe Felder, z. B. `Address` und `Rooms`, enthalten Unterfelder. `Address` umfasst einen einzelnen Wertesatz für diese Unterfelder, da es sich um ein einzelnes Objekt im Dokument handelt. Im Gegensatz dazu umfasst `Rooms` mehrere Wertesätze für die zugehörigen Unterfelder, jeweils einen Satz für jedes Objekt in der Sammlung.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Erstellen komplexer Felder

Wie jede Indexdefinition können Sie ein Schema, das komplexe Typen enthält, im Portal, mit der [REST-API](https://docs.microsoft.com/rest/api/searchservice/create-index) oder mit dem [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) erstellen. 

Im folgenden Beispiel ist ein JSON-Indexschema mit einfachen Feldern, Sammlungen und komplexen Typen dargestellt. Beachten Sie, dass in einem komplexen Typ jedes Unterfeld einen Typ enthält und Attribute enthalten kann, so wie das auch bei übergeordneten Feldern der Fall ist. Das Schema entspricht den Beispieldaten oben. `Address` ist ein komplexes Feld, das keine Sammlung ist (ein Hotel weist eine Adresse auf). `Rooms` ist ein komplexes Sammlungsfeld (ein Hotel hat viele Zimmer).

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Aktualisieren komplexer Felder

Alle [Neuindizierungsregeln](search-howto-reindex.md), die allgemein für Felder gelten, gelten auch für komplexe Felder. Das Anpassen einiger Hauptregeln oder das Hinzufügen eines Felds erfordern keine Indexneuerstellung, aber die meisten anderen Änderungen erfordern dies.

### <a name="structural-updates-to-the-definition"></a>Strukturelle Aktualisierungen der Definition

Sie können einem komplexen Feld jederzeit neue Unterfelder hinzufügen, ohne dass eine Indexneuerstellung erforderlich ist. Beispielsweise ist es möglich, `Address` „ZipCode“ oder `Rooms` „Amenities“ hinzuzufügen, so wie ein Feld auf oberster Ebene einem Index hinzugefügt wird. Vorhandene Dokumente haben einen NULL-Wert für neue Felder, bis Sie diese Felder durch Aktualisieren Ihrer Daten explizit füllen.

Beachten Sie, dass in einem komplexen Typ jedes Unterfeld einen Typ enthält und Attribute enthalten kann, so wie das auch bei übergeordneten Feldern der Fall ist.

### <a name="data-updates"></a>Datenaktualisierungen

Die Aktualisierung vorhandener Dokumente in einem Index mit der Aktion `upload` wird für komplexe und einfache Felder auf identische Weise durchgeführt, d. h., alle Felder werden ersetzt. Jedoch wird `merge` (oder `mergeOrUpload` beim Anwenden auf ein vorhandenes Dokument) nicht für alle Felder gleich ausgeführt. Insbesondere unterstützt `merge` nicht das Zusammenführen von Elementen in einer Sammlung. Dies gilt für Sammlungen von primitiven Typen sowie für komplexe Sammlungen. Zum Aktualisieren einer Sammlung müssen Sie den vollständigen Sammlungswert abrufen, Änderungen vornehmen und dann die neue Sammlung in die Anforderung der Index-API einfügen.

## <a name="searching-complex-fields"></a>Durchsuchen komplexer Felder

Freiform-Suchausdrücke funktionieren bei komplexen Typen wie erwartet. Wenn ein durchsuchbares Feld oder Unterfeld an beliebiger Stelle in einem Dokument übereinstimmt, ist das Dokument selbst eine Übereinstimmung.

Abfragen werden bei mehreren Begriffen und Operatoren differenzierter, und bei einigen Begriffen sind Feldnamen angegeben, wie das mit der [Lucene-Syntax](query-lucene-syntax.md) möglich ist. Mit der folgenden Abfrage wird beispielsweise versucht, zwei Begriffe, „Portland“ und „OR“, mit zwei Unterfeldern des Felds „Address“ zu vergleichen:

    search=Address/City:Portland AND Address/State:OR

Abfragen wie diese sind im Unterschied zu Filtern *nicht korreliert* für die Volltextsuche. In Filtern werden Abfragen für untergeordnete Felder einer komplexen Sammlung mithilfe der Bereichsvariablen in [`any` oder `all`](search-query-odata-collection-operators.md) korreliert. Die obige Lucene-Abfrage gibt Dokumente zurück, die „Portland, Maine“ und „Portland, Oregon“ enthalten, sowie andere Städte in Oregon. Dies trifft zu, da jede Klausel für alle Werte des jeweiligen Felds im gesamten Dokument gilt – es gibt also kein Konzept für ein „aktuell untergeordnetes Dokument“. Weitere Informationen hierzu finden Sie unter [Grundlegendes zu OData-Sammlungsfiltern in Azure Search](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Auswählen komplexer Felder

Über den Parameter `$select` wird ausgewählt, welche Felder in den Suchergebnissen zurückgegeben werden. Um diesen Parameter zum Auswählen bestimmter Unterfelder eines komplexen Felds zu verwenden, fügen Sie das übergeordnete Feld und das Unterfeld getrennt durch einen Schrägstrich (`/`) ein.

    $select=HotelName, Address/City, Rooms/BaseRate

Felder müssen im Index als „Abrufbar“ markiert sein, wenn sie in den Suchergebnissen enthalten sein sollen. Nur die als „Abrufbar“ markierten Felder können in einer `$select`-Anweisung verwendet werden.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtern, Faceting und Sortieren komplexer Felder

Die für die Filterung und für feldbezogene Suchen verwendete [OData-Pfadsyntax](query-odata-filter-orderby-syntax.md) kann auch für das Faceting, die Sortierung und die Auswahl von Feldern in einer Suchanforderung verwendet werden. Für komplexe Typen gelten Regeln, mit denen gesteuert wird, welche Unterfelder als sortierbar oder facettierbar markiert werden können. Weitere Informationen zu diesen Regeln finden Sie in der [Referenz zur API zur Indexerstellung](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

### <a name="faceting-sub-fields"></a>Faceting von Unterfeldern

Jedes Unterfeld kann als facettierbar markiert werden, mit Ausnahme von Feldern der Typen `Edm.GeographyPoint` und `Collection(Edm.GeographyPoint)`.

Die in den Facettenergebnissen zurückgegebene Anzahl von Dokumenten wird für das übergeordnete Dokument (ein Hotel) berechnet, nicht für die untergeordneten Dokumente in einer komplexen Sammlung (Zimmer). Beispiel: Ein Hotel hat 20 Zimmer vom Typ „suite“. Für den facettierten Parameter `facet=Rooms/Type` lautet die Anzahl der Facetten für das Hotel 1, nicht 20 für die Zimmer.

### <a name="sorting-complex-fields"></a>Sortieren komplexer Felder

Sortiervorgänge gelten für Dokumente (Hotels) und nicht für Unterdokumente (Zimmer). Bei einer Sammlung von komplexen Typen, z. B. „Rooms“ (Zimmer), ist es wichtig zu wissen, dass für „Rooms“ keinerlei Sortiervorgänge durchgeführt werden können. Sortiervorgänge können für keine Sammlung durchgeführt werden.

Sortiervorgänge sind möglich, wenn Felder in einem Dokument einwertig sind. Dabei kann es sich um einfache Felder oder um Unterfelder in einem komplexen Typ handeln. `Address/City` darf z. B. sortierbar sein, da es nur eine Adresse pro Hotel gibt, `$orderby=Address/City` sortiert die Hotels also nach der Stadt.

### <a name="filtering-on-complex-fields"></a>Filtern komplexer Felder

Sie können auf die untergeordneten Felder eines komplexen Felds in einem Filterausdruck verweisen. Verwenden Sie einfach die gleiche [OData-Pfadsyntax](query-odata-filter-orderby-syntax.md) wie für die Facettierung, Sortierung und Auswahl von Feldern. Der folgende Filter gibt z. B. alle Hotels in Kanada zurück:

    $filter=Address/Country eq 'Canada'

Um nach einem Feld in einer komplexen Sammlung zu filtern, können Sie einen **Lambdaausdruck** mit den [Operatoren `any` und `all`](search-query-odata-collection-operators.md) verwenden. In diesem Fall ist die **Bereichsvariable** des Lambdaausdrucks ein Objekt mit untergeordneten Feldern. Sie können auf diese untergeordneten Felder mit der OData-Standardpfadsyntax verweisen. Der folgende Filter gibt beispielsweise alle Hotels zurück, die mindestens ein Luxuszimmer und ausschließlich Nichtraucherzimmer haben:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Wie schon bei einfachen Feldern der obersten Ebene können auch einfache untergeordnete Felder von komplexen Feldern nur in Filtern verwendet werden, wenn Ihr **filterable**-Attribut in der Indexdefinition auf `true` festgelegt wurde. Weitere Informationen finden Sie in der [Referenz zur API zur Indexerstellung](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

## <a name="next-steps"></a>Nächste Schritte

Testen Sie das [Dataset „Hotels“](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) im Assistenten **Daten importieren**. Für den Zugriff auf die Daten benötigen Sie die in der Infodatei angegebenen Cosmos DB-Verbindungsinformationen.

Mit diesen Informationen erstellen Sie im ersten Schritt im Assistenten eine neue Azure Cosmos DB-Datenquelle. Später im Assistenten, wird auf der Seite für den Zielindex ein Index mit komplexen Typen angezeigt. Erstellen und laden Sie diesen Index, und führen Sie dann Abfragen aus, um sich mit der neuen Struktur vertraut zu machen.

> [!div class="nextstepaction"]
> [Schnellstart: Portal-Assistent für Import, Indizierung und Abfragen](search-get-started-portal.md)