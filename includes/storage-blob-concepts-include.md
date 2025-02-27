---
title: include file
description: include file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8d1f9ca5a964787f0ee68e6d99fe870cd7b5994b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673348"
---
Azure Blob Storage ist die Objektspeicherlösung von Microsoft für die Cloud. Blobspeicher ist für die Speicherung großer Mengen unstrukturierter Daten optimiert. Unstrukturierte Daten sind Daten, die keinem bestimmten Datenmodell und keiner bestimmten Definition entsprechen (also beispielsweise Text- oder Binärdaten).

## <a name="about-blob-storage"></a>Informationen zu Azure-Blobspeicher

Blobspeicher ist für Folgendes konzipiert:

* Speichern von Bildern oder Dokumenten direkt für einen Browser
* Speichern von Dateien für verteilten Zugriff
* Video- und Audio-Streaming
* Schreiben in Protokolldateien
* Speichern von Daten für Sicherung und Wiederherstellung, Notfallwiederherstellung und Archivierung
* Speichern von Daten für Analysen durch einen lokalen oder von Azure gehosteten Dienst

Benutzer- oder Clientanwendungen können von überall auf der Welt über HTTP/HTTPS auf Objekte in Blobspeicher zugreifen. Auf Objekte in Blobspeicher kann über die [Azure Storage-REST-API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), über [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), über die [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/storage) oder über eine Azure Storage-Clientbibliothek zugegriffen werden. Clientbibliotheken sind für verschiedene Sprachen verfügbar. Hierzu zählen etwa [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [Go](https://github.com/azure/azure-storage-blob-go/), [PHP](https://azure.github.io/azure-storage-php/) und [Ruby](https://azure.github.io/azure-storage-ruby).

## <a name="about-azure-data-lake-storage-gen2"></a>Informationen zu Azure Data Lake Storage Gen2

Blobspeicher unterstützt Azure Data Lake Storage Gen2, die cloudbasierte Big Data-Analyselösung von Microsoft für Unternehmen. Azure Data Lake Storage Gen2 bietet ein hierarchisches Dateisystem sowie die Vorteile von Blobspeicher. Hierzu zählen unter anderem niedrige Kosten, Tiered Storage, Hochverfügbarkeit, hohe Konsistenz und Notfallwiederherstellungsfunktionen.

Weitere Informationen zu Data Lake Storage Gen2 finden Sie in der [Einführung in Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
