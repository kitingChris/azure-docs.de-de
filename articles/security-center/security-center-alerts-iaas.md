---
title: Bedrohungserkennung für virtuelle Computer und Server in Azure Security Center | Microsoft-Dokumentation
description: In diesem Thema werden die in Azure Security Center verfügbaren Warnungen für virtuelle Computer und Server vorgestellt.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: f23865fc0a1943a5157e4ff8eb8de10a71ef0883
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295789"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Bedrohungserkennung für virtuelle Computer und Server in Azure Security Center

In diesem Thema werden die verschiedenen Arten von Erkennungsmethoden und Warnungen vorgestellt, die für virtuelle Computer und Server mit den folgenden Betriebssystemen verfügbar sind. Eine Liste mit den unterstützten Versionen finden Sie unter [Von Azure Security Center unterstützte Features und Plattformen](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Security Center wird zur Überwachung und zum Schutz Ihrer Windows-basierten Computer in Azure-Dienste integriert.  In Security Center werden die Warnungen und Behandlungsvorschläge aus allen diesen Diensten in einem benutzerfreundlichen Format präsentiert.

### Microsoft Server Defender ATP <a nanme="windows-atp"></a>

Azure Security Center erweitert seine Plattformen für den Cloudworkloadschutz durch die Integration in Windows Defender Advanced Threat Protection (ATP). Dadurch stehen umfassende EDR-Funktionen (Endpoint Detection and Response; Endpunkterkennung und -reaktion) zur Verfügung.

> [!NOTE]
> Der Windows Server Defender ATP-Sensor wird automatisch auf Windows-Servern aktiviert, die in Azure Security Center integriert werden.

Wenn von Windows Server Defender ATP eine Bedrohung erkannt wird, wird eine Warnung ausgelöst. Die Warnung wird auf dem Security Center-Dashboard angezeigt. Über das Dashboard können Sie zur Windows Defender ATP-Konsole wechseln, um eine detaillierte Untersuchung durchzuführen und das Ausmaß des Angriffs zu ermitteln. Weitere Informationen zu Windows Server Defender ATP finden Sie unter [Onboard servers to the Windows Defender ATP service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) (Integrieren von Servern in den Windows Defender ATP-Dienst).

### Absturzabbildanalyse <a nanme="windows-dump"></a>

Beim Absturz von Software wird in einem Absturzabbild ein Teil des Arbeitsspeichers zum Zeitpunkt des Absturzes erfasst.

Ein Absturz kann durch Schadsoftware verursacht worden sein oder Malware enthalten. Um einer Erkennung durch Sicherheitsprodukte zu entgehen, nutzen verschiedene Arten von Schadsoftware einen dateilosen Angriff. Dadurch werden Schreibvorgänge auf dem Datenträger sowie die Verschlüsselung von auf den Datenträger geschriebenen Softwarekomponenten vermieden. Diese Art von Angriff ist mit einem herkömmlichen datenträgerbasierten Ansatz nur schwer zu erkennen.

Sie kann jedoch mithilfe einer Arbeitsspeicheranalyse erkannt werden. Durch die Analyse des Arbeitsspeichers im Absturzabbild kann Security Center die Verfahren erkennen, die bei dem Angriff zum Einsatz kommen, um Schwachstellen in der Software auszunutzen, auf vertrauliche Daten zuzugreifen und sich heimlich auf einem kompromittierten Computer einzunisten. Die Leistung der Hosts wird dabei durch das Security Center-Back-End nur minimal beeinträchtigt.

> [!div class="mx-tableFixed"]

|Warnung|BESCHREIBUNG|
|---|---|
|**Code injection discovered** (Codeinjektion erkannt)|Codeinjektion ist das Einfügen von ausführbaren Modulen in aktive Prozesse oder Threads. Diese Technik wird von Schadsoftware genutzt, um auf Daten zuzugreifen und sich selbst zu verbergen, um nicht entdeckt und entfernt zu werden. <br/>Die Warnung weist darauf hin, dass ein injiziertes Modul im Absturzabbild vorhanden ist. Zur Unterscheidung zwischen schädlichen und nicht schädlichen injizierten Modulen überprüft Azure Security Center, ob das injizierte Modul einem verdächtigen Verhaltensprofil entspricht.|
|**Suspicious code segment discovered** (Verdächtiges Codesegment erkannt)|Deutet darauf hin, dass ein Codesegment mit nicht standardmäßigen Methoden zugeordnet wurde – etwa durch reflektierende Injektion oder Prozessaushöhlung. Diese Warnung gibt zusätzliche Merkmale des Codesegments an, die verarbeitet wurden, um Kontext im Hinblick auf die Funktionen und das Verhalten des gemeldeten Codesegments bereitzustellen.|
|**Shellcode discovered** (Shellcode erkannt)|Shellcode ist die Nutzlast, die ausgeführt wird, nachdem eine Schadsoftware ein Sicherheitsrisiko einer Software ausgenutzt hat.<br/>Diese Warnung deutet darauf hin, dass bei einer Absturzabbildanalyse ausführbarer Code mit einem Verhalten erkannt wurde, das üblicherweise bei schädlichen Nutzlasten zu beobachten ist. Zwar kann auch nicht schädliche Software dieses Verhalten aufweisen, es ist jedoch nicht typisch für normale Vorgehensweisen bei der Softwareentwicklung.|

### Erkennung dateiloser Angriffe <a nanme="windows-fileless"></a>

In Azure sind die Endpunkte unserer Kunden immer wieder Ziel dateiloser Angriffe.

Bei dateilosen Angriffen werden schädliche Nutzlasten in den Arbeitsspeicher injiziert, um einer Erkennung zu entgehen. Nutzlasten von Angreifern nisten sich im Arbeitsspeicher von kompromittierten Prozessen ein und führen ein breites Spektrum an schädlichen Aktivitäten aus.

Die Erkennung dateiloser Angriffe erkennt dank automatisierter forensischer Techniken für den Arbeitsspeicher Toolkits, Techniken und Verhaltensweisen im Zusammenhang mit dateilosen Angriffen. Diese Lösung überprüft zur Laufzeit in regelmäßigen Abständen Ihren Computer und extrahiert Erkenntnisse direkt aus dem Arbeitsspeicher sicherheitskritischer Prozesse.

Sie sucht nach Hinweisen auf Missbrauch, Codeinjektion oder die Ausführung schädlicher Nutzlasten. Die Erkennung dateiloser Angriffe generiert detaillierte Sicherheitswarnungen, um die Warnungsselektierung und Korrelation zu beschleunigen und Downstream-Reaktionszeiten zu verkürzen. Dieser Ansatz dient zur Ergänzung ereignisbasierter EDR-Lösungen und vergrößert das Erkennungsspektrum.

> [!NOTE]
> Sie können Windows-Warnungen simulieren, indem Sie [dieses Azure Security Center-Playbook](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) herunterladen und den bereitgestellten Richtlinien folgen.

> [!div class="mx-tableFixed"]

|Warnung|BESCHREIBUNG|
|---|---|
|**Fileless attack technique detected** (Dateilose Angriffstechnik erkannt)|Der Arbeitsspeicher des folgenden Prozesses enthält ein Toolkit für einen dateilosen Angriff: Meterpreter. Toolkits für dateilose Angriffe sind in der Regel nicht im Dateisystem vorhanden und somit für herkömmliche Virenschutzlösungen nur schwer zu erkennen.|

### <a name="further-reading"></a>Weitere nützliche Informationen

Beispiele und weitere Informationen zur Security Center-Erkennung finden Sie hier:

* [Leverage Azure Security Center to detect when compromised Linux machines attack](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/) (Erkennen von Angriffen durch kompromittierte Linux-Computer mithilfe von Azure Security Center)
* [Azure Security Center can detect emerging vulnerabilities in Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/) (Erkennung neuer Sicherheitsrisiken in Linux durch Azure Security Center)

## Linux <a name="linux-machines"></a>

Security Center sammelt Überwachungsdatensätze von Linux-Computern mithilfe von **AuditD** – einem der am häufigsten verwendeten Linux-Überwachungsframeworks. AuditD hat den Vorteil, dass es bereits lange etabliert und in den Hauptkernel integriert ist. 

### Integration von Linux-AuditD-Warnungen und Microsoft Monitoring Agent (MMA) <a name="linux-auditd"></a>

Beim AuditD-System handelt es sich um ein Subsystem auf Kernelebene. Es dient zur Überwachung von Systemaufrufen, filtert diese Aufrufe nach einem bestimmten Regelsatz und schreibt entsprechende Meldungen in einen Socket. Security Center integriert Funktionen aus dem AuditD-Paket in Microsoft Monitoring Agent (MMA). Diese Integration ermöglicht das Sammeln von AuditD-Ereignissen in allen unterstützten Linux-Distributionen ohne weitere Bedingungen.  

AuditD-Datensätze werden gesammelt, angereichert und mithilfe des Linux-MMA-Agents zu Ereignissen aggregiert. In Security Center werden immer wieder neue Analysen hinzugefügt, die auf der Grundlage von Linux-Signalen schädliches Verhalten auf cloudbasierten und lokalen Linux-Computern erkennen. Diese Analysen umfassen ähnlich wie bei Windows-Funktionen verdächtige Prozesse und Anmeldeversuche, das Laden von Kernmodulen sowie andere Aktivitäten, die darauf hindeuten, dass ein Computer angegriffen wird oder kompromittiert wurde.  

Im Anschluss folgen einige Beispiele für Analysen, um die Abdeckung verschiedener Phasen des Angriffslebenszyklus zu veranschaulichen.

> [!div class="mx-tableFixed"]

|Warnung|BESCHREIBUNG|
|---|---|
|**Process seen accessing the SSH authorized keys file in an unusual way** (Ein Prozess hat auf ungewöhnliche Weise auf eine Datei mit autorisierten SSH-Schlüsseln zugegriffen.)|Auf eine Datei mit autorisierten SSH-Schlüsseln wurde mit einer Methode zugegriffen, die so ähnlich auch in bekannten Schadsoftwareszenarien zum Einsatz kommt. Dieser Zugriff deutet möglicherweise darauf hin, dass ein Angreifer versucht, sich dauerhaft Zugang zu einem Computer zu verschaffen.|
|**Detected Persistence Attempt** (Persistenzversuch erkannt)|Bei der Hostdatenanalyse wurde festgestellt, dass ein Startskript für den Einzelbenutzermodus installiert wurde. <br/>Da legitime Prozesse nur selten in diesem Modus ausgeführt werden müssen, kann dies darauf hindeuten, dass ein Angreifer jeder Ausführungsebene einen schädlichen Prozess hinzugefügt hat, um Persistenz zu erreichen.|
|**Manipulation of scheduled tasks detected** (Manipulation geplanter Aufgaben erkannt)|Bei der Hostdatenanalyse wurde eine mögliche Manipulation geplanter Aufgaben erkannt. Angreifer fügen kompromittierten Computern oftmals geplante Aufgaben hinzu, um Persistenz zu erreichen.|
|**Suspicious file timestamp modification** (Verdächtige Änderung von Dateizeitstempeln)|Bei der Hostdatenanalyse wurde eine verdächtige Zeitstempeländerung erkannt. Angreifer kopieren oftmals Zeitstempel aus vorhandenen legitimen Dateien in neue Tools, um die Erkennung dieser neu abgelegten Dateien zu verhindern.|
|**A new user was added to the sudoers group** (Der Sudoer-Gruppe wurde ein neuer Benutzer hinzugefügt.)|Bei der Hostdatenanalyse wurde erkannt, dass der Sudoer-Gruppe ein Benutzer hinzugefügt wurde. Mitglieder dieser Gruppe können Befehle mit hohen Berechtigungen ausführen.|
|**Likely exploit of DynoRoot vulnerability in dhcp client** (Mögliche Ausnutzung der DynoRoot-Sicherheitslücke im DHCP-Client)|Bei der Hostdatenanalyse wurde die Ausführung eines ungewöhnlichen Befehls mit dem dhclient-Skript als übergeordneter Prozess erkannt.|
|**Suspicious kernel module detected** (Verdächtiges Kernelmodul erkannt)|Bei der Hostdatenanalyse wurde eine freigegebene Objektdatei erkannt, die als Kernelmodul geladen wird. Hierbei kann es sich um eine legitime Aktivität handeln. Der Vorgang kann aber auch darauf hindeuten, dass einer Ihrer Computer kompromittiert wurde.|
|**Process associated with digital currency mining detected** (Prozess erkannt, der mit digitalem Währungs-Mining assoziiert wird)|Bei der Hostdatenanalyse wurde die Ausführung eines Prozesses erkannt, der normalerweise mit digitalem Währungs-Mining assoziiert wird.|
|**Potential port forwarding to external IP address** (Potenzielle Portweiterleitung an eine externe IP-Adresse)|Bei der Hostdatenanalyse wurde die Initiierung einer Portweiterleitung an eine externe IP-Adresse erkannt.|

> [!NOTE]
> Sie können Windows-Warnungen simulieren, indem Sie [dieses Azure Security Center-Playbook](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) herunterladen und den bereitgestellten Richtlinien folgen.


Weitere Informationen und Beispiele finden Sie in diesen Artikeln:  

* [Leverage Azure Security Center to detect when compromised Linux machines attack](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/) (Erkennen von Angriffen durch kompromittierte Linux-Computer mithilfe von Azure Security Center)

* [Azure Security Center can detect emerging vulnerabilities in Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/) (Erkennung neuer Sicherheitsrisiken in Linux durch Azure Security Center)

 