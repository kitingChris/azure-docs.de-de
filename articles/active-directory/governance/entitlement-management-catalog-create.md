---
title: Erstellen und Verwalten eines Katalogs in der Azure AD-Berechtigungsverwaltung (Vorschauversion) – Azure Active Directory
description: Erfahren Sie, wie Sie einen neuen Container für Ressourcen und Zugriffspakete in der Azure Active Directory-Berechtigungsverwaltung (Vorschauversion) erstellen.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e563d86abe3817e4c77cc0d5c8df928e41563f8
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489084"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Erstellen und Verwalten eines Katalogs in der Azure AD-Berechtigungsverwaltung (Vorschauversion)

> [!IMPORTANT]
> Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Erstellen eines Katalogs

Ein Katalog ist ein Container für Ressourcen und Zugriffspakete. Sie erstellen einen Katalog, wenn Sie zugehörige Ressourcen und Zugriffspakete gruppieren möchten. Der Benutzer, der den Katalog erstellt, ist der erste Katalogbesitzer. Ein Katalogbesitzer kann weitere Katalogbesitzer hinzufügen.

**Erforderliche Rolle:** Benutzeradministrator oder Katalogersteller

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**.

    ![Berechtigungsverwaltungskataloge im Azure-Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klicken Sie auf **Neuer Katalog**.

1. Geben Sie einen eindeutigen Namen sowie eine Beschreibung für den Katalog ein.

    Benutzern werden diese Informationen in den Details eines Zugriffspakets angezeigt.

1. Wenn Sie möchten, dass die Zugriffspakete in diesem Katalog unmittelbar nach der Erstellung von Benutzern angefordert werden können, legen Sie **Aktiviert** auf **Ja** fest.

1. Wenn Sie zulassen möchten, dass Benutzer in ausgewählten externen Verzeichnissen Zugriffspakete in diesem Katalog anfordern können, legen Sie **Für externe Benutzer aktiviert** auf **Ja** fest.

    ![Bereich „Neuer Katalog“](./media/entitlement-management-catalog-create/new-catalog.png)

1. Klicken Sie auf **Erstellen**, um den Katalog zu erstellen.

## <a name="add-resources-to-a-catalog"></a>Hinzufügen von Ressourcen zu einem Katalog

Um Ressourcen in ein Zugriffspaket einschließen zu können, müssen die Ressourcen in einem Katalog vorhanden sein. Bei den Typen von Ressourcen, die Sie hinzufügen können, handelt es sich um Gruppen, Anwendungen und SharePoint Online-Websites. Die Gruppen können in der Cloud erstellte Office 365-Gruppen oder in der Cloud erstellte Azure AD-Sicherheitsgruppen sein. Die Anwendungen können Azure AD-Unternehmensanwendungen sein, einschließlich SaaS-Anwendungen und Ihrer eigenen Anwendungen, die mit Azure AD verbunden sind. Die Websites können SharePoint Online-Websites oder SharePoint Online-Websitesammlungen sein.

**Erforderliche Rolle:** Siehe [Erforderliche Rollen, um einem Katalog Ressourcen hinzuzufügen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, dem Sie Ressourcen hinzufügen möchten.

1. Klicken Sie im linken Menü auf **Ressourcen**.

1. Klicken Sie auf **Ressourcen hinzufügen**.

1. Klicken Sie auf einen Ressourcentyp: **Gruppen**, **Anwendungen** oder **SharePoint-Websites**.

    Wenn Sie eine Ressource, die Sie hinzufügen möchten, nicht sehen, oder wenn Sie eine Ressource nicht hinzufügen können, stellen Sie sicher, dass Sie die erforderliche Azure AD-Verzeichnisrolle und -Berechtigungsverwaltungsrolle haben. Möglicherweise müssen Sie jemanden mit den erforderlichen Rollen beauftragen, die Ressource Ihrem Katalog hinzuzufügen. Weitere Informationen finden Sie unter [Erforderliche Rollen, um einem Katalog Ressourcen hinzuzufügen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Wählen Sie eine oder mehrere Ressourcen des Typs aus, die Sie dem Katalog hinzufügen möchten.

1. Klicken Sie abschließend auf **Hinzufügen**.

    Diese Ressourcen können jetzt in Zugriffspakete im Katalog aufgenommen werden.

## <a name="remove-resources-from-a-catalog"></a>Entfernen von Ressourcen aus einem Katalog

Sie können Ressourcen aus einem Katalog entfernen. Eine Ressource kann nur aus einem Katalog entfernt werden, wenn sie nicht in einem der Zugriffspakete des Katalogs verwendet wird.

**Erforderliche Rolle:** Siehe [Erforderliche Rollen, um einem Katalog Ressourcen hinzuzufügen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, aus dem Sie Ressourcen entfernen möchten.

1. Klicken Sie im linken Menü auf **Ressourcen**.

1. Wählen Sie die Ressourcen aus, die Sie entfernen möchten.

1. Klicken Sie auf **Entfernen**. Oder klicken Sie auf die Auslassungspunkte ( **...** ), und klicken Sie dann auf **Ressource entfernen**.

## <a name="edit-a-catalog"></a>Bearbeiten eines Katalogs

Sie können den Namen und die Beschreibung eines Katalogs bearbeiten. Benutzern werden diese Informationen in den Details eines Zugriffspakets angezeigt.

**Erforderliche Rolle:** Benutzeradministrator oder Katalogbesitzer

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, den Sie bearbeiten möchten.

1. Klicken Sie auf der Seite **Übersicht** des Katalogs auf **Bearbeiten**.

1. Bearbeiten Sie den Namen oder die Beschreibung des Katalogs.

1. Klicken Sie auf **Speichern**.

## <a name="delete-a-catalog"></a>Löschen eines Katalogs

Sie können einen Katalog nur löschen, wenn er keine Zugriffspakete enthält.

**Erforderliche Rolle:** Benutzeradministrator oder Katalogbesitzer

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, den Sie löschen möchten.

1. Klicken Sie auf der Seite **Übersicht** des Katalogs auf **Löschen**.

1. Klicken Sie im angezeigten Meldungsfeld auf **Ja**.

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen eines Katalogerstellers](entitlement-management-delegate.md#add-a-catalog-creator)
- [Erstellen und Verwalten eines Zugriffspakets](entitlement-management-access-package-create.md)
