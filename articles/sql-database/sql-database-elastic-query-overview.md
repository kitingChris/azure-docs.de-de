---
title: Übersicht über elastische Abfragen in Azure SQL-Datenbank | Microsoft Docs
description: Durch elastische Abfragen können Sie eine Transact-SQL-Abfrage für mehrere Datenbanken ausführen.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5188862c50895c8e3f1bdecb4e08d39409bb5f9e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491665"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Übersicht über elastische Abfragen in Azure SQL-Datenbank (Vorschau)

Mithilfe des Features für elastische Abfragen (in der Vorschauphase) können Sie eine Transact-SQL-Abfrage ausführen, die mehrere Datenbanken in Azure SQL-Datenbank umfasst. Es ermöglicht das Ausführen datenbankübergreifender Abfragen für den Zugriff auf Remotetabellen und das Verbinden von Microsoft- und Drittanbietertools (Excel, Power BI, Tableau usw.), um Datenebenen mit mehreren Datenbanken abzufragen. Mit dieser Funktion können Sie Abfragen horizontal auf große Datenebenen in SQL-Datenbanken skalieren und die Abfrageergebnisse in Berichten für Business Intelligence (BI) darstellen.

## <a name="why-use-elastic-queries"></a>Gründe für die Verwendung elastischer Abfragen

### <a name="azure-sql-database"></a>Azure SQL-Datenbank

Die Abfrage über Azure SQL-Datenbanken hinweg erfolgt vollständig in T-SQL. Dies ermöglicht das Durchführen schreibgeschützter Abfragen in Remotedatenbanken und bietet derzeitigen Kunden von lokalem SQL Server eine Option zum Migrieren von Anwendungen unter Verwendung von Namen mit drei oder vier Teilen oder eines Verbindungsservers zu SQLDB.

### <a name="available-on-standard-tier"></a>Im Standard-Tarif verfügbar

Elastische Abfragen werden in den Dienstebenen „Standard“ und „Premium“ unterstützt. Informationen zu Leistungseinschränkungen bei niedrigeren Dienstebenen finden Sie nachstehend im Abschnitt „Einschränkungen der Vorschau“.

### <a name="push-parameters-to-remote-databases"></a>Übertragen von Parametern mithilfe von Push an Remotedatenbanken

Elastische Abfragen können nun SQL-Parameter zur Ausführung an die Remotedatenbank übertragen.

### <a name="stored-procedure-execution"></a>Ausführen gespeicherter Prozeduren

Führen Sie remote gespeicherte Prozeduraufrufe oder Remotefunktionen mithilfe von [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714) durch.

### <a name="flexibility"></a>Flexibilität

Externe Tabellen mit elastischer Abfrage können auf Remotetabellen mit einem anderen Schema- oder Tabellennamen verweisen.

## <a name="elastic-query-scenarios"></a>Szenarien mit elastischen Abfragen

Ziel ist es, Berichterstellungsszenarien zu vereinfachen, in denen mehrere Datenbanken Zeilen zu einem einzelnen Gesamtergebnis beitragen. Die Abfrage kann entweder vom Benutzer oder durch die Anwendung direkt oder indirekt über Tools erstellt werden, die mit der Datenbank verbunden sind. Dies ist besonders hilfreich beim Erstellen von Berichten mit kommerziellen BI- oder Datenintegrationstools bzw. allen Anwendungen, die nicht geändert werden können. Mithilfe einer elastischen Abfrage können Sie mehrere Datenbanken unter Verwendung der gewohnten SQL Server-Verbindungsumgebung in Tools wie Excel, Power BI, Tableau oder Cognos abfragen.
Außerdem ermöglicht eine elastische Abfrage den einfachen Zugriff auf eine ganze Sammlung von Datenbanken, die von SQL Server Management Studio oder Visual Studio ausgegeben werden, und sie vereinfacht datenbankübergreifende Abfragen aus Entity Framework oder anderen ORM-Umgebungen. Abbildung 1 zeigt ein Szenario, in dem eine vorhandene Cloudanwendung (die die [Clientbibliothek für elastische Datenbanken](sql-database-elastic-database-client-library.md) verwendet) auf einer horizontal skalierten Datenebene aufbaut und eine elastische Abfrage für datenbankübergreifende Berichte verwendet wird.

