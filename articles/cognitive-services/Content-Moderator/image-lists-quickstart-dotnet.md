---
title: Überprüfen von Bildern anhand benutzerdefinierter Listen in C# – Content Moderator
titlesuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Bilder mit benutzerdefinierten Bildlisten im Content Moderator SDK für C# moderieren.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: 49f87b7cf3d7f46a66c0992edf25968b1dbae073
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603409"
---
# <a name="moderate-with-custom-image-lists-in-c"></a>Moderieren mit benutzerdefinierten Bildlisten in C#

Dieser Artikel enthält Informationen und Codebeispiele, die Ihnen den Einstieg in die Verwendung des [Content Moderator SDK für .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) erleichtern, um Folgendes durchzuführen:
- Erstellen einer benutzerdefinierten Bildliste
- Hinzufügen von Bildern zu und Entfernen von Bildern aus der Liste
- Abrufen der IDs aller Bilder in der Liste
- Abrufen und Aktualisieren der Listenmetadaten
- Aktualisieren des Listensuchindex
- Überprüfen von Bildern anhand von Bildern in der Liste
- Löschen aller Bilder aus der Liste
- Löschen der benutzerdefinierten Liste

> [!NOTE]
> Die Obergrenze liegt bei **fünf Bildlisten**, wobei jede Liste **max. 10.000 Bilder** enthalten kann.

Die Konsolenanwendung für den vorliegenden Leitfaden simuliert einige der Aufgaben, die Sie mit der Bildlisten-API ausführen können.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

## <a name="sign-up-for-content-moderator-services"></a>Registrieren für Content Moderator-Dienste

Um Content Moderator-Dienste über die REST-API oder über das SDK verwenden zu können, benötigen Sie einen API-Abonnementschlüssel. Abonnieren Sie den Content Moderator-Dienst über das [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator), um diesen Schlüssel zu erhalten.

## <a name="create-your-visual-studio-project"></a>Erstellen Ihres Visual Studio-Projekts

1. Fügen Sie Ihrer Projektmappe ein neues Projekt vom Typ **Konsolen-App (.NET Framework)** hinzu.

   Versehen Sie das Projekt im Beispielcode mit dem Namen **ImageLists**.

1. Wählen Sie dieses Projekt als einzelnes Startprojekt für die Projektmappe aus.

### <a name="install-required-packages"></a>Installieren erforderlicher Pakete

Installieren Sie die folgenden NuGet-Pakete:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualisieren der using-Anweisungen des Programms

Fügen Sie die folgenden `using`-Anweisungen hinzu:

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Erstellen des Content Moderator-Clients

Fügen Sie den folgenden Code hinzu, um einen Content Moderator-Client für Ihr Abonnement zu erstellen.

> [!IMPORTANT]
> Aktualisieren Sie die Felder **AzureRegion** und **CMSubscriptionKey** mit den Werten Ihres Regionsbezeichners und des Abonnementschlüssels.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this 
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The region/location for your Content Moderator account, 
    /// for example, westus.
    /// </summary>
    private static readonly string AzureRegion = "YOUR API REGION";

    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```


### <a name="initialize-application-specific-settings"></a>Initialisieren anwendungsspezifischer Einstellungen

Fügen Sie der Klasse **Program** in „Program.cs“ die folgenden Klassen und statischen Felder hinzu.

```csharp
/// <summary>
/// The minimum amount of time, im milliseconds, to wait between calls
/// to the Image List API.
/// </summary>
private const int throttleRate = 3000;

/// <summary>
/// The number of minutes to delay after updating the search index before
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;

/// <summary>
/// Define constants for the labels to apply to the image list.
/// </summary>
private class Labels
{
    public const string Sports = "Sports";
    public const string Swimsuit = "Swimsuit";
}

/// <summary>
/// Define input data for images for this sample.
/// </summary>
private class Images
{
    /// <summary>
    /// Represents a group of images that all share the same label.
    /// </summary>
    public class Data
    {
        /// <summary>
        /// The label for the images.
        /// </summary>
        public string Label;

