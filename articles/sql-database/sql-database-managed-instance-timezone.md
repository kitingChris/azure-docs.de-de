---
title: 'Verwaltete Azure SQL-Datenbank-Instanz: Zeitzonen | Microsoft-Dokumentation'
description: Enthält eine Beschreibung der Besonderheiten von Zeitzonen einer verwalteten Azure SQL-Datenbank-Instanz.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 07/05/2019
ms.openlocfilehash: 05ec49c98c5bcfe40346550f5570c03a8fb3f881
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657991"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Zeitzonen in einer verwalteten Azure SQL-Datenbank-Instanz

„Koordinierte Weltzeit (UTC)“ ist die empfohlene Zeitzone für die Datenschicht von Cloudlösungen. Bei einer verwalteten Azure SQL-Datenbank-Instanz können Sie auch zwischen Zeitzonen wählen, um die Anforderungen vorhandener Anwendungen zu erfüllen, für die Datums- und Uhrzeitwerte gespeichert und Datums- und Uhrzeitfunktionen mit einem impliziten Kontext einer spezifischen Zeitzone aufgerufen werden.

Bei T-SQL-Funktionen wie [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) oder bei CLR-Code wird die Zeitzone beachtet, die für die Instanzebene festgelegt wurde. Auch bei SQL Server-Agentaufträgen wird der Zeitplan gemäß der Zeitzone der Instanz befolgt.

  >[!NOTE]
  > Verwaltete Instanzen sind die einzige Bereitstellungsoption von Azure SQL-Datenbank, für die die Zeitzoneneinstellung unterstützt wird. Für andere Bereitstellungsoptionen wird immer UTC genutzt.
Verwenden Sie [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) in SQL-Einzel- und -Pooldatenbanken, wenn Sie Datums- und Uhrzeitinformationen in einer anderen Zeitzone als UTC interpretieren müssen.

## <a name="supported-time-zones"></a>Unterstützte Zeitzonen

Vom zugrunde liegenden Betriebssystem der verwalteten Instanz werden verschiedene unterstützte Zeitzonen geerbt. Es wird regelmäßig eine Aktualisierung durchgeführt, um neue Zeitzonendefinitionen einzubinden und Änderungen an vorhandenen Definitionen widerzuspiegeln.

