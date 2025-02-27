---
title: WHERE-Klausel in Azure Cosmos DB
description: Erfahren Sie mehr über die WHERE-Klausel von SQL für Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 6a942e48ffea7785fe971cc2f8fa66e8569ed672
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343117"
---
# <a name="where-clause"></a>WHERE-Klausel

Die optionale WHERE-Klausel (`WHERE <filter_condition>`) gibt Bedingungen an, die die JSON-Elemente in der Quelle erfüllen müssen, damit sie von der Abfrage in die Ergebnisse eingeschlossen werden. Ein JSON-Element muss die angegebenen Bedingungen erfüllen (`true`), um für das Ergebnis berücksichtigt zu werden. Die Indexebene verwendet die WHERE-Klausel, um die kleinste Teilmenge von Quellelementen zu bestimmen, die Teil des Ergebnisses sein können.
  
## <a name="syntax"></a>Syntax
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumente

- `<filter_condition>`  
  
   Gibt die Bedingung an, die erfüllt sein muss, damit die Dokumente zurückgegeben werden.  
  
- `<scalar_expression>`  
  
   Ausdruck, der den zu berechnenden Wert darstellt. Weitere Informationen finden Sie unter [Scalar expressions in Azure Cosmos DB SQL queries](sql-query-scalar-expressions.md) (Skalarausdrücke in SQL-Abfragen in Azure Cosmos DB).  
  

## <a name="remarks"></a>Anmerkungen
  
  Damit das Dokument zurückgegeben wird, muss ein als Filterbedingung angegebener Ausdruck mit „true“ ausgewertet werden. Nur der boolesche Wert TRUE erfüllt die Bedingung, jeder andere Wert – „undefined“, „NULL“, „FALSE“, „Number“, „Array“ oder „Object“ – erfüllt die Bedingung nicht. 

## <a name="examples"></a>Beispiele

Die folgende Abfrage fordert Elemente an, die eine `id`-Eigenschaft mit dem Wert `AndersenFamily` enthalten. Alle Elemente, die keine `id`-Eigenschaft besitzen oder deren Wert nicht `AndersenFamily` ist, werden ausgeschlossen.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Ergebnisse sind wie folgt:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Skalare Ausdrücke in der WHERE-Klausel

Das vorstehende Beispiel zeigt eine einfache Gleichheitsabfrage. Die SQL-API unterstützt auch verschiedene [skalare Ausdrücke](sql-query-scalar-expressions.md). Die am häufigsten verwendeten Ausdrücke sind binäre und unäre Ausdrücke. Eigenschaftsverweise aus dem JSON-Quellobjekt sind ebenfalls gültige Ausdrücke.

Sie können die folgenden unterstützten binären Operatoren verwenden:  

|**Operatortyp**  | **Werte** |
|---------|---------|
|Arithmetik | +,-,*,/,% |
|Bitweise    | \|, &, ^, <<, >>, >>> (Nullauffüllung, Verschiebung nach rechts) |
|Logisch    | AND, OR, NOT      |
|Vergleich | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|string     |  \|\| (Verkettung) |

Die folgenden Abfragen verwenden binäre Operatoren:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Sie können auch die unären Operatoren +, -, ~ und NOT in Abfragen verwenden, wie es in den folgenden Beispielen gezeigt wird:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Sie können auch Eigenschaftsverweise in Abfragen verwenden. Beispielsweise gibt `SELECT * FROM Families f WHERE f.isRegistered` das JSON-Element zurück, das die Eigenschaft `isRegistered` mit dem Wert `true` enthält. Jeder andere Wert (z.B. `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>` oder `<array>`) führt dazu, dass das Element aus dem Ergebnis ausgeschlossen wird. 

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte](sql-query-getting-started.md)
- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmosdb-dotnet)
- [FROM-Klausel](sql-query-from.md)