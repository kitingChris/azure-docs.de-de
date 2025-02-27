---
title: Konfigurieren einer Verbindungszeichenfolge für Azure Storage
description: Konfigurieren Sie eine Verbindungszeichenfolge für ein Azure Storage-Konto. Eine Verbindungszeichenfolge enthält die erforderlichen Informationen zum Autorisieren des Zugriffs auf ein Speicherkonto aus Ihrer Anwendung zur Laufzeit mithilfe der Autorisierung mit einem gemeinsam verwendeten Schlüssel.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7c83b382f8aca3d8fda1c0de4785c51f3f3b1fc5
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302519"
---
# <a name="configure-azure-storage-connection-strings"></a>Konfigurieren von Azure Storage-Verbindungszeichenfolgen

Eine Verbindungszeichenfolge enthält die erforderlichen Authentifizierungsinformationen für den Zugriff auf Daten in einem Azure Storage-Konto aus Ihrer Anwendung zur Laufzeit mithilfe der Authentifizierung mithilfe der Autorisierung mit einem gemeinsam verwendeten Schlüssel. Sie können Verbindungszeichenfolgen konfigurieren, um Folgendes zu tun:

* Verbinden mit dem Azure-Speicheremulator
* Zugreifen auf ein Speicherkonto in Azure
* Zugreifen auf angegebene Ressourcen in Azure über eine Shared Access Signature (SAS)

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Anzeigen und Kopieren einer Verbindungszeichenfolge

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Speichern einer Verbindungszeichenfolge

Die Anwendung muss zur Laufzeit auf die Verbindungszeichenfolge zugreifen, um bei Azure Storage eingehende Anforderungen zu autorisieren. Es gibt mehrere Möglichkeiten, die Verbindungszeichenfolge zu speichern:

