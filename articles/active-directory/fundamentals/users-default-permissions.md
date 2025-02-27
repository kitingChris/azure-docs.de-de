---
title: Standardbenutzerberechtigungen – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, welche verschiedenen Benutzerberechtigungen in Azure Active Directory verfügbar sind.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: lizross
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e501860691cccc0578a0df4eec2b161b99b4c
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341362"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?
In Azure Active Directory (Azure AD) wird allen Benutzern ein Satz mit Standardberechtigungen gewährt. Der Zugriffsumfang eines Benutzers basiert auf dem Benutzertyp, den [Rollenzuweisungen](active-directory-users-assign-role-azure-portal.md) und dem Besitz einzelner Objekte. In diesem Artikel werden diese Standardberechtigungen beschrieben, und es werden die Standardberechtigungen für Mitglieder und Gastbenutzer miteinander verglichen. Die Standardberechtigungen für Benutzer können nur in den Benutzereinstellungen in Azure AD geändert werden.

## <a name="member-and-guest-users"></a>Mitglieder und Gastbenutzer
Der gewährte Satz an Standardberechtigungen richtet sich danach, ob der Benutzer ein natives Mitglied des Mandanten ist (Mitgliedsbenutzer), oder ob der Benutzer als Gast im Rahmen der B2B-Zusammenarbeit (Gastbenutzer) aus einem anderen Verzeichnis übernommen wurde. Weitere Informationen zum Hinzufügen von Gastbenutzern finden Sie unter [Was ist die Azure AD B2B-Zusammenarbeit?](../b2b/what-is-b2b.md).
* Mitgliedsbenutzer können Anwendungen registrieren, ihr eigenes Profilfoto und ihre Mobiltelefonnummer verwalten, das eigene Kennwort verwalten und B2B-Gäste einladen. Zusätzlich können die Benutzer (mit einigen Ausnahmen) alle Verzeichnisinformationen lesen. 
* Gastbenutzer erhalten eingeschränkte Verzeichnisberechtigungen. Beispielsweise können Gastbenutzer über ihre Profilinformationen hinaus keine Informationen im Mandanten durchsuchen. Ein Gastbenutzer kann jedoch Informationen zu einem anderen Benutzer abrufen, indem er den Benutzerprinzipalnamen oder die objectId angibt. Ein Gastbenutzer kann Eigenschaften von Gruppen, denen er angehört, einschließlich der Gruppenmitgliedschaft, lesen, und zwar unabhängig von der Einstellung **Berechtigungen für Gastbenutzer sind eingeschränkt**. Ein Gast kann keine Informationen über andere Mandantenobjekte anzeigen.

Die Standardberechtigungen für Gäste sind per Voreinstellung eingeschränkt. Gäste können zu Administratorrollen hinzugefügt werden, wodurch ihnen alle in der Rolle enthaltenen Lese- und Schreibberechtigungen gewährt werden. Es steht eine weitere Einschränkung zur Verfügung, und zwar die Fähigkeit von Gästen, andere Gäste einzuladen. Durch das Festlegen der Einstellung **Gäste können einladen** auf **Nein** werden Gäste daran gehindert, andere Gäste einzuladen. Weitere Informationen finden Sie unter [Delegieren von Einladungen zur Azure Active Directory B2B-Zusammenarbeit](../b2b/delegate-invitations.md). Um Gastbenutzern standardmäßig dieselben Berechtigungen zuzuweisen wie Mitgliedsbenutzern, legen Sie **Berechtigungen für Gastbenutzer sind eingeschränkt** auf **Nein** fest. Mit dieser Einstellung erhalten alle Gastbenutzer standardmäßig die Berechtigungen für Mitgliedsbenutzer, und Gäste können administrativen Rollen hinzugefügt werden.

## <a name="compare-member-and-guest-default-permissions"></a>Vergleich der Standardberechtigungen für Mitglieds- und Gastbenutzer

