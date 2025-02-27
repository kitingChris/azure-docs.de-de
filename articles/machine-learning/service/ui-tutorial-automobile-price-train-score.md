---
title: 'Tutorial: Prognostizieren von Automobilpreisen mithilfe der grafischen Benutzeroberfläche'
titleSuffix: Azure Machine Learning service
description: Hier erfahren Sie, wie Sie ein Machine Learning-Modell mithilfe einer grafischen Drag & Drop-Benutzeroberfläche trainieren, bewerten und bereitstellen. Dieses Tutorial ist der erste Teil einer zweiteiligen Reihe über das Prognostizieren von Automobilpreisen mithilfe der linearen Regression.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 21f5a2d93b708e93f124bd44177bb7852dfbd86a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720538"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Tutorial: Prognostizieren von Automobilpreisen mithilfe der grafischen Benutzeroberfläche

In diesem Tutorial befassen Sie sich eingehend mit dem Entwickeln einer Vorhersagelösung mithilfe der grafischen Benutzeroberfläche von Azure Machine Learning Service. Am Ende dieses Tutorials verfügen Sie über eine Lösung, mit der Sie den Preis jedes Autos basierend auf den von Ihnen an die Lösung gesendeten technischen Spezifikationen prognostizieren können.

Im ersten Teil des Tutorials lernen Sie Folgendes:

> [!div class="checklist"]
> * Importieren und Bereinigen von Daten
> * Trainieren eines Machine Learning-Modells
> * Bewerten und Auswerten eines Modells

Im [zweiten Teil](ui-tutorial-automobile-price-deploy.md) des Tutorials erfahren Sie, wie Sie Ihr Vorhersagemodell als Azure-Webdienst bereitstellen.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

Eine abgeschlossene Version dieses Tutorials ist als Beispielexperiment verfügbar.