        /// <summary>
        /// The URLs of the images.
        /// </summary>
        public string[] Urls;
    }

    /// <summary>
    /// The initial set of images to add to the list with the sports label.
    /// </summary>
    public static readonly Data Sports = new Data()
    {
        Label = Labels.Sports,
        Urls = new string[] {
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png",
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png",
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample9.png"
        }
    };

    /// <summary>
    /// The initial set of images to add to the list with the swimsuit label.
    /// </summary>
    /// <remarks>We're adding sample16.png (image of a puppy), to simulate
    /// an improperly added image that we will later remove from the list.
    /// Note: each image can have only one entry in a list, so sample4.png
    /// will throw an exception when we try to add it with a new label.</remarks>
    public static readonly Data Swimsuit = new Data()
    {
        Label = Labels.Swimsuit,
        Urls = new string[] {
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg",
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample3.png",
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png",
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png"
        }
    };

    /// <summary>
    /// The set of images to subsequently remove from the list.
    /// </summary>
    public static readonly string[] Corrections = new string[] {
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png"
    };
}

/// <summary>
/// The images to match against the image list.
/// </summary>
/// <remarks>Samples 1 and 4 should scan as matches; samples 5 and 16 should not.</remarks>
private static readonly string[] ImagesToScreen = new string[] {
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg",
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png",
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png"
};

/// <summary>
/// A dictionary that tracks the ID assigned to each image URL when 
/// the image is added to the list.
/// </summary>
/// <remarks>Indexed by URL.</remarks>
private static readonly Dictionary<string, int> ImageIdMap =
    new Dictionary<string, int>();

/// <summary>
/// The name of the file to contain the output from the list management operations.
/// </summary>
/// <remarks>Relative paths are relative to the execution directory.</remarks>
private static string OutputFile = "ListOutput.log";

/// <summary>
/// A static reference to the text writer to use for logging.
/// </summary>
private static TextWriter writer;

