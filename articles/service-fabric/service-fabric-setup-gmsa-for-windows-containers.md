---
title: Einrichten gruppenverwalteter Dienstkonten für Azure Service Fabric-Containerdienste | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie gruppenverwaltete Dienstkonten für einen in Azure Service Fabric ausgeführten Container einrichten.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: subramar
ms.openlocfilehash: b4aa08c10cef91b3bf2eef4c87967a74524f6500
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621795"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Einrichten gruppenverwalteter Dienstkonten für in Service Fabric ausgeführte Windows-Container

Zur Einrichtung gruppenverwalteter Dienstkonten (group Managed Service Accounts, gMSAs) wird eine Datei mit Anmeldeinformationen (`credspec`) auf allen Knoten im Cluster platziert. Die Datei kann mithilfe einer VM-Erweiterung auf alle Knoten kopiert werden.  Die Datei vom Typ `credspec` muss die gMSA-Kontoinformationen enthalten. Weitere Informationen zur `credspec`-Datei finden Sie unter [Erstellen einer Anmeldeinformationen-Spezifikation](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec) (Dienstkonten). Die Angabe von Anmeldeinformationen und das Tag `Hostname` werden im Anwendungsmanifest angegeben. Das Tag `Hostname` muss dem gMSA-Kontonamen entsprechen, unter dem der Container ausgeführt wird.  Durch das Tag `Hostname` kann sich der Container gegenüber anderen Diensten in der Domäne mittels Kerberos-Authentifizierung authentifizieren.  Der folgende Codeausschnitt zeigt ein Beispiel für die Angabe von `Hostname` und `credspec` im Anwendungsmanifest:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Lesen Sie als Nächstes die folgenden Artikel:

* [Bereitstellen eines Windows-Containers in Service Fabric unter Windows Server 2016](service-fabric-get-started-containers.md)
* [Bereitstellen eines Docker-Containers in Service Fabric unter Linux](service-fabric-get-started-containers-linux.md)
