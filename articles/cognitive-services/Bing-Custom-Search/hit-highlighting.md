---
title: Verwenden von Dekorationsmarkierungen zum Hervorheben von Text – benutzerdefinierte Bing-Suche
titlesuffix: Azure Cognitive Services
description: In diesem Artikel wird die Aktivierung von Textdekorationen in Suchantworten erläutert.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 42b30b14e561fd3851a41701d2ecb8d98d5a02ed
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445566"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Verwenden von Dekorationsmarkierungen zum Markieren von Text

Für Bing wird die Treffermarkierung unterstützt, bei der Abfrageausdrücke (oder andere Ausdrücke, die von Bing als relevant eingestuft werden) in den Anzeigezeichenfolgen einiger Antworten markiert werden. Mit den Feldern `name`, `displayUrl` und `snippet` können beispielsweise die Abfrageausdrücke markiert werden.

Standardmäßig werden in Bing keine Hervorhebungsmarkierungen in Anzeigezeichenfolgen eingefügt. Fügen Sie zum Einbinden der Markierungen den Abfrageparameter `textDecorations` in Ihre Anforderung ein, und legen Sie ihn auf **true** fest. In Bing werden die Abfrageausdrücke mit den Unicode-Zeichen E000 und E001 markiert, um den Anfang und das Ende des Ausdrucks anzugeben. Falls der Abfrageausdruck beispielsweise „Sailing Dinghy“ lautet und beide Ausdrücke im Feld vorhanden sind, wird der Ausdruck wie im folgenden Beispiel in Zeichen für die Treffermarkierung gesetzt:  
  
![Treffermarkierung](./media/bing-hit-highlighting.PNG) 

Bevor die Zeichenfolge auf Ihrer Benutzeroberfläche angezeigt wird, sollten Sie die Unicode-Zeichen durch Zeichen ersetzen, die für das gewünschte Anzeigeformat geeignet sind. Wenn Sie den Text beispielsweise im HTML-Format anzeigen, können Sie den Abfrageausdruck hervorheben, indem Sie E000 durch „<b\>“ und E001 durch „</b\>“ ersetzen. Entfernen Sie die Markierungen der Zeichenfolge, falls Sie keine Formatierung anwenden möchten. 

In Bing haben Sie die Möglichkeit, Unicode-Zeichen oder HTML-Tags als Markierungen bereitzustellen. Fügen Sie den Abfrageparameter `textFormat` ein, um anzugeben, welche Markierungen verwendet werden sollen. Legen Sie `textFormat` auf „Raw“ (Standardeinstellung) fest, um den Inhalt mit Unicode-Zeichen zu markieren, und `textFormat` auf „HTML“, um den Inhalt mit HTML-Tags zu markieren. 
  
Wenn `textDecorations` auf **true** festgelegt ist, können in Bing die unten angegebenen Markierungen in die Anzeigezeichenfolgen von Antworten eingefügt werden. Falls keine HTML-Entsprechung vorhanden ist, enthält die Spalte „HTML“ der Tabelle keine Angabe.

|Unicode|HTML|BESCHREIBUNG
|-|-|-
|U+E000|\<b&gt;|Markiert den Anfang des Abfrageausdrucks (Treffermarkierung)
|U+E001|\</b&gt;|Markiert das Ende des Abfrageausdrucks
|U+E002|\<i&gt;|Markiert den Anfang von Text in Kursivdruck 
|U+E003|\</i&gt;|Markiert das Ende von Text in Kursivdruck
|U+E004|\<br/&gt;|Markiert einen Zeilenumbruch
|U+E005||Markiert den Anfang einer Telefonnummer
|U+E006||Markiert das Ende einer Telefonnummer
|U+E007||Markiert den Anfang einer Adresse
|U+E008||Markiert das Ende einer Adresse
|U+E009|\&nbsp;|Markiert ein geschütztes Leerzeichen
|U+E00C|\<strong&gt;|Markiert den Anfang von Text in Fettdruck
|U+E00D|\</strong&gt;|Markiert das Ende von Text in Fettdruck
|U+E00E||Markiert den Anfang von Text, dessen Hintergrund heller als der umgebende Hintergrund sein soll
|U+E00F||Markiert das Ende von Text, dessen Hintergrund heller als der umgebende Hintergrund sein soll
|U+E010||Markiert den Anfang von Text, dessen Hintergrund dunkler als der umgebende Hintergrund sein soll
|U+E011||Markiert das Ende von Text, dessen Hintergrund dunkler als der umgebende Hintergrund sein soll
|U+E012|\<del&gt;|Markiert den Anfang von Text, der durchgestrichen sein soll
|U+E013|\</del&gt;|Markiert das Ende von Text, der durchgestrichen sein soll
|U+E016|\<sub&gt;|Markiert den Anfang von tiefgestelltem Text
|U+E017|\</sub&gt;|Markiert das Ende von tiefgestelltem Text
|U+E018|\<sup&gt;|Markiert den Anfang von hochgestelltem Text
|U+E019|\</sup&gt;|Markiert das Ende von hochgestelltem Text

Das folgende Beispiel enthält die Antwort `Computation` mit Tiefstellungsmarkierungen für einen log(2)-Abfrageausdruck. Das Feld `expression` enthält die Markierungen nur dann, wenn für `textDecoration` **true** festgelegt ist.

![Computation-Markierungen](./media/bing-markers-computation.PNG) 

Wenn für die Anforderung keine Anpassungen angefordert werden, lautet der Ausdruck „log10(2)“. 
  
