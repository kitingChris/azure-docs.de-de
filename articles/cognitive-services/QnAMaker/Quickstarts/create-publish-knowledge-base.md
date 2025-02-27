---
title: 'Erstellen, Trainieren und Veröffentlichen einer Wissensdatenbank: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Sie können eine QnA Maker-Wissensdatenbank (KB) aus Ihren eigenen Inhalten erstellen, z.B. aus FAQs oder Produkthandbüchern. Die QnA Maker-Wissensdatenbank in diesem Beispiel wird auf der Grundlage einer einfachen FAQ-Webseite erstellt, um Fragen zur BitLocker-Schlüsselwiederherstellung zu beantworten.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 03ce1047dd175ae4a676fa1461632a8e23122a8d
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249731"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>Erstellen, Trainieren und Veröffentlichen der QnA Maker-Wissensdatenbank

Sie können eine QnA Maker-Wissensdatenbank (KB) aus Ihren eigenen Inhalten erstellen, z.B. aus FAQs oder Produkthandbüchern. Dieser Artikel enthält ein Beispiel zum Erstellen einer QnA Maker-Wissensdatenbank auf der Grundlage einer einfachen FAQ-Webseite erstellt, um Fragen zur BitLocker-Schlüsselwiederherstellung zu beantworten.

## <a name="prerequisite"></a>Voraussetzung