**Bereich** | **Berechtigungen für Mitgliedsbenutzer** | **Berechtigungen für Gastbenutzer**
------------ | --------- | ----------
Benutzer und Kontakte | Alle öffentlichen Eigenschaften von Benutzern und Kontakten lesen<br>Gäste einladen<br>Eigenes Kennwort ändern<br>Eigene Mobiltelefonnummer verwalten<br>Eigenes Foto verwalten<br>Eigene Aktualisierungstoken für ungültig erklären | Eigene Eigenschaften lesen<br>Eigenschaften für Anzeigename, E-Mail-Adresse, Anmeldename, Foto, Benutzerprinzipalname und Benutzertyp anderer Benutzer und Kontakte lesen<br>Eigenes Kennwort ändern
Gruppen | Sicherheitsgruppen erstellen<br>Office 365-Gruppen erstellen<br>Alle Eigenschaften von Gruppen lesen<br>Nicht ausgeblendete Gruppenmitgliedschaften lesen<br>Ausgeblendete Office 365-Gruppenmitgliedschaften für eingebundene Gruppe lesen<br>Eigenschaften, Besitz und Mitgliedschaft von Gruppen im Besitz des Benutzers verwalten<br>Gäste zu Gruppen im Besitz des Benutzers hinzufügen<br>Einstellungen für dynamische Mitgliedschaften verwalten<br>Gruppen im Besitz des Benutzers löschen<br>Office 365-Gruppen im Besitz des Benutzers wiederherstellen | Alle Eigenschaften von Gruppen lesen<br>Nicht ausgeblendete Gruppenmitgliedschaften lesen<br>Ausgeblendete Office 365-Gruppenmitgliedschaften für eingebundene Gruppen lesen<br>Gruppen im Besitz des Benutzers verwalten<br>Gäste zu Gruppen im Besitz des Benutzers hinzufügen (sofern zulässig)<br>Gruppen im Besitz des Benutzers löschen<br>Office 365-Gruppen im Besitz des Benutzers wiederherstellen<br>Lesen von Eigenschaften von Gruppen, denen sie angehören, einschließlich der Mitgliedschaft.
ANWENDUNGEN | Neue Anwendung registrieren (erstellen)<br>Eigenschaften für registrierte Anwendungen und Unternehmensanwendungen lesen<br>Anwendungseigenschaften, Zuweisungen und Anmeldeinformationen für Anwendungen im Besitz des Benutzers verwalten<br>Anwendungskennwort für Benutzer erstellen oder löschen<br>Anwendungen im Besitz des Benutzers löschen<br>Anwendungen im Besitz des Benutzers wiederherstellen | Eigenschaften für registrierte Anwendungen und Unternehmensanwendungen lesen<br>Anwendungseigenschaften, Zuweisungen und Anmeldeinformationen für Anwendungen im Besitz des Benutzers verwalten<br>Anwendungen im Besitz des Benutzers löschen<br>Anwendungen im Besitz des Benutzers wiederherstellen
Geräte | Alle Eigenschaften von Geräten lesen<br>Alle Eigenschaften von Geräten im Besitz des Benutzers verwalten<br> | Keine Berechtigungen<br>Geräte im Besitz des Benutzers löschen<br>
Verzeichnis | Alle Unternehmensinformationen lesen<br>Alle Domänen lesen<br>Alle Partnerverträge lesen | Anzeigenamen und überprüfte Domänen lesen
Rollen und Bereiche | Alle administrativen Rollen und Mitgliedschaften lesen<br>Alle Eigenschaften und Mitgliedschaften von administrativen Einheiten lesen | Keine Berechtigungen 
Abonnements | Alle Abonnements lesen<br>Mitglied für Diensttarif aktivieren | Keine Berechtigungen
Richtlinien | Alle Eigenschaften von Richtlinien lesen<br>Alle Eigenschaften von Richtlinien im Besitz des Benutzers verwalten | Keine Berechtigungen

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Einschränken der Standardberechtigungen für Mitgliedsbenutzer

Die Standardberechtigungen für Mitgliedsbenutzer können auf die folgende Weise eingeschränkt werden.

