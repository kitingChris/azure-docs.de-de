---
title: Erweiterte Bedrohungserkennung von Azure | Microsoft-Dokumentation
description: Enthält eine Beschreibung von Azure AD Identity Protection und der dazugehörigen Funktionen.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 2a6a0e6219a45821e2a4416a4e563aa6edb86eba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67127168"
---
# <a name="azure-advanced-threat-detection"></a>Erweiterte Bedrohungserkennung von Azure

Azure enthält integrierte Funktionen zur erweiterten Bedrohungserkennung über Dienste wie Azure Active Directory (Azure AD), Azure Monitor-Protokolle und Azure Security Center. Diese Auflistung von Sicherheitsdiensten und -funktionen bietet eine einfache und schnelle Möglichkeit zu verstehen, was innerhalb Ihrer Azure-Bereitstellungen abläuft.

Azure bietet eine Vielzahl von Optionen zum Konfigurieren und Anpassen der Sicherheit, um den Anforderungen Ihrer App-Bereitstellungen zu entsprechen. In diesem Artikel wird beschrieben, wie Sie diese Anforderungen erfüllen.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) ist ein Feature der Edition [Azure Active Directory Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions), mit dem Sie eine Übersicht über die Risikoereignisse und potenziellen Sicherheitsrisiken anzeigen können, die für die Identitäten Ihrer Organisation bestehen. Für Identity Protection werden die vorhandenen Azure AD-Funktionen zur Erkennung von Anomalien genutzt, die über die [Berichte zu anomalen Aktivitäten von Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports) verfügbar sind, und es werden neue Risikoereignistypen eingeführt, mit denen Echtzeitanomalien erkannt werden können.

![Diagramm zu Azure AD Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)

Identity Protection nutzt adaptive Machine Learning-Algorithmen und heuristische Verfahren, um Anomalien und Risikoereignisse zu erkennen, die auf die Kompromittierung einer Identität hindeuten können. Mit diesen Daten generiert Identity Protection Berichte und Warnungen, damit Sie diese Risikoereignisse untersuchen und entsprechende Aktionen zur Korrektur oder Lösung durchführen können.

Azure Active Directory Identity Protection ist viel mehr als nur ein Tool für die Überwachung und Berichterstellung. Basierend auf Risikoereignissen berechnet Identity Protection eine Benutzerrisikostufe für jeden Benutzer, sodass Sie risikobasierte Richtlinien konfigurieren können, um die Identitäten Ihrer Organisation automatisch zu schützen.

Mit diesen risikobasierten Richtlinien in Verbindung mit anderen Steuerelementen für den [bedingten Zugriff](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) von Azure Active Directory und [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) können adaptive Korrekturaktionen blockiert oder bereitgestellt werden. Hierzu gehören Kennwortzurücksetzungen und die Durchsetzung der mehrstufigen Authentifizierung.

### <a name="identity-protection-capabilities"></a>Funktionen von Identity Protection

Azure Active Directory Identity Protection ist viel mehr als nur ein Tool für die Überwachung und Berichterstellung. Zum Schutz der Identitäten Ihrer Organisation können Sie risikobasierte Richtlinien konfigurieren, die automatisch auf erkannte Probleme reagieren, wenn eine angegebene Risikostufe erreicht wurde. Mit diesen Richtlinien in Verbindung mit anderen Steuerelementen für den bedingten Zugriff von Azure Active Directory und EMS können adaptive Korrekturaktionen entweder blockiert oder initiiert werden. Hierzu gehören Kennwortzurücksetzungen und die Durchsetzung der mehrstufigen Authentifizierung.

Beispiele zu einigen Methoden, mit denen Azure Identity Protection dabei helfen kann, Ihre Konten und Identitäten zu schützen:

[Erkennen von Risikoereignissen und gefährdeten Konten](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Erkennen von sechs Risikoereignistypen per Machine Learning und mit heuristischen Regeln
-   Berechnen von Benutzerrisikostufen
-   Bereitstellen von benutzerdefinierten Empfehlungen zur Verbesserung der allgemeinen Sicherheit, indem Sicherheitsrisiken aufgedeckt werden

[Untersuchen von Risikoereignissen](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Senden von Benachrichtigungen für Risikoereignisse
-   Untersuchen von Risikoereignissen mit relevanten und kontextbezogenen Informationen
-   Bereitstellen grundlegender Workflows zum Nachverfolgen von Untersuchungen
-   Bereitstellen des einfachen Zugriffs auf Korrekturaktionen, z.B. Kennwortzurücksetzung

[Risikobasierte Richtlinien für bedingten Zugriff](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
-   Durchführen von Abwehrmaßnahmen bei risikobehafteten Anmeldungen, indem Anmeldungen blockiert oder die Multi-Factor Authentication erzwungen wird
-   Blockieren oder Schützen von risikobehafteten Benutzerkonten
-   Erzwingen, dass sich Benutzer für die mehrstufige Authentifizierung registrieren

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Mit [Azure Active Directory Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) können Sie den Zugriff innerhalb Ihrer Organisation verwalten, steuern und überwachen. Dieses Feature umfasst den Zugriff auf Ressourcen in Azure AD und anderen Microsoft-Onlinediensten, z.B. Office 365 oder Microsoft Intune.

![Diagramm zu Azure AD Privileged Identity Management](./media/azure-threat-detection/azure-threat-detection-fig2.png)

PIM ermöglicht Folgendes:

-   Abrufen von Warnungen und Berichten zu Azure AD-Administratoren und Just-In-Time-Administratorzugriff (JIT) auf Microsoft-Onlinedienste, z.B. Office 365 und Intune

-   Abrufen von Berichten zum Administratorzugriffsverlauf und zu Änderungen bei Administratorzuweisungen.

-   Aktivieren von Benachrichtigungen zum Zugriff auf eine privilegierte Rolle.

## <a name="azure-monitor-logs"></a>Azure Monitor-Protokolle

[Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) ist eine cloudbasierte IT-Verwaltungslösung von Microsoft, die Ihnen die Verwaltung und den Schutz Ihrer lokalen und cloudbasierten Infrastruktur erleichtert. Da Azure Monitor als cloudbasierter Dienst implementiert wird, ist die Lösung mit minimalen Investitionen in Infrastrukturdienste schnell betriebsbereit. Neue Sicherheitsfeatures werden automatisch bereitgestellt, sodass Sie Kosten für die laufende Wartung und für Upgrades sparen.

Zusätzlich zur Bereitstellung wertvoller Dienste kann Azure Monitor in System Center-Komponenten wie [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/) integriert werden, um Ihre bestehenden Investitionen für die Sicherheitsverwaltung auf die Cloud zu erweitern. Durch die Kombination von System Center und Azure Monitor-Protokollen können Sie vollständig vom Hybrid-Management profitieren.

### <a name="holistic-security-and-compliance-posture"></a>Ganzheitlicher Ansatz für den Sicherheits- und Konformitätsstatus

Das [Log Analytics-Dashboard für Sicherheit und Überwachung](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) bietet dank integrierter Suchabfragen für relevante Probleme, die Ihre Aufmerksamkeit erfordern, einen umfassenden Einblick in die Lage der IT-Sicherheit Ihres Unternehmens. Das Dashboard „Sicherheit und Überwachung“ ist die Startseite für sämtliche Sicherheitsaspekte in Azure Monitor-Protokolle. Hier erhalten Sie einen allgemeinen Überblick über den Sicherheitszustand Ihres Computers. Sie können auch alle Ereignisse der letzten 24 Stunden, 7 Tage oder für einen anderen benutzerdefinierten Zeitraum anzeigen.

Azure Monitor-Protokolle helfen Ihnen dabei, den Gesamtsicherheitsstatus einer beliebigen Umgebung schnell und einfach im Kontext von IT-Vorgängen zu erfassen. Hierzu zählen unter anderem die Bewertung von Softwareupdates, Antischadsoftwarebewertungen und Konfigurationsgrundwerte. Die Sicherheitsprotokolldaten sind leicht zugänglich, um die Überwachungsprozesse für Sicherheit und Konformität zu optimieren.

![Das Log Analytics-Dashboard „Sicherheit und Überwachung“](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

Das Log Analytics-Dashboard „Sicherheit und Überwachung“ ist in vier Hauptkategorien unterteilt:

-   **Sicherheitsdomänen:** Ermöglichen es Ihnen, die Sicherheitsdatensätze in Abhängigkeit der Zeit näher zu untersuchen. Außerdem können Sie auf Schadsoftwarebewertungen zugreifen, Bewertungen aktualisieren, Informationen zu Netzwerksicherheit, Identität und Zugriff anzeigen, Computer mit Sicherheitsereignissen anzeigen und schnell auf das Azure Security Center-Dashboard zugreifen.

-   **Relevante Probleme:** Mit dieser Option können Sie schnell die Anzahl von aktiven Problemen und den jeweiligen Schweregrad ermitteln.

-   **Erkennungen (Vorschau):** Ermöglicht Ihnen, Angriffsmuster zu identifizieren, indem Sicherheitswarnungen für Ihre Ressourcen beim Auftreten angezeigt werden.

-   **Informationen zu Bedrohungen:** Ermöglicht Ihnen, Angriffsmuster zu identifizieren, indem die Gesamtanzahl von Servern mit ausgehendem schädlichem IP-Datenverkehr, die Art der Bedrohung und eine Karte mit den Standorten der IP-Adressen angezeigt werden.

-   **Allgemeine Sicherheitsabfragen:** Diese Option liefert eine Liste mit den gängigsten Sicherheitsabfragen, die Sie zum Überwachen der Umgebung verwenden können. Wenn Sie eine Abfrage auswählen, wird der Suchbereich geöffnet, und die Ergebnisse der Abfrage werden angezeigt.

### <a name="insight-and-analytics"></a>Insight und Analytics
Im Mittelpunkt von [Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) steht das Repository, das von Azure gehostet wird.

![Diagramm zu Insight und Analytics](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Sie sammeln Daten von verbundenen Quellen im Repository, indem Sie Datenquellen konfigurieren und Ihrem Abonnement Lösungen hinzufügen.

![Das Dashboard von Azure Monitor](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Für Datenquellen und Lösungen werden jeweils separate Datensatztypen mit eigenen Eigenschaften erstellt, aber Sie können sie mit Abfragen für das Repository trotzdem zusammen analysieren. Sie können die gleichen Tools und Methoden verwenden, um mit den unterschiedlichsten Daten zu arbeiten, die über verschiedene Quellen erfasst werden.


Der größte Teil der Interaktion mit Azure Monitor-Protokollen erfolgt über das Azure-Portal, das in jedem Browser verwendet werden kann und Ihnen Zugriff auf Konfigurationseinstellungen und verschiedene Tools bietet, mit denen Sie die gesammelten Daten analysieren und entsprechend reagieren können. Im Portal können Sie Folgendes nutzen:
* [Protokollsuchen](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches), mit denen Sie Abfragen zum Analysieren von gesammelten Daten erstellen.
* [Dashboards](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards), die Sie mit grafischen Ansichten Ihrer wertvollsten Suchen anpassen können.
* [Lösungen](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), mit denen zusätzliche Funktionen und Analysetools bereitgestellt werden.

![Analysetools](./media/azure-threat-detection/azure-threat-detection-fig6.png)

Lösungen fügen Azure Monitor-Protokolle weitere Funktionalität hinzu. Sie werden primär in der Cloud ausgeführt und ermöglichen die Analyse von Daten, die im Log Analytics-Repository gesammelt wurden. Lösungen definieren ggf. auch neue Datensatztypen, die gesammelt und mit Protokollsuchvorgängen oder über eine zusätzliche Benutzeroberfläche analysiert werden können, die von der Lösung auf dem Log Analytics-Dashboard bereitgestellt wird.

Das Dashboard „Sicherheit und Überwachung“ ist ein Beispiel für diese Arten von Lösungen.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automation & Control: Warnung zu Abweichungen von der Sicherheitskonfiguration

Azure Automation automatisiert Verwaltungsprozesse mithilfe von Runbooks, die auf PowerShell basieren und in der Cloud ausgeführt werden. Runbooks können auch auf einem Server in Ihrem lokalen Rechenzentrum ausgeführt werden, um lokale Ressourcen zu verwalten. Azure Automation stellt die Konfigurationsverwaltung mit PowerShell Desired State Configuration (DSC) bereit.

![Diagramm zu Azure Automation](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Sie können DSC-Ressourcen erstellen und verwalten, die in Azure gehostet werden, und diese auf Cloudsysteme und lokale Systeme anwenden. Auf diese Weise können Sie die entsprechende Konfiguration definieren und automatisch erzwingen oder Berichte zu Abweichungen abrufen, um sicherzustellen, dass Sicherheitskonfigurationen die Richtlinienanforderungen erfüllen.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center unterstützt Sie beim Schützen Ihrer Azure-Ressourcen. Es bietet eine integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements. Im Dienst können Sie Richtlinien sowohl für Ihre Azure-Abonnements als auch für [Ressourcengruppen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) definieren, um eine bessere Granularität zu erzielen.

![Azure Security Center-Diagramm](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Microsoft-Sicherheitsexperten suchen ständig nach neuen Bedrohungen. Sie haben Zugriff auf umfassende Telemetriedaten, die auf der globalen Präsenz von Microsoft in der Cloud und in lokalen Umgebungen basieren. Aufgrund dieser weit reichenden und verschiedenartigen Sammlung von Datasets kann Microsoft neue Angriffsmuster und Trends für seine lokalen Privatkunden- und Unternehmensprodukte sowie für seine Onlinedienste erkennen.

Auf diese Weise können die Erkennungsalgorithmen von Security Center schnell aktualisiert werden, wenn Angreifer neue und immer anspruchsvollere Exploit-Verfahren nutzen. So können Sie mit der rasanten Entwicklung von Bedrohungen Schritt halten.

![Security Center-Bedrohungserkennung](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

Bei der Bedrohungserkennung von Security Center werden automatisch Sicherheitsinformationen von Ihren Azure-Ressourcen, aus dem Netzwerk und von verbundenen Partnerlösungen gesammelt. Diese Informationen, bei denen es sich um korrelierende Informationen aus mehreren Quellen handelt, werden analysiert, um Bedrohungen zu identifizieren.

Sicherheitswarnungen werden in Security Center zusammen mit Empfehlungen zur Lösung der Bedrohung priorisiert.

Für Security Center werden professionelle Sicherheitsanalysen genutzt, die weit über signaturbasierte Ansätze hinausgehen. Bahnbrechende Neuerungen der Big Data- und [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)-Technologie werden genutzt, um Ereignisse übergreifend für die gesamte Cloud-Fabric auszuwerten. Mit der erweiterten Analyse können Bedrohungen erkannt werden, die mit manuellen Ansätzen und der Vorhersage zur Entwicklung von Angriffen nicht identifiziert werden können. Diese Arten von Lösungen für die Sicherheitsanalyse werden in den nächsten Abschnitten beschrieben.

### <a name="threat-intelligence"></a>Bedrohungsanalyse

Microsoft verfügt über Zugriff auf eine große Menge von Informationen zu globalen Bedrohungen.

Die Telemetriedaten stammen aus mehreren Quellen, z. B. Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) und Microsoft Security Response Center (MSRC).

![Informationen zu Bedrohungen – Ergebnisse](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Sicherheitsexperten erhalten außerdem Informationen zu Bedrohungen, die zwischen großen Cloudanbietern ausgetauscht werden, und haben Threat Intelligence-Feeds von Drittanbietern abonniert. Azure Security Center kann diese Informationen verwenden, um Sie vor Bedrohungen durch bekannte Angreifer zu warnen. Beispiele hierfür sind:

-   **Nutzen des Potenzials von maschinellem Lernen:** Azure Security Center hat Zugriff auf eine riesige Menge an Daten zur Cloudnetzwerkaktivität, mit denen Bedrohungen erkannt werden können, die auf Ihre Azure-Bereitstellungen abzielen.

-   **Brute-Force-Erkennung:** Mithilfe von maschinellem Lernen wird ein Verlaufsmuster für Remotezugriffsversuche erstellt, mit dem Brute-Force-Angriffe auf SSH- (Secure Shell), RDP- (Remotedesktopprotokoll) und SQL-Ports erkannt werden können.

-   **Ausgehende DDoS- und Botnet-Erkennung:** Ein häufiges Ziel bei Angriffen auf Cloudressourcen besteht darin, die Verarbeitungsleistung dieser Ressourcen zum Durchführen von anderen Angriffen zu nutzen.

-   **Neue Server und virtuelle Computer für Verhaltensanalysen:** Nachdem ein Server oder virtueller Computer kompromittiert wurde, verwenden Angreifer eine Vielzahl von Techniken, um schädlichen Code auf diesem System auszuführen. Hierbei soll die Erkennung vermieden und Persistenz sichergestellt werden, und Sicherheitskontrollen sollen umgangen werden.

-   **Bedrohungserkennung von Azure SQL-Datenbank:** Bedrohungserkennung für Azure SQL-Datenbank, mit der anomale Datenbankaktivitäten identifiziert werden, indem ungewöhnliche und potenziell schädliche Zugriffsversuche auf Datenbanken (Exploits) angezeigt werden.

### <a name="behavioral-analytics"></a>Verhaltensanalyse

Die Verhaltensanalyse ist ein Verfahren, bei dem Daten mit einer Sammlung bekannter Muster analysiert und verglichen werden. Bei diesen Mustern handelt es sich aber nicht nur um einfache Signaturen. Sie werden anhand von komplexen Machine Learning-Algorithmen bestimmt, die auf große Datasets angewendet werden.

![Verhaltensanalyse – Ergebnisse](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)

Die Muster werden auch anhand einer sorgfältigen Analyse von schädlichem Verhalten bestimmt, die von erfahrenen Analysten durchgeführt wird. Azure Security Center kann die Verhaltensanalyse verwenden, um kompromittierte Ressourcen basierend auf der Analyse der Protokolle von virtuellen Computern, virtuellen Netzwerkgeräten, Fabric-Protokolle, Absturzabbilder und anderen Quellen zu identifizieren.

Außerdem werden Muster mit anderen Signalen korreliert, damit weitere Beweise für eine größere Aktion ermittelt werden können. So können Ereignisse identifiziert werden, die mit vorhandenen Indikatoren für eine Kompromittierung übereinstimmen.

Beispiele hierfür sind:
-   **Ausführung von verdächtigen Prozessen**: Angreifer nutzen verschiedene Verfahren, um unbemerkt Schadsoftware auszuführen. Beispielsweise können Angreifer Schadsoftware mit dem Namen regulärer Systemdateien versehen, diese dann aber an einem anderen Speicherort ablegen. Alternativ können sie einen Namen verwenden, der dem Namen einer unbedenklichen Datei ähnelt, oder die tatsächliche Dateinamenerweiterung verschleiern. Security Center modelliert Prozessverhalten und überwacht die Prozessausführung, um Ausreißer dieser Art zu erkennen.

-   **Versteckte Schadsoftware und versuchte Ausnutzung von Schwachstellen**: Ausgereifte Schadsoftware kann herkömmliche Antischadsoftware-Produkte umgehen, indem sie entweder nie auf den Datenträger schreibt oder auf dem Datenträger gespeicherte Softwarekomponenten verschlüsselt. Schadsoftware dieser Art kann aber mithilfe der Arbeitsspeicheranalyse erkannt werden, da die Schadsoftware Spuren im Arbeitsspeicher hinterlassen muss, um funktionieren zu können. Beim Absturz von Software wird in einem Absturzabbild ein Teil des Arbeitsspeichers zum Zeitpunkt des Absturzes erfasst. Indem die Arbeitsspeicherdaten im Absturzabbild analysiert werden, kann Azure Security Center Verfahren erkennen, die für folgende Zwecke verwendet werden: Ausnutzen von Schwachstellen in Software, Zugreifen auf vertrauliche Daten und Bewegen auf einem kompromittierten Computer ohne Auswirkung auf die Leistung des Computers.

-   **Seitwärtsbewegung und interne Aufklärung**: Um wertvolle Daten in einem kompromittierten Netzwerk ausfindig machen und abschöpfen zu können, versuchen Angreifer häufig, vom kompromittierten Computer aus auf andere Computer im selben Netzwerk zuzugreifen. Security Center überwacht die Verarbeitungs- und Anmeldeaktivitäten, um versuchte Aktionen aufzudecken, mit denen Angreifer ihre Basis im Netzwerk ausbauen möchten, z. B. Ausführung von Remotebefehlen im Netzwerk und Kontoauflistung.

-   **Schädliche PowerShell-Skripts:** PowerShell wird von Angreifern verwendet, um zu unterschiedlichen Zwecken Schadcode auf virtuellen Zielcomputern auszuführen. Security Center untersucht die PowerShell-Aktivitäten auf Beweise für verdächtige Vorgänge.

-   **Ausgehende Angriffe**: Angreifer nehmen häufig Cloudressourcen ins Visier, um diese zur Durchführung weiterer Angriffe zu nutzen. Kompromittierte virtuelle Computer können beispielsweise verwendet werden, um Brute-Force-Angriffe auf andere virtuelle Computer zu starten, Spam zu senden oder nach offenen Ports und anderen Geräten im Internet zu suchen. Indem Machine Learning-Verfahren auf den Netzwerkdatenverkehr angewendet werden, kann Security Center erkennen, wenn ausgehende Netzwerkkommunikation außerhalb der Norm liegt. Bei der Erkennung von Spam korreliert Security Center außerdem ungewöhnlichen E-Mail-Datenverkehr mit Informationen aus Office 365, um zu ermitteln, ob die E-Mail vermutlich schädlich oder das Ergebnis einer legitimen E-Mail-Kampagne ist.

### <a name="anomaly-detection"></a>Anomalieerkennung

Azure Security Center nutzt auch die Anomalieerkennung, um Bedrohungen zu identifizieren. Im Gegensatz zur Verhaltensanalyse (basiert auf bekannten Mustern, die aus großen Datasets abgeleitet werden) ist die Anomalieerkennung „personalisierter“ und nutzt Baselines, die speziell für Ihre Bereitstellungen gelten. Machine Learning wird angewendet, um die normale Aktivität für Ihre Bereitstellungen zu ermitteln. Anschließend werden Regeln generiert, um Ausreißerbedingungen zu definieren, die ein Sicherheitsereignis darstellen können. Hier ist ein Beispiel angegeben:

-   **Eingehende RDP/SSH-Brute-Force-Angriffe**: Es kann sein, dass Ihre Bereitstellungen gleichzeitig stark ausgelastete virtuelle Computer mit vielen täglichen Anmeldungen und andere virtuelle Computer mit wenigen oder gar keinen Anmeldungen umfassen. Azure Security Center kann eine Baseline der Anmeldeaktivität für diese virtuellen Computer ermitteln und anhand von Machine Learning-Verfahren definieren, welche Aktionen normale Anmeldeaktivitäten sind. Wenn es Abweichungen bei der Baseline gibt, die für anmeldungsbezogene Eigenschaften definiert ist, wird unter Umständen eine Warnung generiert. Auch hier wird wieder per Machine Learning-Verfahren ermittelt, was relevant ist.

### <a name="continuous-threat-intelligence-monitoring"></a>Fortlaufende Threat Intelligence-Überwachung

Azure Security Center arbeitet mit Sicherheitsforschungs- und Data Science-Teams auf der ganzen Welt, die ständig Änderungen der Bedrohungslandschaft überwachen. Dies umfasst Folgendes:

-   **Threat Intelligence-Überwachung**: Informationen zu Bedrohungen (Threat Intelligence) umfassen Mechanismen, Indikatoren, Auswirkungen und nützliche Hinweise zu vorhandenen oder neuen Bedrohungen. Diese Informationen werden in der Sicherheitscommunity bereitgestellt, und Microsoft überwacht fortlaufend Threat Intelligence-Feeds von internen und externen Quellen.

-   **Signalaustausch**: Die Erkenntnisse der Sicherheitsteams des gesamten umfangreichen Microsoft-Portfolios mit Clouddiensten und lokalen Diensten, Servern und Clientendpunkt-Geräten werden ausgetauscht und analysiert.

-   **Microsoft-Sicherheitsexperten**: Ständiger Austausch mit Teams von Microsoft, die sich mit speziellen Sicherheitsfeldern beschäftigen, z.B. Forensik und Erkennung von Webangriffen.

-   **Erkennungsoptimierung**: Algorithmen werden für echte Kundendatasets ausgeführt, und Sicherheitsexperten werten die Ergebnisse zusammen mit den Kunden aus. Richtige und falsche Positivmeldungen werden verwendet, um Machine Learning-Algorithmen zu verfeinern.

Diese kombinierten Verfahren führen zu neuen und verbesserten Erkennungsergebnissen, von denen Sie sofort profitieren können. Sie müssen hierfür nichts unternehmen.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Features für die erweiterte Bedrohungserkennung: Weitere Azure-Dienste

### <a name="virtual-machines-microsoft-antimalware"></a>Virtuelle Computer: Microsoft Antimalware

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) für Azure ist eine Lösung mit einem einzelnen Agent für Anwendungen und Mandantenumgebungen, die im Hintergrund ohne Eingreifen des Benutzers ausgeführt wird. Sie können Schutz basierend auf den Anforderungen der Anwendungsworkloads bereitstellen, entweder mit der einfachen Konfiguration, die Schutz durch die Standardeinstellungen bietet, oder mit einer erweiterten benutzerdefinierten Konfiguration, einschließlich Antischadsoftwareüberwachung. Azure-Antischadsoftware ist eine Sicherheitsoption für virtuelle Azure-Computer, die automatisch auf allen virtuellen Azure-PaaS-Computern installiert wird.

#### <a name="microsoft-antimalware-core-features"></a>Kernfeatures von Microsoft Antimalware

Hier sind die Features von Azure zum Bereitstellen und Aktivieren von Microsoft Antimalware für Ihre Anwendungen aufgeführt:

-   **Echtzeitschutz**: Überwacht die Aktivitäten in Cloud Services und auf virtuellen Computern, um die Ausführung von Schadsoftware zu erkennen und zu blockieren.

-   **Geplantes Scannen**: Führt regelmäßig gezielte Scans aus, um Schadsoftware, einschließlich aktiv ausgeführter Programme, zu erkennen.

-   **Schadsoftwarebehandlung**: Führt automatisch Aktionen für erkannte Schadsoftware aus, z.B. Löschen schädlicher Dateien, Unter-Quarantäne-Stellen schädlicher Dateien und Bereinigen schädlicher Registrierungseinträge.

-   **Signaturaktualisierungen**: Installiert automatisch mit einer vordefinierten Häufigkeit die neuesten Schutzsignaturen (Virendefinitionen), um sicherzustellen, dass der Schutz auf dem neuesten Stand ist.

-   **Aktualisierungen des Antischadsoftware-Moduls:** Führt eine automatische Aktualisierung des Microsoft Antimalware-Moduls durch.

-   **Aktualisierungen der Antimalware-Plattform**: Führt eine automatische Aktualisierung der Microsoft Antimalware-Plattform durch.

-   **Aktiver Schutz**: Übermittelt Telemetriemetadaten über erkannte Bedrohungen und verdächtige Ressourcen an Microsoft Azure, um eine schnelle Reaktion auf die ständig zunehmenden Bedrohungen sicherzustellen. Auf diese Weise wird eine synchrone Echtzeitübermittlung von Signaturen über das aktive Schutzsystem von Microsoft ermöglicht.

-   **Übermittlung von Stichproben**: Übermittelt Stichproben an den Microsoft Antimalware-Dienst, um den Dienst zu optimieren und eine Problembehandlung zu ermöglichen.

-   **Ausschlüsse**: Ermöglicht es Anwendungs- und Dienstadministratoren, aus Gründen der Leistung oder anderen Gründen bestimmte Dateien, Prozesse und Laufwerke vom Schutz und von Scans auszuschließen.

-   **Antischadsoftware-Ereigniserfassung**: Zeichnet die Integrität des Antimalware-Diensts, verdächtige Aktivitäten und durchgeführte Wiederherstellungsaktionen im Ereignisprotokoll des Betriebssystems auf und erfasst sie im Azure Storage-Konto des Kunden.

### <a name="azure-sql-database-threat-detection"></a>Bedrohungserkennung von Azure SQL-Datenbank

[Bedrohungserkennung von Azure SQL-Datenbank](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) ist ein neues Security Intelligence-Feature, das im Azure SQL-Datenbankdienst integriert ist. Die Bedrohungserkennung von Azure SQL-Datenbank arbeitet rund um die Uhr, um Informationen zu anomalen Datenbankaktivitäten zu erhalten, Profile über diese Aktivitäten zu erstellen und sie zu erkennen, um potenzielle Bedrohungen für die Datenbank zu identifizieren.

Sicherheitsbeauftragte oder andere zugewiesene Administratoren können eine sofortige Benachrichtigung über verdächtige Datenbankaktivitäten erhalten, sobald diese auftreten. Jede Benachrichtigung enthält Details zur verdächtigen Aktivität und empfiehlt die Vorgehensweise zur weiteren Untersuchung und Abwendung der Bedrohung.

Derzeit erkennt die Bedrohungserkennung von Azure SQL-Datenbank potenzielle Sicherheitsrisiken und Angriffe mit Einschleusung von SQL-Befehlen sowie anomale Datenbankzugriffsmuster.

Wenn Benutzer eine E-Mail-Benachrichtigung zu einer Bedrohungserkennung erhalten, können sie über einen Deep-Link in der E-Mail zu den relevanten Überwachungsdatensätzen navigieren und diese anzeigen. Der Deep-Link öffnet eine Überwachungsansicht oder eine vorkonfigurierte Excel-Vorlage für die Überwachung, um die relevanten Überwachungsdatensätze für den Zeitpunkt des verdächtigen Ereignisses basierend auf den folgenden Daten anzuzeigen:

-   Überwachungsspeicher für die Datenbank bzw. den Server mit den anomalen Datenbankaktivitäten

-   Relevante Überwachungsspeichertabellen, die zum Zeitpunkt des Ereignisses verwendet wurden, um das Überwachungsprotokoll zu schreiben

-   Überwachungsdatensätze für die Stunde, die direkt auf das Auftreten des Ereignisses folgt

-   Überwachungsdatensätze mit einer ähnlichen Ereignis-ID zum Zeitpunkt des Ereignisses (optional für einige Erkennungsmodule)

Erkennungsmodule für SQL-Datenbank-Bedrohungen verwenden eine der folgenden Methoden für die Erkennung:

-   **Deterministische Erkennung:** Erkennt verdächtige Muster (regelbasiert) in den SQL-Clientabfragen, die bekannten Angriffen entsprechen. Diese Methode weist eine hohe Erkennungsrate und wenig falsch positive Ergebnisse auf, aber die Abdeckung ist begrenzt, da sie in die Kategorie „atomare Erkennungen“ fällt.

-   **Verhaltensbedingte Erkennung:** Erkennt anomale Aktivitäten, also anomales Verhalten in der Datenbank, das während der letzten 30 Tage nicht aufgetreten ist. Beispiele für anomale Aktivitäten eines SQL-Clients können Steigerungen bei den fehlerhaften Anmeldungen oder Abfragen, große Menge extrahierter Daten, ungewöhnliche kanonische Abfragen oder unbekannte IP-Adressen für den Datenbankzugriff sein.

### <a name="application-gateway-web-application-firewall"></a>Web Application Firewall für Application Gateway

[Web Application Firewall (WAF)](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) ist ein Feature von [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview), das Schutz für Webanwendungen bietet, die ein Anwendungsgateway für ADC-Standardfunktionen ([Application Delivery Control](https://kemptechnologies.com/in/application-delivery-controllers), Steuerung der Anwendungsbereitstellung) nutzen. Web Application Firewall schützt vor den [zehn häufigsten Websicherheitsrisiken, die im Open Web Application Security Project (OWASP) angegeben sind](https://www.owasp.org/index.php/Top_10_2010-Main).

![Web Application Firewall für Application Gateway – Diagramm](./media/azure-threat-detection/azure-threat-detection-fig13.png)

Zu den Schutzmaßnahmen gehört Folgendes:

-   Schutz vor Einschleusung von SQL-Befehlen

-   Schutz vor websiteübergreifenden Skripts

-   Schutz vor allgemeinen Webangriffen wie Befehlseinschleusung, HTTP Request Smuggling, HTTP Response Splitting und Remote File Inclusion

-   Schutz vor Verletzungen des HTTP-Protokolls

-   Schutz vor HTTP-Protokollanomalien, z.B. fehlende user-agent- und accept-Header des Hosts

-   Verhinderung des Einsatzes von Bots, Crawlern und Scannern

-   Erkennung häufiger Fehler bei der Anwendungskonfiguration (in Bezug auf Apache, IIS usw.)

Die Konfiguration einer WAF auf Ihrem Anwendungsgateway bietet die folgenden Vorteile:

-   Schützt Ihre Webanwendung vor Sicherheitsrisiken und Angriffen im Web, ohne den Back-End-Code zu verändern.

-   Schützt mehrere Webanwendungen gleichzeitig hinter einem Anwendungsgateway. Ein Anwendungsgateway unterstützt das Hosten von bis zu 20 Websites.

-   Überwacht Webanwendungen auf Angriffe mithilfe von Echtzeitberichten, die von WAF-Protokollen des Anwendungsgateways generiert werden.

-   Unterstützt die Einhaltung von Konformitätsanforderungen. Für bestimmte Konformitätssteuerelemente ist es erforderlich, dass alle über das Internet zugänglichen Endpunkte durch eine WAF-Lösung geschützt werden.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Anomalieerkennungs-API: Mit Azure Machine Learning erstellt

Die Anomalieerkennungs-API ist eine API, die hilfreich zum Erkennen von vielen verschiedenen anomalen Mustern in Ihren Zeitreihendaten ist. Die API weist jedem Datenpunkt in der Zeitreihe einen Anomaliewert zu, der zum Generieren von Warnungen, zum Durchführen der Überwachung per Dashboard oder zum Verbinden mit Ihren Ticketausstellungssystemen verwendet werden kann.

Die [API zur Anomalieerkennung](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) kann die folgenden Arten von Anomalien in Zeitreihendaten erkennen:

-   **Spitzen und Abfälle:** Beim Überwachen der Anzahl von fehlerhaften Anmeldeversuchen bei einem Dienst oder der Anzahl von Auscheckvorgängen einer E-Commerce-Website können ungewöhnliche Spitzen oder Abfälle auf Sicherheitsangriffe oder Dienstunterbrechungen hinweisen.

-   **Positive und negative Trends**: Beim Überwachen der Computing-Speicherauslastung deutet eine stärkere Verringerung des freien Speichers auf einen potenziellen Arbeitsspeicherverlust hin. In Bezug auf die Überwachung der Dienstwarteschlangenlänge kann ein beständiger Aufwärtstrend auf ein zugrunde liegendes Softwareproblem hinweisen.

-   **Niveauänderungen und Änderungen am dynamischen Bereich von Werten:** Niveauänderungen bei den Latenzzeiten eines Diensts nach einem Dienstupgrade oder ein geringerer Grad an Ausnahmen nach dem Upgrade können für die Überwachung interessant sein.

Die Machine Learning-basierte API ermöglicht Folgendes:

-   **Flexible und robuste Erkennung:** Die Modelle zur Erkennung von Anomalien ermöglichen Benutzern die Konfiguration von Empfindlichkeitseinstellungen und die Erkennung von Anomalien über saisonale und nicht saisonale Datasets. Benutzer können das Modell zur Anomalieerkennung anpassen, um die API für die Erkennung entsprechend ihren Anforderungen mehr oder weniger empfindlich einzustellen. Das würde die Erkennung von mehr oder weniger sichtbaren Anomalien in Daten mit und ohne saisonalen Mustern bedeuten.

-   **Skalierbare und rechtzeitige Erkennung:** Die traditionelle Methode zur Überwachung mit von Fachexperten festgelegten Schwellenwerten ist teuer und kann nicht für Millionen von sich dynamisch ändernden Datasets skaliert werden. Die Modelle zur Anomalieerkennung in dieser API sind angelernt und werden mithilfe von Verlaufsdaten und Echtzeitdaten automatisch optimiert.

-   **Proaktive und handlungsrelevante Erkennung:** Für die frühe Erkennung von Anomalien kann die Erkennung langsamer Trends und von Niveauänderungen angewandt werden. Die früh erkannten anomalen Signale können verwendet werden, um Benutzer anzuweisen, Untersuchungen durchzuführen und auf Problembereiche zu reagieren. Darüber hinaus können zusätzlich zu diesem API-Dienst zur Anomalieerkennung entsprechende Analysemodelle für Hauptursachen und Benachrichtigungstools entwickelt werden.

Die API zur Erkennung von Anomalien ist eine effektive und effiziente Lösung für eine Vielzahl von Szenarien, z.B. Dienstintegrität und KPI-Überwachung, IoT, Leistungsüberwachung und Überwachung des Netzwerkdatenverkehrs. Es folgen einige verbreitete Szenarien, in denen diese API hilfreich sein kann:

- IT-Abteilungen benötigen Tools zum zeitnahen Überwachen von Ereignissen, Fehlercodes, Nutzungsprotokollen und Leistung (CPU, Arbeitsspeicher usw.).

-   Online Commerce-Websites möchten Kundenaktivitäten, Seitenaufrufe, Klicks usw. nachverfolgen.

-   Versorgungsunternehmen möchten den Verbrauch von Wasser, Gas, Strom und anderen Ressourcen nachverfolgen.

-   Für Einrichtungs- und Gebäudemanagementdienste sollen Temperatur, Feuchtigkeit, Datenverkehr usw. überwacht werden.

-   IoT/Hersteller möchten Sensordaten in Zeitreihen verwenden, um Arbeitsabläufe, Qualität usw. zu überwachen.

-   Dienstanbieter, z.B. Callcenter, müssen Trends in Bezug auf Serviceanforderungen, Ereignisvolumen, Warteschlangenlängen usw. überwachen.

-   Business Analytics-Gruppen möchten die Möglichkeit haben, anomale Bewegungen bei den Leistungsindikatoren von Unternehmen in Echtzeit zu überwachen (z.B. Umsatzvolumen, Kundenmeinungen oder Preise).

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) ist eine wichtige Komponente des Microsoft Cloud Security-Stapels. Es handelt sich um eine umfassende Lösung, die für Ihre Organisation nützlich ist, wenn Sie die Umstellung zur Realisierung der vielen Vorteile von Cloudanwendungen durchführen möchten. Sie behalten jederzeit die Kontrolle, da für eine bessere Sichtbarkeit der Aktivitäten gesorgt ist. Sie hilft zudem dabei, den Schutz wichtiger Daten in Cloudanwendungen zu erhöhen.

Mithilfe von Tools, die Sie dabei unterstützen, Schatten-IT zu enthüllen, Risiken zu bewerten, Richtlinien durchzusetzen, Aktivitäten zu untersuchen und Bedrohungen zu stoppen, kann Ihr Unternehmen sicherer zur Cloud wechseln, während es gleichzeitig die Kontrolle über wichtige Daten behält.

| | |
|---|---|
| Entdecken | Enthüllen Sie Schatten-IT mit Cloud App Security. Erhalten Sie den Überblick, indem Sie Apps, Aktivitäten, Benutzer, Daten und Dateien in Ihrer Cloudumgebung entdecken. Entdecken Sie Apps von Drittanbietern, die mit Ihrer Cloud verbunden sind.|
|Untersuchen | Untersuchen Sie Ihre Cloud-Apps mithilfe von Tools für die Cloudforensik, um ausführliche Informationen zu riskanten Apps, bestimmten Benutzern und Dateien in Ihrem Netzwerk zu erhalten. Suchen Sie in den aus der Cloud erfassten Daten nach Mustern. Generieren Sie Berichte, um Ihre Cloud zu überwachen. |
| Kontrolle | Verringern Sie das Risiko, indem Sie Richtlinien und Warnungen festlegen, um die volle Kontrolle über den Netzwerkdatenverkehr in der Cloud zu erhalten. Verwenden Sie Cloud App Security, um Ihre Benutzer in sichere, unterstützte Alternativen für Cloud-Apps zu migrieren. |
| Schützen | Verwenden Sie Cloud App Security, um Anwendungen zu unterstützen oder zu verbieten, die Verhinderung von Datenverlust zu erzwingen, Berechtigungen und Freigaben zu kontrollieren sowie benutzerdefinierte Berichte und Warnungen zu generieren. |
| Kontrolle | Verringern Sie das Risiko, indem Sie Richtlinien und Warnungen festlegen, um die volle Kontrolle über den Netzwerkdatenverkehr in der Cloud zu erhalten. Verwenden Sie Cloud App Security, um Ihre Benutzer in sichere, unterstützte Alternativen für Cloud-Apps zu migrieren. |
| | |


![Cloud App Security – Diagramm](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Mit Cloud App Security wird die Sichtbarkeit für Ihre Cloud wie folgt integriert:

-   Verwenden von Cloud Discovery zum Zuordnen und Identifizieren Ihrer Cloudumgebung und der Cloud-Apps, die von Ihrem Unternehmen verwendet werden.

-   Unterstützen und Verbieten von Apps in Ihrer Cloud.

-   Verwenden von einfach bereitzustellenden App-Verbindungen, die Anbieter-APIs für die Sichtbarkeit und Governance von Apps verwenden, mit denen Sie eine Verbindung herstellen.

-   Bereitstellen von Unterstützung, damit Sie ständig die Kontrolle behalten, indem Richtlinien festgelegt und dann fortlaufend optimiert werden.

Beim Erfassen der Daten aus diesen Quellen führt Cloud App Security ausgereifte Analysen dafür durch. Sie werden sofort über ungewöhnliche Aktivitäten informiert und erhalten einen umfassenden Einblick in die Cloudumgebung. Sie können eine Richtlinie in Cloud App Security konfigurieren und damit alles in Ihrer Cloudumgebung schützen.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Drittanbieterfunktionen für die erweiterte Bedrohungserkennung über Azure Marketplace

### <a name="web-application-firewall"></a>Web Application Firewall

Web Application Firewall untersucht eingehenden Webdatenverkehr und blockiert die Einschleusung von SQL-Befehlen, websiteübergreifendes Scripting, das Hochladen von Schadsoftware sowie DDoS- und andere Angriffe, die gegen Ihre Webanwendungen gerichtet sind. WAF überprüft zur Verhinderung von Datenverlust (Data Loss Prevention, DLP) außerdem die Antworten von den Back-End-Webservern. Die integrierte Engine für die Zugriffssteuerung ermöglicht es Administratoren, präzise Zugriffssteuerungsrichtlinien für die Authentifizierung, Autorisierung und Ressourcenerfassung (Authentication, Authorization and Accounting, AAA) zu erstellen, um für Unternehmen eine strenge Authentifizierung und Benutzerkontrolle zu ermöglichen.

Web Application Firewall ist mit den folgenden Vorteilen verbunden:

-   Erkennt und blockiert die Einschleusung von SQL-Befehlen, websiteübergreifendes Scripting, das Hochladen von Schadsoftware, sowie DDoS- und andere Angriffe, die gegen Ihre Anwendung gerichtet sind.

-   Authentifizierung und Zugriffssteuerung.

-   Überprüfung des ausgehenden Datenverkehrs, um sensible Daten zu erkennen und ggf. zu maskieren oder zu blockieren, damit die Informationen nicht von unberechtigten Personen eingesehen werden können.

-   Beschleunigt die Übermittlung von Webanwendungsinhalten mithilfe von Funktionen wie Zwischenspeicherung, Komprimierung und anderen Optimierungen des Datenverkehrs.

Beispiele für Web Application Firewalls, die im Azure Marketplace verfügbar sind, finden Sie unter [Barracuda WAF, Brocade Virtual Web Application Firewall (vWAF), Imperva SecureSphere und ThreatSTOP IP Firewall](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Nächste Schritte

- [Azure Security Center-Erkennungsfunktionen:](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) Dienen zum Identifizieren von aktiven Bedrohungen, die auf Ihre Azure-Ressourcen abzielen. Sie erhalten hiermit die Erkenntnisse, die Sie für eine schnelle Reaktion benötigen.

- [Bedrohungserkennung von Azure SQL-Datenbank:](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) Bietet Hinweise zu den potenziellen Bedrohungen für Ihre Datenbanken.
