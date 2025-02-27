---
title: 'Aktivieren von Azure Monitor für VMs (Vorschauversion): Übersicht | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie eine Azure Monitor für VMs bereitstellen und konfigurieren. Informieren Sie sich über die Systemanforderungen.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: magoedte
ms.openlocfilehash: 6fb7e6b9611b28dab856209aaf03aa93c25d3968
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478047"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Aktivieren von Azure Monitor für VMs (Vorschauversion): Übersicht

Dieser Artikel bietet eine Übersicht über die verfügbaren Optionen zum Einrichten von Azure Monitor für VMs. Verwenden Sie Azure Monitor für VMs, um die Integrität und Leistung zu überwachen. Erkennen Sie Anwendungsabhängigkeiten, die für virtuelle Azure-Computer (VMs) und VM-Skalierungsgruppen, lokale virtuelle Computer oder virtuelle Computer in einer anderen Cloudumgebung ausgeführt werden.  

So richten Sie Azure Monitor für VMs ein

* Aktivieren Sie einen einzelnen virtuellen Azure-Computer bzw. eine VM-Skalierungsgruppe, indem Sie direkt auf der VM oder in der VM-Skalierungsgruppe die Option **Insights (Vorschau)** wählen.
* Aktivieren Sie zwei oder mehr Azure-VMs und VM-Skalierungsgruppen, indem Sie Azure Policy verwenden. Diese Methode stellt sicher, dass bei bestehenden und neuen virtuellen Computern und Skalierungsgruppen die erforderlichen Abhängigkeiten installiert und ordnungsgemäß konfiguriert werden. Nicht konforme VMs und Skalierungsgruppen werden gemeldet, sodass Sie entscheiden können, ob Sie sie aktivieren und den Zustand korrigieren möchten.
* Aktivieren Sie mindestens zwei virtuelle Azure-Computer oder VM-Skalierungsgruppen über ein bestimmtes Abonnement oder eine bestimmte Ressourcengruppe mithilfe von PowerShell.
* Aktivieren Sie Azure Monitor für VMs zum Überwachen von virtuellen Computern oder physischen Computern, die in Ihrem Unternehmensnetzwerk oder einer anderen Cloudumgebung gehostet werden.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass Sie die Informationen in den folgenden Abschnitten verstanden haben.

### <a name="log-analytics"></a>Log Analytics

Azure Monitor für VMs unterstützt einen Log Analytics-Arbeitsbereich in den folgenden Regionen:

- USA, Westen-Mitte
- USA, Westen 2<sup>1</sup>
- East US
- Kanada, Mitte
- UK, Süden
- Europa, Westen
- Asien, Südosten

<sup>1</sup> Diese Region unterstützt derzeit das Integritätsfeature von Azure Monitor für VMs nicht.

>[!NOTE]
>Sie können virtuelle Azure-Computer aus beliebigen Regionen bereitstellen. Diese virtuellen Computer sind nicht auf die vom Log Analytics-Arbeitsbereich unterstützten Bereiche beschränkt.
>

