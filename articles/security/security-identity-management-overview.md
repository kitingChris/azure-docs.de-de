---
title: Azure-Sicherheitsfeatures, die die Identitätsverwaltung unterstützen | Microsoft-Dokumentation
description: " Dieser Artikel bietet eine Übersicht über die wichtigsten Azure-Sicherheitsfeatures, die die Identitätsverwaltung unterstützen. Lösungen zur Identitäts- und Zugriffsverwaltung von Microsoft unterstützen IT-Profis dabei, den Zugriff auf Anwendungen und Ressourcen über das Unternehmensrechenzentrum und in der Cloud zu schützen, wobei zusätzliche Überprüfungsebenen aktiviert werden, z. B. mehrstufige Authentifizierung und Richtlinien für bedingten Zugriff. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: 758ed2e44718da709acec1379cfc79936c8b7cdf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083639"
---
# <a name="azure-identity-management-security-overview"></a>Übersicht über die Sicherheit der Azure-Identitätsverwaltung

 Identitätsverwaltung ist der Prozess der Authentifizierung und Autorisierung von [Sicherheitsprinzipalen](https://docs.microsoft.com/windows/security/identity-protection/access-control/security-principals). Sie umfasst auch das Steuern von Informationen über diese Prinzipale (Identitäten). Sicherheitsprinzipale (Identitäten) können Dienste, Anwendungen, Benutzer, Gruppen usw. umfassen. Lösungen zur Identitäts- und Zugriffsverwaltung von Microsoft unterstützen IT-Profis dabei, den Zugriff auf Anwendungen und Ressourcen über das Unternehmensrechenzentrum und in der Cloud zu schützen. Für diesen Schutz werden zusätzliche Überprüfungsebenen aktiviert, wie etwa mehrstufige Authentifizierung und Richtlinien für bedingten Zugriff. Die Überwachung verdächtiger Aktivitäten über erweiterte Sicherheitsberichtserstellung, Überwachung und Warnung trägt dazu bei, potenzielle Sicherheitsprobleme zu verringern. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) ermöglicht einmaliges Anmelden (SSO) bei Tausenden von SaaS-Cloudanwendungen (Software-as-a-Service) und Zugriff auf Webanwendungen, die Sie lokal ausführen.
 
Die Nutzung der Sicherheitsvorteile von Azure Active Directory (Azure AD) gibt Ihnen folgende Möglichkeiten:

* Erstellen und Verwalten einer einzelnen Identität für jeden Benutzer in Ihrer gesamten hybriden Unternehmensumgebung, wobei Benutzer, Gruppen und Geräte synchronisiert werden 
* Bereitstellen des SSO-Zugriffs auf Ihre Anwendungen, einschließlich Tausender bereits integrierter SaaS-Apps
* Aktivieren der Sicherheit für den Anwendungszugriff durch Erzwingen der regelbasierten Multi-Factor Authentication für lokale Anwendungen und Cloudanwendungen
* Bereitstellen des sicheren Remotezugriffs auf lokale Webanwendungen mit dem Azure AD-Anwendungsproxy

Das Ziel dieses Artikels ist, eine Übersicht über die wichtigsten Azure-Sicherheitsfunktionen zu bieten, die die Identitätsverwaltung unterstützen. Wir bieten auch Links zu Artikeln mit weiteren Informationen zu jedem Feature.  

Der Artikel konzentriert sich auf die folgenden wesentlichen Funktionen der Azure-Identitätsverwaltung:

* Einmaliges Anmelden
* Reverseproxy
* Multi-Factor Authentication
* Rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC)
* Sicherheitsüberwachung, Warnungen und Machine Learning-basierte Berichte
* Kundenidentitäts- und Kundenzugriffsverwaltung
* Geräteregistrierung
* Privileged Identity Management
* Schutz der Identität (Identity Protection)
* Hybrididentitätsverwaltung/Azure AD Connect
* Azure AD-Zugriffsüberprüfungen

## <a name="single-sign-on"></a>Einmaliges Anmelden

SSO bedeutet, dass Sie Zugriff auf sämtliche für Ihre Geschäftsaktivitäten benötigten Anwendungen und Ressourcen erhalten, indem Sie sich nur einmal mit einem einzigen Benutzerkonto anmelden. Nach der Anmeldung können Sie auf alle benötigten Anwendungen zugreifen, ohne sich ein zweites Mal (z.B. durch Eingabe eines Kennworts) authentifizieren zu müssen.

