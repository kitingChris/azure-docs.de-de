---
title: Entwickeln von sicheren Anwendungen in Microsoft Azure
description: In diesem Artikel sind die bewährten Methoden beschrieben, die in der Implementierungs- und Überprüfungsphase eines Webanwendungsprojekts zu beachten sind.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f4add4bf07178aa616e86f8a64b313630466824f
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653263"
---
# <a name="develop-secure-applications-on-azure"></a>Entwickeln sicherer Anwendungen in Azure
In diesem Artikel werden Sicherheitsaktivitäten und -kontrollen vorgestellt, die Sie berücksichtigen sollten, wenn Sie Anwendungen für die Cloud entwickeln. Es werden Sicherheitsfragen und -konzepten behandelt, die Sie während der Implementierungs- und Überprüfungsphase von Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) berücksichtigen müssen. Das Ziel ist, Ihnen das Festlegen von Aktivitäten und Azure-Diensten zu ermöglichen, mit denen Sie eine sicherere Anwendung entwickeln können.

In diesem Artikel werden die folgenden SDL-Phasen behandelt:

- Implementierung
- Überprüfung

## <a name="implementation"></a>Implementierung
Der Schwerpunkt der Implementierungsphase besteht darin, bewährte Methoden für die frühe Prävention einzurichten und Sicherheitsprobleme im Code zu erkennen und daraus zu entfernen.
Angenommen, Ihre Anwendung wird auf Arten verwendet, für die sie nicht beabsichtigt war. Dies hilft dabei, Sie vor versehentlichem oder vorsätzlichem Missbrauch Ihrer Anwendung zu schützen.

### <a name="perform-code-reviews"></a>Ausführen von Code Reviews

Führen Sie vor dem Einchecken von Code [Code Reviews](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) durch, um die Gesamtqualität des Codes zu erhöhen und das Risiko für die Erzeugung von Fehlern zu verringern. Sie können [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) verwenden, um den Code Review-Prozess zu verwalten.

### <a name="perform-static-code-analysis"></a>Ausführen statischer Codeanalysen

