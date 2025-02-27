---
title: 'Schnellstart: Analysieren von Textinhalten in Python – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Textinhalte mit dem Content Moderator SDK für Python auf verschiedene anstößige Inhalte analysieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 0fef3bffd30c19d0313e5fce7eb610ae7f6349f5
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606989"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>Schnellstart: Analysieren von Text auf anstößige Inhalte in Python

Dieser Artikel enthält Informationen und Codebeispiele, die Ihnen den Einstieg in die Verwendung des Content Moderator SDK für Python erleichtern. Sie erfahren, wie Sie eine begriffsbasierte Filterung und Klassifizierung von Textinhalten ausführen, um potenziell anstößige Inhalte zu moderieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

## <a name="prerequisites"></a>Voraussetzungen
- Ein Content Moderator-Abonnementschlüssel. Gehen Sie wie unter [Schnellstart: Erstellen eines Cognitive Services-Kontos im Azure-Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) beschrieben vor, um Content Moderator zu abonnieren und Ihren Schlüssel zu erhalten.
- [Python 2.7+ oder 3.5+](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)-Tool

## <a name="get-the-content-moderator-sdk"></a>Installieren des Content Moderator SDK

Installieren Sie das Content Moderator Python SDK, indem Sie eine Eingabeaufforderung öffnen und den folgenden Befehl ausführen:

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>Importieren von Modulen

Erstellen Sie ein neues Python-Skript mit dem Namen _ContentModeratorQS.py_, und fügen Sie den folgenden Code hinzu, um die erforderlichen Teile des SDK zu importieren.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=1-10)]

Importieren Sie auch die Funktion „Schöndruck“ zum Verarbeiten der endgültigen Ausgabe.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=12)]


## <a name="initialize-variables"></a>Initialisieren der Variablen

Fügen Sie als Nächstes Variablen für Ihren Content Moderator-Abonnementschlüssel und Ihre Endpunkt-URL hinzu. Sie müssen `<your subscription key>` durch den Wert Ihres Schlüssels ersetzen. Möglicherweise müssen Sie auch den Wert von `endpoint_url` entsprechend dem Regionsbezeichner für Ihren Abonnementschlüssel ändern. Abonnementschlüssel für kostenlose Testversionen werden in der Region **westus** generiert.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=14-16)]


Der Einfachheit halber analysieren Sie Text direkt aus dem Skript. Definieren Sie eine neue Zeichenfolge mit Textinhalt, der moderiert werden soll:

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=18-21)]


## <a name="query-the-moderator-service"></a>Abfragen des Moderator-Diensts

Erstellen Sie mit Ihrem Abonnementschlüssel und Ihrer Endpunkt-URL eine **ContentModeratorClient**-Instanz. Rufen Sie dann mithilfe der **TextModerationOperations**-Memberinstanz die Moderations-API auf. Weitere Informationen zum Aufrufen der API finden Sie in der Referenzdokumentation zu **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** .

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=23-36)]

## <a name="print-the-response"></a>Ausgeben der Antwort

Überprüfen Sie zum Schluss, ob der Aufruf erfolgreich abgeschlossen und eine **Screen**-Instanz zurückgegeben wurde. Geben Sie anschließend die zurückgegebenen Daten an die Konsole aus.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=38-39)]


Die Ausgabe für den Beispieltext in diesem Schnellstart sieht wie folgt aus:

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie ein einfaches Python-Skript erstellt, das unter Verwendung des Content Moderator-Diensts relevante Informationen zu einem bestimmten Textbeispiel zurückgibt. Informieren Sie sich als Nächstes über die Bedeutung der verschiedenen Flags und Klassifizierungen, um entscheiden zu können, welche Daten Sie benötigen und wie Ihre App mit ihnen umgehen soll.

> [!div class="nextstepaction"]
> [Textmoderation](text-moderation-api.md)