/// <summary>
/// A copy of the list details.
/// </summary>
/// <remarks>Used to initially create the list, and later to update the
/// list details.</remarks>
private static Body listDetails;
```
   
> [!NOTE]
> Die RPS-Rate (Requests Per Second, Anforderungen pro Sekunde) Ihres Content Moderator-Dienstschlüssels ist begrenzt. Bei Überschreitung des Grenzwerts löst das SDK eine Ausnahme mit dem Fehlercode 429 aus. Bei einem Schlüssel des Free-Tarifs ist die Anforderungsrate auf eine einzelne Anforderung pro Sekunde beschränkt.


## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Erstellen einer Methode zum Schreiben von Nachrichten in die Protokolldatei

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. 

```csharp
/// <summary>
/// Writes a message to the log file, and optionally to the console.
/// </summary>
/// <param name="message">The message.</param>
/// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
private static void WriteLine(string message = null, bool echo = false)
{
    writer.WriteLine(message ?? String.Empty);

    if (echo)
    {
        Console.WriteLine(message ?? String.Empty);
    }
}
```

## <a name="create-a-method-to-create-the-custom-list"></a>Erstellen einer Methode zum Erstellen der benutzerdefinierten Liste

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. 

```csharp
/// <summary>
/// Creates the custom list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The response object from the operation.</returns>
private static ImageList CreateCustomList(ContentModeratorClient client)
{
    // Create the request body.
    listDetails = new Body("MyList", "A sample list",
        new BodyMetadata("Acceptable", "Potentially racy"));

    WriteLine($"Creating list {listDetails.Name}.", true);

    var result = client.ListManagementImageLists.Create(
        "application/json", listDetails);
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    return result;
}
```

## <a name="create-a-method-to-add-a-collection-of-images-to-the-list"></a>Erstellen einer Methode zum Hinzufügen einer Sammlung von Bildern zur Liste

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. In diesem Leitfaden wird nicht veranschaulicht, wie Tags auf Bilder in der Liste angewendet werden. 

```csharp
/// <summary>
/// Adds images to an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <param name="imagesToAdd">The images to add.</param>
/// <param name="label">The label to apply to each image.</param>
/// <remarks>Images are assigned content IDs when they are added to the list.
/// Track the content ID assigned to each image.</remarks>
private static void AddImages(
ContentModeratorClient client, int listId,
IEnumerable<string> imagesToAdd, string label)
{
    foreach (var imageUrl in imagesToAdd)
    {
        WriteLine();
        WriteLine($"Adding {imageUrl} to list {listId} with label {label}.", true);
        try
        {
            var result = client.ListManagementImage.AddImageUrlInput(
                listId.ToString(), "application/json", new BodyModel("URL", imageUrl), null, label);

            ImageIdMap.Add(imageUrl, Int32.Parse(result.ContentId));

            WriteLine("Response:");
            WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));
        }
        catch (Exception ex)
        {
            WriteLine($"Unable to add image to list. Caught {ex.GetType().FullName}: {ex.Message}", true);
        }
        finally
        {
            Thread.Sleep(throttleRate);
        }
    }
}
```

## <a name="create-a-method-to-remove-images-from-the-list"></a>Erstellen einer Methode zum Entfernen von Bildern aus der Liste

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. 

```csharp
/// <summary>
/// Removes images from an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <param name="imagesToRemove">The images to remove.</param>
/// <remarks>Images are assigned content IDs when they are added to the list.
/// Use the content ID to remove the image.</remarks>
private static void RemoveImages(
    ContentModeratorClient client, int listId,
    IEnumerable<string> imagesToRemove)
{
    foreach (var imageUrl in imagesToRemove)
    {
        if (!ImageIdMap.ContainsKey(imageUrl)) continue;
        int imageId = ImageIdMap[imageUrl];

        WriteLine();
        WriteLine($"Removing entry for {imageUrl} (ID = {imageId}) from list {listId}.", true);

        var result = client.ListManagementImage.DeleteImage(
            listId.ToString(), imageId.ToString());
        Thread.Sleep(throttleRate);

        ImageIdMap.Remove(imageUrl);

        WriteLine("Response:");
        WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));
    }
}
```

## <a name="create-a-method-to-get-all-of-the-content-ids-for-images-in-the-list"></a>Erstellen einer Methode zum Abrufen aller Inhalts-IDs für Bilder in der Liste

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. 

```csharp
/// <summary>
/// Gets all image IDs in an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <returns>The response object from the operation.</returns>
private static ImageIds GetAllImageIds(
    ContentModeratorClient client, int listId)
{
    WriteLine();
    WriteLine($"Getting all image IDs for list {listId}.", true);

    var result = client.ListManagementImage.GetAllImageIds(listId.ToString());
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    return result;
}
```

## <a name="create-a-method-to-update-the-details-of-the-list"></a>Erstellen einer Methode zum Aktualisieren der Details der Liste

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. 

```csharp
/// <summary>
/// Updates the details of an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <returns>The response object from the operation.</returns>
private static ImageList UpdateListDetails(
    ContentModeratorClient client, int listId)
{
    WriteLine();
    WriteLine($"Updating details for list {listId}.", true);

    listDetails.Name = "Swimsuits and sports";

    var result = client.ListManagementImageLists.Update(
        listId.ToString(), "application/json", listDetails);
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    return result;
}
```

## <a name="create-a-method-to-retrieve-the-details-of-the-list"></a>Erstellen einer Methode zum Abrufen der Details der Liste

Fügen Sie der **Program**-Klasse die folgende Methode hinzu.

```csharp
/// <summary>
/// Gets the details for an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <returns>The response object from the operation.</returns>
private static ImageList GetListDetails(
    ContentModeratorClient client, int listId)
{
    WriteLine();
    WriteLine($"Getting details for list {listId}.", true);

    var result = client.ListManagementImageLists.GetDetails(listId.ToString());
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    return result;
}
```

## <a name="create-a-method-to-refresh-the-search-index-of-the-list"></a>Erstellen einer Methode zum Aktualisieren des Suchindexes der Liste

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. Jedes Mal, wenn Sie eine Liste aktualisieren, müssen Sie den Suchindex aktualisieren, bevor Sie die Liste zum Überprüfen von Bildern verwenden.

```csharp
/// <summary>
/// Refreshes the search index for an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <returns>The response object from the operation.</returns>
private static RefreshIndex RefreshSearchIndex(
    ContentModeratorClient client, int listId)
{
    WriteLine();
    WriteLine($"Refreshing the search index for list {listId}.", true);

    var result = client.ListManagementImageLists.RefreshIndexMethod(listId.ToString());
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    return result;
}
```

## <a name="create-a-method-to-match-images-against-the-list"></a>Erstellen einer Methode zum Abgleichen der Bilder mit der Liste

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. 

```csharp
/// <summary>
/// Matches images against an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <param name="imagesToMatch">The images to screen.</param>
private static void MatchImages(
    ContentModeratorClient client, int listId,
    IEnumerable<string> imagesToMatch)
{
    foreach (var imageUrl in imagesToMatch)
    {
        WriteLine();
        WriteLine($"Matching image {imageUrl} against list {listId}.", true);

        var result = client.ImageModeration.MatchUrlInput(
                "application/json", new BodyModel("URL", imageUrl), listId.ToString());
        Thread.Sleep(throttleRate);

        WriteLine("Response:");
        WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));
    }
}
```

## <a name="create-a-method-to-delete-all-images-from-the-list"></a>Erstellen einer Methode zum Löschen aller Bilder aus der Liste

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. 

```csharp
/// <summary>
/// Deletes all images from an image list.
/// </summary>
/// <param name="client">The Content Modertor client.</param>
/// <param name="listId">The list identifier.</param>
private static void DeleteAllImages(
    ContentModeratorClient client, int listId)
{
    WriteLine();
    WriteLine($"Deleting all images from list {listId}.", true);

    var result = client.ListManagementImage.DeleteAllImages(listId.ToString());
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));
}
```

## <a name="create-a-method-to-delete-the-list"></a>Erstellen einer Methode zum Löschen der Liste

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. 

```csharp
/// <summary>
/// Deletes an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
private static void DeleteCustomList(
    ContentModeratorClient client, int listId)
{
    WriteLine();
    WriteLine($"Deleting list {listId}.", true);

    var result = client.ListManagementImageLists.Delete(listId.ToString());
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));
}
```

## <a name="create-a-method-to-retrieve-ids-for-all-image-lists"></a>Erstellen einer Methode zum Abrufen von IDs für alle Bildlisten

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. 

```csharp
/// <summary>
/// Gets all list identifiers for the client.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The response object from the operation.</returns>
private static IList<ImageList> GetAllListIds(ContentModeratorClient client)
{
    WriteLine();
    WriteLine($"Getting all image list IDs.", true);

    var result = client.ListManagementImageLists.GetAllImageLists();
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    return result;
}
```

## <a name="add-code-to-simulate-the-use-of-an-image-list"></a>Hinzufügen von Code zum Simulieren der Verwendung einer Bildliste

Fügen Sie der Methode **Main** den folgenden Code hinzu. Dieser Code simuliert viele Vorgänge, die Sie zu Definition und Verwaltung der Liste ausführen würden, sowie die Verwendung der Liste zum Überprüfen von Bildern. Mit den Protokollierungsfunktionen können Sie die Antwortobjekte anzeigen, die von den SDK-Aufrufen an den Content Moderator-Dienst generiert werden.

```csharp
// Create the text writer to use for logging, and cache a static reference to it.
using (StreamWriter outputWriter = new StreamWriter(OutputFile))
{
    writer = outputWriter;

    // Create a Content Moderator client.
    using (var client = Clients.NewClient())
    {
        // Create a custom image list and record the ID assigned to it.
        var creationResult = CreateCustomList(client);
        if (creationResult.Id.HasValue)
        {
            // Cache the ID of the new image list.
            int listId = creationResult.Id.Value;

            // Perform various operations using the image list.
            AddImages(client, listId, Images.Sports.Urls, Images.Sports.Label);
            AddImages(client, listId, Images.Swimsuit.Urls, Images.Swimsuit.Label);
                    
            GetAllImageIds(client, listId);
            UpdateListDetails(client, listId);
            GetListDetails(client, listId);

            // Be sure to refresh search index
            RefreshSearchIndex(client, listId);

            // WriteLine();
            WriteLine($"Waiting {latencyDelay} minutes to allow the server time to propagate the index changes.", true);
            Thread.Sleep((int)(latencyDelay * 60 * 1000));

            // Match images against the image list.
            MatchImages(client, listId, ImagesToMatch);

            // Remove images
            RemoveImages(client, listId, Images.Corrections);

            // Be sure to refresh search index
            RefreshSearchIndex(client, listId);

            WriteLine();
            WriteLine($"Waiting {latencyDelay} minutes to allow the server time to propagate the index changes.", true);
            Thread.Sleep((int)(latencyDelay * 60 * 1000));

            // Match images again against the image list. The removed image should not get matched.
            MatchImages(client, listId, ImagesToMatch);

            // Delete all images from the list.
            DeleteAllImages(client, listId);

            // Delete the image list.
            DeleteCustomList(client, listId);

            // Verify that the list was deleted.
            GetAllListIds(client);
            }
        }

        writer.Flush();
        writer.Close();
        writer = null;
}