Viele Organisationen nutzen SaaS-Anwendungen, z.B. Office 365, Box und Salesforce, um die Benutzerproduktivität zu steigern. In der Vergangenheit musste das IT-Personal Benutzerkonten in jeder SaaS-Anwendung individuell erstellen und aktualisieren, und Benutzer mussten sich für jede SaaS-Anwendung ein Kennwort merken.

Azure AD weitet lokale Active Directory-Umgebungen auf die Cloud aus und ermöglicht Benutzern auf diese Weise, sich mit ihrem primären Organisationskonto nicht nur bei den mit ihrer Domäne verknüpften Geräten und Unternehmensressourcen anzumelden, sondern auch bei sämtlichen Web- und SaaS-Anwendungen, die sie für ihre Arbeit benötigen.

Nicht nur müssen Benutzer sich keine Vielzahl von Benutzernamen und Kennwörtern mehr merken, sondern Sie können den Anwendungszugriff für Benutzer basierend auf ihren Organisationsgruppen und ihrem Mitarbeiterstatus automatisch bereitstellen oder deaktivieren. Azure AD stellt Funktionen zum Steuern von Sicherheit und Zugriff bereit, mit denen Sie den Benutzerzugriff auf SaaS-Anwendungen zentral verwalten können.

Weitere Informationen:

* [Übersicht über „Einmaliges Anmelden“](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integrieren des einmaligen Anmeldens mit Azure Active Directory in SaaS-Apps](../active-directory/manage-apps/configure-single-sign-on-portal.md)

## <a name="reverse-proxy"></a>Reverseproxy

