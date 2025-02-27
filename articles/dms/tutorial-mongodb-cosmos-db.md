---
title: 'Tutorial: Verwenden von Azure Database Migration Service zur Offlinemigration von MongoDB zur Azure Cosmos DB-API für MongoDB | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service eine Offlinemigration von einer lokalen MongoDB-Instanz zur Azure Cosmos DB-API für MongoDB durchführen.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/04/2019
ms.openlocfilehash: 2ff5ebefbe379edda94dcf8ac066027398e2f3f4
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565544"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Tutorial: Offlinemigration von MongoDB zur Azure Cosmos DB-API für MongoDB mit DMS

Sie können Azure Database Migration Service zum Durchführen einer Offlinemigration (einmalig) von Datenbanken aus einer lokalen oder cloudbasierten MongoDB-Instanz zur Azure Cosmos DB-API für MongoDB verwenden.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> * Erstellen einer Instanz von Azure Database Migration Service
> * Erstellen eines Migrationsprojekts mithilfe von Azure Database Migration Service
> * Ausführen der Migration
> * Überwachen der Migration

In diesem Tutorial migrieren Sie ein Dataset in MongoDB, das auf einem virtuellen Azure-Computer gehostet wird, mit Azure Database Migration Service zur Azure Cosmos DB-API für MongoDB. Wenn Sie noch keine MongoDB-Quelle eingerichtet haben, lesen Sie den Artikel [Installieren und Konfigurieren von MongoDB auf einem virtuellen Windows-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* [Führen Sie die Schritte zur Migrationsvorbereitung](../cosmos-db/mongodb-pre-migration.md) aus. Schätzen Sie beispielsweise den Durchsatz, und wählen Sie einen Partitionsschlüssel und die Indizierungsrichtlinie aus.
* [Erstellen einer Azure Cosmos DB-API für das MongoDB-Konto.](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)
* Erstellen Sie ein virtuelles Azure-Netzwerk (VNET) für Azure Database Migration Service, indem Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, das über [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) oder [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Site-to-Site-Konnektivität für Ihre lokalen Quellserver bereitstellt. Weitere Informationen zum Erstellen eines VNET finden Sie in der [Dokumentation zu Virtual Network](https://docs.microsoft.com/azure/virtual-network/) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.

    > [!NOTE]
    > Fügen Sie bei Verwendung von ExpressRoute mit Netzwerkpeering zu Microsoft während des VNET-Setups die folgenden [Dienstendpunkte](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) zu dem Subnetz hinzu, in dem der Dienst bereitgestellt werden soll:
    >
    > * Zieldatenbankendpunkt (z. B. SQL-Endpunkt, Cosmos DB-Endpunkt usw.)
    > * Speicherendpunkt
    > * Service Bus-Endpunkt
    >
    > Diese Konfiguration ist erforderlich, weil Azure Database Migration Service über keine Internetverbindung verfügt.

* Stellen Sie sicher, dass die Netzwerksicherheitsgruppen-Regeln des VNET nicht die folgenden Kommunikationsports blockieren: 53, 443, 445, 9354 und 10000-20000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem Azure-VNET finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf den MongoDB-Quellserver zugreifen kann (standardmäßig TCP-Port 27017).
* Wenn Sie eine Firewallappliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, um Azure Database Migration Service den Zugriff auf die Quelldatenbanken für die Migration zu ermöglichen.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrieren des Ressourcenanbieters „Microsoft.DataMigration“

1. Melden Sie sich beim Azure-Portal an, und klicken Sie auf **Alle Dienste** und anschließend auf **Abonnements**.

   ![Abonnements im Portal anzeigen](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie auf **Ressourcenanbieter**.

    ![Ressourcenanbieter anzeigen](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Suchen Sie nach „Migration“, und wählen Sie rechts neben **Microsoft.DataMigration** die Option **Registrieren** aus.

    ![Registrieren des Ressourcenanbieters](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Erstellen einer Instanz

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus der Dropdownliste aus.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. Geben Sie auf dem Bildschirm **Migrationsdienst erstellen** einen Namen für den Dienst, das Abonnement und eine neue oder vorhandene Ressourcengruppe an.

4. Wählen Sie den Standort aus, an dem Sie die Azure Database Migration Service-Instanz erstellen möchten. 

5. Wählen Sie ein vorhandenes VNet aus, oder erstellen Sie ein neues VNet.

    Das VNET erteilt Azure Database Migration Service Zugriff auf die MongoDB-Quellinstanz und auf das Azure Cosmos DB-Zielkonto.

    Weitere Informationen zum Erstellen eines VNet im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](https://aka.ms/DMSVnet).

6. Wählen Sie einen Tarif.

    Weitere Informationen zu Kosten und Tarifen finden Sie in der [Preisübersicht](https://aka.ms/dms-pricing).

    ![Konfigurieren der Einstellungen einer Azure Database Migration Service-Instanz](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Wählen Sie **Erstellen**, um den Dienst zu erstellen.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem der Dienst erstellt wurde, suchen Sie diesen im Azure-Portal, öffnen Sie ihn, und erstellen Sie anschließend ein neues Migrationsprojekt.

1. Wählen Sie im Azure-Portal **Alle Dienste**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus.

      ![Suchen aller Instanzen von Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Suchen Sie auf dem Bildschirm **Azure Database Migration Services** nach dem Namen der von Ihnen erstellten Azure Database Migration Service-Instanz, und wählen Sie die Instanz aus.

3. Wählen Sie **+ Neues Migrationsprojekt** aus.

4. Geben Sie im Bildschirm **Neues Migrationsprojekt** einen Projektnamen ein, und wählen Sie im Textfeld **Quellservertyp** die Option **MongoDB** und im Textfeld **Zielservertyp** die Option **CosmosDB (MongoDB-API)** aus. Wählen Sie außerdem für **Aktivitätstyp auswählen** die Option **Offlinedatenmigration** aus. 

    ![Erstellen eines Database Migration Service-Projekts](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Klicken Sie auf **Aktivität erstellen und ausführen**, um das Projekt zu erstellen und die Migrationsaktivität auszuführen.

## <a name="specify-source-details"></a>Angeben von Quelldetails

1. Geben Sie im Bildschirm **Source details** (Quelldetails) die Verbindungsdetails für die MongoDB-Server-Quellinstanz an.

    Es gibt drei Modi, um eine Verbindung mit einer Quelle herzustellen:
   * **Standardmodus**, in dem ein vollqualifizierter Domänenname oder eine IP-Adresse, eine Portnummer und die Anmeldeinformationen für die Verbindung akzeptiert werden.
   * **Verbindungszeichenfolgen-Modus**, in dem eine MongoDB-Verbindungszeichenfolge akzeptiert wird. Dies ist im Artikel [Connection String URI Format](https://docs.mongodb.com/manual/reference/connection-string/) (URI-Format von Verbindungszeichenfolgen) beschrieben.
   * **Daten aus Azure-Speicher**, wobei eine Blobcontainer-SAS-URL akzeptiert wird. Aktivieren Sie die Option **Das Blob enthält BSON-Speicherabbilder**, wenn für den Blobcontainer vom MongoDB-Tool [bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) erstellte BSON-Speicherabbilder vorhanden sind. Deaktivieren Sie sie, wenn der Container JSON-Dateien enthält.

     Bei Verwendung dieser Option muss die Verbindungszeichenfolge für das Speicherkonto folgendes Format haben:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Beachten Sie außerdem den folgenden Aspekt (abhängig von der Art der Sicherungsinformationen in Azure Storage):

     * Bei BSON-Sicherungen müssen die Daten im Blobcontainer im bsondump-Format vorliegen, sodass Datendateien in Ordnern platziert werden, die nach den enthaltenden Datenbanken im Format „sammlung.bson“ benannt sind. Gegebenenfalls vorhandene Metadatendateien müssen im Format „*sammlung*.metadata.json“ benannt werden.

     * Bei JSON-Sicherungen müssen die Dateien im Blobcontainer in Ordnern platziert werden, die nach den enthaltenden Datenbanken benannt sind. Innerhalb der einzelnen Datenbankordner müssen Datendateien in einem Unterordner namens „data“ platziert und im Format „*sammlung*.json“ benannt werden. Gegebenenfalls vorhandene Metadaten müssen in einem Unterordner namens „metadata“ platziert und ebenfalls im Format „*sammlung*.json“ benannt werden. Die Metadatendateien müssen in dem Format vorliegen, das vom MongoDB-Tool „bsondump“ generiert wird.

    > [!IMPORTANT]
    > Es wird nicht empfohlen, ein selbstsigniertes Zertifikat auf dem Mongo-Server zu verwenden. Wenn dennoch eines verwendet wird, stellen Sie die Verbindung mit dem Server im **Verbindungszeichenfolgen-Modus** her, und stellen Sie sicher, dass Ihre Verbindungszeichenfolge in Anführungszeichen eingeschlossen ist.
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Sie können auch die IP-Adresse für Situationen verwenden, in denen DNS-Namensauflösung nicht möglich ist.

   ![Angeben von Quelldetails](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Wählen Sie **Speichern** aus.

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Geben Sie auf dem Bildschirm **Zieldetails für die Migration** die Verbindungsdetails für das Azure Cosmos DB-Zielkonto an (also für die vorab bereitgestellte Azure Cosmos DB-API für das MongoDB-Konto, zu dem Sie die MongoDB-Daten migrieren).

    ![Angeben von Zieldetails](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Wählen Sie **Speichern** aus.

## <a name="map-to-target-databases"></a>Zuordnen zu Zieldatenbanken

1. Ordnen Sie im Bildschirm **Map to target databases** (Zu Zieldatenbanken zuordnen) die Quell- und die Zieldatenbank für die Migration einander zu.

    Wenn die Zieldatenbank denselben Datenbanknamen wie die Quelldatenbank enthält, wählt Azure Database Migration Service die Zieldatenbank standardmäßig aus.

    Wenn die Zeichenfolge **Erstellen** neben dem Datenbanknamen angezeigt wird, hat Azure Database Migration Service die Zieldatenbank nicht gefunden, und der Dienst erstellt die Datenbank für Sie.

    An diesem Punkt der Migration können Sie [Durchsatz bereitstellen](https://docs.microsoft.com/azure/cosmos-db/set-throughput). In Cosmos DB können Sie den Durchsatz entweder auf Datenbankebene oder für jede Sammlung individuell bereitstellen. Der Durchsatz wird in [Anforderungseinheiten](https://docs.microsoft.com/azure/cosmos-db/request-units) (RUs) gemessen. Weitere Informationen: [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Zuordnen zu Zieldatenbanken](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Wählen Sie **Speichern** aus.
3. Erweitern Sie auf dem Einstellungsbildschirm **Sammlung** die Auflistung der Sammlungen, und überprüfen Sie dann die Liste der Sammlungen, die migriert werden.

    Azure Database Migration Service wählt automatisch alle Sammlungen aus, die in der MongoDB-Quellinstanz vorhanden sind, jedoch nicht im Azure Cosmos DB-Zielkonto. Wenn Sie Sammlungen, die bereits Daten enthalten, erneut migrieren möchten, müssen Sie die Sammlungen auf diesem Blatt explizit auswählen.

    Sie können die Anzahl an RUs angeben, die die Sammlungen verwenden sollen. Azure Database Migration Service schlägt intelligente Standardwerte basierend auf der Sammlungsgröße vor.

    > [!NOTE]
    > Führen Sie die Datenbankmigration und -sammlung parallel und ggf. unter Verwendung mehrerer Azure Database Migration Service-Instanzen aus, um die Ausführung zu beschleunigen.

    Sie können auch einen Shardschlüssel angeben, um die [Partitionierung in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) für eine optimale Skalierbarkeit zu nutzen. Überprüfen Sie unbedingt die [bewährten Methoden für die Auswahl eines Shard-/Partitionsschlüssels](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Auswählen von Sammlungstabellen](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Wählen Sie **Speichern** aus.

5. Geben Sie auf dem Bildschirm **Migrationszusammenfassung** im Textfeld **Aktivitätsname** einen Namen für die Migrationsaktivität an.

    ![Migrationszusammenfassung](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Ausführen der Migration

* Wählen Sie **Migration ausführen** aus.

    Das Fenster „Migrationsaktivität“ wird angezeigt, und der **Status** der Aktivität lautet **Nicht gestartet**.

    ![Aktivitätsstatus](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Überwachen der Migration

* Klicken Sie auf dem Bildschirm „Migrationsaktivität“ auf **Aktualisieren**, um die Anzeige zu aktualisieren, bis der **Status** der Migration **Abgeschlossen** lautet.

   > [!NOTE]
   > Sie können die Aktivität zum Abrufen von Details der Migrationsmetriken auf Datenbank- und Sammlungsebene auswählen.

    ![Aktivitätsstatus: Abgeschlossen](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Überprüfen von Daten in Cosmos DB

* Nachdem die Migration abgeschlossen ist, können Sie Ihr Azure Cosmos DB-Konto überprüfen, um sicherzustellen, dass alle Sammlungen erfolgreich migriert wurden.

    ![Aktivitätsstatus: Abgeschlossen](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optimierung nach der Migration

Nach der Migration der in einer MongoDB-Datenbank gespeicherten Daten zur Azure Cosmos DB-API für MongoDB können Sie eine Verbindung mit Azure Cosmos DB herstellen und die Daten verwalten. Sie können nach der Migration auch andere Optimierungsschritte ausführen und beispielsweise die Indizierungsrichtlinie optimieren, die Standardkonsistenzebene aktualisieren oder die globale Verteilung für Ihr Azure Cosmos DB-Konto konfigurieren. Weitere Informationen finden Sie im Artikel zur [Optimierung nach der Migration](../cosmos-db/mongodb-post-migration.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Cosmos DB-Dienstinformationen](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich in der Migrationsanleitung im [Leitfaden zur Datenbankmigration von Microsoft](https://datamigration.microsoft.com/) über weitere Szenarios.