Wählen Sie auf der Seite **Experimente** die Option **Neu hinzufügen** und anschließend das Experiment **Sample 1 - Regression: Automobile Price Prediction(Basic)** (Beispiel 1: Regression: Automobilpreisvorhersage (einfach)) aus.

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Wenn Sie über einen Azure Machine Learning Service-Arbeitsbereich verfügen, fahren Sie mit dem [nächsten Abschnitt](#open-the-visual-interface-webpage) fort.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Öffnen der Webseite der grafischen Benutzeroberfläche

1. Öffnen Sie Ihren Arbeitsbereich im [Azure-Portal](https://portal.azure.com/).

1. Wählen Sie in Ihrem Arbeitsbereich die Option **Grafische Benutzeroberfläche** aus. Wählen Sie dann **Grafische Benutzeroberfläche starten** aus. 

    ![Screenshot des Azure-Portals, in dem dargestellt ist, wie der Zugriff auf die grafische Benutzeroberfläche über einen Machine Learning Service-Arbeitsbereich erfolgt](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

## <a name="create-your-first-experiment"></a>Erstellen Ihres ersten Experiments

Die grafische Benutzeroberfläche bietet einen interaktiven, visuellen Arbeitsbereich für die Erstellung von Predictive Analytics-Modellen. Fügen Sie Datasets und Analysemodule per Drag & Drop einer interaktiven Canvas hinzu, und verknüpfen Sie sie zu einem *Experiment*.

1. Erstellen Sie ein neues Experiment, indem Sie unten im Fenster der grafischen Benutzeroberfläche die Option **+Neu** auswählen.

    ![Hinzufügen eines neuen Experiments](./media/ui-tutorial-automobile-price-train-score/add-new.png)

1. Wählen Sie **Leeres Experiment** aus.

1. Wählen Sie oben im Canvas-Panel den Standardexperimentnamen **Experimented Created on ...** (Experiment erstellt am ...) aus, und geben Sie einen aussagekräftigen Namen ein. Beispielsweise **Automobile price prediction** (Automobilpreisvorhersage). Der Name muss nicht eindeutig sein.

## <a name="add-data"></a>Hinzufügen von Daten

Grundvoraussetzung für Machine Learning sind Daten. Diese Benutzeroberfläche enthält bereits einige Beispieldatasets, die Sie verwenden können. Alternativ können Sie auch Daten aus bereits vorhandenen Quellen importieren. Verwenden Sie für dieses Tutorial das Beispieldataset **Automobile Price Data (Raw)** (Automobilpreisdaten (Rohdaten)). 

1. Links vom Experimentbereich finden Sie eine Palette mit Datensätzen und Modulen. Wählen Sie **Saved Datasets** (Gespeicherte Datasets) und dann **Samples** (Beispiele) aus, um die verfügbaren Beispieldatasets anzuzeigen.

1. Wählen Sie das Dataset **Automobile Price Data (raw)** aus, und ziehen Sie es in den Experimentbereich.

   ![Ziehen von Daten in den Experimentbereich](./media/ui-tutorial-automobile-price-train-score/drag-data.png)

## <a name="select-columns"></a>Spalten auswählen

Wählen Sie die Datenspalten aus, mit denen Sie arbeiten möchten. Konfigurieren Sie das Modul zunächst so, dass alle verfügbaren Spalten angezeigt werden.

> [!TIP]
> Wenn Sie den Namen der gewünschten Daten oder des Moduls kennen, verwenden Sie die Suchleiste oben in der Palette, um sie schnell zu finden. Dieses Verfahren wird im weiteren Verlauf des Tutorials verwendet.


1. Geben Sie im Suchfeld **Select** (Auswählen) ein, um nach dem Modul **Select Columns in Dataset** (Spalten im Dataset auswählen) zu suchen.

1. Klicken Sie auf **Select Columns in Dataset**, und ziehen Sie das Modul in den Experimentbereich. Legen Sie das Modul unter dem Dataset ab, das Sie zuvor hinzugefügt haben.

1. Verbinden Sie das Dataset mit dem Modul **Select Columns in Dataset**: Klicken Sie auf den Ausgabeport des Datasets, ziehen Sie ihn auf den Eingabeport des Moduls **Select Columns in Dataset**, und lassen Sie die Maustaste dann los. Das Dataset und das Modul bleiben verbunden, auch wenn Sie diese im Bereich verschieben.

    > [!TIP]
    > Datasets und Module verfügen über Ein- und Ausgabe-Anschlüsse, die in Form kleiner Kreise dargestellt werden: Eingabe-Anschlüsse oben und Ausgabe-Anschlüsse unten. Zum Erstellen eines Datenflusses über Ihr Experiment verbinden Sie einen Ausgabeport eines Moduls mit dem Eingabeport eines anderen Moduls.
    >

    ![Verbinden von Modulen](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    Das rote Ausrufezeichen gibt an, dass Sie die Eigenschaften für das Modul noch nicht festgelegt haben.

1. Wählen Sie das Modul **Select Columns in Dataset** aus.

1. Wählen Sie im Bereich **Eigenschaften** rechts neben der Canvas **Spalten bearbeiten** aus.

    Wählen Sie im Dialogfeld **Select columns** (Spalten auswählen) die Option **ALL COLUMNS** (Alle Spalten) aus, und schließen Sie **alle Features** ein. Das Dialogfeld sollte wie folgt aussehen:

     ![Spaltenauswahl](./media/ui-tutorial-automobile-price-train-score/select-all.png)

1. Wählen Sie unten rechts **OK** aus, um die Spaltenauswahl zu schließen.

## <a name="run-the-experiment"></a>Ausführen des Experiments

Sie können zu einem beliebigen Zeitpunkt auf den Ausgabeport eines Datasets oder eines Moduls klicken, um die Daten an diesem Punkt im Datenfluss anzuzeigen. Wenn die Option **Visualize** (Visualisieren) deaktiviert ist, müssen Sie das Experiment zuerst ausführen.

Ein Experiment wird auf einem Computeziel ausgeführt. Dabei handelt es sich um eine an Ihren Arbeitsbereich angefügte Computeressource. Wenn Sie ein Computeziel erstellen, können Sie es für künftige Ausführungen wiederverwenden.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Nachdem das Computeziel verfügbar ist, wird das Experiment ausgeführt. Nach Abschluss der Ausführung wird für die einzelnen Module jeweils ein grünes Häkchen angezeigt.


## <a name="preview-the-data"></a>Vorschau der Daten

Nachdem Sie nun Ihr erstes Experiment durchgeführt haben, können Sie die Daten visualisieren, um mehr über das zu verwendende Dataset zu erfahren.

1. Wählen Sie den Ausgabeport am unteren Rand des Moduls **Select Columns in Dataset** aus, und wählen Sie dann **Visualize** (Visualisieren) aus.

1. Klicken Sie im Datenfenster auf verschiedene Spalten, um Informationen zu dieser Spalte anzuzeigen.

    In diesem Dataset steht jede Zeile für ein Fahrzeug, und die Variablen, die den einzelnen Fahrzeugen zugeordnet sind, werden als Spalten angezeigt. Es gibt 205 Zeilen und 26 Spalten in diesem Dataset.

     Jedes Mal, wenn Sie auf eine Datenspalte klicken, werden links die Informationen **Statistics** (Statistik) und das Bild **Visualization** (Visualisierung) dieser Spalte angezeigt. Wenn Sie beispielsweise auf **num-of-doors** klicken, sehen Sie, dass diese Spalte 2 eindeutige Werte und 2 fehlende Werte aufweist. Scrollen Sie nach unten, um die Werte anzuzeigen: zwei und vier Türen.

     ![Vorschau der Daten](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)

1. Klicken Sie auf jede Spalte, um weitere Informationen zum Dataset zu erhalten, und überlegen Sie, ob die Spalten hilfreich sind, um den Preis eines Autos vorherzusagen.

## <a name="prepare-data"></a>Vorbereiten von Daten

DataSets müssen vor der Analyse normalerweise vorverarbeitet werden. Bei der Visualisierung des Datasets sind Ihnen unter Umständen einige fehlende Werte aufgefallen. Damit das Modell die Daten richtig analysieren kann, müssen diese fehlenden Werte bereinigt werden. Sie entfernen alle Zeilen, in denen Werte fehlen. Außerdem enthält die Spalte **normalized-losses** viele fehlende Werte, daher schließen wir diese Spalte ganz aus dem Modell aus.

> [!TIP]
> Die Bereinigung fehlender Werte aus den Eingabedaten ist eine Voraussetzung für die Verwendung der meisten Module.

### <a name="remove-column"></a>Entfernen der Spalte

Entfernen Sie zunächst die Spalte **normalized-losses** vollständig.

1. Wählen Sie das Modul **Select Columns in Dataset** aus.

1. Wählen Sie im Bereich **Eigenschaften** rechts neben der Canvas **Spalten bearbeiten** aus.

    * Lassen Sie **With rules** (Mit Regeln) und **ALL COLUMNS** (Alle Spalten) ausgewählt.

    * Wählen Sie in den Dropdownlisten die Optionen **Ausschließen** und **Spaltennamen** aus, und klicken Sie auf das Textfeld. Geben Sie **normalized-losses** ein.

    * Wählen Sie unten rechts **OK** aus, um die Spaltenauswahl zu schließen.

    ![Ausschließen einer Spalte](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    Der Eigenschaftenbereich für „Select Columns in Dataset“ zeigt nun an, dass mit Ausnahme von **normalized-losses** alle Spalten des Datasets übergeben werden.
        
    Der Eigenschaftenbereich zeigt, dass die Spalte **normalized-losses** ausgeschlossen wurde.
        
    ![Eigenschaftenbereich](./media/ui-tutorial-automobile-price-train-score/property-pane.png)
        
    Sie können einen Kommentar zu einem Modul eingeben, indem Sie auf das Modul doppelklicken und Text eingeben. Auf diese Weise können Sie mit einem Blick sehen, welche Funktion das Modul in Ihrem Experiment erfüllt. 

1. Doppelklicken Sie auf das Modul **Select Columns in Dataset**, und geben Sie den Kommentar „Exclude normalized losses“ (Normalisierte Verluste ausschließen) ein. 
    
    Nachdem Sie den Kommentar eingeben haben, klicken Sie außerhalb des Moduls. Ein Pfeil nach unten wird angezeigt, um anzugeben, dass das Modul einen Kommentar enthält.

1. Klicken Sie auf den Pfeil nach unten, um den Kommentar anzuzeigen.

    Das Modul zeigt jetzt einen Pfeil nach oben an, mit dem der Kommentar ausgeblendet werden kann.
        
    ![Kommentare](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Fehlende Daten bereinigen

Wenn Sie ein Modell trainieren, müssen Sie etwas bezüglich der fehlenden Daten unternehmen. In diesem Fall fügen Sie ein Modul hinzu, um alle restlichen Zeilen zu entfernen, in denen Daten fehlen.

1. Geben Sie im Suchfeld das Wort **Clean** ein, um nach dem Modul **Clean Missing Data** (Fehlende Daten bereinigen) zu suchen.

1. Ziehen Sie das Modul **Clean Missing Data** in den Experimentbereich, und verbinden Sie es mit dem Modul **Select Columns in Dataset**. 

1. Klicken Sie im Eigenschaftenbereich unter **Cleaning mode** (Bereinigungsmodus) auf die Option **Remove entire row** (Gesamte Zeile entfernen).

    Mit diesen Optionen wird **Clean Missing Data** (Fehlende Daten bereinigen) angewiesen, Daten durch das Entfernen von Zeilen mit fehlenden Werten zu bereinigen.

1. Doppelklicken Sie auf das Modul, und geben Sie den Kommentar "Remove missing value rows" ein.
 
    ![Entfernen von Zeilen](./media/ui-tutorial-automobile-price-train-score/remove-rows.png)

    Ihr Experiment sollte in etwa wie folgt aussehen:
    
    ![select-column](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="visualize-the-results"></a>Visualisieren der Ergebnisse

Da Sie Änderungen an den Modulen in Ihrem Experiment vorgenommen haben, hat sich der Status in „In draft“ (Entwurf) geändert.  Um die neuen bereinigten Daten zu visualisieren, müssen Sie zuerst das Experiment erneut ausführen.

1. Wählen Sie unten **Run** (Ausführen) aus, um das Experiment auszuführen.

    Dieses Mal können Sie das Computeziel wiederverwenden, das Sie zuvor erstellt haben.

1. Wählen Sie **Run**  im Dialogfeld aus.

   ![Ausführen des Experiments](./media/ui-tutorial-automobile-price-train-score/select-compute.png)

1. Wenn die Ausführung abgeschlossen ist, klicken Sie mit der rechten Maustaste auf das Modul **Clean Missing Data**, um die neuen bereinigten Daten zu visualisieren.

    ![Visualisieren bereinigter Daten](./media/ui-tutorial-automobile-price-train-score/visualize-cleaned.png)

1. Klicken Sie auf die verschiedenen Spalten im Fenster mit den bereinigten Daten, um sich anzusehen, wie sich die Daten geändert haben.

    ![Visualisieren bereinigter Daten](media/ui-tutorial-automobile-price-train-score/visualize-result.png)

    Es gibt jetzt 193 Zeilen und 25 Spalten.

    Wenn Sie auf **num-of-doors** klicken, sehen Sie, dass diese Spalte noch immer 2 eindeutige Werte aufweist, aber 0 fehlende Werte. Klicken Sie auf die restlichen Spalten, um zu überprüfen, ob das Dataset noch fehlende Daten aufweist. 

## <a name="train-the-model"></a>Modelltraining

Da die Daten jetzt vorbereitet wurden, können Sie ein Vorhersagemodell erstellen. Zum Trainieren des Modells verwenden Sie Ihre Daten. Dann testen Sie das Modell, um festzustellen, wie genau die Preisvorhersagen sind.

**Klassifizierung** und **Regression** sind zwei Algorithmen für beaufsichtigtes maschinelles Lernen. Mit der **Klassifizierung** wird eine Antwort aus einem definierten Satz von Kategorien vorhergesagt, z. B. eine Farbe (Rot, Blau oder Grün). Die **Regression** wird verwendet, um eine Zahl vorherzusagen.

Da Sie einen Preis (also eine Zahl) vorhersagen möchten, können Sie einen Regressionsalgorithmus verwenden. In diesem Beispiel verwenden Sie ein lineares Regressionsmodell.

Trainieren Sie das Modell, indem Sie einen Datensatz mit dem Preis einspeisen. Das Modell überprüft die Daten und sucht nach Korrelationen zwischen den Features eines Autos und seinem Preis. Testen Sie dann das Modell, indem Sie einen Satz von Features und Funktionen für bekannte Automobile eingeben und prüfen, wie genau das Modell den bekannten Preis vorhersagt.

Verwenden Sie Ihre Daten sowohl zum Trainieren als auch zum Testen des Modells, indem Sie die Daten in separate Trainings- und Testdatasets aufteilen.

1. Geben Sie im Suchfeld die Wörter **split data** (Daten aufteilen) ein, um nach dem Modul **Split Data** (Daten aufteilen) zu suchen, und verbinden Sie es mit dem linken Port des Moduls **Clean Missing Data** (Fehlende Daten bereinigen).

1. Wählen Sie das Modul **Split Data** (Daten aufteilen) aus, das Sie eben verbunden haben. Legen Sie im Bereich „Properties“ (Eigenschaften) den Anteil der Zeilen im ersten Ausgabedatensatz auf „0,7“ fest. Mit dieser Einstellung verwenden wir 70 Prozent der Daten zum Trainieren des Modells und halten 30 Prozent für Tests zurück.

    ![Screenshot der richtigen Konfiguration des Eigenschaftenbereichs. Für „Split Data“ (Daten aufteilen) sollten die Werte „Split Rows“ (Zeilen aufteilen), „0,7“, „Randomized split“ (Zufällige Aufteilung), „0“ und „False“ (Falsch) verwendet werden.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. Doppelklicken Sie auf **Split Data** (Daten aufteilen), und geben Sie den Kommentar „Split the dataset into training set(0.7) and test set(0.3)“ (Dataset in Trainingssatz (0,7) und Testsatz (0,3) aufteilen) ein.

1. Löschen Sie zum Auswählen des Lernalgorithmus das Suchfeld der Modulpalette.

1. Erweitern Sie **Machine Learning**, und erweitern Sie dann **Initialize Model** (Modell initialisieren). Daraufhin werden verschiedene Kategorien von Modulen angezeigt, die zur Initialisierung eines Algorithmus für maschinelles Lernen verwendet werden können.

1. Wählen Sie für dieses Experiment die Optionen **Regression** > **Linear Regression** (Lineare Regression) aus, und ziehen Sie das Modul in den Experimentbereich.

    ![Screenshot der richtigen Konfiguration des Eigenschaftenbereichs. Für „Split Data“ (Daten aufteilen) sollten die Werte „Split Rows“ (Zeilen aufteilen), „0,7“, „Randomized split“ (Zufällige Aufteilung), „0“ und „False“ (Falsch) verwendet werden.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. Suchen Sie nach dem Modul **Train Model** (Modell trainieren), und ziehen Sie es in den Experimentbereich. Verbinden Sie die Ausgabe des Moduls „Linear Regression“ mit der linken Eingabe des Moduls „Train Model“, und verbinden Sie die Ausgabe der Trainingsdaten (den linken Port) des Moduls **Split Data** mit der rechten Eingabe des Moduls **Train Model**.

    ![Screenshot der richtigen Konfiguration des Moduls „Train Model“. Das Modul „Linear Regression“ ist mit dem linken Port des Moduls „Train Model“ verbunden, und das Modul „Split Data“ ist mit dem rechten Port von „Train Model“ verbunden.](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Wählen Sie das Modul **Train Model** (Modell trainieren) aus. Wählen Sie im Bereich „Properties“ (Eigenschaften) die Option „Launch column selector“ (Spaltenauswahl starten) aus, und geben Sie dann neben **Include column names** (Spaltennamen einschließen) das Wort **price** (Preis) ein. „Price“ ist der Wert, den unser Modell vorhersagen wird.

    ![Screenshot der richtigen Konfiguration des Moduls für die Spaltenauswahl. With rules (Mit Regeln) > Include column names (Spaltennamen einschließen) > „price“ (Preis)](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    Ihr Experiment sollte wie folgt aussehen:

    ![Screenshot der richtigen Konfiguration des Experiments nach dem Hinzufügen des Moduls „Train Model“ (Modell trainieren)](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Ausführen des Trainingsexperiments

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Bewerten und Auswerten des Modells

Nachdem Sie das Modell mit 70 Prozent Ihrer Daten trainiert haben, können Sie unter Verwendung der restlichen 30 Prozent der Daten bewerten, wie gut das Modell funktioniert.

1. Geben Sie im Suchfeld die Wörter **score model** (Modell bewerten) ein, um nach dem Modul **Score Model** (Modell bewerten) zu suchen, und ziehen Sie das Modul in den Experimentbereich. Verbinden Sie die Ausgabe des Moduls **Train Model** mit dem linken Eingabeport des Moduls **Score Model**. Verbinden Sie die Testdatenausgabe (den rechten Port) des Moduls **Split Data** mit dem rechten Eingabeport des Moduls **Score Model**.

1. Geben Sie im Suchfeld das Wort **evaluate** (auswerten) ein, um nach dem Modul **Evaluate Model** (Modell auswerten) zu suchen, und ziehen Sie das Modul in den Experimentbereich. Verbinden Sie die Ausgabe des Moduls **Score Model** mit der linken Eingabe des Moduls **Evaluate Model**. Das endgültige Experiment sollte in etwa wie folgt aussehen:

    ![Screenshot der endgültigen richtigen Konfiguration des Experiments.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Führen Sie das Experiment mit dem gleichen Computeziel aus, das Sie zuvor verwendet haben.

1. Zeigen Sie die Ausgabe des Moduls **Score Model** an, indem Sie den Ausgabeport des Moduls **Score Model** und dann **Visualize** (Visualisieren) auswählen. Die Ausgabe zeigt die vorhergesagten Preiswerte zusammen mit den bekannten Werten aus den Testdaten an.

    ![Screenshot der Ausgabevisualisierung mit hervorgehobener Spalte „Scored Labels“](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Um die Ausgabe des Moduls „Evaluate Model“ anzuzeigen, wählen Sie den Ausgabeport aus, und wählen Sie dann „Visualize“ (Visualisieren) aus.

    ![Screenshot der Auswertungsergebnisse für das endgültige Experiment.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

Die folgenden Statistiken werden für Ihr Modell angezeigt:

* **Mean Absolute Error (MAE)** (Mittlerer absoluter Fehler): Der Mittelwert der absoluten Fehler (ein Fehler ist die Differenz zwischen dem prognostizierten und dem tatsächlichen Wert).
* **Root Mean Squared Error (RMSE)** (Wurzel des mittleren quadratischen Fehlers): Die Quadratwurzel des Durchschnitts des Quadrats der Prognosefehler für das Testdataset.
* **Relative Absolute Error**: Der Mittelwert der absoluten Fehler relativ zur absoluten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
* **Relative Squared Error**: Der Durchschnitt der quadrierten Fehler relativ zur quadrierten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
* **Coefficient of Determination**: Dieser auch als „R-Quadrat“ bezeichnete Wert ist eine statistische Kenngröße, die angibt, wie gut ein Modell zu den Daten passt.

Für jede Fehlerstatistik sind kleinere Werte besser. Ein kleinerer Wert gibt an, dass die Vorhersagen genauer mit den tatsächlichen Werten übereinstimmen. Für den Bestimmungskoeffizienten gilt Folgendes: Je näher der Bestimmungskoeffizient am Wert eins (1,0) liegt, desto besser die Vorhersage.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Verwalten von Experimenten im Azure Machine Learning Service-Arbeitsbereich

Die von Ihnen auf der grafischen Benutzeroberfläche erstellten Experimente können im Azure Machine Learning Service-Arbeitsbereich verwaltet werden. Verwenden Sie den Arbeitsbereich, um detailliertere Informationen anzuzeigen, z. B. einzelne Experimentausführungen, Diagnoseprotokolle, Ausführungsdiagramme und mehr.

1. Öffnen Sie Ihren Arbeitsbereich im [Azure-Portal](https://portal.azure.com/).  

1. Wählen Sie in Ihrem Arbeitsbereich die Option **Experiments** (Experimente) aus. Wählen Sie dann das Experiment aus, das Sie erstellt haben.

    ![Screenshot, der zeigt, wie Sie im Azure-Portal zu Experimenten navigieren](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    Auf dieser Seite werden eine Übersicht über das Experiment und die letzten Ausführungen angezeigt.

    ![Screenshot einer Übersicht über die Experimentstatistik im Azure-Portal](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. Wählen Sie eine Ausführungsnummer aus, um weitere Details zu einer bestimmten Ausführung anzuzeigen.

    ![Screenshot eines detaillierten Ausführungsberichts](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    Der Ausführungsbericht wird in Echtzeit aktualisiert. Wenn Sie in Ihrem Experiment das Modul **Execute Python Script** (Python-Skript ausführen) verwendet haben, können Sie die auszugebenden Skriptprotokolle auf der Registerkarte **Logs** (Protokolle) angeben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Im ersten Teil dieses Tutorial haben Sie die folgenden Schritte ausgeführt:

* Erstellen eines Experiments
* Vorbereiten der Daten
* Modelltraining
* Bewerten und Auswerten des Modells

Im zweiten Teil erfahren Sie, wie Sie Ihr Modell als Azure-Webdienst bereitstellen.

> [!div class="nextstepaction"]
> [Mit dem Bereitstellen von Modellen fortfahren](ui-tutorial-automobile-price-deploy.md)
