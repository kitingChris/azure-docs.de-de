---
title: Lesereplikate in Azure Database for MariaDB
description: In diesem Artikel werden Lesereplikate für Azure Database for MariaDB beschrieben.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 8abe257090b5159053a37350c9e24cc27073679b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081747"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Lesereplikate in Azure Database for MariaDB

Mithilfe des Lesereplikatfeatures können Sie Daten von einem Azure Database for MariaDB-Server auf einen schreibgeschützten Server replizieren. Sie können vom Masterserver auf bis zu fünf Replikate innerhalb derselben Region replizieren. Replikate werden asynchron mithilfe des auf der Position der binären Protokolldatei (binlog) basierenden Replikationsverfahrens der MariaDB-Engine und der globalen Transaktions-ID (GTID) aktualisiert. Weitere Informationen zur binlog-Replikation finden Sie [Übersicht über die binlog Replikation](https://mariadb.com/kb/en/library/replication-overview/).

> [!IMPORTANT]
> Die Lesereplikation in derselben Region befindet sich derzeit in der öffentlichen Vorschauversion.

Replikate sind neue Server, die Sie ähnlich wie normale Azure Database for MariaDB-Server verwalten. Für jedes Lesereplikat werden Ihnen die bereitgestellten Computeressourcen in Form von virtuellen Kernen sowie der Speicher in GB/Monat in Rechnung gestellt.

Weitere Informationen zur GTID-Replikation finden Sie in der [Dokumentation zur MariaDB-Replikation](https://mariadb.com/kb/en/library/gtid/).

## <a name="when-to-use-a-read-replica"></a>Einsatzmöglichkeiten von Lesereplikaten

Das Feature für Lesereplikate kann die Leistung und Skalierung von leseintensiven Workloads verbessern. Leseworkloads können in den Replikaten isoliert werden, während Schreibworkloads an den Masterserver weitergeleitet werden können.

In einem häufig anzutreffenden Szenario verwenden BI- und Analyseworkloads das Lesereplikat als Datenquelle für die Berichterstellung.

Da Replikate schreibgeschützt sind, führen sie nicht direkt zu einer verringerten Auslastung der Schreibkapazität auf dem Master. Dieses Feature ist nicht auf schreibintensive Workloads ausgerichtet.

Das Lesereplikatfeature verwendet die asynchrone Replikation. Das Feature ist nicht für synchrone Replikationsszenarien vorgesehen. Zwischen dem Masterserver und dem Replikat entsteht eine messbare Verzögerung. Letztendlich sind die Daten auf dem Replikat mit den Daten auf dem Masterserver konsistent. Verwenden Sie das Feature für Workloads, für die diese Verzögerung akzeptabel ist.

Lesereplikate können Ihren Notfallwiederherstellungsplan verbessern. Wenn ein regionaler Notfall eintritt und Ihr Masterserver nicht verfügbar ist, können Sie Ihren Workload auf ein Replikat in einer anderen Region umleiten. Dazu lassen Sie das Replikat zunächst Schreibzugriffe mithilfe der Funktion zum Beenden der Replikation annehmen. Sie können Ihre Anwendung dann umleiten, indem Sie die Verbindungszeichenfolge aktualisieren. Weitere Informationen finden Sie im Abschnitt [Beenden der Replikation](#stop-replication).

## <a name="create-a-replica"></a>Erstellen eines Replikats

Wenn ein Masterserver keine vorhandenen Replikatserver aufweist, wird der Master zunächst neu gestartet, um sich auf die Replikation vorzubereiten.

Wenn Sie den Workflow zum Erstellen von Replikaten starten, wird ein leerer Azure Database for MariaDB-Server erstellt. Der neue Server wird mit den Daten gefüllt, die auf dem Masterserver vorhanden waren. Die Erstellungszeit hängt von der Datenmenge auf dem Masterserver und der verstrichenen Zeit seit der letzten wöchentlichen vollständigen Sicherung ab. Dieser Zeitraum kann wenige Minuten bis zu mehrere Stunden umfassen.

> [!NOTE]
> Wenn Sie auf Ihren Servern keine Speicherwarnung eingerichtet haben, empfehlen wir, dies nachzuholen. Die Warnung informiert Sie, wenn ein Server sich dem Speicherlimit nähert und dadurch die Replikation beeinträchtigt wird.

[Erfahren Sie, wie Sie ein Lesereplikat im Azure-Portal erstellen](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Herstellen einer Verbindung mit einem Replikat

Wenn Sie ein Replikat erstellen, erbt dieses weder die Firewallregeln noch den VNET-Dienstendpunkt des Masterservers. Diese Regeln müssen separat für das Replikat eingerichtet werden.

Das Replikat erbt das Administratorkonto vom Masterserver. Alle Benutzerkonten auf dem Masterserver werden auf die Lesereplikate repliziert. Sie können nur mit denjenigen Benutzerkonten eine Verbindung mit einem Lesereplikat herstellen, die auf dem Masterserver verfügbar sind.

Sie können eine Verbindung mit dem Replikat herstellen, indem Sie wie bei einem normalen Azure Database for MariaDB-Server seinen Hostnamen und ein gültiges Benutzerkonto verwenden. Für einen Server namens **myreplica** mit dem Administratorbenutzernamen **myadmin** können Sie eine Verbindung mit dem Replikat herstellen, indem Sie die mysql-Befehlszeilenschnittstelle verwenden:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Geben Sie an der Eingabeaufforderung das Kennwort für das Benutzerkonto ein.

## <a name="monitor-replication"></a>Überwachen der Replikation

Azure Database for MariaDB bietet die Metrik **Replikationsverzögerung in Sekunden** in Azure Monitor. Diese Metrik steht nur für Replikate zur Verfügung.

Diese Metrik wird mithilfe der `seconds_behind_master`-Metrik berechnet, die im MariaDB-Befehl `SHOW SLAVE STATUS` verfügbar ist.

Legen Sie eine Warnung fest, um Sie zu benachrichtigen, wenn die Replikationsverzögerung einen Wert erreicht, der für Ihre Workload nicht annehmbar ist.

## <a name="stop-replication"></a>Beenden der Replikation

Sie können die Replikation zwischen einem Masterserver und einem Replikat beenden. Sobald die Replikation zwischen einem Masterserver und einem Lesereplikat beendet wurde, wird das Replikat zu einem eigenständigen Server. Bei den Daten auf diesem eigenständigen Server handelt es sich um die Daten, die zu dem Zeitpunkt, als der Befehl zum Beenden der Replikation gestartet wurde, auf dem Replikatserver vorhanden waren. Der eigenständige Server wird nicht zusammen mit dem Masterserver aktualisiert.

Wenn Sie die Replikation zu einem Replikat stoppen, gehen alle Links zu seinem vorherigen Master und zu anderen Replikaten verloren. Zwischen einem Master und seinem Replikat gibt es kein automatisiertes Failover.

> [!IMPORTANT]
> Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.
> Stellen Sie vor dem Beenden der Replikation auf einem Lesereplikat sicher, dass das Replikat alle erforderlichen Daten enthält.

Erfahren Sie, wie Sie die [Replikation auf ein Replikat beenden](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Überlegungen und Einschränkungen

### <a name="pricing-tiers"></a>Tarife

Lesereplikate sind zurzeit nur in den Tarifen „Universell“ und „Arbeitsspeicheroptimiert“ verfügbar.

### <a name="master-server-restart"></a>Masterserverneustart

Wenn Sie ein Replikat für einen Master erstellen, der keine vorhandenen Replikate hat, startet der Master zunächst neu, um sich auf die Replikation vorzubereiten. Bitte beachten Sie dies und führen Sie diese Operationen nicht zu Spitzenzeiten durch.

### <a name="new-replicas"></a>Neue Replikate

Ein Lesereplikat wird als neuer Azure Database for MariaDB-Server erstellt. Ein vorhandener Server kann nicht in ein Replikat umgewandelt werden. Es kann kein Replikat eines anderen Lesereplikats erstellt werden.

### <a name="replica-configuration"></a>Replikatkonfiguration

Ein Replikat wird mit der gleichen Serverkonfiguration wie der Masterserver erstellt. Nachdem ein Replikat erstellt wurde, können mehrere Einstellungen unabhängig vom Masterserver geändert werden: die Computegeneration, die virtuellen Kerne, der Speicher, der Aufbewahrungszeitraum für Sicherungen und die Version der MariaDB-Engine. Auch der Tarif kann unabhängig geändert werden, allerdings nicht in den oder aus dem Tarif „Basic“.

> [!IMPORTANT]
> Bevor Sie die Konfiguration eines Masterservers mit neuen Werten aktualisieren, ändern Sie die Replikatkonfiguration in gleiche oder größere Werte. Durch diese Aktion wird sichergestellt, dass das Replikat mit allen Änderungen, die auf dem Masterserver durchgeführt werden, Schritt halten kann.

### <a name="stopped-replicas"></a>Beendete Replikate

Wenn Sie die Replikation zwischen einem Masterserver und einem Lesereplikat beenden, wird das beendete Replikat zu einem eigenständigen Server, der sowohl Lese- als auch Schreibzugriffe akzeptiert. Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.

### <a name="deleted-master-and-standalone-servers"></a>Gelöschte Masterserver und eigenständige Server

Wenn ein Masterserver gelöscht wird, wird die Replikation an alle Lesereplikate beendet. Diese Replikate werden zu eigenständigen Servern. Der Masterserver selbst wird gelöscht.

### <a name="user-accounts"></a>Benutzerkonten

Benutzer auf dem Masterserver werden an die Lesereplikate repliziert. Sie können nur mit denjenigen Benutzerkonten eine Verbindung mit einem Lesereplikat herstellen, die auf dem Masterserver verfügbar sind.

### <a name="server-parameters"></a>Serverparameter

Um zu verhindern, dass Daten nicht synchronisiert werden und um einen möglichen Datenverlust oder eine Beschädigung zu vermeiden, sind einige Serverparameter bei der Verwendung von Lesereplikaten für die Aktualisierung gesperrt.

Die folgenden Serverparameter sind sowohl auf dem Master- als auch auf dem Replikatserver gesperrt:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

Der Parameter [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) ist auf den Replikatservern gesperrt.

### <a name="other"></a>Andere

- Die Erstellung des Replikats eines Replikats wird nicht unterstützt.
- In-Memory-Tabellen können dazu führen, dass Replikate nicht mehr synchron sind. Dies ist eine Einschränkung der MariaDB-Replikationstechnologie.
- Stellen Sie sicher, dass die Masterservertabellen über Primärschlüssel verfügen. Das Fehlen von Primärschlüsseln kann zu Replikationslatenz zwischen dem Master und den Replikaten führen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Lesereplikate über das Azure-Portal erstellen und verwalten](howto-read-replicas-portal.md).
