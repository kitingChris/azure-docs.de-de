---
title: Referenz zu OData-Geofunktionen – Azure Search
description: Für Azure Search-Abfragen sind die OData-Geofunktionen „geo.distance“ und „geo.intersects“ verfügbar.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 0ce63ab1143c784eb3e10f47c20ef2b5034d63a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081879"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>OData-Geofunktionen in Azure Search – `geo.distance` und `geo.intersects`

Azure Search unterstützt Geoabfragen in [OData-Filterausdrücken](query-odata-filter-orderby-syntax.md) über die Funktionen `geo.distance` und `geo.intersects`. Die Funktion `geo.distance` gibt die Entfernung zwischen zwei Punkten in Kilometern zurück, wobei es sich bei einem Punkt um ein Feld oder eine Bereichsvariable und bei dem anderen Punkt um eine Konstante handelt, die als Teil des Filters übergeben wird. Die Funktion `geo.intersects` gibt `true` zurück, wenn ein bestimmter Punkt innerhalb eines bestimmten Polygons liegt. Der Punkt ist hierbei ein Feld oder eine Bereichsvariable, und das Polygon wird als Konstante als Teil des Filters übergeben.

Die Funktion `geo.distance` kann auch im [Parameter **$orderby**](search-query-odata-orderby.md) verwendet werden, um Suchergebnisse nach der Entfernung von einem bestimmten Punkt zu sortieren. Die Syntax für `geo.distance` in **$orderby** entspricht der Syntax in **$filter**. Bei Verwendung von `geo.distance` in **$orderby** muss das Feld, für das die Funktion gilt, vom Typ `Edm.GeographyPoint` und zudem **sortierbar** sein.

## <a name="syntax"></a>Syntax

Die folgende [erweiterte Backus-Naur-Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) (EBNF) definiert die Grammatik der Funktionen `geo.distance` und `geo.intersects` sowie die Geowerte, für die sie ausgeführt werden:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Ein interaktives Syntaxdiagramm ist ebenfalls verfügbar:

> [!div class="nextstepaction"]
> [OData-Syntaxdiagramm für Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Die vollständige EBNF finden Sie unter [Referenz zur OData-Ausdruckssyntax für Azure Search](search-query-odata-syntax-reference.md).

### <a name="geodistance"></a>geo.distance

Die Funktion `geo.distance` akzeptiert zwei Parameter vom Typ `Edm.GeographyPoint` und gibt einen Wert vom Typ `Edm.Double` zurück, der die Entfernung zwischen diesen in Kilometern angibt. Im Unterschied dazu werden bei anderen Diensten, die OData-Geovorgänge unterstützen, Entfernungen in der Regel in Metern zurückgegeben.

Einer der Parameter für `geo.distance` muss eine Konstante des geografischen Punkts sein, und der andere muss ein Feldpfad (oder eine Bereichsvariable im Fall einer Filteriteration über ein Feld vom Typ `Collection(Edm.GeographyPoint)`) sein. Die Reihenfolge dieser Parameter spielt keine Rolle.

Die Konstante des geografischen Punkts weist das Format `geography'POINT(<longitude> <latitude>)'` auf, wobei Längengrad und Breitengrad numerische Konstanten sind.

> [!NOTE]
> Wenn Sie `geo.distance` in einem Filter verwenden, müssen Sie die von der Funktion zurückgegebene Entfernung mithilfe von `lt`, `le`, `gt` oder `ge` mit einer Konstante vergleichen. Die Operatoren `eq` und `ne` werden beim Vergleichen von Entfernungen nicht unterstützt. Hier ist ein Beispiel für die korrekte Verwendung von `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>geo.intersects

Die Funktion `geo.intersects` akzeptiert eine Variable vom Typ `Edm.GeographyPoint` und eine Konstante vom Typ `Edm.GeographyPolygon` und gibt einen Wert `Edm.Boolean` -- `true` zurück, wenn sich der Punkt innerhalb der Grenzen des Polygons befindet. Andernfalls wird `false` zurückgegeben.

Das Polygon ist eine zweidimensionale Fläche und wird als eine Reihe von Punkten gespeichert, die einen Begrenzungsring definieren (siehe unten stehende [Beispiele](#examples)). Das Polygon muss geschlossen sein, d. h. der erste und der letzte Punkt müssen identisch sein. [Punkte in einem Polygon müssen entgegen dem Uhrzeigersinn angegeben werden](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geoabfragen und Polygone, die den 180. Längengrad umfassen

Bei vielen Bibliotheken für Geoabfragen ist die Erstellung einer Abfrage, die den 180. Längengrad (nahe der Datumsgrenze) umfasst, entweder nicht oder nur mithilfe einer Problemumgehung möglich, z. B. durch Aufteilung des Polygons in zwei Teile auf jeweils einer Seite des Längengrads.

In Azure Search funktionieren Geoabfragen mit dem 180. Längengrad wie erwartet, wenn die Form der Abfrage rechteckig ist und die Koordinaten von Längen- und Breitengrad an einem Rasterlayout ausgerichtet sind (z. B. `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). Erwägen Sie andernfalls für nicht rechteckige oder nicht ausgerichtete Formen die Aufteilung des Polygons.  

