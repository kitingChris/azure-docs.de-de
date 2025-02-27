---
title: Bereitstellen des Containerdurchsatzes in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie in Azure Cosmos DB Durchsatz auf der Containerebene bereitstellen.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: f195eaa0f5d22160de8c1e9e2f429073de001828
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986022"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Bereitstellen von Durchsatz für einen Azure Cosmos-Container

In diesem Artikel erfahren Sie, wie Sie Durchsatz für einen Container (Sammlung, Diagramm oder Tabelle) in Azure Cosmos DB bereitstellen. Sie können Durchsatz für einen einzelnen Container oder [für eine Datenbank](how-to-provision-database-throughput.md) bereitstellen und ihn für die in der Datenbank enthaltenen Container freigeben. Der Durchsatz für einen Container kann über das Azure-Portal, über die Azure-Befehlszeilenschnittstelle oder mithilfe der Azure Cosmos DB SDKs bereitgestellt werden.

## <a name="provision-throughput-using-azure-portal"></a>Bereitstellen des Durchsatzes mithilfe des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-sql-api-dotnet.md#create-account), oder wählen Sie ein bereits vorhandenes Azure Cosmos-Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer**, und wählen Sie **Neue Sammlung** aus. Geben Sie anschließend die folgenden Details an:

   * Geben Sie an, ob Sie eine neue Datenbank erstellen oder eine vorhandene Datenbank verwenden.
   * Geben Sie die ID eines Containers (oder einer Tabelle oder eines Diagramms) ein.
   * Geben Sie einen Partitionsschlüsselwert ein (etwa `/userid`).
   * Geben Sie den bereitzustellenden Durchsatz an (etwa 1.000 RUs).
   * Klicken Sie auf **OK**.

![Screenshot des Daten-Explorers mit hervorgehobener Option „Neue Sammlung“](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Bereitstellen des Durchsatzes mithilfe der Azure-Befehlszeilenschnittstelle

```azurecli-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 400
```

## <a name="provision-throughput-using-powershell"></a>Bereitstellen von Durchsatz mithilfe von PowerShell

```azurepowershell-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

Verwenden Sie beim Bereitstellen des Durchsatzes für einen Container in einem Azure Cosmos-Konto, das mit der Azure Cosmos DB-API für MongoDB konfiguriert wurde, `/myShardKey` als Partitionsschlüsselpfad. Verwenden Sie beim Bereitstellen des Durchsatzes für einen Container in einem Azure Cosmos-Konto, das mit der Cassandra-API konfiguriert wurde, `/myPrimaryKey` als Partitionsschlüsselpfad.

## <a name="provision-throughput-by-using-net-sdk"></a>Bereitstellen des Durchsatzes mithilfe des .NET SDK

> [!Note]
> Verwenden Sie die Cosmos SDKs für die SQL-API, um Durchsatz für alle Cosmos DB-APIs (mit Ausnahme der Cassandra-API) bereitzustellen.

### <a id="dotnet-most"></a>SQL-, MongoDB-, Gremlin- und Tabellen-API
### <a name="net-v2-sdk"></a>.Net V2 SDK

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>Cassandra-API

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Durchsatzbereitstellung in Azure Cosmos DB finden Sie in den folgenden Artikeln:

* [Bereitstellen von Durchsatz für eine Datenbank](how-to-provision-database-throughput.md)
* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](request-units.md)