[Statische Codeanalysen](https://www.owasp.org/index.php/Static_Code_Analysis) (auch bekannt als *Quellcodeanalyse*) werden in der Regel als Teil eines Code Reviews durchgeführt. Die statische Codeanalyse verwendet herkömmlicherweise Tools zur Analyse von ausgeführtem Code, um potenzielle Sicherheitslücken in nicht ausgeführtem Code zu finden, indem Methoden wie [Taint-Prüfung](https://en.wikipedia.org/wiki/Taint_checking) und [Datenflussanalyse](https://en.wikipedia.org/wiki/Data-flow_analysis) eingesetzt werden.

Azure Marketplace bietet [Entwicklertools](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review), die statische Codeanalyse ausführen und bei Code Reviews helfen.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Überprüfen und Bereinigen aller Eingaben für Ihre Anwendung

Behandeln Sie alle Eingaben als nicht vertrauenswürdig, um Ihre Anwendung vor den häufigsten Sicherheitsrisiken für Webanwendungen zu schützen. Nicht vertrauenswürdige Daten sind ein Transportmittel für Einschleusungsangriffe. Eingaben für Ihre Anwendung umfassen Parameter in der URL, Eingaben des Benutzers, Daten aus der Datenbank oder von einer API sowie alles, das als Eingabe übergeben wird, das ein Benutzer möglicherweise manipulieren könnte. Eine Anwendung sollte [überprüfen](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs), ob Daten syntaktisch und semantisch gültig sind, bevor die Anwendung die Daten in irgendeiner Weise (einschließlich der Anzeige für den Benutzer) verwendet.

Überprüfen Sie Eingaben zu einem frühen Zeitpunkt im Datenfluss, um sicherzustellen, dass nur ordnungsgemäß formatierte Daten in den Workflow gelangen. Sie möchten keine falsch formatierten Daten dauerhaft in Ihrer Datenbank speichern oder eine Fehlfunktion in einer Downstreamkomponente auslösen.

Blacklisting und Whitelisting sind zwei allgemeine Ansätze zur Durchführung einer Syntaxüberprüfung der Eingabe:

  - Blacklisting versucht, zu überprüfen, ob eine bestimmte Benutzereingabe keinen Inhalt enthält, von dem „bekannt ist, dass er bösartig ist“.

  - Whitelisting versucht, zu überprüfen, ob eine bestimmte Benutzereingabe einem Satz „bekannter gutartiger“ Eingaben entspricht. Zeichenbasiertes Whitelisting ist eine Form des Whitelistings, bei der eine Anwendung überprüft, ob die Benutzereingabe nur als „gutartig bekannte“ Zeichen enthält, bzw. ob die Eingabe ein bekanntes Format hat.
    Dies kann beispielsweise die Überprüfung umfassen, ob ein Benutzername nur alphanumerische Zeichen enthält, oder ob er genau zwei Zahlen enthält.

Whitelisting ist der zu bevorzugende Ansatz zum Erstellen sicherer Software.
Blacklisting ist anfällig für Fehler, da es nicht möglich ist, eine vollständige Liste potenziell schädlicher Eingaben aufzustellen.

Führen Sie diese Arbeit auf dem Server aus, nicht auf dem Client (oder auf dem Server und auf dem Client).

### <a name="verify-your-applications-outputs"></a>Überprüfen der Ausgaben Ihrer Anwendung

Jede Ausgabe, die Sie entweder visuell oder innerhalb eines Dokuments darstellen, sollte immer codiert und mit Escapezeichen versehen sein. Das [Versehen mit Escapezeichen](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), auch bekannt als *Ausgabecodierung*, wird verwendet, um sicherzustellen, dass nicht vertrauenswürdige Daten kein Transportmittel für einen Einschleusungsangriff sind. Das Versehen mit Escapezeichen in Kombination mit Datenüberprüfung bietet Verteidigungsebenen, um die Sicherheit des Systems als Ganzes zu erhöhen.

Das Versehen mit Escapezeichen stellt sicher, dass alles als *Ausgabe* angezeigt wird. Das Versehen mit Escapezeichen teilt außerdem dem Interpreter mit, dass die Daten nicht zur Ausführung bestimmt sind, und dies verhindert, dass Angriffe funktionieren können. Dies ist eine weitere gängige Angriffstechnik namens *Cross-Site Scripting* (XSS).

Wenn Sie ein Webframework von einem Drittanbieter verwenden, können Sie Ihre Optionen für die Ausgabecodierung auf Websites überprüfen, indem Sie das [OWASP XSS-Präventions-Cheatsheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md) verwenden.

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Verwenden parametrisierter Abfragen beim Verbinden mit der Datenbank

Erstellen Sie nie in Ihrem Code „auf die Schnelle“ eine Inline-Datenbankabfrage und senden diese direkt an die Datenbank. In Ihre Anwendung eingefügter bösartiger Code könnte potenziell den Diebstahl, das Löschen oder Ändern Ihrer Datenbank verursachen. Ihre Anwendung könnte auch verwendet werden, um bösartige Betriebssystembefehle auf dem Betriebssystem auszuführen, das Ihre Datenbank hostet.

Verwenden Sie stattdessen parametrisierte Abfragen oder gespeicherte Prozeduren. Wenn Sie parametrisierte Abfragen verwenden, können Sie die Prozedur sicher aus Ihrem Code aufrufen und an diese einen Zeichenfolge übergeben, ohne sich Sorgen machen zu müssen, dass diese als Teil der Abfrageanweisung behandelt wird.

### <a name="remove-standard-server-headers"></a>Entfernen von Standardserverheadern

Header wie „Server“, „X-Powered-By“ und „X-AspNet-Version“ geben Informationen zum Server und zu zugrunde liegenden Technologien preis. Wir empfehlen, dass Sie diese Header unterdrücken, um das Fingerprinting der Anwendung zu verhindern.
Siehe [Entfernen von Standardserverheadern auf Windows Azure-Websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Trennen Ihrer Produktionsdaten

Ihre Produktionsdaten oder „echten“ Daten sollten nicht zu Entwicklungs-, Test- oder anderen Zwecken verwendet werden, die nicht dem Unternehmenszweck dienen. Für jegliche Entwicklungs- und Testzwecke sollte ein maskiertes ([anonymisiertes](https://en.wikipedia.org/wiki/Data_anonymization)) Dataset verwendet werden.

Dies bedeutet, dass weniger Personen Zugriff auf Ihre echten Daten haben, wodurch sich Ihre Angriffsfläche verringert. Dies bedeutet auch, dass weniger Mitarbeiter personenbezogene Daten sehen können, was eine potenzielle Vertraulichkeitsverletzung beseitigt.

### <a name="implement-a-strong-password-policy"></a>Implementieren einer Richtlinie für sichere Kennwörter

Zur Abwehr von Brute-Force- und wörterbuchbasierten Angriffen muss eine Richtlinie für sichere Kennwörter implementiert werden, um sicherzustellen, dass Benutzer komplexe Kennwörter erstellen (beispielsweise mit einer Mindestlänge von 12 Zeichen und einer verpflichtenden Kombination aus alphanumerischen Zeichen und Sonderzeichen).

Sie können ein Identitätsframework verwenden, um Kennwortrichtlinien zu erstellen und zu erzwingen. Azure AD B2C hilft Ihnen bei der Kennwortverwaltung durch die Bereitstellung von [integrierten Richtlinien](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows#create-a-password-reset-user-flow), [Self-service-Kennwortzurücksetzung](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr) und vielem mehr.

Vergewissern Sie sich zur Abwehr von Angriffen auf Standardkonten, dass alle Schlüssel und Kennwörter ersetzbar sind und nach der Installation von Ressourcen generiert oder ersetzt werden.

Falls die Anwendung Kennwörter automatisch generieren muss, stellen Sie sicher, dass es sich um Zufallskennwörter mit hoher Entropie handelt.

### <a name="validate-file-uploads"></a>Überprüfen von Dateiuploads

Wenn Ihre Anwendung [Dateiuploads](https://www.owasp.org/index.php/Unrestricted_File_Upload) zulässt, erwägen Sie Vorsichtsmaßnahmen, die Sie für diese riskante Aktivität ergreifen können. Bei vielen Angriffen besteht der erste Schritt darin, bösartigen Code in ein System einzuschleusen, das angegriffen wird. Die Verwendung eines Dateiuploads hilft dem Angreifer, dies zu erreichen. OWASP bietet Lösungen zum Überprüfen einer Datei, um sicherzustellen, dass die Datei, die Sie hochladen, sicher ist.

Antischadsoftware-Schutz hilft dabei, Viren, Spyware und andere Schadsoftware zu erkennen und zu entfernen. Sie können [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) oder die Endpunktschutz-Lösung eines Microsoft-Partners ([Trend Micro](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) und [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)) installieren.

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) umfasst Features wie Echtzeitschutz, geplante Überprüfungen, Schadsoftwarebehandlung, Signaturupdates, Engine-Updates, Beispielberichte und Sammlung von Ausschlussereignissen. Sie können Microsoft Antimalware und Partnerlösungen zur Vereinfachung der Bereitstellung und für integrierte Erkennungen (Warnungen und Vorfälle) in [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) integrieren.

### <a name="dont-cache-sensitive-content"></a>Speichern Sie keine vertraulichen Inhalte zwischen.

Speichern Sie keine vertraulichen Inhalte im Browser zwischen. Browser können Informationen für die Zwischenspeicherung und den Verlauf/die Chronik speichern. Zwischengespeicherte Dateien werden in einem Ordner abgelegt, z. B. dem Ordner „Temporäre Internetdateien“ bei Internet Explorer. Wenn auf diese Seite erneut zugegriffen wird, zeigt der Browser diese Seiten aus dem Cache an. Falls dem Benutzer vertrauliche Informationen (z. B. Adresse, Kreditkartendaten, Sozialversicherungsnummer oder Benutzername) angezeigt werden, werden diese Informationen möglicherweise im Cache des Browsers gespeichert. Sie können dann abgerufen werden, indem der Cache des Browsers untersucht wird, oder indem im Browser einfach auf die Schaltfläche **Zurück** geklickt wird.

## <a name="verification"></a>Überprüfung
Die Überprüfungsphase beinhaltet umfassende Bemühungen, um sicherzustellen, dass der Code die Grundsätze für Sicherheit und Datenschutz erfüllt, die in den vorangehenden Phasen festgelegt wurden.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Auffinden und Beheben von Sicherheitsrisiken in Ihren Anwendungsabhängigkeiten

Sie überprüfen Ihre Anwendung und deren abhängige Bibliotheken, um alle bekannten anfälligen Komponenten zu identifizieren. Produkte, mit denen diese Überprüfung ausgeführt werden kann, sind u. a. [OWASP-Abhängigkeitsprüfung](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/) und [Black Duck](https://www.blackducksoftware.com/).

Die Überprüfung auf Sicherheitsrisiken, unterstützt von [Tinfoil Security](https://www.tinfoilsecurity.com/), ist für Azure App Service-Web-Apps verfügbar. [Tinfoil Security-Überprüfung über App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) bietet Entwicklern und Administratoren eine schnelle, integrierte und wirtschaftliche Möglichkeit zur Ermittlung und Behandlung von Sicherheitsrisiken, bevor ein bösartiger Akteur diese ausnutzen kann.

> [!NOTE]
> Sie können [Tinfoil Security auch in Azure AD integrieren](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial). Die Integration von Tinfoil Security in Azure AD bietet Ihnen die folgenden Vorteile:
>  - Sie können in Azure AD steuern, wer Zugriff auf Tinfoil Security hat.
>  - Ihre Benutzer können automatisch mithilfe ihrer Azure AD-Konten bei Tinfoil Security angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
>  - Sie können Ihre Konten an einem einzigen zentralen Ort verwalten, dem Azure-Portal.

### <a name="test-your-application-in-an-operating-state"></a>Testen Ihrer Anwendung im Betriebszustand

Dynamische Anwendungssicherheitstests (DAST) sind ein Prozess des Testens einer Anwendung in einem Betriebszustand, um Schwachstellen bei der Sicherheit zu finden. DAST-Tools analysieren Programme, während diese ausgeführt werden, um Sicherheitslücken, wie z. B. Speicherbeschädigungen, unsichere Serverkonfigurationen, Cross-Site Scripting, Probleme mit Benutzerberechtigungen, SQL-Einschleusung und andere kritische Sicherheitsaspekte, aufzuspüren.

DAST unterscheidet sich von statischen Anwendungssicherheitstests (SAST). SAST-Tools analysieren Quellcode oder kompilierte Versionen von Code, wenn der Code nicht ausgeführt wird, um Sicherheitslücken zu finden.

Führen Sie DAST aus, vorzugsweise mit Unterstützung eines Sicherheitsexperten (einem [Penetrationtester](https://docs.microsoft.com/azure/security/azure-security-pen-testing) oder Sicherheitsrisikobewerter). Wenn kein Sicherheitsexperte verfügbar ist, können Sie DAST selber mit einem Webproxyscanner und ein wenig Schulung und Übung ausführen. Binden Sie einen DAST-Scanner zu einem möglichst führen Zeitpunkt ein, um sicherzustellen, dass Sie keine offensichtlichen Sicherheitsprobleme in Ihren Code einführen. Eine Liste von Scannern für Sicherheitsrisiken in Webanwendungen finden Sie auf der [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools)-Site.

### <a name="perform-fuzz-testing"></a>Durchführen von Fuzzing

Beim [Fuzzing](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) lösen Sie Programmfehler aus, indem Sie absichtlich falsch formatierte oder zufällige Daten in eine Anwendung einführen. Das Auslösen eines Programmfehlers hilft dabei, potenzielle Sicherheitsprobleme aufzudecken, bevor die Anwendung veröffentlicht wird.

[Erkennung von Sicherheitsrisiken](https://docs.microsoft.com/security-risk-detection/) ist der einzigartige Fuzzing-Dienst von Microsoft zum Auffinden sicherheitskritischer Fehler in Software.

### <a name="conduct-attack-surface-review"></a>Durchführen einer Überprüfung der Angriffsfläche

Das Überprüfen der Angriffsfläche nach Fertigstellung des Codes stellt sicher, dass alle Entwurfs- oder Implementierungsänderungen an einer Anwendung oder einem System berücksichtigt wurden. Es wird dadurch sichergestellt, dass alle neuen Angriffsvektoren, die als Ergebnis der Änderungen erzeugt wurden, einschließlich Bedrohungsmodellen, überprüft und korrigiert wurden.

Sie können sich ein Bild von der Angriffsfläche machen, indem Sie die Anwendung überprüfen. Microsoft bietet ein Tool zur Analyse der Angriffsfläche namens [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487) an. Sie können unter zahlreichen kommerziellen Tools oder Diensten für dynamische Tests und die Überprüfung auf Sicherheitsrisiken auswählen, einschließlich [OWASP ZED Attack Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/) und [w3af](http://w3af.sourceforge.net/). Diese Überprüfungstools durchforsten Ihre App und kartieren die Teile der Anwendung, auf die aus dem Internet zugegriffen werden kann. Sie können auch den Azure Marketplace nach ähnlichen [Entwicklertools](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1) durchsuchen.

### <a name="perform-security-penetration-testing"></a>Ausführen von Penetrationstests

Die Bereitstellung von Anwendungssicherheit ist genauso wichtig wie das Testen jeder anderen Funktionalität. Richten Sie [Penetrationstests](https://docs.microsoft.com/azure/security/azure-security-pen-testing) als standardmäßigen Bestandteil des Build- und Bereitstellungsprozesses ein. Planen Sie regelmäßige Sicherheitstests und Überprüfungen auf Sicherheitsrisiken für bereitgestellte Anwendungen, und überwachen Sie das System auf offene Ports, Endpunkte und Angriffe.

### <a name="run-security-verification-tests"></a>Ausführen von Sicherheitsüberprüfungstests

[Secure DevOps Kit für Azure](https://azsk.azurewebsites.net/index.html) (AzSK) enthält Sicherheitsüberprüfungstests für mehrere Dienste der Azure-Plattform. Sie führen diese Sicherheitsüberprüfungstests regelmäßig aus, um sicherzustellen, dass sich Ihr Azure-Abonnement und die verschiedenen Ressourcen, aus denen Ihre Anwendung besteht, in einem sicheren Zustand befinden. Sie können diese Tests auch mithilfe der AzSK-Funktion für CI/CD-Erweiterungen (Continuous Integration/Continuous Deployment) automatisieren, wodurch Sicherheitsüberprüfungstests als Visual Studio-Erweiterungen zur Verfügung gestellt werden.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln empfehlen wir die Sicherheitskontrollen und -aktivitäten, die Ihnen beim Entwerfen und Bereitstellen von sicheren Anwendungen helfen können.

- [Entwerfen sicherer Anwendungen](secure-design.md)
- [Bereitstellen sicherer Anwendungen](secure-deploy.md)
