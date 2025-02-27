---
title: Einführung in die Gremlin-API von Azure Cosmos DB
description: Erfahren Sie, wie Sie anhand der Graph-Abfragesprache Gremlin von Apache TinkerPop umfangreiche Diagramme mit niedrigen Latenzen durch Azure Cosmos DB speichern, abfragen und traversieren können.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/25/2019
ms.author: lbosq
ms.openlocfilehash: 126c825106b7844a5fc8a5a3cdbcc7aa6c273b5b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502792"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Einführung in Azure Cosmos DB: Gremlin-API

[Azure Cosmos DB](introduction.md) ist der global verteilte Datenbankdienst von Microsoft mit mehreren Modellen für unternehmenskritische Anwendungen. Es handelt sich um eine Datenbank für mehrere Modelle, die Dokumente, Schlüsselwerte, Graphen und spaltenorientierte Datenmodelle unterstützt. Die Azure Cosmos DB Gremlin-API dient zum Speichern und Verarbeiten der Graphdaten in einer vollständig verwalteten Datenbankumgebung jeder Größenordnung.  

![Architektur von Azure Cosmos DB-Diagrammen](./media/graph-introduction/cosmosdb-graph-architecture.png)

Dieser Artikel enthält eine Übersicht über die Gremlin-API von Azure Cosmos DB und erläutert, wie Sie diese zum Speichern von umfangreichen Diagrammen mit Milliarden von Scheitelpunkten und Kanten verwenden können. Sie können die Graphen mit einer Latenz im Millisekundenbereich abfragen und die Graphstruktur mühelos entwickeln. Die Gremlin-API von Azure Cosmos DB basiert auf dem Graphdatenbankstandard [Apache TinkerPop](https://tinkerpop.apache.org) und verwendet die Gremlin-Abfragesprache. 

Die Gremlin-API von Azure Cosmos DB kombiniert die Leistungsfähigkeit von Algorithmen für Graphdatenbanken mit einer hochskalierbaren und verwalteten Infrastruktur und bietet eine einzigartige, flexible Lösung für die meisten gängigen Datenprobleme, die mit mangelnder Flexibilität und relationalen Ansätzen einher gehen. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Features der Azure Cosmos DB-Graphdatenbank
 
Azure Cosmos DB ist eine vollständig verwaltete Diagrammdatenbank, die eine globale Verteilung, flexible Skalierung von Speicher und Durchsatz, automatische Indizierung und Abfrage sowie einstellbare Konsistenzebenen bietet und den TinkerPop-Standard unterstützt. 

Die Azure Cosmos DB Gremlin-API bietet die folgenden differenzierten Funktionen:

* **Flexibel skalierbarer Durchsatz und Speicher**

  Diagramme müssen in der Praxis über die Kapazität eines einzelnen Servers hinweg skaliert werden. Azure Cosmos DB unterstützt horizontal skalierbare Graphdatenbanken, die eine praktisch unbegrenzte Größe in Bezug auf den bereitgestellten Durchsatz haben können. Bei zunehmender Skalierung der Graphdatenbank werden die Daten automatisch mithilfe der [Graphpartitionierung](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning) verteilt.

* **Replikation in mehreren Regionen**

  Azure Cosmos DB kann die Graphdaten automatisch in jeder Azure-Region replizieren. Die Replikation vereinfacht die Entwicklung von Anwendungen, die globalen Zugriff auf Daten erfordern. Zusätzlich zur Minimierung der Leselatenz bietet Azure Cosmos DB einen regionalen Failovermechanismus, mit dem die Kontinuität Ihrer Anwendung im seltenen Fall einer Dienstunterbrechung in einer Region sichergestellt werden kann. 

* **Schnelle Abfragen und Traversierungen mit dem am weitesten verbreiteten Standard für Graphabfragen**

  Speichern Sie heterogene Vertices und Edges, und führen Sie Abfragen dieser Dokumente über eine vertraute Gremlin-Syntax durch. Gremlin ist eine imperative, funktionale Abfragesprache, die eine umfangreiche Schnittstelle zur Implementierung gängiger Graphalgorithmen bietet. 
  
  Azure Cosmos DB ermöglicht umfassende Echtzeitabfragen und -traversierungen, ohne dass Schemahinweise, sekundäre Indizes oder Ansichten festgelegt werden müssen. Mehr erfahren Sie unter [Abfragegraphen mithilfe von Gremlin](gremlin-support.md).

* **Vollständig verwaltete Graphdatenbank**

  Sie müssen sich nicht mehr mit der Verwaltung von Datenbanken und Rechenressourcen befassen. Die meisten vorhandenen Graphdatenbankplattformen sind an die Grenzen ihrer Infrastruktur gebunden und erfordern häufig einen hohen Wartungsaufwand für den Betrieb. 
  
  Mit diesem vollständig verwalteten Microsoft Azure-Dienst ist es nicht erforderlich, virtuelle Computer zu verwalten, Runtimesoftware zu aktualisieren, Sharding oder die Replikation zu verwalten oder komplexe Upgrades auf Datenebene durchzuführen. Alle Diagramme werden automatisch gesichert und vor regionalen Ausfällen geschützt. Dadurch können sich Entwickler auf die Bereitstellung von Anwendungen konzentrieren, anstatt Datenbanken zu betreiben und zu verwalten. 

* **Automatische Indizierung**

  Alle Eigenschaften in Knoten und Edges im Graphen werden von Azure Cosmos DB automatisch indiziert, ohne dass ein Schema oder die Erstellung sekundärer Indizes erwartet oder gefordert wird. Weitere Informationen zur Indizierung in Azure Cosmos DB finden Sie [hier](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Kompatibilität mit Apache TinkerPop**

  Azure Cosmos DB unterstützt den [Open-Source-Standard Apache TinkerPop](http://tinkerpop.apache.org/). Der TinkerPop-Standard verfügt über ein umfangreiches Ökosystem von Anwendungen und Bibliotheken, die sich problemlos in die Gremlin-API von Azure Cosmos DB integrieren lassen. 

* **Einstellbare Konsistenzebenen**

  Die Konsistenz kann über fünf klar definierte Ebenen abgestimmt werden, um für ein ausgewogenes Verhältnis zwischen Konsistenz und Leistung zu sorgen. Für Abfragen und Lesevorgänge bietet Azure Cosmos DB fünf verschiedene Konsistenzebenen – „stark“, „begrenzte Veraltung“, „Sitzung“, „Präfixkonsistenz“ und „letztlich“. Mit diesen granularen, wohldefinierten Konsistenzebenen können fundierte Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz geschlossen werden. Weitere Informationen finden Sie unter [Einstellbare Datenkonsistenzebenen in Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-can-use-gremlin-api"></a>Szenarios, in denen die Gremlin-API verwendet werden kann
Im Folgenden werden einige Szenarien vorgestellt, in denen die Diagrammunterstützung von Azure Cosmos DB verwendet werden kann:

* Soziale Netzwerke

  Durch die Kombination von Daten über Ihre Kunden und deren Interaktionen mit anderen Personen können Sie individuelle Erlebnisse schaffen, das Kundenverhalten vorhersagen oder Personen mit ähnlichen Interessen vernetzen. Azure Cosmos DB kann zum Verwalten sozialer Netzwerke sowie zum Nachverfolgen von Kundenpräferenzen und -daten verwendet werden.

* Empfehlungs-Engines

  Dieses Szenario kommt häufig im Einzelhandel vor. Durch die Kombination von Informationen zu Produkten, Benutzern und Benutzerinteraktionen (z.B. Einkäufe, Surfverhalten oder Bewertungen eines Artikels) können Sie benutzerdefinierte Empfehlungen erstellen. Die geringe Latenz, flexible Skalierung und native Diagrammunterstützung von Azure Cosmos DB sind für die Modellierung solcher Interaktionen ideal.

* Geodaten

  Zahlreiche Anwendungen in den Bereichen Telekommunikation, Logistik und Reiseplanung müssen einen bestimmten Ort in einer bestimmten Region oder die kürzeste bzw. optimale Route zwischen zwei Orten finden. Azure Cosmos DB ist die ideale Lösung für derartige Probleme.

* Internet der Dinge

  Durch die Modellierung von Netzwerken und Verbindungen zwischen IoT-Geräten als Graphen können Sie sich eine bessere Übersicht über den Status Ihrer Geräte und Ressourcen verschaffen. Sie können auch herausfinden, inwiefern sich Änderungen an einem Teil des Netzwerks möglicherweise auf andere Teile auswirken können.

## <a name="introduction-to-graph-databases"></a>Einführung in Graphdatenbanken
Daten sind in der Praxis naturgemäß vernetzt. Die konventionelle Datenmodellierung konzentriert sich darauf, Entitäten separat zu definieren und ihre Beziehungen zur Laufzeit zu berechnen. Obwohl dieses Modell seine Vorteile hat, kann sich die Verwaltung stark vernetzter Daten angesichts der Einschränkungen des Modells schwierig gestalten.  

Der Ansatz einer Graphdatenbank basiert dagegen auf persistenten Beziehungen in der Speicherebene, was zu hocheffizienten Abrufvorgängen der Graphen führt. Die Gremlin-API von Azure Cosmos DB unterstützt das [Eigenschaftsgraphmodell](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Eigenschaftsgraphobjekte

Ein [Eigenschaftsgraph](http://mathworld.wolfram.com/Graph.html) ist eine Struktur aus [Vertices](http://mathworld.wolfram.com/GraphVertex.html) und [Edges](http://mathworld.wolfram.com/GraphEdge.html). Beide Objekte können eine beliebige Anzahl von Schlüssel-Wert-Paaren als Eigenschaften aufweisen. 

* **Vertices** – Vertices bezeichnen diskrete Entitäten wie etwa eine Person, ein Ort oder ein Ereignis.

* **Edges** – Edges bezeichnen Beziehungen zwischen Vertices. Beispielsweise könnte eine Person eine andere Person kennen, an einem Ereignis beteiligt sein und sich vor Kurzem an einem Ort befunden haben. 

* **Eigenschaften** – Eigenschaften geben Informationen zu den Vertices und Edges an. Vertices und Edges können eine beliebige Anzahl an Eigenschaften umfassen. Mithilfe der Eigenschaften können die Objekte in einer Abfrage beschrieben und gefiltert werden. Zu Eigenschaften zählen beispielsweise Name und Alter eines Vertex sowie ein Edge, der einen Zeitstempel und/oder eine Gewichtung aufweisen kann. 

Graphdatenbanken werden oft NoSQL- oder nicht relationalen Datenbanken zugeordnet, da es keine Abhängigkeit von einem Schema oder einem eingeschränkten Datenmodell gibt. Aufgrund der fehlenden Schemas können vernetzte Strukturen natürlich und effizient modelliert und gespeichert werden. 

### <a name="gremlin-by-example"></a>Gremlin anhand eines Beispiels
Anhand eines Beispieldiagramms wird erläutert, wie Abfragen in Gremlin ausgedrückt werden können. Die folgende Abbildung zeigt eine Geschäftsanwendung, die Daten zu Benutzern, Interessen und Geräten in Form eines Diagramms verwaltet.  

![Beispieldatenbank mit Personen, Geräten und Interessen](./media/gremlin-support/sample-graph.png) 

Dieses Diagramm weist folgende Vertex-Typen (in Gremlin als „Bezeichnung“ bezeichnet) auf:

- Personen: Das Diagramm enthält drei Personen: Robin, Thomas und Ben.
- Interessen: In diesem Beispiel interessieren sie sich für Football.
- Geräte: Die von den Personen verwendeten Geräte.
- Betriebssysteme: Die Betriebssysteme auf den Geräten.

Die Beziehungen zwischen diesen Entitäten werden anhand von folgenden Edgetypen/Bezeichnungen dargestellt:

- Kennt: Beispiel: „Thomas kennt Robin.“
- Interessiert an: Um die Interessen der Personen in unserem Diagramm darzustellen, verwenden wir beispielsweise „Ben ist an Football interessiert.“.
- Betriebssystem ausgeführt: Auf dem Laptop wird das Windows-Betriebssystem ausgeführt.
- Verwendet: Dies gibt an, welches Gerät eine Person verwendet. Beispiel: „Robin verwendet ein Motorola-Telefon mit der Seriennummer 77.“

Nun führen wir anhand dieses Diagramms einige Vorgänge mit der [Gremlin-Konsole](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console) aus. Sie können diese Vorgänge auch mithilfe von Gremlin-Treibern auf der Plattform Ihrer Wahl (Java, Node.js, Python oder .NET) ausführen.  Bevor wir uns mit den unterstützten Funktionen von Azure Cosmos DB befassen, gehen wir einige Beispiele durch, um uns mit der Syntax vertraut zu machen.

Sehen wir uns zuerst CRUD an. Die folgende Gremlin-Anweisung fügt den Vertex „Thomas“ in das Diagramm ein:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Als Nächstes fügt die folgende Gremlin-Anweisung einen „kennt“-Edge zwischen Thomas und Robin ein.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Die folgende Abfrage gibt die Vertices „Person“ in absteigender Reihenfolge ihrer Vornamen zurück:
```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Diagramme eignen sich am besten, wenn Sie Fragen wie „Welche Betriebssysteme verwenden Freunde von Thomas?“ beantworten müssen. Um diese Information anhand des Diagramms zu ermitteln, können Sie diese Gremlin-Traversierung ausführen:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Nun sehen wir uns, welche Funktionen Azure Cosmos DB für Gremlin-Entwickler bietet.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Diagrammunterstützung in Azure Cosmos DB finden Sie durch folgende Ressourcen:

* Erste Schritte durch das [Tutorial zum Azure Cosmos DB-Diagramm](create-graph-dotnet.md)
* Erfahren Sie mehr über das [Abfragen von Graphen in Azure Cosmos DB mithilfe von Gremlin](gremlin-support.md).
