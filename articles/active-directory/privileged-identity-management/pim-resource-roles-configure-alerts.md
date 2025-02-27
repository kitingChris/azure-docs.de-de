---
title: Konfigurieren von Sicherheitswarnungen für Azure-Ressourcenrollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Sicherheitswarnungen für Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) konfigurieren.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7ce8b79644a9ffc9481ba825ec5623a9268983
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476337"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Konfigurieren von Sicherheitswarnungen für Azure-Ressourcenrollen in PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) generiert bei verdächtigen oder nicht sicheren Aktivitäten in Ihrer Umgebung Warnungen. Wenn eine Warnung ausgelöst wird, wird sie auf der Seite „Warnungen“ angezeigt. 

![Azure-Ressourcen – Auflisten der Warnung auf der Seite „Warnungen“, Risikostufe und Anzahl](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Überprüfen von Warnungen
Wählen Sie eine Warnung aus, um einen Bericht mit den Benutzern oder Rollen, die die Warnung ausgelöst haben, zusammen mit Ratschlägen zur Lösung anzuzeigen.

![Warnungenbericht mit Anzeige von letzter Überprüfungszeit, Beschreibung, Entschärfungsschritten, Typ, Schweregrad, Sicherheitsauswirkungen und Vorschlägen zur zukünftigen Verhinderung](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alerts
| Warnung | severity | Trigger | Empfehlung |
| --- | --- | --- | --- |
| **Zu viele Besitzer zu Ressource zugewiesen** |Mittel |Zu viele Benutzer verfügen über die Rolle „Besitzer“. |Überprüfen Sie die Benutzer in der Liste, und weisen Sie einigen Benutzern Rollen mit weniger Rechten zu. |
| **Zu viele dauerhafte Besitzer zu Ressource zugewiesen** |Mittel |Zu viele Benutzer sind dauerhaft einer Rolle zugewiesen. |Überprüfen Sie die Benutzer in der Liste, und weisen Sie einige neu zu, damit für die Rollennutzung die Aktivierung erzwungen wird. |
| **Doppelte Rolle erstellt** |Mittel |Mehrere Rollen verfügen über die gleichen Kriterien. |Verwenden Sie nur eine dieser Rollen. |


### <a name="severity"></a>severity
* **Hoch**: Aufgrund einer Richtlinienverletzung ist eine sofortige Aktion erforderlich. 
* **Mittel:** Es ist keine sofortige Aktion erforderlich, aber es wird eine potenzielle Richtlinienverletzung angezeigt.
* **Niedrig**: Es ist keine sofortige Aktion erforderlich, aber es wird eine bevorzugte Richtlinienänderung vorgeschlagen.

## <a name="configure-security-alert-settings"></a>Konfigurieren der Einstellungen für Sicherheitswarnungen
Navigieren Sie auf der Seite „Warnungen“ zu **Einstellungen**.

![Seite „Warnungen“ mit hervorgehobenen „Einstellungen“](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Passen Sie die Einstellungen für die verschiedenen Warnungen so an, dass sie für Ihre Umgebung und Sicherheitsziele funktionieren.

![Einstellungenseite für eine Warnung zum Aktivieren und Konfigurieren von Einstellungen](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Einstellungen für Azure-Ressourcenrollen in PIM](pim-resource-roles-configure-role-settings.md)
