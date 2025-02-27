---
title: Offlineauswertung – Personalisierung
titleSuffix: Azure Cognitive Services
description: In dieser C#-Schnellstartanleitung erstellen Sie eine Feedbackschleife mit dem Personalisierungsdienst.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 3fdedd1af9b683b221dfa4aebad7a30559b7abff
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722495"
---
# <a name="offline-evaluation"></a>Offlineauswertung

Offlineauswertung ist eine Methode, die es Ihnen gestattet, die Wirksamkeit des Personalisierungsdiensts zu testen und zu bewerten, ohne Ihren Code zu ändern oder die Benutzererfahrung zu beeinträchtigen. Offlineauswertung verwendet zurückliegende Daten, die von Ihrer Anwendung an die Rangfolge-API gesendet wurden, um zu vergleichen, wie unterschiedliche Ränge leistungsmäßig abgeschnitten haben.

Offlineauswertung wird in einem Datumsbereich ausgeführt. Das Ende des Bereichs kann so spät wie die aktuelle Zeit sein. Der Anfang des Bereichs kann nicht früher als die für [Datenaufbewahrung](how-to-settings.md) angegebene Anzahl von Tagen sein.

Offlineauswertung kann Ihnen helfen, die folgenden Fragen zu beantworten:

* Wie effektiv sind Personalisierungsränge für erfolgreiche Personalisierung?
    * Was sind die durchschnittlichen Belohnungen, die durch die Onlinerichtlinie für maschinelles Lernen der Personalisierung erzielt werden?
    * Wie schneidet die Personalisierung im Vergleich zu der Effektivität dessen ab, was die Anwendung standardmäßig getan hätte?
    * Wie hoch wäre die relative Effektivität einer zufälligen Auswahl für die Personalisierung gewesen?
    * Wie hoch wäre die relative Effektivität verschiedener, manuell angegebener Lernrichtlinien gewesen?
* Welche Merkmale des Kontexts tragen mehr oder weniger zur erfolgreichen Personalisierung bei?
* Welche Merkmale der Aktionen tragen mehr oder weniger zur erfolgreichen Personalisierung bei?

Darüber hinaus kann die Offlineauswertung verwendet werden, um stärker optimierte Lernrichtlinien zu ermitteln, die die Personalisierung verwenden kann, um Ergebnisse in der Zukunft zu verbessern.

Offlineauswertungen bieten keine Richtwerte für den Prozentsatz der für die Untersuchung zu verwendenden Ereignisse.

## <a name="prerequisites-for-offline-evaluation"></a>Voraussetzungen für die Offlineauswertung

Im Folgenden finden Sie wichtige Überlegungen für die repräsentative Offlineauswertung:

* Ausreichende Datenmenge. Der empfohlene Mindestwert sind mindestens 50.000 Ereignisse.
* Sammeln von Daten aus Zeiträumen mit repräsentativem Benutzerverhalten und Datenverkehr.

## <a name="discovering-the-optimized-learning-policy"></a>Ermitteln der optimierten Lernrichtlinie

Die Personalisierung kann den Offlineauswertungsprozess verwenden, um eine stärker optimierte Lernrichtlinie automatisch zu ermitteln.

Nach der Durchführung der Offlineauswertung können Sie die relative Effektivität der Personalisierung mit dieser neuen Richtlinie im Vergleich zur aktuellen Onlinerichtlinie sehen. Sie können diese Lernrichtlinie dann anwenden, damit sie bei der Personalisierung sofort wirksam wird, oder sie für die zukünftige Analyse oder Verwendung herunterladen.

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Verstehen der Relevanz von Offlineauswertungsergebnissen

Wenn Sie eine Offlineauswertung ausführen, ist es sehr wichtig, _Konfidenzgrenzen_ der Ergebnisse zu analysieren. Wenn diese breit sind, bedeutet dies, dass Ihre Anwendung nicht genügend Daten erhalten hat, um präzise oder signifikante Belohnungsabschätzungen zu erzeugen. Wenn das System zunehmend Daten akkumuliert und Sie Offlineauswertungen über längere Zeiträume ausführen, werden die Konfidenzintervalle enger.

## <a name="how-offline-evaluations-are-done"></a>Ausführen von Offlineauswertungen

Offlineauswertungen werden mittels einer Methode namens **Kontrafaktische Auswertung** durchgeführt. 

Personalisierung basiert auf der Annahme, dass sich das Verhalten von Benutzern (und somit Belohnungen) unmöglich im Nachhinein vorhersagen lässt (Personalisierung kann nicht wissen, was passiert wäre, wenn dem Benutzer etwas anderes angezeigt worden wäre, als das, was er gesehen hat) und kann nur aus gemessenen Belohnungen lernen. 

Dies ist der konzeptuelle Prozess, der für Auswertungen verwendet wird:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call
    
        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.
        
    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

Die Offlineauswertung verwendet nur beobachtetes Benutzerverhalten. Bei diesem Prozess werden große Mengen von Daten verworfen, insbesondere dann, wenn Ihre Anwendung Rangfolgeaufrufe mit einer großen Anzahl von Aktionen ausführt.


## <a name="evaluation-of-features"></a>Auswertung von Merkmalen

Offlineauswertungen können Informationen dazu liefern, wie viele der spezifischen Merkmale für Aktionen oder Kontext eine Gewichtung zu höheren Belohnungen erzeugen. Die Informationen werden mithilfe der Auswertung für den angegebenen Zeitraum und die Daten berechnet und können im Laufe der Zeit variieren.

Wir empfehlen, sich Merkmalsauswertungen anzusehen, und folgende Fragen zu stellen:

* Welche anderen, zusätzlichen Merkmale könnte Ihre Anwendung oder Ihr System bereitstellen, die denen ähneln, die effektiver sind?
* Welche Merkmale können aufgrund niedriger Effektivität entfernt werden? Merkmale mit niedriger Effektivität fügen dem maschinellen Lernen _Störungen_ hinzu.
* Gibt es Merkmale, die versehentlich aufgenommen wurden? Beispiele hierfür sind: personenbezogene Informationen (PII), doppelte IDs usw.
* Gibt es unerwünschte Merkmale, die aufgrund gesetzlicher Bestimmungen oder Überlegungen zur verantwortlichen Nutzung nicht für die Personalisierung verwendet werden sollten? Gibt es Merkmale, die Stellvertreter (d. h. eng abbildend oder korrelierend mit) unerwünschte Merkmale sein könnten?


## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren der Personalisierung](how-to-settings.md)
