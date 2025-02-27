---
title: Verbindungsarchitektur von Azure SQL-Datenbank und SQL Data Warehouse| Microsoft-Dokumentation
description: In diesem Artikel wird die Verbindungsarchitektur von Azure SQL für Datenbankverbindungen von Azure aus oder von außerhalb von Azure erläutert.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
manager: craigg
ms.date: 07/02/2019
ms.openlocfilehash: 8441e64981b7157e91a56124a08c0aa02a9b1db0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537930"
---
# <a name="azure-sql-connectivity-architecture"></a>Verbindungsarchitektur von Azure SQL

In diesem Artikel wird die Verbindungsarchitektur von Azure SQL-Datenbank und SQL Data Warehouse erläutert. Zudem wird dargelegt, wie die verschiedenen Komponenten Datenverkehr an Ihre Instanz von Azure SQL leiten. Diese Verbindungskomponenten leiten Netzwerkdatenverkehr mithilfe von Clients, die von innerhalb und von außerhalb von Azure verbunden sind, an Azure SQL-Datenbank und SQL Data Warehouse. Dieser Artikel bietet auch Skriptbeispiele zum Ändern der Verbindungsart und Überlegungen, die beim Ändern der Standardverbindungseinstellungen berücksichtigt werden können.

## <a name="connectivity-architecture"></a>Verbindungsarchitektur

Das folgende Diagramm bietet einen allgemeinen Überblick über die Verbindungsarchitektur von Azure SQL-Datenbank.

![Architekturübersicht](./media/sql-database-connectivity-architecture/connectivity-overview.png)

In den folgenden Schritten wird das Herstellen einer Verbindung mit einer Azure SQL-Datenbank beschrieben:

- Clients stellen eine Verbindung mit dem Gateway her, das über eine öffentliche IP-Adresse verfügt und an Port 1433 lauscht.
- Je nach effektiver Verbindungsrichtlinie leitet das Gateway den Datenverkehr an den richtigen Datenbankcluster um oder fungiert als Proxy.
- Innerhalb des Datenbankclusters wird der Datenverkehr an die entsprechende Azure SQL-Datenbank weitergeleitet.

## <a name="connection-policy"></a>Verbindungsrichtlinie

Die Azure SQL-Datenbank unterstützt diese drei Optionen zum Festlegen der Verbindungsrichtlinie für einen SQL-Datenbank-Server:

- **Umleiten (empfohlen):** Clients stellen Verbindungen direkt mit dem Knoten her, der die Datenbank hostet. Zum Aktivieren der Konnektivität müssen die Clients ausgehende Firewallregeln für alle Azure-IP-Adressen in der Region mithilfe von Netzwerksicherheitsgruppen (NSG) mit [Diensttags](../virtual-network/security-overview.md#service-tags) für Ports 11000 bis 11999 zulassen – nicht nur für IP-Adressen des Azure SQL-Datenbankgateways an Port 1433. Da Pakete direkt an die Datenbank gesendet werden, haben Latenz und Durchsatz die Leistung verbessert.
- **Proxy:** In diesem Modus werden alle Verbindungen über die Azure SQL-Datenbank-Gateways hergestellt. Zum Aktivieren der Konnektivität müssen Clients über ausgehende Firewallregeln verfügen, die nur die IP-Adressen des Azure SQL-Datenbank-Gateways zulassen (i. d. R. zwei IP-Adressen pro Region). Dieser Modus kann je nach Workload höhere Latenzen und geringeren Durchsatz verursachen. Es wird empfohlen, die Verbindungsrichtlinie `Redirect` statt der Verbindungsrichtlinie `Proxy` zu verwenden, um die niedrigste Latenz und den höchsten Durchsatz zu erzielen.
- **Standard:** Diese Verbindungsrichtlinie ist nach dem Erstellen auf allen Servern aktiv, es sei denn, Sie ändern sie explizit in `Proxy` oder `Redirect`. Die effektive Richtlinie hängt davon ab, ob Verbindungen innerhalb von Azure (`Redirect`) oder außerhalb von Azure (`Proxy`) hergestellt werden.

## <a name="connectivity-from-within-azure"></a>Verbindung aus Azure

Wenn Sie eine Verbindung aus Azure herstellen, verfügen Ihre Verbindungen standardmäßig über die Verbindungsrichtlinie `Redirect`. Die Verbindungsrichtlinie `Redirect` bedeutet, dass nach Aufbau der TCP-Sitzung mit Azure SQL-Datenbank die Clientsitzung an den richtigen Datenbankcluster umgeleitet wird. Dabei wird die virtuelle Ziel-IP von der des Gateways für Azure SQL-Datenbank in die des Clusters geändert. Danach fließen alle nachfolgenden Pakete direkt an den Cluster, wobei das Gateway von Azure SQL-Datenbank umgangen wird. Das folgende Diagramm veranschaulicht diesen Datenverkehrfluss.

![Architekturübersicht](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Verbindung von außerhalb von Azure

Wenn Sie von außerhalb von Azure eine Verbindung herstellen, verfügen Ihre Verbindungen standardmäßig über die Verbindungsrichtlinie `Proxy`. Die Richtlinie `Proxy` bedeutet, dass die TCP-Sitzung über das Gateway von Azure SQL-Datenbank hergestellt wird und dass alle nachfolgenden Pakete über das Gateway fließen. Das folgende Diagramm veranschaulicht diesen Datenverkehrfluss.

![Architekturübersicht](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>IP-Adressen vom Gateway von Azure SQL-Datenbank

Die untenstehende Tabelle zeigt die IP-Adressen der Gateways nach Region. Wenn Sie sich mit einer Azure SQL-Datenbank verbinden wollen, müssen Sie den Netzwerkdatenverkehr von und aus **allen** Gateways der Region zulassen.

Im nächsten Schritt fügen wir jeder Region mehr Gateways hinzu und setzen die Gateway in der außer Betrieb genommenen Gateway-IP-Adressenspalte der folgenden Tabelle außer Kraft. Weitere Informationen über diesen Prozess finden Sie im folgenden Artikel: [Azure SQL Database Traffic-Migration auf neuere Gateways](sql-database-gateway-migration.md)


| Name der Region          | Gateway-IP-Adresse | Außer Betrieb gesetztes Gateway </br> IP-Adresse| Hinweise zur Außerbetriebnahme | 
| --- | --- | --- | --- |
| Australien (Osten)       | 13.75.149.87, 40.79.161.1 | | |
| Australien, Südosten | 191.239.192.109, 13.73.109.251 | | |
| Brasilien Süd         | 104.41.11.5        |                 | |
| Kanada, Mitte       | 40.85.224.249      |                 | |
| Kanada, Osten          | 40.86.226.166      |                 | |
| USA (Mitte)           | 13.67.215.62, 52.182.137.15 | 23.99.160.139 | Keine Verbindungen nach dem 1. September 2019 |
| China, Osten 1         | 139.219.130.35     |                 | |
| China, Osten 2         | 40.73.82.1         |                 | |
| China, Norden 1        | 139.219.15.17      |                 | |
| China, Norden 2        | 40.73.50.0         |                 | |
| Asien, Osten            | 191.234.2.139, 52.175.33.150 |       | |
| US, Osten 1            | 40.121.158.30, 40.79.153.12 | 191.238.6.43 | Keine Verbindungen nach dem 1. September 2019 |
| USA (Ost) 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0 | 191.239.224.107    | Keine Verbindungen nach dem 1. September 2019 |
| Frankreich, Mitte       | 40.79.137.0, 40.79.129.1 |           | |
| Deutschland, Mitte      | 51.4.144.100       |                 | |
| Deutschland, Nordosten   | 51.5.144.179       |                 | |
| Indien, Mitte        | 104.211.96.159     |                 | |
| Indien, Süden          | 104.211.224.146    |                 | |
| Indien, Westen           | 104.211.160.80     |                 | |
| Japan, Osten           | 13.78.61.196, 40.79.184.8, 13.78.106.224 | 191.237.240.43 | Keine Verbindungen nach dem 1. September 2019 |
| Japan, Westen           | 104.214.148.156, 40.74.100.192 | 191.238.68.11 | Keine Verbindungen nach dem 1. September 2019 |
| Korea, Mitte        | 52.231.32.42       |                 | |
| Korea, Süden          | 52.231.200.86      |                 | |
| USA Nord Mitte     | 23.96.178.199      | 23.98.55.75     | Keine Verbindungen nach dem 1. September 2019 |
| Nordeuropa         | 40.113.93.91       | 191.235.193.75  | Keine Verbindungen nach dem 1. September 2019 |
| USA Süd Mitte     | 13.66.62.124       | 23.98.162.75    | Keine Verbindungen nach dem 1. September 2019 |
| Südostasien      | 104.43.15.0        | 23.100.117.95   | Keine Verbindungen nach dem 1. September 2019 |
| UK, Süden             | 51.140.184.11      |                 | |
| UK, Westen              | 51.141.8.11        |                 | |
| USA, Westen-Mitte      | 13.78.145.25       |                 | |
| Europa, Westen          | 191.237.232.75, 40.68.37.158 |       | |
| USA, Westen 1            | 23.99.34.75, 104.42.238.205 |        | |
| USA, Westen 2            | 13.66.226.202      |                 | |
|                      |                    |                 | |

## <a name="change-azure-sql-database-connection-policy"></a>Ändern der Verbindungsrichtlinie von Azure SQL-Datenbank

Um die Verbindungsrichtlinie von Azure SQL-Datenbank für einen Azure SQL-Datenbank-Server zu ändern, verwenden Sie die [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

- Wenn Ihre Verbindungsrichtlinie auf `Proxy` festgelegt ist, fließen alle Netzwerkpakete über das Gateway von Azure SQL-Datenbank. Um dies einzustellen, lassen Sie ausgehenden Datenverkehr nur für Gateway-IPs von Azure SQL-Datenbank zu. Die Einstellung `Proxy` hat längere Wartezeiten als die Einstellung `Redirect`.
- Wenn die Einstellung der Verbindungsrichtlinie `Redirect` ist, fließen alle Netzwerkpakete direkt an den Datenbankcluster. Um dies einzustellen, lassen Sie ausgehenden Datenverkehr an mehrere IP-Adressen zu.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skript zum Ändern der Verbindungseinstellungen über PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von der Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch. Das folgende Skript erfordert das [Azure PowerShell-Modul](/powershell/azure/install-az-ps).

Das folgende PowerShell-Skript veranschaulicht, wie Sie die Verbindungsrichtlinie ändern können.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Skript zum Ändern der Verbindungseinstellungen über die Azure CLI

> [!IMPORTANT]
> Dieses Skript erfordert die [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in einer Bash-Shell

> [!IMPORTANT]
> Dieses Skript erfordert die [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Das folgende Skript für die Befehlszeilenschnittstelle veranschaulicht, wie Sie die Verbindungsrichtlinie in einer Bash-Shell ändern.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI aus einer Windows-Eingabeaufforderung

> [!IMPORTANT]
> Dieses Skript erfordert die [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Das folgende CLI-Skript zeigt Ihnen, wie Sie die Verbindungsrichtlinie von einer Windows-Eingabeaufforderung aus ändern können (wenn Azure CLI installiert ist).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Ändern der Verbindungsrichtlinie von Azure SQL-Datenbank für einen Azure SQL-Datenbank-Server finden Sie unter [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Weitere Informationen zum Verbindungsverhalten von Azure SQL-Datenbank für Kunden, die ADO.NET 4.5 oder höher verwenden, finden Sie unter [Andere Ports als 1433 für ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Einen allgemeinen Überblick über die Anwendungsentwicklung finden Sie unter [SQL-Datenbankanwendungsentwicklung – Übersicht](sql-database-develop-overview.md).