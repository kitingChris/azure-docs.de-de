---
title: Erstellen einer .NET-Konsolen-App zum Verwalten von Ressourcen der Azure Cosmos DB-SQL-API
description: Erfahren Sie, wie Sie eine .NET-Konsolen-App zum Verwalten von Azure Cosmos DB-SQL-API-Kontoressourcen erstellen.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: 1687a81952418c0b751fe0b9ec0ec560ae0b2b2b
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297612"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Schnellstart: Erstellen einer .NET-Konsolen-App zum Verwalten von Ressourcen der Azure Cosmos DB-SQL-API

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Erste Schritte mit der Azure Cosmos DB-SQL-API-Clientbibliothek für .NET. Führen Sie die in diesem Dokument aufgeführten Schritte aus, um das .NET-Paket zu installieren, eine App zu erstellen und den Beispielcode für grundlegende CRUD-Vorgänge für die in Azure Cosmos DB gespeicherten Daten zu testen. 

Azure Cosmos DB ist der global verteilte Microsoft-Datenbankdienst mit mehreren Modellen. Mit Azure Cosmos DB können Sie Schlüssel-Werte-, Dokument- und Graphdatenbanken schnell erstellen und abfragen. Verwenden Sie die Azure Cosmos DB-SQL-API-Clientbibliothek für .NET zum Ausführen der folgenden Aufgaben:

* Erstellen einer Azure Cosmos-Datenbank und eines Containers
* Hinzufügen von Beispieldaten zum Container
* Abfragen von Daten 
* Löschen der Datenbank