Console.WriteLine();
Console.WriteLine("Press any key to exit...");
Console.ReadKey();
```

## <a name="run-the-program-and-review-the-output"></a>Ausführen des Programms und Überprüfen der Ausgabe

Die Listen-ID und die Bildinhalt-IDs sind bei jeder Ausführung der Anwendung unterschiedlich.
Die Ausgabe der vom Programm geschriebenen Protokolldatei sieht wie folgt aus:

```json
Creating list MyList.
Response:
{
    "Id": 169642,
    "Name": "MyList",
    "Description": "A sample list",
    "Metadata": {
        "Key One": "Acceptable",
        "Key Two": "Potentially racy"
    }
}

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png to list 169642 with label Sports.
Response:
{
    "ContentId": "169490",
    "AdditionalInfo": [
    {
        "Key": "Source",
        "Value": "169642"
    },
    {
        "Key": "ImageDownloadTimeInMs",
        "Value": "233"
    },
    {
        "Key": "ImageSizeInBytes",
        "Value": "2945548"
    }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_b4d3e20a-0751-4760-8829-475e5da33ce8"
}

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png to list 169642 with label Sports.
Response:
{
    "ContentId": "169491",
    "AdditionalInfo": [
    {
        "Key": "Source",
        "Value": "169642"
    },
    {
        "Key": "ImageDownloadTimeInMs",
        "Value": "215"
    },
    {
        "Key": "ImageSizeInBytes",
        "Value": "2440050"
    }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_cc1eb6af-2463-4e5e-9145-2a11dcecbc30"
}

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample9.png to list 169642 with label Sports.
Response:
{
    "ContentId": "169492",
    "AdditionalInfo": [
    {
        "Key": "Source",
        "Value": "169642"
    },
    {
        "Key": "ImageDownloadTimeInMs",
        "Value": "98"
    },
    {
        "Key": "ImageSizeInBytes",
        "Value": "1631958"
    }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_01edc1f2-b448-48cf-b7f6-23b64d5040e9"
}

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg to list 169642 with label Swimsuit.
Response:
{
    "ContentId": "169493",
    "AdditionalInfo": [
    {
        "Key": "Source",
        "Value": "169642"
    },
    {
        "Key": "ImageDownloadTimeInMs",
        "Value": "27"
    },
    {
        "Key": "ImageSizeInBytes",
        "Value": "17280"
    }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_41f7bc6f-8778-4576-ba46-37b43a6c2434"
}

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample3.png to list 169642 with label Swimsuit.
Response:
{
    "ContentId": "169494",
    "AdditionalInfo": [
    {
        "Key": "Source",
        "Value": "169642"
    },
    {
        "Key": "ImageDownloadTimeInMs",
        "Value": "129"
    },
    {
        "Key": "ImageSizeInBytes",
        "Value": "1242855"
    }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_61a48f33-eb55-4fd9-ac97-20eb0f3622a5"
}

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png to list 169642 with label Swimsuit.
Unable to add image to list. Caught Microsoft.CognitiveServices.ContentModerator.Models.APIErrorException: Operation returned an invalid status code 'Conflict'

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png to list 169642 with label Swimsuit.
Response:
{
    "ContentId": "169495",
    "AdditionalInfo": [
    {
        "Key": "Source",
        "Value": "169642"
    },
    {
        "Key": "ImageDownloadTimeInMs",
        "Value": "65"
    },
    {
        "Key": "ImageSizeInBytes",
        "Value": "1088127"
    }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_1c1f3de4-58b9-4aa8-82fa-1b0f479f6d7c"
}

Getting all image IDs for list 169642.
Response:
{
    "ContentSource": "169642",
    "ContentIds": [
        169490,
        169491,
        169492,
        169493,
        169494,
        169495
],
"Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
    },
"TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_0d017deb-38fa-4701-a7b1-5b6608c79da2"
}

Updating details for list 169642.
Response:
{
    "Id": 169642,
    "Name": "Swimsuits and sports",
    "Description": "A sample list",
    "Metadata": {
        "Key One": "Acceptable",
        "Key Two": "Potentially racy"
    }
}

Getting details for list 169642.
Response:
{
    "Id": 169642,
    "Name": "Swimsuits and sports",
    "Description": "A sample list",
    "Metadata": {
        "Key One": "Acceptable",
        "Key Two": "Potentially racy"
    }
}

Refreshing the search index for list 169642.
Response:
{
    "ContentSourceId": "169642",
    "IsUpdateSuccess": true,
    "AdvancedInfo": [],
    "Status": {
        "Code": 3000,
        "Description": "RefreshIndex successfully completed.",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_c72255cd-55a0-415e-9c18-0b9c08a9f25b"
}
Waiting 0.5 minutes to allow the server time to propagate the index changes.

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_ec384878-dbaa-4999-9042-6ac986355967",
    "CacheID": null,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169493,
            "Source": "169642",
            "Tags": [],
            "Label": "Swimsuit"
        }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_e9db4b8f-3067-400f-9552-d3e6af2474c0",
    "CacheID": null,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        }
}

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_25991575-05da-4904-89db-abe88270b403",
    "CacheID": null,
    "IsMatch": false,
    "Matches": [],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_c65d1c91-0d8a-4511-8ac6-814e04adc845",
    "CacheID": null,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169495,
            "Source": "169642",
            "Tags": [],
            "Label": "Swimsuit"
        }
        ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Removing entry for https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png (ID = 169495) from list 169642.
Response:
""

Refreshing the search index for list 169642.
Response:
{
    "ContentSourceId": "169642",
    "IsUpdateSuccess": true,
    "AdvancedInfo": [],
    "Status": {
        "Code": 3000,
        "Description": "RefreshIndex successfully completed.",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_b55a375e-30a1-4612-aa7b-81edcee5bffb"
}

Waiting 0.5 minutes to allow the server time to propagate the index changes.

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_00544948-2936-489c-98c8-b507b654bff5",
    "CacheID": null,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169493,
            "Source": "169642",
            "Tags": [],
            "Label": "Swimsuit"
        }
        ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_c36ec646-53c2-4705-86b2-d72b5c2273c7",
    "CacheID": null,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
        ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png against list 169642.
Response:
{
    TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_22edad74-690d-4fbc-b7d0-bf64867c4cb9",
    "CacheID": null,
    "IsMatch": false,
    "Matches": [],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_abd4a178-3238-4601-8e4f-cf9ee66f605a",
    "CacheID": null,
    "IsMatch": false,
    "Matches": [],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Deleting all images from list 169642.
Response:
"Reset Successful."

Deleting list 169642.
Response:
""

Getting all image list IDs.
Response:
[]
```

## <a name="next-steps"></a>Nächste Schritte

Rufen Sie das [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) und die [Visual Studio-Projektmappe](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dafür sowie andere Content Moderator-Schnellstarts für .NET ab, und beginnen Sie mit der Integration.