**Abbildung 1** Elastische Abfrage auf horizontal hochskalierter Datenebene

![Elastische Abfrage auf horizontal hochskalierter Datenebene][1]

Kundenszenarien für elastische Abfragen zeichnen sich durch die folgenden Topologien aus:

* **Vertikale Partitionierung – datenbankübergreifende Abfragen** (Topologie 1): Die Daten werden zwischen mehreren Datenbanken auf einer Datenschicht vertikal partitioniert. In der Regel befinden sich verschiedene Sätze von Tabellen in verschiedenen Datenbanken. Das bedeutet, dass das Schema bei verschiedenen Datenbanken unterschiedlich ist. Beispielsweise befinden sich alle bestandsbezogenen Tabellen in einer Datenbank, während alle Buchhaltungstabellen in einer zweiten Datenbank enthalten sind. Gängige Anwendungsfälle mit dieser Topologie erfordern zum Erstellen von Berichten das Abfragen von Tabellen in mehreren Datenbanken.
* **Horizontale Partitionierung – Sharding** (Topologie 2): Die Daten werden horizontal partitioniert, um Zeilen auf einer horizontal hochskalierten Datenschicht zu verteilen. Bei diesem Ansatz ist das Schema für alle teilnehmenden Datenbanken identisch. Dieser Ansatz wird auch „Sharding“ genannt. Sharding kann (1.) mithilfe der Bibliothek für elastische Datenbanktools oder (2.) eines eigenständigen Shardings ausgeführt und verwaltet werden. Eine elastische Abfrage dient zum viele Shards übergreifenden Abfragen oder Erstellen von Berichten.

> [!NOTE]
> Die elastische Abfrage eignet sich am besten für Berichtsszenarios, bei denen der Großteil der Verarbeitung (Filterung, Aggregation) auf der Seite der externen Quelle ausgeführt werden kann. Sie eignet sich nicht für ETL-Vorgänge, bei denen große Datenmengen von Remotedatenbanken übertragen werden. Bei hohen Reporting-Arbeitslasten oder Data Warehousing-Szenarien mit komplexeren Abfragen, sollten Sie auch die Verwendung von [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)in Betracht ziehen.
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Vertikale Partitionierung – datenbankübergreifende Abfragen

Lesen Sie zum Einstieg in die Programmierung [Erste Schritte mit datenbankübergreifenden Abfragen (vertikale Partitionierung)](sql-database-elastic-query-getting-started-vertical.md).

Eine elastische Abfrage kann verwendet werden, um Daten in einer SQL-Datenbank anderen SQL-Datenbank-Instanzen zur Verfügung zu stellen. Dadurch können Abfragen aus einer Datenbank auf Tabellen in einer beliebigen anderen Remoteinstanz von SQL-Datenbank verweisen. Der erste Schritt ist das Definieren einer externen Datenquelle für jede Remotedatenbank. Die externe Datenquelle wird in der lokalen Datenbank definiert, aus der Sie auf Tabellen zugreifen möchten, die sich in der Remotedatenbank befinden. Es sind keine Änderungen an der Remotedatenbank erforderlich. Für normale vertikale Partitionierungsszenarien, in denen verschiedene Datenbanken unterschiedliche Schemas haben, können elastische Abfragen verwendet werden, um gängige Anwendungsfälle zu implementieren, z. B. den Zugriff auf Verweisdaten und datenbankübergreifende Abfragen.

> [!IMPORTANT]
> Sie müssen über die Berechtigung ALTER ANY EXTERNAL DATA SOURCE verfügen. Diese Berechtigung ist in der Berechtigung ALTER DATABASE enthalten. ALTER ANY EXTERNAL DATA SOURCE-Berechtigungen sind erforderlich, um auf die zu Grunde liegende Datenquelle zu verweisen.
>