[API-Referenzdokumentation](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) | [Quellcode der Bibliothek](https://github.com/Azure/azure-cosmos-dotnet-v3) | [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Erstellen Sie ein kostenloses Azure-Konto](https://azure.microsoft.com/free/), oder [testen Sie Azure Cosmos DB kostenlos](https://azure.microsoft.com/try/cosmosdb/) – ohne Azure-Abonnement und unverbindlich. 
* [.NET Core 2.1 SDK oder höher](https://dotnet.microsoft.com/download/dotnet-core/2.1)
* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="setting-up"></a>Einrichten

Dieser Abschnitt führt Sie durch das Erstellen eines Azure Cosmos-Kontos und durch das Einrichten eines Projekts, das die Azure Cosmos DB-SQL-API-Clientbibliothek für .NET zum Verwalten von Ressourcen verwendet. Mit dem in diesem Artikel beschriebenen Beispielcode erstellen Sie eine Datenbank mit dem Namen `FamilyDatabase` und Familienmitglieder (jedes Familienmitglieder ist ein Element) in dieser Datenbank. Jedes Familienmitglied weist Eigenschaften auf, wie z. B. `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. Die Eigenschaft `LastName` wird als Partitionsschlüssel für den Container verwendet. 

### <a id="create-account"></a>Erstellen eines Azure Cosmos-Kontos

Mit dem folgenden Code wird ein Azure Cosmos-Konto mit Sitzungskonsistenz erstellt. Das Konto wird in `South Central US` und `North Central US` repliziert. Wählen Sie die Schaltfläche **Ausprobieren** aus, und fügen Sie den Code ein, um ihn in der Azure Cloud Shell auszuführen. 

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'
accountName='mysqlapicosmosdb' 
databaseName='FamilyDatabase'
containerName='FamilyContainer'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

### <a id="create-dotnet-core-app"></a>Erstellen einer neuen .NET-App

Erstellen Sie eine neue .NET-Anwendung in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE. Führen Sie in einem Konsolenfenster den folgenden „dotnet new“-Befehl aus, um eine neue App mit dem Namen `todo` zu erstellen.

```console
dotnet new console --langVersion 7.1 -n todo
```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

```console
cd todo
dotnet build
```

Die erwartete Ausgabe aus dem Build sollte etwa wie folgt aussehen:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>Installieren des Azure Cosmos DB-Pakets

Installieren Sie im Anwendungsverzeichnis mit dem Befehl „dotnet add package“ die Azure Cosmos DB-Clientbibliothek für .NET Core.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopieren der Anmeldeinformationen für Ihr Azure Cosmos-Konto aus dem Azure-Portal

Die Beispielanwendung muss sich bei Ihrem Azure Cosmos-Konto authentifizieren. Für die Authentifizierung müssen Sie die Anmeldeinformationen für das Azure Cosmos-Konto an die Anwendung übergeben. Führen Sie zum Abrufen der Anmeldeinformationen für Ihr Azure Cosmos-Konto die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zu Ihrem Azure Cosmos-Konto. 

1. Öffnen Sie den Bereich **Schlüssel**, und kopieren Sie den **URI** und den **PRIMÄRSCHLÜSSEL** Ihres Kontos. Den URI- und den Schlüsselwert fügen Sie im nächsten Schritt zu einer Umgebungsvariablen hinzu.

### <a name="set-the-environment-variables"></a>Festlegen der Umgebungsvariablen

Nachdem Sie den **URI** und den **PRIMÄRSCHLÜSSEL** Ihres Kontos kopiert haben, können Sie diese Werte in einer neuen Umgebungsvariablen auf dem lokalen Computer speichern, auf dem die Anwendung ausgeführt wird. Um die Umgebungsvariable festzulegen, öffnen Sie ein Konsolenfenster, und führen Sie den folgenden Befehl aus. Ersetzen Sie unbedingt die Werte `<Your_Azure_Cosmos_account_URI>` und `<Your_Azure_Cosmos_account_PRIMARY_KEY>`.

```console
setx EndpointUrl <Your_Azure_Cosmos_account_URI>
setx PrimaryKey <Your_Azure_Cosmos_account_PRIMARY_KEY>
```

 ## <a id="object-model"></a>Objektmodell

Bevor Sie mit dem Erstellen der Anwendung beginnen, befassen wir uns noch mit der Hierarchie der Ressourcen in Azure Cosmos DB und dem Objektmodell, das verwendet wird, um diese Ressourcen zu erstellen und darauf zuzugreifen. Azure Cosmos DB erstellt Ressourcen in der folgenden Reihenfolge:

* Azure Cosmos-Konto 
* Datenbanken 
* Container 
* Items

Weitere Informationen zur Hierarchie der verschiedenen Entitäten finden Sie im Artikel [Arbeiten mit Datenbanken, Containern und Elementen in Azure Cosmos DB](databases-containers-items.md). Für die Interaktion mit diesen Ressourcen verwenden Sie die folgenden .NET-Klassen:

* [CosmosClient](): Diese Klasse bietet eine clientseitige logische Darstellung für den Azure Cosmos DB-Dienst. Das Clientobjekt wird zum Konfigurieren und Ausführen von Anforderungen für den Dienst verwendet.

* [CreateDatabaseIfNotExistsAsync](): Mit dieser Methode wird eine Datenbankressource als asynchroner Vorgang erstellt (falls nicht vorhanden) oder abgerufen (wenn bereits vorhanden). 

* [CreateContainerIfNotExistsAsync](): Mit dieser Methode wird ein Container als asynchroner Vorgang erstellt (falls er nicht vorhanden ist) oder abgerufen (wenn er bereits vorhanden ist). Sie können den Statuscode in der Antwort überprüfen, um zu ermitteln, ob der Container neu erstellt wurde (201) oder ein vorhandener Container zurückgegeben wurde (200). 
* [CreateItemAsync](): Diese Methode erstellt ein Element im Container. 

* [QueryItemsAsync](): Führt eine Abfrage zum Abrufen der erforderlichen Elemente aus. Eine SQL-Abfrage wird innerhalb dieser Methode übermittelt. 

* [DeleteAsync](): Löscht die angegebene Datenbank aus Ihrem Azure Cosmos-Konto. Die `DeleteAsync`-Methode löscht nur die Datenbank. Das Löschen der `Cosmosclient`-Instanz sollte separat erfolgen (und zwar in der „DeleteDatabaseAndCleanupAsync“-Methode). 

 ## <a id="code-examples"></a>Codebeispiele

Mit dem in diesem Artikel beschriebenen Beispielcode wird in Azure Cosmos DB eine Familiendatenbank („FamilyDatabase“) erstellt. Die Familiendatenbank enthält Familiendetails wie Name, Adresse, Standort, die zugehörigen Eltern, Kinder und Haustiere. Definieren Sie die Eigenschaften eines Familienelements, bevor Sie Ihr Azure Cosmos-Konto mit den Daten auffüllen. Erstellen Sie eine neue Klasse mit dem Namen `Family.cs` auf der Stammebene Ihrer Beispielanwendung, und fügen Sie ihr den folgenden Code hinzu:

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>Hinzufügen der using-Direktiven und Definieren des Clientobjekts

Öffnen Sie im Projektverzeichnis die Datei `Program.cs` im Editor, und fügen Sie die folgenden using-Direktiven am Anfang der Anwendung hinzu:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Fügen Sie der `program.cs file` Code zum Lesen der im vorherigen Schritt festgelegten Umgebungsvariablen hinzu. Definieren Sie die `CosmosClient`-, `Database`- und `Container`-Objekte. Fügen Sie als Nächstes Code zur Main-Methode hinzu, die die `GetStartedDemoAsync`-Methode aufruft, mit der Sie Azure Cosmos-Kontoressourcen verwalten. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

    // The Cosmos client instance
    private CosmosClient cosmosClient;

    // The database we will create
    private Database database;

    // The container we will create.
    private Container container;

    // The name of the database and container we will create
    private string databaseId = "FamilyDatabase";
    private string containerId = "FamilyContainer";

    public static async Task Main(string[] args)
    {
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Erstellen einer Datenbank 

Definieren Sie die `CreateDatabaseAsync`-Methode in der `program.cs`-Klasse. Diese Methode erstellt die `FamilyDatabase`, falls sie noch nicht vorhanden ist. 

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Erstellen eines Containers

Definieren Sie die `CreateContainerAsync`-Methode in der `program.cs`-Klasse. Diese Methode erstellt den `FamilyContainer`, falls er noch nicht vorhanden ist. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Erstellen eines Elements

Erstellen Sie ein Familienelement, indem Sie die `AddItemsToContainerAsync`-Methode mit dem folgenden Code hinzufügen:

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
    Family andersenFamily = new Family
    {
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
           new Parent { FirstName = "Thomas" },
           new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
           new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = false
 };

try
{
    // Read the item to see if it exists. ReadItemAsync will throw an exception if the item does not exist and return status code 404 (Not found).
    ItemResponse<Family> andersenFamilyResponse = await this.container.ReadItemAsync<Family>(andersenFamily.Id, new PartitionKey(andersenFamily.LastName));
    Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
}
catch(CosmosException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
{
    // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
    ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));

    // Note that after creating the item, we can access the body of the item with the Resource property off the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
    Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
}

```

### <a name="query-the-items"></a>Abfragen der Elemente

Nachdem Sie ein Element eingefügt haben, können Sie eine Abfrage zum Abrufen der Details der Familie „Andersen“ ausführen. Im folgenden Codebeispiel wird gezeigt, wie die Abfrage direkt mithilfe der SQL-Abfrage ausgeführt wird. Die SQL-Abfrage zum Abrufen der Details der Familie „Anderson“ lautet `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Definieren Sie die `QueryItemsAsync`-Methode in der `program.cs`-Klasse, und fügen Sie ihr den folgenden Code hinzu:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>Löschen der Datenbank 

Schließlich können Sie die Datenbank löschen, indem Sie die `DeleteDatabaseAndCleanupAsync`-Methode mit dem folgenden Code hinzufügen:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>Ausführen der CRUD-Vorgänge

Nachdem Sie alle erforderlichen Methoden definiert haben, können Sie diese in der `GetStartedDemoAsync`-Methode ausführen. Die `DeleteDatabaseAndCleanupAsync`-Methode ist in diesem Code auskommentiert, weil keine Ressourcen angezeigt werden, wenn diese Methode ausgeführt wird. Sie können die Auskommentierung aufheben, nachdem Sie überprüft haben, ob Ihre Azure Cosmos DB-Ressourcen im Azure-Portal erstellt wurden. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
    await this.ReplaceFamilyItemAsync();
    await this.DeleteFamilyItemAsync();
    //await this.DeleteDatabaseAndCleanupAsync();
}
```

Speichern Sie die Datei `Program.cs`, nachdem Sie alle erforderlichen Methoden hinzugefügt haben. 

## <a name="run-the-code"></a>Ausführen des Codes

Als Nächstes können Sie die Anwendung zum Erstellen der Azure Cosmos DB-Ressourcen erstellen und ausführen. Stellen Sie sicher, dass Sie ein neues Eingabeaufforderungsfenster öffnen und nicht dieselbe Instanz verwenden, mit der Sie die Umgebungsvariablen festgelegt haben. Die Umgebungsvariablen sind nämlich im aktuellen geöffneten Fenster nicht festgelegt. Sie müssen eine neue Eingabeaufforderung öffnen, um die Aktualisierungen anzeigen zu können. 

```console
dotnet build
```

```console
dotnet run
```

Die folgende Ausgabe wird generiert, wenn Sie die Anwendung ausführen. Sie können sich auch beim Azure-Portal anmelden und überprüfen, ob die Ressourcen erstellt wurden:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Sie können überprüfen, ob die Daten erstellt wurden, indem Sie sich beim Azure-Portal anmelden und die erforderlichen Elemente in Ihrem Azure Cosmos-Konto anzeigen. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, können Sie die Azure-Befehlszeilenschnittstelle (Azure CLI) oder Azure PowerShell verwenden, um das Azure Cosmos-Konto und die zugehörige Ressourcengruppe zu entfernen. Der folgende Befehl zeigt, wie die Ressourcengruppe mit der Azure CLI gelöscht wird:

```azurecli
az group delete -g "myResourceGroup" -l "southcentralus"
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie ein Azure Cosmos-Konto erstellen und eine Datenbank und einen Container mithilfe einer .NET Core-App erstellen. Sie können jetzt zusätzliche Daten in Ihr Azure Cosmos-Konto importieren. Die entsprechenden Anweisungen finden Sie im folgenden Artikel. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB (Importieren von Daten in Azure Cosmos DB)](import-data.md)