* Sie können die Verbindungszeichenfolge in einer Umgebungsvariable speichern.
* Eine Anwendung, die auf dem Desktop oder auf einem Gerät ausgeführt wird, kann die Verbindungszeichenfolge in der Datei **app.config** oder **web.config** speichern. Fügen Sie die Verbindungszeichenfolge dem Abschnitt **AppSettings** in diesen Dateien hinzu.
* Eine Anwendung, die in einem Clouddienst in Azure ausgeführt wird, kann die Verbindungszeichenfolge zumeist im [Azure-Dienstkonfigurationsschema (CSCFG-Datei)](https://msdn.microsoft.com/library/ee758710.aspx) speichern. Fügen Sie die Verbindungszeichenfolge zum Abschnitt **ConfigurationSettings** der Dienstkonfigurationsdatei hinzu.

Das Speichern der Verbindungszeichenfolge in der Konfigurationsdatei erleichtert die Aktualisierung der Verbindungszeichenfolge, um zwischen dem Speicheremulator und einem Azure-Speicherkonto in der Cloud zu wechseln. Sie müssen die Verbindungszeichenfolge nur so bearbeiten, dass sie auf Ihre Zielumgebung verweist.

Über den [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) können Sie zur Laufzeit auf die Verbindungszeichenfolge zugreifen, unabhängig davon, wo Ihre Anwendung ausgeführt wird.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>Konfigurieren einer Verbindungszeichenfolge für den Speicheremulator

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Weitere Informationen zum Speicheremulator finden Sie unter [Einsatz des Azure-Speicheremulators für Entwicklung und Tests](storage-use-emulator.md).

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Konfigurieren einer Verbindungszeichenfolge für ein Azure-Speicherkonto

Um eine Verbindungszeichenfolge für Ihr Azure-Speicherkonto zu erstellen, verwenden Sie das folgende Format. Geben Sie an, ob Sie über HTTPS (empfohlen) oder HTTP eine Verbindung mit dem Speicherkonto herstellen möchten, ersetzen Sie `myAccountName` durch den Namen Ihres Speicherkontos, und ersetzen Sie `myAccountKey` durch Ihren Kontozugriffsschlüssel:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Die Verbindungszeichenfolge kann beispielsweise wie folgt aussehen:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Obwohl Azure Storage sowohl HTTP als auch HTTPS in einer Verbindungszeichenfolge unterstützt, wird die *Verwendung von HTTPS ausdrücklich empfohlen*.

> [!TIP]
> Sie finden die Verbindungszeichenfolgen Ihres Speicherkontos im [Azure-Portal](https://portal.azure.com). Navigieren Sie auf dem Menüblatt Ihres Speicherkontos zu **EINSTELLUNGEN** > **Zugriffsschlüssel**, um sowohl für den primären als auch sekundären Zugriffsschlüssel Verbindungszeichenfolgen anzuzeigen.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Erstellen einer Verbindungszeichenfolge mit einer Shared Access Signature

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Erstellen einer Verbindungszeichenfolge für einen bestimmten Speicherendpunkt

Sie können die Dienstendpunkte in Ihrer Verbindungszeichenfolge explizit angeben, anstatt die Standardendpunkte zu verwenden. Um eine Verbindungszeichenfolge zu erstellen, die einen expliziten Endpunkt festlegt, geben Sie für jeden Dienst den vollständigen Dienstendpunkt einschließlich der Protokollspezifikation (HTTPS (empfohlen) oder HTTP) im folgenden Format an:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Bei einem möglichen Szenario für die Angabe eines expliziten Endpunkts haben Sie den Endpunkt Ihres Blob Storage einer [benutzerdefinierten Domäne](../blobs/storage-custom-domain-name.md) zugeordnet. In diesem Fall können Sie Ihren benutzerdefinierten Endpunkt für Blob Storage in der Verbindungszeichenfolge angeben. Optional können Sie die Standardendpunkte für die anderen Dienste angeben, wenn Ihre Anwendung sie verwendet.

Es folgt ein Beispiel einer Verbindungszeichenfolge, die einen expliziten Endpunkt für den Blob-Dienst angibt:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

In diesem Beispiel werden explizite Endpunkte für alle Dienste angegeben, einschließlich einer benutzerdefinierten Domäne für den Blob-Dienst:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Die Endpunktwerte in einer Verbindungszeichenfolge werden verwendet, um die Anforderungs-URIs für die Speicherdienste zu erstellen. Ferner geben sie die Form aller URIs vor, die dem Code zurückgegeben werden.

Wenn Sie einen Speicherendpunkt einer benutzerdefinierten Domäne zugeordnet haben, und diesen Endpunkt in einer Verbindungszeichenfolge auslassen, können Sie nicht anhand dieser Verbindungszeichenfolge von Ihrem Code aus auf die Daten in diesem Dienst zugreifen.

> [!IMPORTANT]
> Dienstendpunktwerte in den Verbindungszeichenfolgen müssen wohlgeformte URIs einschließlich `https://` (empfohlen) oder `http://` sein. Da Azure Storage noch nicht HTTPS für benutzerdefinierte Domänen unterstützt, *müssen* Sie `http://` für jeden Endpunkt-URI angeben, der auf eine benutzerdefinierte Domäne verweist.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Erstellen einer Verbindungszeichenfolge mit einem Endpunktsuffix

Zum Erstellen einer Verbindungszeichenfolge für den Speicherdienst in Regionen oder Instanzen mit anderen Endpunktsuffixen, z.B. für Azure China 21Vianet oder Azure Government, verwenden Sie das folgende Format der Verbindungszeichenfolge. Geben Sie an, ob Sie über HTTPS (empfohlen) oder HTTP eine Verbindung mit dem Speicherkonto herstellen möchten, ersetzen Sie `myAccountName` durch den Namen Ihres Speicherkontos, `myAccountKey` durch Ihren Kontozugriffsschlüssel und `mySuffix` durch das URI-Suffix:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Hier sehen Sie das Beispiel einer Verbindungszeichenfolge für Speicherdienste in Azure China 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analysieren einer Verbindungszeichenfolge

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Einsatz des Azure-Speicheremulators für Entwicklung und Tests](storage-use-emulator.md)
* [Azure Storage-Explorer](storage-explorers.md)
* [Verwenden von Shared Access Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md)