### <a name="geo-spatial-functions-and-null"></a>Geofunktionen und `null`

Wie alle anderen Nicht-Sammlungsfelder in Azure Search können Felder vom Typ `Edm.GeographyPoint` auch `null`-Werte enthalten. Wenn Azure Search `geo.intersects` für ein Feld auswertet, dessen Wert `null` lautet, ist das Ergebnis immer `false`. Das Verhalten von `geo.distance` hängt in diesem Fall vom Kontext ab:

- In Filtern resultiert die `geo.distance` eines Felds mit dem Wert `null` in `null`. Dies bedeutet, dass das Dokument nicht übereinstimmt, weil `null` im Vergleich zu jedem Wert ungleich NULL zu `false` ausgewertet wird.
- Beim Sortieren von Ergebnissen mit **$orderby** ergibt die `geo.distance` eines Felds mit dem Wert `null` die größtmögliche Entfernung. Dokumente mit einem solchen Feld werden bei Verwendung der Sortierrichtung `asc` (Standardeinstellung) niedriger als alle anderen und bei Verwendung der Sortierrichtung `desc` höher als alle anderen sortiert.

## <a name="examples"></a>Beispiele

### <a name="filter-examples"></a>Beispiele für Filter

Suche nach allen Hotels in einem Umkreis von 10 Kilometern von einem angegebenen Referenzpunkt (wobei „location“ ein Feld vom Typ `Edm.GeographyPoint` ist):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Suche nach allen Hotels in einem bestimmten Anzeigebereich, der als Polygon definiert ist (wobei „location“ ein Feld vom Typ `Edm.GeographyPoint` ist). Beachten Sie, dass das Polygon geschlossen ist (der erste und der letzte Punkt müssen identisch sein), und [die Punkte müssen entgegen dem Uhrzeigersinn aufgelistet sein](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Beispiele für das Sortieren

Sortieren von Hotels in absteigender Reihenfolge nach `rating` und anschließend in aufsteigender Reihenfolge nach der Entfernung von den angegebenen Koordinaten:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Sortieren von Hotels in absteigender Reihenfolge nach `search.score` und `rating` und anschließend in aufsteigender Reihenfolge nach der Entfernung von den angegebenen Koordinaten, sodass von zwei Hotels mit identischen Bewertungen das nächstgelegene zuerst aufgeführt wird:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Nächste Schritte  

- [Filter in Azure Search](search-filters.md)
- [Übersicht über die OData-Ausdruckssprache für Azure Search](query-odata-filter-orderby-syntax.md)
- [Referenz zur OData-Ausdruckssyntax für Azure Search](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Suchen nach Dokumenten: REST-API für den Azure Search-Dienst)