Wenn Sie über keinen Arbeitsbereich verfügen, können Sie einen erstellen, indem Sie eine der folgenden Ressourcen verwenden:
* [Die Azure-CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure-Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Sie können auch einen Arbeitsbereich erstellen, während Sie im Azure-Portal die Überwachung für einen einzelnen virtuellen Azure-Computer oder eine VM-Skalierungsgruppe aktivieren.

So richten Sie in Ihrem Log Analytics-Arbeitsbereich ein bedarfsorientiertes Szenario ein, das Vorlagen für Azure Policy, Azure PowerShell oder Azure Resource Manager verwendet

* Installieren Sie die Projektmappen „ServiceMap“ und „InfrastructureInsights“. Sie können diese Installation mithilfe einer bereitgestellten Azure Resource Manager-Vorlage abschließen. Oder wählen Sie auf der Registerkarte **Erste Schritte** die Option **Arbeitsbereich konfigurieren** aus.
* Konfigurieren Sie den Log Analytics-Arbeitsbereich zur Erfassung von Leistungsindikatoren.

Verwenden Sie zum Konfigurieren Ihres Arbeitsbereichs für das bedarfsorientierte Szenario eine der folgenden Methoden:

* Mithilfe von [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)
* Wählen Sie auf der Seite zur [**Richtlinienabdeckung**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) von Azure Monitor für VMs die Option **Arbeitsbereich konfigurieren** aus. 

### <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Die folgende Tabelle enthält die Windows- und Linux-Betriebssysteme, die von Azure Monitor für VMs unterstützt werden. Eine vollständige Liste mit ausführlicheren Informationen zu den größeren und kleineren Release- und unterstützen Kernelversionen des Linux-Betriebssystems ist weiter unten in diesem Abschnitt angegeben.

|Betriebssystemversion |Leistung |Karten |Health |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> Das Leistungsfeature von Azure Monitor für VMs ist nur über Azure Monitor verfügbar. Es ist nicht direkt über den linken Bereich des virtuellen Azure-Computers verfügbar.

>[!NOTE]
>Das Integritätsfeature von Azure Monitor für VMs unterstützt keine [geschachtelte Virtualisierung](../../virtual-machines/windows/nested-virtualization.md) in einem virtuellen Azure-Computer.
>

>[!NOTE]
>Für das Linux-Betriebssystem gilt Folgendes:
> - Es werden nur die Standardversion und SMP-Version des Linux-Kernels unterstützt.
> - Nicht-Standardversionen des Kernels, z. B. PAE (Physical Address Extension) und Xen, werden für keine Linux-Distribution unterstützt. Beispielsweise wird ein System mit der Versionszeichenfolge *2.6.16.21-0.8-xen* nicht unterstützt.
> - Benutzerdefinierte Kernels, einschließlich Neukompilierungen von Standardkernels, werden nicht unterstützt.
> - Der CentOSPlus-Kernel wird unterstützt.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Betriebssystemversion | Kernelversion |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7,5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Betriebssystemversion | Kernelversion |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="centosplus"></a>CentOSPlus
| Betriebssystemversion | Kernelversion |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Betriebssystemversion | Kernelversion |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | Kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Betriebssystemversion | Kernelversion
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |
|12 SP4 | 4.4.* |
|12 SP4 | Optimierte Azure-Kernel |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency-Agent

Das Zuordnungsfeature in Azure Monitor für VMs erhält seine Daten vom Microsoft Dependency-Agent. Der Dependency-Agent baut auf dem Log Analytics-Agent auf und ist auf dessen Verbindung mit Log Analytics angewiesen. Daher muss für Ihr System der Log Analytics-Agent installiert und mit dem Dependency-Agent konfiguriert sein.

Wenn Sie Azure Monitor für VMs für eine einzelne Azure-VM aktivieren oder die Methoden für die bedarfsorientierte Bereitstellung verwenden, installieren Sie den Agent mit der Azure-VM-Erweiterung für den Depency-Agent im Rahmen der Erfahrung.

In einer Hybridumgebung können Sie den Dependency-Agent manuell herunterladen und installieren. Wenn Ihre virtuellen Computer außerhalb von Azure gehostet werden, verwenden Sie eine automatisierte Bereitstellungsmethode.

In der folgenden Tabelle sind die verbundenen Quellen beschrieben, die vom Zuordnungsfeature in einer Hybridumgebung unterstützt werden.

| Verbundene Quelle | Unterstützt | BESCHREIBUNG |
|:--|:--|:--|
| Windows-Agents | Ja | Zusätzlich zum [Log Analytics-Agent für Windows](../../azure-monitor/platform/log-analytics-agent.md) benötigen Windows-Agents den Dependency-Agent. Weitere Informationen finden Sie unter [Unterstützte Betriebssysteme](#supported-operating-systems). |
| Linux-Agents | Ja | Zusätzlich zum [Log Analytics-Agent für Linux](../../azure-monitor/platform/log-analytics-agent.md) benötigen Linux-Agents den Dependency-Agent. Weitere Informationen finden Sie unter [Unterstützte Betriebssysteme](#supported-operating-systems). |
| System Center Operations Manager-Verwaltungsgruppe | Nein | |

Sie können den Dependency-Agent über die folgenden Speicherorte herunterladen:

| Datei | OS | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Um die Features in Azure Monitor für VMs zu aktivieren und darauf zuzugreifen, müssen Sie über die Rolle *Log Analytics-Mitwirkender* verfügen. Sie müssen über die Rolle *Überwachungsleser* für den virtuellen Azure-Computer verfügen, um Leistungs-, Integritäts- und Zuordnungsdaten anzeigen zu können. Der Log Analytics-Arbeitsbereich muss für Azure Monitor für VMs konfiguriert sein.

Weitere Informationen zur Zugriffssteuerung auf einen Log Analytics-Arbeitsbereich finden Sie unter [Verwalten von Arbeitsbereichen](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Gewusst wie: Aktivieren von Azure Monitor für VMs (Vorschauversion)

Aktivieren Sie Azure Monitor für VMs, indem Sie eine der in dieser Tabelle beschriebenen Methoden verwenden:

| Bereitstellungszustand | Methode | BESCHREIBUNG |
|------------------|--------|-------------|
| Einzelne Azure-VM oder VM-Skalierungsgruppe | [Über die VM aktivieren](vminsights-enable-single-vm.md) | Sie können einen einzelnen virtuellen Azure-Computer aktivieren, indem Sie direkt auf der VM oder in der VM-Skalierungsgruppe die Option **Insights (Vorschau)** wählen. |
| Mehrere Azure-VMs oder VM-Skalierungsgruppen | [Über Azure Policy aktivieren](vminsights-enable-at-scale-policy.md) | Sie können mehrere Azure-VMs aktivieren, indem Sie Azure Policy und die verfügbaren Richtliniendefinitionen verwenden. |
| Mehrere Azure-VMs oder VM-Skalierungsgruppen | [Über Azure PowerShell- oder Azure Resource Manager-Vorlagen aktivieren](vminsights-enable-at-scale-powershell.md) | Sie können mehrere Azure-VMs oder VM-Skalierungsgruppen übergreifend für ein angegebenes Abonnement oder eine Ressourcengruppe aktivieren, indem Sie Azure PowerShell- oder Azure Resource Manager-Vorlagen verwenden. |
| Hybrid Cloud | [Für Hybridumgebungen aktivieren](vminsights-enable-hybrid-cloud.md) | Sie können die Bereitstellung auf virtuellen oder physischen Computern durchführen, die in Ihrem Rechenzentrum oder in anderen Cloudumgebungen gehostet werden. |

## <a name="performance-counters-enabled"></a>Aktivierte Leistungsindikatoren 

Azure Monitor für VMs konfiguriert einen Log Analytics-Arbeitsbereich, um die verwendeten Leistungsindikatoren zu sammeln. In der folgenden Tabelle sind die Objekte und Indikatoren aufgelistet, die alle 60 Sekunden erfasst werden.

### <a name="windows-performance-counters"></a>Windows-Leistungsindikatoren

|Objektname |Name des Leistungsindikators |
|------------|-------------|
|Logischer Datenträger |% freier Speicher |
|Logischer Datenträger |Durchschn. Datenträger s/gelesen |
|Logischer Datenträger |Durchschn. Datenträger s/übertragen |
|Logischer Datenträger |Durchschn. Datenträger s/geschrieben |
|Logischer Datenträger |Datenträger Bytes/s |
|Logischer Datenträger |Byte gelesen/s |
|Logischer Datenträger |Lesevorgänge/s |
|Logischer Datenträger |Übertragungen/s |
|Logischer Datenträger |Byte geschrieben/s |
|Logischer Datenträger |Schreibvorgänge/s |
|Logischer Datenträger |Freie Megabytes |
|Arbeitsspeicher |Verfügbare MB |
|Netzwerkadapter |Empfangene Byte/Sek. |
|Netzwerkadapter |Gesendete Byte/Sek. |
|Prozessor |% Prozessorzeit |

### <a name="linux-performance-counters"></a>Leistungsindikatoren von Linux

|Objektname |Name des Leistungsindikators |
|------------|-------------|
|Logischer Datenträger |% verwendeter Speicher |
|Logischer Datenträger |Byte gelesen/s |
|Logischer Datenträger |Lesevorgänge/s |
|Logischer Datenträger |Übertragungen/s |
|Logischer Datenträger |Byte geschrieben/s |
|Logischer Datenträger |Schreibvorgänge/s |
|Logischer Datenträger |Freie Megabytes |
|Logischer Datenträger |Logischer Datenträger Bytes/s |
|Arbeitsspeicher |Verfügbarer Speicher in MB |
|Netzwerk |Summe empfangene Bytes |
|Netzwerk |Summe übertragene Bytes |
|Prozessor |% Prozessorzeit |

## <a name="diagnostic-and-usage-data"></a>Diagnose- und Nutzungsdaten

Wenn Sie den Azure Monitor-Dienst verwenden, sammelt Microsoft automatisch Nutzungs- und Leistungsdaten. Microsoft verwendet diese Daten, um die Qualität, Sicherheit und Integrität des Diensts zu verbessern. 

Das Zuordnungsfeature enthält Daten zur Konfiguration Ihrer Software, um genaue und effiziente Funktionen zur Problembehandlung bereitzustellen. Die Daten enthalten Informationen wie Betriebssystem und Version, IP-Adresse, DNS-Name und Arbeitsstationsname. Microsoft erfasst weder Namen noch Adressen oder andere Kontaktinformationen.

Weitere Informationen zur Sammlung und Nutzung von Daten finden Sie in den [Datenschutzbestimmungen für Onlinedienste von Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Nachdem Sie die Überwachung für Ihre VM aktiviert haben, stehen die Überwachungsinformationen für die Analyse in Azure Monitor für VMs bereit.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Verwenden des Integritätsfeatures finden Sie unter [Azure Monitor für VMs – Integrität anzeigen](vminsights-health.md). Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Azure Monitor für VMs – Zuordnung anzeigen](vminsights-maps.md).