> [!div class="checklist"]
> * Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-a-qna-maker-knowledge-base"></a>Erstellen einer QnA Maker-Wissensdatenbank

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen beim Portal [QnAMaker.ai](https://QnAMaker.ai) an.

1. Wählen Sie im QnA Maker-Portal die Option **Wissensdatenbank erstellen** aus.

   ![Screenshot des QnA Maker-Portals](../media/qna-maker-create-kb.png)

1. Wählen Sie auf der Seite **Erstellen** in Schritt 1 die Option **QnA-Dienst erstellen** aus. Sie werden an das [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) weitergeleitet, um einen QnA Maker-Dienst in Ihrem Abonnement einzurichten. Wenn für das Azure-Portal ein Timeout auftritt, wählen Sie **Noch mal versuchen** für die Website aus. Nachdem Sie eine Verbindung hergestellt haben, wird Ihr Azure-Dashboard angezeigt.

1. Nachdem Sie erfolgreich einen neuen QnA Maker-Dienst in Azure erstellt haben, kehren Sie zu qnamaker.ai/create zurück. Wählen Sie den QnA Maker-Dienst aus den Dropdownlisten in Schritt 2 aus. Wenn Sie einen neuen QnA Maker-Dienst erstellt haben, achten Sie darauf, dass Sie die Seite aktualisieren.

   ![Screenshot: Auswählen einer Wissensdatenbank im QnA Maker-Dienst](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Nennen Sie Ihre Wissensdatenbank in Schritt 3 **My Sample QnA KB**.

1. Wählen Sie drei Arten von Datenquellen aus, um Ihrer Wissensdatenbank Inhalte hinzuzufügen. Fügen Sie in Schritt 4 unter **KB mit Daten auffüllen** die URL der [FAQ zur BitLocker-Wiederherstellung](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) in das Feld **URL** ein.

   ![Screenshot: Hinzufügen von Datenquellen](../media/qnamaker-quickstart-kb/add-datasources.png)

1. Wählen Sie in Schritt 5 **Wissensdatenbank erstellen** aus.

1. Während QnA Maker die Wissensdatenbank erstellt, wird ein Popupfenster angezeigt. Der Extraktionsvorgang nimmt einige Minuten in Anspruch, um die HTML-Seite zu lesen und Fragen und Antworten zu identifizieren.

1. Nachdem die Wissensdatenbank von QnA Maker erfolgreich erstellt wurde, wird die Seite **Wissensdatenbank** geöffnet. Auf dieser Seite können Sie den Inhalt der Wissensdatenbank bearbeiten.

## <a name="edit-the-knowledge-base"></a>Bearbeiten der Wissensdatenbank

1. Wählen Sie im QnA Maker-Portal im Abschnitt **Bearbeiten** die Option **Fragen-und-Antworten-Paar hinzufügen** aus, um der Wissensdatenbank eine neue Zeile hinzuzufügen. Geben Sie unter **Frage** die Begrüßung **Hi** ein. Geben Sie unter **Antwort** die Antwort **„Hallo“ ein. Ask me BitLocker questions.** (Hallo. Stellen Sie mir Fragen zu BitLocker.)

    ![Screenshot des QnA Maker-Portals](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. Wählen Sie in der oberen rechten Ecke **Speichern und trainieren** aus, um Ihre Änderungen zu speichern und das QnA Maker-Modell zu trainieren. Änderungen werden nur beibehalten, wenn sie gespeichert werden.

## <a name="test-the-knowledge-base"></a>Testen der Wissensdatenbank

1. Wählen Sie rechts oben im QnA Maker-Portal die Option **Testen** aus, um zu testen, ob die von Ihnen vorgenommenen Änderungen übernommen wurden. Geben Sie `hi there` in das Feld ein, und drücken Sie die EINGABETASTE. Daraufhin sollte die Antwort, die Sie als Reaktion erstellt haben, angezeigt werden.

1. Wählen Sie **Untersuchen** aus, um die Antwort ausführlicher zu untersuchen. Das Testfenster wird verwendet, um die Änderungen an der Wissensdatenbank zu testen, bevor sie veröffentlicht werden.

    ![Screenshot des Testbereichs](../media/qnamaker-quickstart-kb/inspect.png)

1. Wählen Sie erneut **Testen** aus, um das Popupfenster **Testen** zu schließen.

## <a name="publish-the-knowledge-base"></a>Veröffentlichen der Knowledge Base

Wenn Sie eine Wissensdatenbank veröffentlichen, werden die Frage-Antwort-Inhalte Ihrer Wissensdatenbank aus dem Testindex in einen Produktionsindex in Azure Search verschoben.

![Screenshot: Verschieben der Inhalte Ihrer Wissensdatenbank](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Wählen Sie im QnA Maker-Portal im Menü neben **Bearbeiten** die Option **Veröffentlichen** aus. Wählen Sie dann zum Bestätigen **Veröffentlichen** auf der Seite aus.

1. Die QnA Maker-Dienst wurde nun erfolgreich veröffentlicht. Sie können den Endpunkt in Ihrer Anwendung oder im Botcode verwenden.

    ![Screenshot: Erfolgreiche Veröffentlichung](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Erstellen eines Bots

Nach der Veröffentlichung können Sie auf der Seite **Veröffentlichen** einen Bot erstellen: 

* Sie können schnell mehrere Bots erstellen, die alle auf die gleiche Wissensdatenbank verweisen, und dabei verschiedene Regionen oder Tarife für die einzelnen Bots verwenden. 
* Wenn Sie nur einen einzelnen Bot für die Wissensdatenbank erstellen möchten, verwenden Sie den Link zum **Anzeigen Ihrer Bots im Azure-Portal**, um eine Liste mit Ihren derzeitigen Bots anzuzeigen. 

Wenn Sie Änderungen an der Wissensdatenbank vornehmen und sie erneut veröffentlichen, müssen Sie keine weiteren Schritte für den Bot ausführen. Er ist bereits für die Verwendung mit der Wissensdatenbank konfiguriert und funktioniert auch nach späteren Änderungen. Nach jeder Veröffentlichung einer Wissensdatenbank werden alle mit ihr verbundenen Bots automatisch aktualisiert.

1. Wählen Sie im QnA Maker-Portal auf der Seite **Veröffentlichen** die Option **Bot erstellen** aus. Diese Schaltfläche wird erst angezeigt, wenn die Wissensdatenbank veröffentlicht wurde.

    ![Screenshot zum Erstellen eines Bots](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Für das Azure-Portal wird eine neue Browserregisterkarte mit der Erstellungsseite von Azure Bot Service geöffnet. Konfigurieren Sie Azure Bot Service. Weitere Informationen zu diesen Konfigurationseinstellungen finden Sie unter [Erstellen eines QnA-Bots mit Azure Bot Service v4](../tutorials/create-qna-bot.md).
    
    * Behalten Sie beim Erstellen des Bots die folgenden Einstellungen im Azure-Portal bei. Sie wurden automatisch für Ihre vorhandene Wissensdatenbank aufgefüllt: 
        * QnA-Authentifizierungsschlüssel
        * App Service-Plan und Standort
        * Azure Storage
    * Der Bot und QnA Maker können den gleichen Web-App-Serviceplan, aber nicht die gleiche Web-App verwenden. Der **App-Name** muss sich daher von dem App-Namen unterscheiden, den Sie beim Erstellen des QnA Maker-Diensts verwendet haben. 


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Wissensdatenbank](../How-To/create-knowledge-base.md)
