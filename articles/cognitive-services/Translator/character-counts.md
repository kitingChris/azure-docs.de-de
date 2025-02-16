---
title: Zählen von Zeichen – Textübersetzungs-API
titlesuffix: Azure Cognitive Services
description: Es wird beschrieben, wie mit der Textübersetzungs-API Zeichen gezählt werden.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cfd5823009b66b6b525c7add1fb56953d3c1a507
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445260"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Zählen von Zeichen mit der Textübersetzungs-API

Die Textübersetzungs-API zählt jeden Unicode-Codepunkt des Eingabetextes als ein Zeichen. Jede Übersetzung eines Texts in eine Sprache gilt als separate Übersetzung, auch wenn die Anforderung in einem einzigen API-Aufruf zur Übersetzung in mehrere Sprachen gestellt wurde. Die Länge der Antwort spielt keine Rolle.

Folgendes wird gezählt:

* Text, der im Hauptteil der Anforderung an die Textübersetzungs-API übergeben wird
   * `Text` bei Verwendung der Methoden „Übersetzen“, „Transkribieren“ und „Wörterbuchsuche“
   * `Text` und `Translation` bei Verwendung der Methode „Wörterbuchbeispiele“
* Das gesamte Markup: HTML-, XML-Tags usw. im Textfeld des Anforderungstexts. Die JSON-Notation, die für die Erstellung der Anforderung verwendet wird (z.B. „Text:“), wird nicht gezählt.
* Ein einzelner Buchstabe
* Interpunktion
* Ein Leerzeichen, Tabulator, Markup und alle anderen Arten von Leerzeichen
* Jeder in Unicode definierte Codepunkt
* Eine Übersetzungswiederholung, selbst wenn Sie zuvor den gleichen Text übersetzt haben

Für Schriftzeichen, die auf Ideogrammen basieren, wie z.B. chinesische und japanische Kanji, zählt die Textübersetzungs-API weiterhin die Anzahl der Unicode-Codepunkte, also ein Zeichen pro Ideogramm. Ausnahme: Unicode-Ersatzzeichen gelten als zwei Zeichen.

Die Anzahl der Anforderungen, Wörtern, Bytes oder Sätze ist für die Anzahl der Zeichen nicht relevant.

Aufrufe der Methoden Detect und BreakSentence werden nicht in den Zeichenverbrauch eingerechnet. Es wird aber erwartet, dass die Aufrufe der Methoden Detect und BreakSentence in einem angemessenen Verhältnis zur Verwendung anderer zu zählender Funktionen stehen. Wenn die Anzahl der erfolgten Aufrufe von „Detect“ oder „BreakSentence“ die Anzahl der anderen gezählten Methoden um das 100-fache übersteigt, behält sich Microsoft das Recht vor, Ihre Verwendung dieser Methoden einzuschränken.


Weitere Informationen zur Zeichenzählung finden Sie unter [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx) (Microsoft Translator – Häufig gestellte Fragen).
