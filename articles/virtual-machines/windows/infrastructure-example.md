---
title: Exemplarische Vorgehensweise für eine Azure-Beispielinfrastruktur | Microsoft Docs
description: Erfahren Sie mehr über die wichtigsten Entwurfs- und Implementierungsrichtlinien für die Bereitstellung einer Beispielinfrastruktur in Azure.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ff98079c6156783442078546a4783a367863057
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722594"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Exemplarische Vorgehensweise für eine Azure-Beispielinfrastruktur für Windows-VMs
In diesem Artikel wird das Erstellen einer Beispielanwendungsinfrastruktur erläutert. Wir beschreiben das Entwerfen einer Infrastruktur für einen einfachen Onlineshop, wobei alle Richtlinien und Entscheidungen hinsichtlich Namenskonventionen, Verfügbarkeit, virtuelle Netzwerke und Lastenausgleiche relevant sind, und das eigentliche Bereitstellen Ihrer virtuellen Computer (VMs).

## <a name="example-workload"></a>Beispielworkload
Adventure Works Cycles möchte eine Anwendung für einen Onlineshop in Azure erstellen, die aus Folgendem besteht:

* Zwei IIS-Servern, auf denen der Client-Front-End in einer Webebene ausgeführt wird
* Zwei IIS-Servern, auf denen Daten und Aufträge in einer Anwendungsebene verarbeitet werden
* Zwei Microsoft SQL Server-Instanzen mit AlwaysOn-Verfügbarkeitsgruppen (zwei SQL-Server und ein Mehrheitsknotenzeuge) zum Speichern von Produktdaten und -aufträgen in einer Datenbankebene
* Zwei Active Directory-Domänencontroller für Kundenkonten und Lieferanten in einer Authentifizierungsebene
* Alle Server befinden sich in zwei Subnetzen:
  * einem Front-End-Subnetz für die Webserver 
  * einem Back-End-Subnetz für die Anwendungsserver, den SQL-Cluster und die Domänencontroller

![Diagramm der verschiedenen Ebenen für die Anwendungsinfrastruktur](./media/infrastructure-example/example-tiers.png)

Eingehender sicherer Webdatenverkehr erfordert einen Lastenausgleich zwischen den Webservern, wenn die Kunden den Onlineshop durchsuchen. Datenverkehr für die Auftragsverarbeitung in Form von HTTP-Anforderungen der Webserver muss zwischen den Anwendungsservern ausgeglichen werden. Darüber hinaus muss die Infrastruktur für Hochverfügbarkeit entworfen werden.

Der Entwurf muss Folgendes umfassen:

* Ein Azure-Abonnement und -Konto
* Eine einzelne Ressourcengruppe
* Azure Managed Disks
* Ein virtuelles Netzwerk mit zwei Subnetzen
* Verfügbarkeitsgruppen für die virtuellen Computer mit gleichen Rollen
* Virtuelle Computer

Alle oben aufgeführten Elemente werden anhand der folgenden Namenskonventionen benannt:

* Adventure Works Cycles verwendet **[IT-Workload]-[Standort]-[Azure-Ressource]** als Präfix.
  * In diesem Beispiel ist **azos** (Azure-Onlineshop) der Name der IT-Workload und **use** (USA, Osten 2) der Standort.
* Virtuelle Netzwerke folgen der Konvention „AZOS-USE-VN **[Nummer]** “.
* Verfügbarkeitsgruppen folgen der Konvention „azos-use-as- **[Rolle]** “.
* Die Namen der virtuellen Computer folgen der Konvention „azos-use-vm- **[VM-Name]** “.

## <a name="azure-subscriptions-and-accounts"></a>Azure-Abonnements und -Konten
Adventure Works Cycles verwendet das Enterprise-Abonnement mit dem Namen „Adventure Works-Enterprise-Abonnement“ zur Abrechnung dieser IT-Workload.

## <a name="storage"></a>Storage
Adventure Works Cycles bestimmt, dass Azure Managed Disks verwendet werden soll. Beim Erstellen der virtuellen Computer werden beide verfügbaren Speicherebenen verwendet:

* **Storage Standard** für Webserver, Anwendungsserver und Domänencontroller und deren Datenträger
* **Storage Premium** für virtuelle SQL Server-Computer und deren Datenträger

## <a name="virtual-network-and-subnets"></a>Virtuelles Netzwerk und Subnetze
Da das virtuelle Netzwerk keine permanente Verbindung mit dem lokalen Netzwerk von Adventure Work Cycles benötigt, fiel die Entscheidung auf ein virtuelles Netzwerk auf ausschließlicher Cloudbasis.

Sie haben ein virtuelles Netzwerk auf ausschließlicher Cloudbasis mit den folgenden Einstellungen über das Azure-Portal erstellt:

* Name: AZOS-USE-VN01
* Standort: USA (Ost) 2
* Adressraum des virtuellen Netzwerks: 10.0.0.0/8
* Erstes Subnetz:
  * Name: FrontEnd
  * Adressraum: 10.0.1.0/24
* Zweites Subnetz:
  * Name: BackEnd
  * Adressraum: 10.0.2.0/24

## <a name="availability-sets"></a>Verfügbarkeitsgruppen
Um Hochverfügbarkeit für alle vier Ebenen des Onlineshops zu gewährleisten, entschied sich Adventure Works Cycles für vier Verfügbarkeitsgruppen:

* **azos-use-as-web** für die Webserver
* **azos-use-as-app** für die Anwendungsserver
* **azos-use-as-sql** für die SQL-Server
* **azos-use-as-dc** für Domänencontroller

## <a name="virtual-machines"></a>Virtuelle Computer
Adventure Works Cycles hat sich für die folgenden Namen für die virtuellen Azure-Computer entschieden:

* **azos-use-vm-web01** für den ersten Webserver
* **azos-use-vm-web02** für den zweiten Webserver
* **azos-use-vm-app01** für den ersten Anwendungsserver
* **azos-use-vm-app02** für den zweiten Anwendungsserver
* **azos-use-vm-sql01** für den ersten SQL Server im Cluster
* **azos-use-vm-sql02** für den zweiten SQL Server im Cluster
* **azos-use-vm-dc01** für den ersten Domänencontroller
* **azos-use-vm-dc02** für den zweiten Domänencontroller

Die resultierende Konfiguration sieht folgendermaßen aus.

![Endgültige in Azure bereitgestellte Anwendungsinfrastruktur](./media/infrastructure-example/example-config.png)

Diese Konfiguration umfasst:

* Ein virtuelles Netzwerk auf ausschließlicher Cloudbasis mit zwei Subnetzen (Front-End- und Back-End)
* Azure Managed Disks mit Standard- und Premium-Datenträgern
* Vier Verfügbarkeitsgruppen, eine für jede Ebene des Onlineshops
* Die virtuellen Computer für die vier Ebenen
* Eine externe Lastenausgleichsgruppe für HTTPS-basierten Webdatenverkehr aus dem Internet an die Webserver
* Eine interne Lastenausgleichsgruppe für unverschlüsselten Webdatenverkehr von den Webservern an die Anwendungsserver
* Eine einzelne Ressourcengruppe
