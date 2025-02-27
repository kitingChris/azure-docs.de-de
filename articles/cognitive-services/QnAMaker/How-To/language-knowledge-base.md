---
title: 'Nicht englischsprachige Wissensdatenbank: QnA Maker'
titleSuffix: Azure Cognitive Services
description: QnA Maker unterstützt Wissensdatenbank-Inhalte in mehreren Sprachen. Allerdings sollte für jeden QnA Maker-Dienst eine einzelne Sprache festgelegt werden. Die erste Wissensdatenbank, die für einen bestimmten QnA Maker-Dienst erstellt wurde, bestimmt die Sprache dieses Dienstes.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 1203748b43ea5911d4717f451bca2cbe82753386
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447415"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Sprachunterstützung für Inhalte in QnA Maker-Wissensdatenbanken
QnA Maker unterstützt Wissensdatenbank-Inhalte in mehreren Sprachen. Allerdings sollte für jeden QnA Maker-Dienst eine einzelne Sprache festgelegt werden. Die erste Wissensdatenbank, die für einen bestimmten QnA Maker-Dienst erstellt wurde, bestimmt die Sprache dieses Dienstes. Die unterstützten Sprachen finden Sie [hier](../Overview/languages-supported.md).

Die Sprache wird automatisch aus dem jeweils extrahierten Datenquelleninhalt erkannt. Wenn Sie einen neuen QnA Maker-Dienst und eine neue Wissensdatenbank innerhalb dieses Dienstes erstellt haben, können Sie überprüfen, ob die Sprache richtig festgelegt wurde.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/).

2. Wählen Sie **Ressourcengruppen** aus und navigieren Sie zu der Ressourcengruppe, in der der QnA Maker-Dienst bereitgestellt wird. Wählen Sie die Ressource **Azure Search** aus.

    ![Auswählen der Ressource „Azure Search“](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Wählen Sie den Index **testkb** aus. Dieser Azure Search-Index wird immer als Erstes erstellt und enthält den gespeicherten Inhalt aller Wissensdatenbanken im jeweiligen Dienst. 

    ![Auswählen der Test-Wissensdatenbank](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Wählen Sie den Abschnitt **Felder** aus, der die Details von „testkb“ beinhaltet.

    ![Auswählen von „Felder“](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Aktivieren Sie das Kontrollkästchen für **Analysetool**, um Sprachdetails anzuzeigen.

    ![Auswählen des Analysetools](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Für das Analysetool sollte eine bestimmte Sprache eingestellt sein. Diese Sprache wird beim Erstellen der Wissensdatenbank automatisch erkannt. Sie kann nicht mehr geändert werden, nachdem die Ressource erstellt wurde.

    ![Ausgewähltes Analysetool](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines QnA-Bots mit Azure Bot Service](../Tutorials/create-qna-bot.md)