Die [Richtlinie zur Sommerzeit/Zeitzonenänderung](https://aka.ms/time) garantiert historische Genauigkeit ab dem Jahr 2010.

Eine Liste mit Namen der unterstützten Zeitzonen wird über die Systemansicht [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) verfügbar gemacht.

## <a name="set-a-time-zone"></a>Festlegen einer Zeitzone

Eine Zeitzone einer verwalteten Instanz kann nur während der Instanzerstellung festgelegt werden. Die Standardzeitzone ist UTC.

  >[!NOTE]
  > Die Zeitzone einer vorhandenen verwalteten Instanz kann nicht geändert werden.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Festlegen der Zeitzone über das Azure-Portal

Wählen Sie beim Eingeben der Parameter für eine neue Instanz in der Liste mit den unterstützten Zeitzonen eine Zeitzone aus.
  
![Festlegen einer Zeitzone während der Instanzerstellung](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage

Geben Sie die timezoneId-Eigenschaft in Ihrer [Resource Manager-Vorlage](https://aka.ms/sql-mi-create-arm-posh) an, um die Zeitzone während der Instanzerstellung festzulegen.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Eine Liste mit unterstützten Werten für die timezoneId-Eigenschaft finden Sie am Ende dieses Artikels.

Wenn nichts anderes angegeben ist, wird UTC als Zeitzone festgelegt.

## <a name="check-the-time-zone-of-an-instance"></a>Überprüfen der Zeitzone einer Instanz

Mit der Funktion [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) wird ein Anzeigename der Zeitzone einer Instanz zurückgegeben.

## <a name="cross-feature-considerations"></a>Featureübergreifende Aspekte

### <a name="restore-and-import"></a>Wiederherstellung und Import

Sie können für eine verwaltete Instanz eine Sicherungsdatei wiederherstellen oder Daten importieren, wenn die entsprechende Instanz oder der Server über andere Zeitzoneneinstellungen verfügt. Gehen Sie hierbei mit Bedacht vor. Analysieren Sie das Anwendungsverhalten und die Ergebnisse der Abfragen und Berichte, wie Sie dies auch beim Übertragen von Daten zwischen zwei SQL Server-Instanzen mit verschiedenen Zeitzoneneinstellungen tun.

### <a name="point-in-time-restore"></a>Point-in-Time-Wiederherstellung

<del>Wenn Sie eine Point-in-Time-Wiederherstellung durchführen, wird der Wiederherstellungszeitpunkt als UTC-Zeit interpretiert. Mit dieser Einstellung werden Mehrdeutigkeiten aufgrund von Sommerzeit und den damit verbundenen Umstellungen vermieden.<del>

 >[!WARNING]
  > Das aktuelle Verhalten entspricht nicht der obigen Aussage, und die Zeit für die Wiederherstellung wird gemäß der Zeitzone der von der Quelle verwalteten Instanz interpretiert, aus der automatische Datenbanksicherungen entnommen werden. Wir arbeiten daran, dieses Verhalten zu korrigieren, um einen bestimmten Point-in-Time als UTC-Zeit zu interpretieren. Weitere Details dazu finden Sie unter [Bekannte Probleme](sql-database-managed-instance-timezone.md#known-issues).

### <a name="auto-failover-groups"></a>Autofailover-Gruppen

Die Verwendung der gleichen Zeitzone für eine primäre und sekundäre Instanz einer Failovergruppe wird zwar nicht erzwungen, aber dringend empfohlen.

  >[!WARNING]
  > Wir empfehlen Ihnen dringend, in einer Failovergruppe jeweils die gleiche Zeitzone für die primäre und die sekundäre Instanz zu verwenden. Aufgrund von einigen selten vorkommenden Szenarien wird die Beibehaltung der Zeitzone für primäre und sekundäre Instanzen nicht erzwungen. Es ist wichtig zu verstehen, dass bei einem manuellen oder automatischen Failover für die sekundäre Instanz die ursprüngliche Zeitzone beibehalten wird.

## <a name="limitations"></a>Einschränkungen

- Die Zeitzone der vorhandenen verwalteten Instanz kann nicht geändert werden.
- Für externe Prozesse, die über die SQL Server-Agentaufträge gestartet werden, wird die Zeitzone der Instanz nicht beachtet.

## <a name="known-issues"></a>Bekannte Probleme

Wenn der Point-in-Time-Wiederherstellungsvorgang (Point-in-Time Restore, PITR) durchgeführt wird, wird die Zeit für die Wiederherstellung gemäß der Zeitzone interpretiert, die für die verwaltete Instanz festgelegt ist, aus der automatische Datenbanksicherungen entnommen werden, auch wenn die Portalseite für PITR vermuten lässt, dass die Zeit als UTC interpretiert wird.

Beispiel:

Angenommen, für die Instanz, aus der automatische Sicherungen übernommen werden, ist die Zeitzone Eastern Normalzeit (UTC-5) festgelegt.
Die Portalseite für Point-in-Time-Wiederherstellung lässt vermuten, dass die Zeit, die Sie für die Wiederherstellung auswählen, UTC-Zeit ist:

![PITR mit der lokalen Uhrzeit im Portal](media/sql-database-managed-instance-timezone/02-pitr-with-nonutc-timezone.png)

Die Zeit für die Wiederherstellung wird jedoch tatsächlich als Eastern Normalzeit interpretiert, und in diesem speziellen Beispiel wird der Zustand der Datenbank um 9 Uhr Eastern Normalzeit und nicht nach UTC-Zeit wiederhergestellt.

Wenn Sie die Point-in-Time-Wiederherstellung zu einem bestimmten Zeitpunkt in UTC-Zeit durchführen möchten, berechnen Sie zunächst die äquivalente Zeit in der Zeitzone der Quellinstanz, und verwenden Sie diese Zeit im Portal oder in PowerShell bzw. einem CLI-Skript.

## <a name="list-of-supported-time-zones"></a>Liste mit den unterstützten Zeitzonen

| **Zeitzonen-ID** | **Anzeigename der Zeitzone** |
| --- | --- |
| Datumsgrenze Normalzeit | (UTC-12:00) Internationale Datumsgrenze West |
| UTC-11 | (UTC-11:00) Koordinierte Weltzeit -11 |
| Aleuten Normalzeit | (UTC-10:00) Aleuten-Inseln |
| Hawaii Normalzeit | (UTC-10:00) Hawaii |
| Marquesas Normalzeit | (UTC-09:30) Marquesas-Inseln |
| Alaska Normalzeit | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Koordinierte Weltzeit -09 |
| Pacific Normalzeit (Mexiko) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Koordinierte Weltzeit -08 |
| Pacific Normalzeit | (UTC-08:00) Pacific Time (USA und Kanada) |
| Mountain Normalzeit (Arizona) | (UTC-07:00) Arizona |
| Mountain Normalzeit (Mexiko) | (UTC-07:00) Chihuahua, La Paz, Mazatlán |
| Mountain Normalzeit | (UTC-07:00) Mountain Time (USA und Kanada) |
| Mittelamerikanische Normalzeit | (UTC-06:00) Mittelamerika |
| Central Normalzeit | (UTC-06:00) Central Time (USA und Kanada) |
| Osterinsel Normalzeit | (UTC-06:00) Osterinsel |
| Central Normalzeit (Mexiko) | (UTC-06:00) Guadalajara, Mexiko-Stadt, Monterrey |
| Central Normalzeit (Kanada) | (UTC-06:00) Saskatchewan |
| Westl. Südamerika Normalzeit | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Eastern Normalzeit (Mexiko) | (UTC-05:00) Chetumal |
| Eastern Normalzeit | (UTC-05:00) Eastern Time (USA und Kanada) |
| Haiti Normalzeit | (UTC-05:00) Haiti |
| Kuba Normalzeit | (UTC-05:00) Havanna |
| Eastern Normalzeit (Indiana) | (UTC-05:00) Indiana (Ost) |
| Turks- und Caicosinseln Normalzeit | (UTC-05:00) Turks- und Caicosinseln |
| Paraguay Normalzeit | (UTC-04:00) Asunción |
| Atlantic Normalzeit | (UTC-04:00) Atlantik-Zeit (Kanada) |
| Venezuela Normalzeit | (UTC-04:00) Caracas |
| Zentralbrasilianische Normalzeit | (UTC-04:00) Cuiabá |
| Mittl. Südamerika Normalzeit | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Chilenische Normalzeit | (UTC-04:00) Santiago |
| Neufundland Normalzeit | (UTC-03:30) Neufundland |
| Tocantins Normalzeit | (UTC-03:00) Araguaína |
| E. Östl. Südamerika Normalzeit | (UTC-03:00) Brasília |
| Östl. Südamerika Normalzeit | (UTC-03:00) Cayenne, Fortaleza |
| Argentinien Normalzeit | (UTC-03:00) Buenos Aires Stadt |
| Grönland Normalzeit | (UTC-03:00) Grönland |
| Montevideo Normalzeit | (UTC-03:00) Montevideo |
| Magellan (Normalzeit) | (UTC-03:00) Punta Arenas |
| Saint-Pierre Normalzeit | (UTC-03:00) St. Pierre und Miquelon |
| Bahia Normalzeit | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Koordinierte Weltzeit -02 |
| Mittelatlantik Normalzeit | (UTC-02:00) Mittelatlantik – Alt |
| Azoren Normalzeit | (UTC-01:00) Azoren |
| Cabo Verde Normalzeit | (UTC-01:00) Cabo Verde Is. |
| UTC | (UTC) Koordinierte Weltzeit |
| Westeuropäische Zeit | (UTC+00:00) Dublin, Edinburgh, Lissabon, London |
| Westafrikanische Normalzeit | (UTC+00:00) Monrovia, Reykjavik |
| W. Mitteleuropäische Zeit | (UTC+01:00) Amsterdam, Berlin, Bern, Rom, Stockholm, Wien |
| Mitteleuropäische Zeit | (UTC+01:00) Belgrad, Bratislava, Budapest, Ljubljana, Prag |
| Mitteleuropäische Zeit | (UTC+01:00) Brüssel, Kopenhagen, Madrid, Paris |
| Marokko Normalzeit | (UTC+01:00) Casablanca |
| São Tomé (Normalzeit) | (UTC+01:00) São Tomé |
| Mitteleuropäische Zeit | (UTC+01:00) Sarajewo, Skopje, Warschau, Zagreb |
| W. West-Zentralafrika Normalzeit | (UTC+01:00) West-Zentralafrika |
| Jordanien Normalzeit | (UTC+02:00) Amman |
| Osteuropäische Zeit | (UTC+02:00) Athen, Bukarest |
| Naher Osten Normalzeit | (UTC+02:00) Beirut |
| Ägypten Normalzeit | (UTC+02:00) Kairo |
| E. Osteuropäische Zeit | (UTC+02:00) Chisinau |
| Syrien Normalzeit | (UTC+02:00) Damaskus |
| Westjordanland Normalzeit | (UTC+02:00) Gaza, Hebron |
| Südafrika Normalzeit | (UTC+02:00) Harare, Pretoria |
| Osteuropäische Zeit | (UTC+02:00) Helsinki, Kiew, Riga, Sofia, Tallinn, Wilna |
| Israel Normalzeit | (UTC+02:00) Jerusalem |
| Kaliningrad Normalzeit | (UTC+02:00) Kaliningrad |
| Sudan Normalzeit | (UTC+02:00) Khartum |
| Libyen Normalzeit | (UTC+02:00) Tripolis |
| Namibia Normalzeit | (UTC+02:00) Windhuk |
| Arabische Normalzeit | (UTC+03:00) Bagdad |
| Türkei Normalzeit | (UTC+03:00) Istanbul |
| Arabische Normalzeit | (UTC+03:00) Kuwait, Riad |
| Belarus Normalzeit | (UTC+03:00) Minsk |
| Russische Normalzeit | (UTC+03:00) Moskau, St. Petersburg |
| E. Ostafrikanische Normalzeit | (UTC+03:00) Nairobi |
| Iran Normalzeit | (UTC+03:30) Teheran |
| Arabische Normalzeit | (UTC+04:00) Abu Dhabi, Maskat |
| Astrachan Normalzeit | (UTC+04:00) Astrachan, Uljanowsk |
| Aserbaidschan Normalzeit | (UTC+04:00) Baku |
| Russland Zeitzone 3 | (UTC+04:00) Ischewsk, Samara |
| Mauritius Normalzeit | (UTC+04:00) Port Louis |
| Saratow (Normalzeit) | (UTC+04:00) Saratow |
| Georgische Normalzeit | (UTC+04:00) Tiflis |
| Wolgograd Normalzeit | (UTC+04:00) Wolgograd |
| Kaukasische Normalzeit | (UTC+04:00) Eriwan |
| Afghanistan Normalzeit | (UTC+04:30) Kabul |
| West Asien Normalzeit | (UTC+05:00) Aschgabat, Taschkent |
| Jekaterinburg Normalzeit | (UTC+05:00) Jekaterinburg |
| Pakistan Normalzeit | (UTC+05:00) Islamabad, Karatschi |
| Indien Normalzeit | (UTC+05:30) Chennai, Kolkata, Mumbai, Neu-Delhi |
| Sri Lanka Normalzeit | (UTC+05:30) Sri Jayawardenepura |
| Nepal Normalzeit | (UTC+05:45) Katmandu |
| Zentralasien Normalzeit | (UTC+06:00) Astana |
| Bangladesch Normalzeit | (UTC+06:00) Dhaka |
| Omsk Normalzeit | (UTC+06:00) Omsk |
| Myanmar Normalzeit | (UTC+06:30) Yangon (Rangun) |
| Südostasiatische Normalzeit | (UTC+07:00) Bangkok, Hanoi, Jakarta |
| Altai Normalzeit | (UTC+07:00) Barnaul, Gorno-Altaisk |
| W. W. Mongolei Normalzeit | (UTC+07:00) Hovd |
| Nordasien Normalzeit | (UTC+07:00) Krasnojarsk |
| N. Nord-Zentralasien Normalzeit | (UTC+07:00) Nowosibirsk |
| Tomsk Normalzeit | (UTC+07:00) Tomsk |
| China Normalzeit | (UTC+08:00) Beijing, Chongqing, Hongkong, Ürümqi |
| Ost-Nordasiatische Normalzeit | (UTC+08:00) Irkutsk |
| Singapur Normalzeit | (UTC+08:00) Kuala Lumpur, Singapur |
| W. Westaustralische Normalzeit | (UTC+08:00) Perth |
| Taipeh Normalzeit | (UTC+08:00) Taipeh |
| Ulan-Bator Normalzeit | (UTC+08:00) Ulan-Bator |
| Zentralaustralische Normalzeit | (UTC+08:45) Eucla |
| Transbaikal Normalzeit | (UTC+09:00) Chita |
| Japanische Normalzeit | (UTC+09:00) Osaka, Sapporo, Tokio |
| Nordkorea Normalzeit | (UTC+09:00) Pjöngjang |
| Koreanische Normalzeit | (UTC+09:00) Seoul |
| Jakutsk Normalzeit | (UTC+09:00) Jakutsk |
| Cen. Zentralaustralische Normalzeit | (UTC+09:30) Adelaide |
| Zentralaustralische Normalzeit | (UTC+09:30) Darwin |
| E. Ostaustralische Normalzeit | (UTC+10:00) Brisbane |
| Ostaustralische Normalzeit | (UTC+10:00) Canberra, Melbourne, Sydney |
| Westpazifische Normalzeit | (UTC+10:00) Guam, Port Moresby |
| Tasmanien Normalzeit | (UTC+10:00) Hobart |
| Wladiwostok Normalzeit | (UTC+10:00) Wladiwostok |
| Lord-Howe Normalzeit | (UTC+10:30) Lord-Howe-Insel |
| Bougainville Normalzeit | (UTC+11:00) Bougainville-Insel |
| Russland Zeitzone 10 | (UTC+11:00) Tschokurdach |
| Magadan Normalzeit | (UTC+11:00) Magadan |
| Norfolk Normalzeit | (UTC+11:00) Norfolkinsel |
| Sachalin Normalzeit | (UTC+11:00) Sachalin |
| Zentralpazifische Normalzeit | (UTC+11:00) Salomonen, Neukaledonien |
| Russland Zeitzone 11 | (UTC+12:00) Anadyr, Petropawlowsk-Kamtschatski |
| Neuseeland Normalzeit | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC-12:00) Koordinierte Weltzeit +12 |
| Fidschi Normalzeit | (UTC+12:00) Fidschi |
| Kamtschatka Normalzeit | (UTC+12:00) Petropawlowsk-Kamtschatski – Alt |
| Chatham-Inseln Normalzeit | (UTC+12:45) Chatham-Inseln |
| UTC+13 | (UTC+13:00) Koordinierte Weltzeit +13 |
| Tonga Normalzeit | (UTC+13:00) Nuku'alofa |
| Samoa Normalzeit | (UTC+13:00) Samoa |
| Linieninseln Normalzeit | (UTC+14:00) Kiritimati-Insel |

## <a name="see-also"></a>Weitere Informationen 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
