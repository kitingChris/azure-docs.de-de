---
title: Geschäftskonten und Partner Center
description: Überprüfen, ob Ihr Unternehmen ein Geschäftskonto eingerichtet hat, Erstellen eines neuen Geschäftskontos und Einrichten mehrerer Geschäftskonten für die Verwendung mit Partner Center
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: d9326cab6cb5ed4ca76c9a84654697f9f90bcfcd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619545"
---
# <a name="company-work-accounts-and-partner-center"></a>Geschäftskonten und Partner Center

Partner Center verwendet Geschäftskonten (auch bekannt als Azure Active Directory-Mandanten (AD)), um den Kontozugriff für mehrere Benutzer, Berechtigungen und Profildaten zu verwalten und Gruppen und Anwendungen zu hosten. Indem Sie die E-Mail-Kontodomäne Ihres Unternehmens mit Ihrem Partner Center-Konto verknüpfen, können sich die Mitarbeiter Ihres Unternehmens zum Verwalten von Marketplace-Angeboten mithilfe der Benutzernamen und Kennwörter ihres eigenen Geschäftskontos bei Partner Center anmelden.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Überprüfen, ob Ihr Unternehmen bereits über ein Geschäftskonto verfügt

Wenn Ihr Unternehmen einen Microsoft-Clouddienst wie Azure, Microsoft Intune oder Office 365 abonniert hat, haben Sie bereits eine E-Mail-Kontodomäne (wird auch Azure Active Directory-Mandant bezeichnet), die mit Partner Center verwendet werden kann.

Führen Sie die folgenden Schritte aus, um dies zu überprüfen:
1. Melden Sie sich unter https://portal.azure.com beim Azure-Verwaltungsportal an.
2. Wählen Sie zunächst im linken Navigationsmenü **Azure Active Directory** und dann **Benutzerdefinierte Domänennamen** aus.
3. Ihr Domänenname wird aufgeführt, wenn Sie bereits über ein Geschäftskonto verfügen.

Wenn Ihr Unternehmen noch nicht über ein Geschäftskonto verfügt, wird während des Partner Center-Registrierungsprozesses ein Konto für Sie erstellt.

## <a name="set-up-multiple-work-accounts"></a>Einrichten mehrerer Geschäftskonten

Sie sollten überlegen, wie viele Benutzer im Geschäftskonto auf Partner Center zugreifen müssen, bevor Sie sich dazu entscheiden, ein vorhandenes Geschäftskonto zu verwenden. Wenn einige Benutzer im Geschäftskonto nicht auf Partner Center zugreifen müssen, sollten Sie ggf. mehrere Geschäftskonten erstellen, sodass nur die Benutzer, die auf Partner Center zugreifen müssen, in einem bestimmten Konto berücksichtigt werden.

## <a name="create-a-new-work-account"></a>Erstellen eines neuen Geschäftskontos

Führen Sie die folgenden Schritte aus, um ein neues Geschäftskonto für Ihr Unternehmen zu erstellen. Sie müssen möglicherweise Unterstützung von jemandem mit Administratorberechtigungen für das Microsoft Azure-Konto Ihres Unternehmens anfordern.

1. Melden Sie sich beim [Microsoft Azure-Portal](https://portal.azure.com)an.
2. Wählen Sie im linken Navigationsbereich **Azure Active Directory** -> **Benutzer** aus.
3. Wählen Sie **Neuer Benutzer** aus, und erstellen Sie ein neues Azure-Geschäftskonto, indem Sie einen Namen und eine E-Mail-Adresse eingeben. Stellen Sie sicher, dass die **Verzeichnisrolle** auf **Benutzer** festgelegt ist, und aktivieren Sie im unteren Bereich das Kontrollkästchen **Kennwort anzeigen**, um das automatisch generierte Kennwort anzuzeigen und zu notieren.
4. Klicken Sie auf **Erstellen**, um den neuen Benutzer zu speichern.

Die E-Mail-Adresse für das Benutzerkonto muss ein verifizierter Domänenname in Ihrem Verzeichnis sein. Wählen Sie im linken Navigationsmenü **Azure Active Directory** -> **Benutzerdefinierte Domänennamen** aus, um alle verifizierten Domänennamen in Ihrem Verzeichnis aufzulisten.

Weitere Informationen zum Hinzufügen von benutzerdefinierten Domänen in Azure Active Directory finden Sie unter [Hinzufügen oder Zuordnen einer Domäne in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Problembehandlung: Anmeldedaten für geschäftliche E-Mail-Konten

Wenn Sie Probleme mit der Anmeldung bei Ihrem Geschäftskonto haben (auch bekannt als Azure AD-Mandant), suchen Sie unten im Diagramm das Szenario, das am besten zu Ihrer Situation passt, und führen Sie dann die empfohlenen Schritte aus.

![Diagramm für Problembehandlung: Anmeldedaten für Geschäftskonto](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten Ihres Kontos im kommerziellen Marketplace in Partner Center](./manage-account.md) 
