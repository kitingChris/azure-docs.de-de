---
title: Was ist eine BLEU-Bewertung? – Custom Translator
titleSuffix: Azure Cognitive Services
description: BLEU ist eine Messung der Unterschiede zwischen einer automatischen Übersetzung und mindestens einer menschlichen Referenzübersetzung desselben Ausgangssatzes. Mit dem BLEU-Algorithmus werden aufeinander folgende Wortgruppen der automatischen Übersetzung mit den aufeinander folgenden Wortgruppen der Referenzübersetzung verglichen, und die Anzahl von Übereinstimmungen wird gezählt (mit Gewichtung).
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: a77fd1a84c1ffc18a1e0c74000c72db5cdbb00e1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447379"
---
# <a name="what-is-a-bleu-score"></a>Was ist eine BLEU-Bewertung?

[BLEU (Bilingual Evaluation Understudy)](https://en.wikipedia.org/wiki/BLEU) ist eine Messung der Unterschiede zwischen einer automatischen Übersetzung und mindestens einer menschlichen Referenzübersetzung desselben Ausgangssatzes.

## <a name="scoring-process"></a>Bewertungsprozess

Mit dem BLEU-Algorithmus werden aufeinander folgende Wortgruppen der automatischen Übersetzung mit den aufeinander folgenden Wortgruppen der Referenzübersetzung verglichen, und die Anzahl von Übereinstimmungen wird gezählt (mit Gewichtung). Diese Übereinstimmungen gelten unabhängig von der Position. Ein höherer Übereinstimmungsgrad weist auf eine höhere Ähnlichkeit mit der Referenzübersetzung hin und führt zu einem höheren Ergebnis. Die Verständlichkeit und die grammatische Korrektheit werden nicht berücksichtigt.

## <a name="how-bleu-works"></a>Wie funktioniert BLEU?

Die Stärke von BLEU besteht darin, dass diese Art der Bewertung gut mit der menschlichen Beurteilung korreliert. Es wird nicht auf einzelne Fehler bei der Satzbeurteilung geachtet, sondern der gesamte zu testende Korpus betrachtet, anstatt zu versuchen, eine präzise menschliche Beurteilung für jeden Satz zu erzielen.

Eine ausführlichere Beschreibung von BLEU-Bewertungen finden Sie [hier](https://youtu.be/-UqDljMymMg).

BLEU-Ergebnisse sind stark davon abhängig, welche Breite Ihre Domäne aufweist, wie konsistent die Testdaten mit den Trainings- und Optimierungsdaten sind und wie viele Daten für das Trainieren zur Verfügung stehen. Wenn Ihre Modelle anhand einer eng gefassten Domäne trainiert wurden und Ihre Trainingsdaten mit den Testdaten konsistent sind, können Sie eine hohe BLEU-Bewertung erwarten.

>[!NOTE]
>Ein Vergleich zwischen BLEU-Bewertungen ist nur sinnvoll, wenn die BLEU-Ergebnisse mit demselben Testdatensatz, dem gleichen Sprachpaar und derselben MT-Engine verglichen werden. Eine BLEU-Bewertung für einen anderen Testdatensatz fällt mit Sicherheit anders aus.