**Verweisdaten:** Die Topologie wird zur Verwaltung von Verweisdaten verwendet. In der folgenden Abbildung sind die beiden Tabellen mit Verweisdaten (T1 und T2) in einer dedizierten Datenbank gespeichert. Über eine elastische Abfrage können Sie nun aus anderen Datenbanken remote auf die Tabellen T1 und T2 zugreifen (siehe die Abbildung). Wählen Sie Topologie 1, wenn Verweistabellen klein sind oder Remoteabfragen von Verweistabellen selektive Prädikate haben.

**Abbildung 2** Vertikale Partitionierung – Verwenden einer elastischen Abfrage zum Abfragen von Verweisdaten

![Vertikale Partitionierung – Verwenden einer elastischen Abfrage zum Abfragen von Verweisdaten][3]

**Datenbankübergreifende Abfragen:** Elastische Abfragen ermöglichen Anwendungsfälle, die das Abfragen mehrerer SQL-Datenbank-Instanzen erfordern. Abbildung 3 zeigt vier unterschiedliche Datenbanken: CRM, Bestand, Personal und Produkte. Abfragen, die auf eine der Datenbanken angewendet werden, benötigen außerdem Zugriff auf eine oder alle anderen Datenbanken. Bei Verwenden einer elastischen Abfrage können Sie Ihre Datenbank für diesen Fall konfigurieren, indem Sie einige einfache DDL-Anweisungen auf jede der vier Datenbanken anwenden. Nach dieser einmaligen Konfiguration ist der Zugriff auf eine Remotetabelle so einfach wie das Verweisen auf eine lokale Tabelle in Ihren T-SQL-Abfragen oder in Ihren BI-Tools. Dieser Ansatz wird empfohlen, wenn von den Remoteabfragen keine umfangreichen Ergebnisse zurückgegeben werden.

**Abbildung 3** Vertikale Partitionierung – Verwenden einer elastischen Abfrage zum Abfragen verschiedener Datenbanken

![Vertikale Partitionierung – Verwenden einer elastischen Abfrage zum Abfragen verschiedener Datenbanken][4]

Die folgenden Schritte dienen zum Konfigurieren elastischer Datenbankabfragen für Szenarien mit vertikaler Partitionierung, die Zugriff auf eine Tabelle in einer Remoteinstanz von SQL-Datenbank mit demselben Schema erfordern:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource of type **RDBMS**
* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Nach Ausführen der DDL-Anweisungen können Sie auf die Remotetabelle „mytable“ wie auf eine lokale Tabelle zugreifen. Azure SQL-Datenbank öffnet automatisch eine Verbindung mit der Remotedatenbank, verarbeitet Ihre Anforderung an die Remotedatenbank und gibt die Ergebnisse zurück.

## <a name="horizontal-partitioning---sharding"></a>Horizontale Partitionierung (Sharding)

Das Verwenden einer elastischen Abfrage für Berichtsaufgaben auf einer Datenebene mit Sharding, d.h. horizontaler Partitionierung, erfordert, dass die Datenbanken der Datenebene durch eine [Shardzuordnung für elastische Datenbanken](sql-database-elastic-scale-shard-map-management.md) dargestellt werden. Normalerweise wird nur eine einzelne Shardzuordnung in diesem Szenario verwendet, und eine dedizierte Datenbank mit elastischen Abfragefunktionen (Hauptknoten) dient als Einstiegspunkt für Berichtsabfragen. Nur diese dedizierte Datenbank benötigt Zugriff auf die Shardzuordnung. Abbildung 4 zeigt diese Topologie und ihre Konfiguration mit der elastischen Abfragedatenbank und Shardzuordnung. Die Datenbanken der Datenebene können eine beliebige Version oder Edition von Azure SQL-Datenbank haben. Weitere Informationen zur Clientbibliothek für elastische Datenbanken und zum Erstellen von Shardzuordnungen finden Sie unter [Verwaltung von Shardzuordnungen](sql-database-elastic-scale-shard-map-management.md).

**Abbildung 4** Horizontale Partitionierung – Verwenden einer elastischen Abfrage von Datenebenen mit Sharding für die Berichterstellung

![Horizontale Partitionierung – Verwenden einer elastischen Abfrage von Datenebenen mit Sharding für die Berichterstellung][5]