Berechtigung | Erläuterung der Einstellung
---------- | ------------
Benutzer können Anwendungen registrieren | Durch das Festlegen dieser Einstellung auf „Nein“ werden Benutzer daran gehindert, Anwendungsregistrierungen zu erstellen. Die Fähigkeit kann anschließend bestimmten Personen wieder gewährt werden, indem diese der Rolle „Anwendungsentwickler“ hinzugefügt werden.
Benutzern die Verbindungsherstellung mit LinkedIn über ihr Geschäfts-, Schul- oder Unikonto erlauben | Durch das Festlegen dieser Einstellung auf „Nein“ werden Benutzer daran gehindert, ihr Geschäfts-, Schul- oder Unikonto mit ihrem LinkedIn-Konto zu verbinden. Weitere Informationen finden Sie unter [Datenfreigabe und Benutzereinwilligung bei LinkedIn-Kontoverbindungen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Fähigkeit zum Erstellen von Sicherheitsgruppen | Durch das Festlegen dieser Einstellung auf „Nein“ werden Benutzer daran gehindert, Sicherheitsgruppen zu erstellen. Globale Administratoren und Benutzeradministratoren können weiterhin Sicherheitsgruppen erstellen. Informationen zur Vorgehensweise finden Sie unter [Azure Active Directory-Cmdlets zum Konfigurieren von Gruppeneinstellungen](../users-groups-roles/groups-settings-cmdlets.md).
Fähigkeit zum Erstellen von Office 365-Gruppen | Durch das Festlegen dieser Einstellung auf „Nein“ werden Benutzer daran gehindert, Office 365-Gruppen zu erstellen. Durch das Festlegen dieser Option auf „Einige“ wird einem ausgewählten Benutzersatz das Erstellen von Office 365-Gruppen ermöglicht. Globale Administratoren und Benutzeradministratoren können weiterhin Office 365-Gruppen erstellen. Informationen zur Vorgehensweise finden Sie unter [Azure Active Directory-Cmdlets zum Konfigurieren von Gruppeneinstellungen](../users-groups-roles/groups-settings-cmdlets.md).
Zugriff auf Azure AD-Verwaltungsportal einschränken | Das Festlegen dieser Option auf „Ja“ hindert Benutzer nur am Zugriff auf Azure Active Directory über das Azure-Portal.
Fähigkeit zum Lesen anderer Benutzer | Diese Einstellung ist nur in PowerShell verfügbar. Das Festlegen dieses Flags auf „$false“ verhindert, dass Nicht-Administratoren Benutzerinformationen aus dem Verzeichnis lesen können. Durch dieses Flag wird jedoch nicht verhindert, dass Benutzerinformationen in anderen Microsoft-Diensten wie z. B. Exchange Online gelesen werden können. Diese Einstellung ist für besondere Umstände bestimmt – die Festlegung dieses Flags auf „$false“ wird nicht empfohlen.

## <a name="object-ownership"></a>Objektbesitz

### <a name="application-registration-owner-permissions"></a>Berechtigungen als Anwendungsregistrierungsbesitzer
Wenn ein Benutzer eine Anwendung registriert, wird er automatisch als Besitzer für die Anwendung hinzugefügt. Als Besitzer kann der Benutzer die Metadaten der Anwendung verwalten, beispielsweise den Namen und Berechtigungen, die von der App anfordert werden. Darüber hinaus kann der Benutzer auch die mandantenspezifische Konfiguration der Anwendung verwalten, z.B. die SSO-Konfiguration und Benutzerzuweisungen. Ein Besitzer kann außerdem andere Besitzer hinzufügen oder entfernen. Im Gegensatz zu globalen Administratoren können Besitzer nur Anwendungen verwalten, deren Besitzer sie sind.

### <a name="enterprise-application-owner-permissions"></a>Besitzerberechtigungen für Unternehmensanwendungen
Wenn ein Benutzer eine neue Unternehmensanwendung hinzufügt, wird er automatisch als Besitzer hinzugefügt. Als Besitzer kann er die mandantenspezifische Konfiguration der Anwendung verwalten, z. B. die SSO-Konfiguration, die Bereitstellung und Benutzerzuweisungen. Ein Besitzer kann außerdem andere Besitzer hinzufügen oder entfernen. Im Gegensatz zu globalen Administratoren können Besitzer nur die Anwendungen verwalten, deren Besitzer sie sind.

### <a name="group-owner-permissions"></a>Berechtigungen als Gruppenbesitzer
Wenn ein Benutzer eine Gruppe erstellt, wird er automatisch als Besitzer für diese Gruppe hinzugefügt. Als Besitzer kann der Benutzer die Eigenschaften der Gruppe verwalten, beispielsweise den Namen und die Gruppenmitgliedschaft. Ein Besitzer kann außerdem andere Besitzer hinzufügen oder entfernen. Im Gegensatz zu globalen Administratoren und Benutzerdministratoren können Besitzer nur Gruppen verwalten, deren Besitzer sie sind. Informationen zum Zuweisen eines Gruppenbesitzers finden Sie unter [Verwalten von Besitzern für eine Gruppe](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Besitzerberechtigungen
In den folgenden Tabellen werden die spezifischen Berechtigungen in Azure Active Directory beschrieben, über die Mitgliedsbenutzer für Objekte in ihrem Besitz verfügen. Der Benutzer verfügt über diese Berechtigungen nur für Objekte, deren Besitzer er ist.

#### <a name="owned-application-registrations"></a>Anwendungsregistrierungen im Besitz des Benutzers
Benutzer können die folgenden Aktionen für Anwendungsregistrierungen ausführen, deren Besitzer sie sind.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.directory/applications/audience/update | Aktualisieren der applications.audience-Eigenschaft in Azure Active Directory |
| microsoft.directory/applications/authentication/update | Aktualisieren der applications.authentication-Eigenschaft in Azure Active Directory |
| microsoft.directory/applications/basic/update | Aktualisieren der Basiseigenschaften für Anwendungen in Azure Active Directory |
| microsoft.directory/applications/credentials/update | Aktualisieren der applications.credentials-Eigenschaft in Azure Active Directory |
| microsoft.directory/applications/delete | Löschen von Anwendungen in Azure Active Directory |
| microsoft.directory/applications/owners/update | Aktualisieren der applications.owners-Eigenschaft in Azure Active Directory |
| microsoft.directory/applications/permissions/update | Aktualisieren der applications.permissions-Eigenschaft in Azure Active Directory |
| microsoft.directory/applications/policies/update | Aktualisieren der applications.policies-Eigenschaft in Azure Active Directory |
| microsoft.directory/applications/restore | Wiederherstellen von Anwendungen in Azure Active Directory |

#### <a name="owned-enterprise-applications"></a>Unternehmensanwendungen im Besitz des Benutzers
Benutzer können die folgenden Aktionen für Unternehmensanwendungen ausführen, deren Besitzer sie sind. Eine Unternehmensanwendung besteht aus dem Dienstprinzipal, einer oder mehrerer Anwendungsrichtlinien und manchmal einem Anwendungsobjekt im gleichen Mandanten wie der Dienstprinzipal.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Überwachungsprotokolle (auditLogs) in Azure Active Directory |
| microsoft.directory/policies/basic/update | Aktualisieren der Basiseigenschaften für Richtlinien in Azure Active Directory |
| microsoft.directory/policies/delete | Löschen von Richtlinien in Azure Active Directory |
| microsoft.directory/policies/owners/update | Aktualisieren der policies.owners-Eigenschaft in Azure Active Directory |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren der servicePrincipals.appRoleAssignedTo-Eigenschaft in Azure Active Directory |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Aktualisieren der users.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.directory/servicePrincipals/audience/update | Aktualisieren der servicePrincipals.audience-Eigenschaft in Azure Active Directory |
| microsoft.directory/servicePrincipals/authentication/update | Aktualisieren der servicePrincipals.authentication-Eigenschaft in Azure Active Directory |
| microsoft.directory/servicePrincipals/basic/update | Aktualisieren der Basiseigenschaften für servicePrincipals in Azure Active Directory |
| microsoft.directory/servicePrincipals/credentials/update | Aktualisieren der servicePrincipals.credentials-Eigenschaft in Azure Active Directory |
| microsoft.directory/servicePrincipals/delete | Löschen von servicePrincipals in Azure Active Directory |
| microsoft.directory/servicePrincipals/owners/update | Aktualisieren der servicePrincipals.owners-Eigenschaft in Azure Active Directory |
| microsoft.directory/servicePrincipals/permissions/update | Aktualisieren der servicePrincipals.permissions-Eigenschaft in Azure Active Directory |
| microsoft.directory/servicePrincipals/policies/update | Aktualisieren der servicePrincipals.policies-Eigenschaft in Azure Active Directory |
| microsoft.directory/signInReports/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Anmeldeberichte (signInReports) in Azure Active Directory |

#### <a name="owned-devices"></a>Geräte im Besitz des Benutzers
Benutzer können die folgenden Aktionen für Geräte ausführen, deren Besitzer sie sind.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Lesen der devices.bitLockerRecoveryKeys-Eigenschaft in Azure Active Directory |
| microsoft.directory/devices/disable | Deaktivieren von Geräten in Azure Active Directory |

#### <a name="owned-groups"></a>Gruppen im Besitz des Benutzers
Benutzer können die folgenden Aktionen für Gruppen ausführen, deren Besitzer sie sind.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Aktualisieren der groups.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.directory/groups/basic/update | Aktualisieren der Basiseigenschaften für Gruppen in Azure Active Directory |
| microsoft.directory/groups/delete | Löschen von Gruppen in Azure Active Directory |
| microsoft.directory/groups/dynamicMembershipRule/update | Aktualisieren der groups.dynamicMembershipRule-Eigenschaft in Azure Active Directory |
| microsoft.directory/groups/members/update | Aktualisieren der groups.members-Eigenschaft in Azure Active Directory |
| microsoft.directory/groups/owners/update | Aktualisieren der groups.owners-Eigenschaft in Azure Active Directory |
| microsoft.directory/groups/restore | Wiederherstellen von Gruppen in Azure Active Directory |
| microsoft.directory/groups/settings/update | Aktualisieren der groups.settings-Eigenschaft in Azure Active Directory |

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Zuweisen von Azure AD-Administratorrollen finden Sie unter [Zuweisen eines Benutzers zu Administratorrollen in Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
* Informationen dazu, wie der Zugriff auf Ressourcen in Microsoft Azure gesteuert wird, finden Sie unter [Grundlegendes zum Zugriff auf Ressourcen in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Weitere Informationen zur Beziehung zwischen Azure Active Directory und Ihrem Azure-Abonnement finden Sie unter [Beziehung zwischen Azure-Abonnements und Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Verwalten von Benutzern](add-users-azure-active-directory.md)