Der Azure AD-Anwendungsproxy ermöglicht Ihnen das Veröffentlichen von lokalen Anwendungen, z.B. [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US)-Websites, [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx) und [IIS](https://www.iis.net/)-basierte Apps innerhalb Ihres privaten Netzwerks und bietet sicheren Zugriff für Benutzer außerhalb Ihres Netzwerks. Der Anwendungsproxy bietet Remotezugriff und SSO für eine Vielzahl von lokalen Webanwendungen sowie Tausende von SaaS-Anwendungen, die Azure AD unterstützt werden. Mitarbeiter können sich auf ihren eigenen Geräten von zu Hause aus bei Ihren Apps anmelden und sich über diesen cloudbasierten Proxy authentifizieren.

Weitere Informationen:

* [Aktivieren des Azure AD-Anwendungsproxys](../active-directory/manage-apps/application-proxy-enable.md)
* [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](../active-directory/active-directory-application-proxy-publish.md)
* [Einmaliges Anmelden mit dem Anwendungsproxy](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Arbeiten mit bedingtem Zugriff](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication ist eine Authentifizierungsmethode, für die die Verwendung von mehr als einem Verifizierungsverfahren erforderlich ist und die eine wichtige zweite Sicherheitsebene für Benutzeranmeldungen und Transaktionen darstellt. Multi-Factor Authentication trägt zum Schutz des Zugriffs auf Daten und Anwendungen bei und bietet gleichzeitig ein einfaches Anmeldeverfahren für Benutzer. Sie bietet eine leistungsfähige Authentifizierung mit verschiedenen Überprüfungsoptionen – Telefonanruf, SMS oder per Benachrichtigung bzw. Überprüfungscode in einer mobilen App sowie OAuth-Token von Drittanbietern.

Weitere Informationen:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Was ist Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Funktionsweise von Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>RBAC

RBAC ist ein Autorisierungssystem, das auf Azure Resource Manager basiert und eine präzise Verwaltung des Zugriffs auf Ressourcen in Azure ermöglicht. RBAC ermöglicht Ihnen eine präzise Steuerung der Zugriffsebene, über die Benutzer verfügen. Beispielsweise können Sie einen Benutzer auf die alleinige Verwaltung von virtuellen Netzwerken und einen anderen Benutzer auf die Verwaltung aller Ressourcen in einer Ressourcengruppe beschränken. Azure umfasst mehrere integrierte Rollen, die Sie verwenden können. Im Folgenden werden vier grundlegende integrierte Rollen aufgeführt. Die ersten drei Rollen gelten für alle Ressourcentypen.

Weitere Informationen:

* [Was ist die rollenbasierte Zugriffssteuerung (RBAC)?](../role-based-access-control/overview.md)
* [Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/built-in-roles.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Sicherheitsüberwachung, Warnungen und Machine Learning-basierte Berichte

Sicherheitsüberwachung, Warnungen und Berichte auf Machine Learning-Basis, die inkonsistente Zugriffsmuster erkennen und Ihnen helfen können, Ihr Unternehmen zu schützen. Sie können die Zugriffs- und Nutzungsberichte von Azure AD verwenden, um sich einen Einblick in die Integrität und Sicherheit des Verzeichnisses Ihrer Organisation zu verschaffen. Mithilfe dieser Informationen kann ein Verzeichnisadministrator mögliche Sicherheitsrisiken besser bestimmen, um angemessen zu planen, wie diese Risiken eingedämmt werden können.

Im Azure-Portal fallen Berichte in die folgenden Kategorien:

* **Anomalieberichte:** Enthalten Anmeldeereignisse, die wir als anomal eingestuft haben. Unser Ziel ist, Sie auf solche Aktivitäten aufmerksam zu machen und Ihnen die Möglichkeit zu geben, zu bestimmen, ob ein Ereignis verdächtig ist.
* **Integrierte Anwendungsberichte:** Bieten Einblicke, wie Cloudanwendungen in Ihrer Organisation verwendet werden. Azure AD ermöglicht die Integration in Tausende von Cloudanwendungen.
* **Fehlerberichte:** Enthalten Hinweise auf Fehler, die bei der Bereitstellung von Konten für externe Anwendungen auftreten können.
* **Benutzerspezifische Berichte:** Enthalten Geräte- und Anmeldeaktivitätsdaten für einen bestimmten Benutzer.
* **Aktivitätsprotokolle**: Enthalten eine Aufzeichnung aller überwachten Ereignisse in den letzten 24 Stunden, 7 Tagen oder 30 Tagen sowie geänderte Gruppenaktivitäten und Kennwortzurücksetzungs- und Registrierungsaktivitäten.

Weitere Informationen:

* [Anzeigen Ihrer Zugriffs- und Nutzungsberichte](../active-directory/active-directory-view-access-usage-reports.md)
* [Erste Schritte mit Azure Active Directory-Berichten](../active-directory/active-directory-reporting-getting-started.md)
* [Anleitung für Azure Active Directory-Berichte](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Kundenidentitäts- und Kundenzugriffsverwaltung

Azure AD B2C ist ein globaler Identitätsverwaltungsdienst mit Hochverfügbarkeit für kundenorientierte Anwendungen, der für Hunderte Millionen von Identitäten skaliert werden kann. Er kann über mobile und Webplattformen integriert werden. Ihre Kunden können sich über eine anpassbare Oberfläche bei all Ihren Anwendungen anmelden und zu diesem Zweck entweder vorhandene Konten aus sozialen Netzwerken nutzen oder neue Anmeldeinformationen festlegen.

In der Vergangenheit mussten Anwendungsentwickler ihren eigenen Code schreiben, wenn sie Verbraucher registrieren und in ihren Anwendungen anmelden wollten. Und sie hätten lokale Datenbanken oder Systeme zum Speichern von Benutzernamen und Kennwörtern verwendet. Azure AD B2C bietet Ihrer Organisation eine bessere Lösung, um die Verwaltung von Kundenidentitäten in ihre Anwendungen zu integrieren. Dabei kommen eine sichere standardbasierte Plattform und ein umfassender Satz von erweiterbaren Richtlinien zum Einsatz.

Wenn Sie Azure AD B2C verwenden, können sich Ihre Kunden mit vorhandenen Konten in sozialen Netzwerken (Facebook, Google, Amazon, LinkedIn) oder durch Erstellen neuer Anmeldeinformationen (E-Mail-Adresse und Kennwort oder Benutzername und Kennwort) bei Ihren Anwendungen registrieren.

Weitere Informationen:

* [Was ist Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C – Vorschauversion: Registrieren und Anmelden von Kunden bei Ihren Anwendungen](../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C – Vorschauversion: Anwendungsarten](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Geräteregistrierung

Die Azure AD-Geräteregistrierung ist die Grundlage gerätebasierter Szenarien für den [bedingten Zugriff](../active-directory/active-directory-conditional-access-device-registration-overview.md). Wenn ein Gerät registriert ist, stellt die Azure AD-Geräteregistrierung eine Identität für das Gerät bereit, die bei der Anmeldung eines Benutzers zum Authentifizieren des Geräts dient. Das authentifizierte Gerät und die Attribute des Geräts können anschließend verwendet werden, um bedingte Zugriffsrichtlinien für Anwendungen zu erzwingen, die in der Cloud und lokal gehostet werden.

In Kombination mit einer Lösung für die Verwaltung mobiler Geräte, wie z.B. Intune, werden die Geräteattribute in Azure AD mit zusätzlichen Informationen über das Gerät aktualisiert. So können Sie Regeln für den bedingten Zugriff erstellen, die erzwingen, dass der Zugriff von Geräten Ihren Standards für Sicherheit und Compliance entspricht.

Weitere Informationen:

* [Erste Schritte bei der Azure AD-Geräteregistrierung](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Automatische Geräteregistrierung bei Azure AD für in Domänen eingebundene Windows-Geräte](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Einrichten der automatischen Registrierung bei Azure AD für in Domänen eingebundene Windows-Geräte](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Mithilfe von Azure AD Privileged Identity Management (PIM) können Sie Ihre privilegierten Identitäten und deren Zugriff auf Ressourcen in Azure AD und anderen Microsoft Online Services wie Office 365 und Microsoft Intune verwalten, steuern und überwachen.

Manchmal müssen Benutzer privilegierte Vorgänge in Azure- oder Office 365-Ressourcen oder anderen SaaS-Apps ausführen. Dieses Erfordernis bedeutet häufig, dass Organisationen diesen Benutzern in Azure AD dauerhaften privilegierten Zugriff gewähren müssen. Diese Zugriffsart stellt ein wachsendes Sicherheitsrisiko für die in der Cloud gehosteten Ressourcen dar, da Organisationen die Aktionen, die diese Benutzer mit ihren Administratorberechtigungen ausführen, nicht ausreichend überwachen können. Darüber hinaus kann die Sicherheit der gesamten Cloud in Gefahr sein, wenn ein Benutzerkonto mit privilegiertem Zugriff kompromittiert wird. Mit Azure AD Privileged Identity Management können Sie dieses Risiko abmildern.

Azure AD Privileged Identity Management gibt Ihnen diese Möglichkeiten:

* Ermitteln, welche Benutzer Azure AD-Administratoren sind.
* Aktivieren von bedarfsgesteuertem Just-In-Time-Administratorzugriff (JIT) auf Microsoft Online Services wie z.B. Office 365 und Intune.
* Abrufen von Berichten zum Administratorzugriffsverlauf und zu Änderungen bei Administratorzuweisungen.
* Aktivieren von Benachrichtigungen zum Zugriff auf eine privilegierte Rolle.

Weitere Informationen:

* [Was ist Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)
* [Zuweisen von Azure AD-Verzeichnisrollen in PIM](../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Schutz der Identität (Identity Protection)

Azure AD Identity Protection ist ein Sicherheitsdienst, der eine umfassende Übersicht über Risikoereignisse und potenzielle Sicherheitsrisiken bietet, die für die Identitäten Ihrer Organisation bestehen. Identity Protection nutzt die vorhandenen Möglichkeiten von Azure AD zur Erkennung von Anomalien, die in Form der Azure AD-Berichte über anomale Aktivitäten zur Verfügung stehen. Identity Protection führt außerdem neue Risikoereignistypen ein, mit denen Anomalien in Echtzeit erkannt werden können.

Weitere Informationen:

* [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Show zu Azure AD und Identität: Schutz der Identität – Vorschauversion](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hybrididentitätsverwaltung/Azure AD Connect

Die Identitätslösungen von Microsoft decken sowohl lokale als auch cloudbasierte Funktionen ab, und es wird eine einzelne Benutzeridentität für die standortunabhängige Authentifizierung und Autorisierung gegenüber allen Ressourcen erstellt. Wir bezeichnen dies als Hybrididentität. Das Microsoft-Tool Azure AD Connect wurde entwickelt, um Sie beim Erreichen Ihrer Hybrididentitätsziele zu unterstützen. Dadurch können Sie für Ihre Benutzer eine einzige Identität für in Azure AD integrierte in Office 365-, Azure- und SaaS-Anwendungen bereitstellen. Er zeichnet sich durch Folgendes aus:

* Synchronisierung
* AD FS und Verbundintegration
* Passthrough-Authentifizierung
* Systemüberwachung

Weitere Informationen:

* [Whitepaper zur Hybrididentität](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Azure AD-Teamblog](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Azure AD-Zugriffsüberprüfungen

Mithilfe von Azure AD-Zugriffsüberprüfungen (Azure Active Directory) können Unternehmen Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie die Zuweisung privilegierter Rollen effizient verwalten.

Weitere Informationen:

* [Azure AD-Zugriffsüberprüfungen](../active-directory/governance/access-reviews-overview.md)
* [Manage user access with Azure AD access reviews (Verwalten des Benutzerzugriffs mit Azure AD-Zugriffsüberprüfungen)](../active-directory/governance/access-reviews-overview.md)