> [!NOTE]
> Die elastische Abfragedatenbank (Hauptknoten) kann eine separate Datenbank oder aber die gleiche Datenbank sein, die als Host für die Shardzuordnung fungiert.
> Stellen Sie unabhängig von der ausgewählten Konfiguration sicher, dass Dienstebene und Computegröße der Datenbank hoch genug sind, um das voraussichtliche Aufkommen an Anmelde- und Abfrageanforderungen zu bewältigen.

Die folgenden Schritte dienen zum Konfigurieren elastischer Datenbankabfragen für Szenarios mit horizontaler Partitionierung, die Zugriff auf eine Gruppe von Tabellen in (üblicherweise) mehreren Remoteinstanzen von SQL-Datenbank erfordern:

* [CREATE MASTER KEY](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* Erstellen Sie eine [Shardzuordnung](sql-database-elastic-scale-shard-map-management.md) , die Ihre Datenebene darstellt, mithilfe der Clientbibliothek für elastische Datenbanken.
* [CREATE/DROP EXTERNAL DATA SOURCE](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource of type **SHARD_MAP_MANAGER**
* [CREATE/DROP EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) mytable

Nachdem Sie diese Schritte ausgeführt haben können Sie auf die horizontal partitionierte Tabelle „mytable“ wie auf eine lokale Tabelle zugreifen. Azure SQL-Datenbank öffnet automatisch mehrere parallele Verbindungen mit den Remotedatenbanken, in denen die Tabellen physisch gespeichert sind, verarbeitet die Anforderungen an die Remotedatenbanken und gibt die Ergebnisse zurück.
Weitere Informationen zu den Schritten, die für das Szenario der horizontalen Partitionierung erforderlich sind, finden Sie unter [Elastische Abfrage für die horizontale Partitionierung](sql-database-elastic-query-horizontal-partitioning.md).

Lesen Sie zum Einstieg in die Programmierung [Erste Schritte mit elastischen Abfragen für horizontale Partitionierung (Sharding)](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>T-SQL-Abfragen

Sobald Sie Ihre externen Datenquellen und externen Tabellen definiert haben, können Sie herkömmliche SQL Server-Verbindungszeichenfolgen zum Verbinden mit den Datenbanken verwenden, in denen Sie Ihre externen Tabellen definiert haben. Sie können dann bei den nachstehend beschriebenen Einschränkungen über diese Verbindung T-SQL-Anweisungen auf Ihre externen Tabellen anwenden. Weitere Informationen und Beispiele für T-SQL-Abfragen finden Sie in den Dokumentationsthemen für [horizontale Partitionierung](sql-database-elastic-query-horizontal-partitioning.md) und [vertikale Partitionierung](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Konnektivität für Tools

Sie können herkömmliche SQL Server-Verbindungszeichenfolgen zum Verbinden Ihrer Anwendungen bzw. BI- oder Datenintegrationstools mit Datenbanken verwenden, die externe Tabellen enthalten. Stellen Sie sicher, dass SQL Server als Datenquelle für das Tool unterstützt wird. Nach dem Herstellen der Verbindung können Sie auf die elastische Abfragedatenbank und externen Tabellen wie auf jede andere SQL Server-Datenbank zugreifen, mit der Sie sich über Ihr Tool verbinden.

> [!IMPORTANT]
> Die Authentifizierung mithilfe von Azure Active Directory mit elastischen Abfragen wird derzeit nicht unterstützt.

## <a name="cost"></a>Kosten

Elastische Abfragen sind in den Kosten für Azure SQL-Datenbanken enthalten. Beachten Sie, dass Topologien unterstützt werden, in denen sich Remotedatenbanken in einem anderen Rechenzentrum befinden als der Endpunkt der elastischen Abfrage. Der Datenausgang aus Remotedatenbanken wird jedoch zu den üblichen [Azure-Gebühren](https://azure.microsoft.com/pricing/details/data-transfers/) in Rechnung gestellt.

## <a name="preview-limitations"></a>Einschränkungen der Vorschau

* Bei der Dienstebene „Standard“ kann das Ausführen Ihrer ersten elastischen Abfrage einige Minuten dauern. Dieser Zeitraum wird für das Laden der elastischen Abfragefunktionalität benötigt: je höher die Dienstebene und Computegröße, desto besser die Ladeleistung.
* Das Erstellen von Skripts für externe Datenquellen oder externe Tabellen in SSMS oder SSDT wird noch nicht unterstützt.
* Import/Export für SQLDB unterstützt noch keine externen Datenquellen und externen Tabellen. Wenn Sie Import/Export verwenden müssen, löschen Sie diese Objekte vor dem Exportieren, und erstellen Sie sie nach dem Importieren neu.
* Elastische Abfragen unterstützen derzeit nur den schreibgeschützten Zugriff auf externe Tabellen. Sie können jedoch die vollständige T-SQL-Funktionalität für die Datenbank nutzen, in der die externe Tabelle definiert ist. Dies kann beispielsweise hilfreich sein, um temporäre Ergebnisse z.B. mithilfe von „SELECT <column_list> INTO <local_table>“ dauerhaft zu speichern, oder um gespeicherte Prozeduren für die elastische Abfragedatenbank zu definieren, die auf externe Tabellen verweisen.
* Mit Ausnahme von „nvarchar(max)“ (einschließlich räumlicher Typen) werden LOB-Typen in externen Tabellendefinitionen nicht unterstützt. Um dieses Problem zu umgehen, können Sie eine Sicht für die Remotedatenbank erstellen, die den LOB-Typ in „nvarchar(max)“ umwandelt, ihre externe Tabelle für die Sicht anstatt für die Basistabelle definieren und sie in Ihren Abfragen in den ursprünglichen LOB-Typ rückumwandeln.
* Durch Spalten vom Datentyp „nvarchar(max)“ im Resultset werden erweiterte Batchverarbeitungstechniken deaktiviert, die bei der Implementierung elastischer Abfragen verwendet werden und möglicherweise die Leistung der Abfragen für eine Größenordnung oder sogar zwei Größenordnungen in nicht kanonischen Anwendungsfällen beeinträchtigen, in denen eine Vielzahl von nicht aggregierten Daten infolge der Abfrage übertragen werden.
* Spaltenstatistiken werden für externe Tabellen derzeit nicht unterstützt. Tabellenstatistiken werden unterstützt, müssen aber manuell erstellt werden.
* Elastische Abfragen funktionieren nur mit Azure SQL-Datenbank. Sie können sie nicht für Abfragen von lokalen SQL Servern oder SQL Servern in einem VM verwenden.

## <a name="feedback"></a>Feedback

Geben Sie uns unten, in den MSDN-Foren oder auf Stack Overflow Feedback zu Ihrer Erfahrung mit elastischen Datenbankabfragen. Für uns sind alle Arten von Feedback zum Dienst (Fehler, Designprobleme und fehlende Features) interessant.

## <a name="next-steps"></a>Nächste Schritte

* Ein Tutorial zur vertikalen Partitionierung finden Sie unter [Erste Schritte mit datenbankübergreifenden Abfragen (vertikale Partitionierung)](sql-database-elastic-query-getting-started-vertical.md).
* Die Syntax und Beispiele für Abfragen von vertikal partitionierten Daten finden Sie unter [Abfragen von vertikal partitionierten Daten](sql-database-elastic-query-vertical-partitioning.md).
* Ein Tutorial zur horizontalen Partitionierung (Sharding) finden Sie unter [Erste Schritte mit elastischen Abfragen für horizontale Partitionierung (Sharding)](sql-database-elastic-query-getting-started.md).
* Die Syntax und Beispiele für Abfragen von horizontal partitionierten Daten finden Sie unter [Abfragen von horizontal partitionierten Daten](sql-database-elastic-query-horizontal-partitioning.md).
* Unter [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714) finden Sie eine gespeicherte Prozedur, mit der eine Transact-SQL-Anweisung für eine einzelne Remoteinstanz von Azure SQL-Datenbank oder für eine Gruppe von Datenbanken ausgeführt wird, die als Shards in einem Schema mit horizontaler Partitionierung dienen.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
