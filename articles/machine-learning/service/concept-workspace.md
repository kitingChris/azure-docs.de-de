---
title: Was ist ein Arbeitsbereich?
titleSuffix: Azure Machine Learning service
description: Der Arbeitsbereich ist die Ressource der obersten Ebene für Azure Machine Learning Service. Der Verlauf aller Trainingsläufe wird gespeichert, z. B. Protokolle, Metriken, Ausgabe und eine Momentaufnahme Ihrer Skripts. Anhand dieser Informationen ermitteln Sie, welcher Trainingslauf das beste Modell ergibt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 912c064fb5ca4e7ca311f60ed04a0122809cb0ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442373"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>Was ist ein Azure Machine Learning Service-Arbeitsbereich?

Der Arbeitsbereich ist die Ressource der obersten Ebene für Azure Machine Learning Service und ein zentraler Ort für die Arbeit mit allen Artefakten, die Sie während der Nutzung von Azure Machine Learning Service erstellen.  Im Arbeitsbereich wird der Verlauf aller Trainingsläufe gespeichert, einschließlich Protokollen, Metriken, Ausgabe und einer Momentaufnahme Ihrer Skripts. Anhand dieser Informationen ermitteln Sie, welcher Trainingslauf das beste Modell ergibt.  

Sobald Sie ein fertiges Modell haben, registrieren Sie es im Arbeitsbereich. Anschließend verwenden Sie das registrierte Modell und die Bewertungsskripts, um das Modell in Azure Container Instances, in Azure Kubernetes Service oder in einem FPGA (Field Programmable Gate Array) als REST-basierten HTTP-Endpunkt bereitzustellen. Sie können das Modell auch als Modul auf einem Azure IoT Edge-Gerät bereitstellen.

## <a name="taxonomy"></a>Taxonomie 

Das folgende Diagramm enthält eine Taxonomie des Arbeitsbereichs:

[![Taxonomie des Arbeitsbereichs](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

In dem Diagramm sind die folgenden Komponenten eines Arbeitsbereichs dargestellt:

+ Ein Arbeitsbereich kann [Notebook VMs](quickstart-run-cloud-notebook.md) enthalten. Diese sind Cloudressourcen, die mit der Python-Umgebung konfiguriert sind, die zum Ausführen von Azure Machine Learning erforderlich ist.
+ [Benutzerrollen](how-to-assign-roles.md) ermöglichen es Ihnen, Ihren Arbeitsbereich für andere Benutzer, Teams oder Projekte freizugeben.
+ [Computeziele](concept-azure-machine-learning-architecture.md#compute-targets) werden verwendet, um Ihre Experimente auszuführen.
+ Wenn Sie den Arbeitsbereich erstellen, werden auch [zugeordnete Ressourcen](#resources) für Sie erstellt.
+ [Experimente](concept-azure-machine-learning-architecture.md#experiments) sind Trainingsläufe (Trainingsausführungen), mit denen Sie Ihre Modelle erstellen.  Experimente können Sie erstellen und ausführen über
    + Das [Azure Machine Learning SDK für Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + Den Abschnitt für [automatisierte Machine Learning-Experimente (Vorschau)](how-to-create-portal-experiments.md) im Azure-Portal.
    + Die [grafische Benutzeroberfläche (Vorschau)](ui-concept-visual-interface.md).
+ [Pipelines](concept-azure-machine-learning-architecture.md#ml-pipelines) sind wiederverwendbare Workflows zum Trainieren und erneuten Trainieren Ihres Modells.
+ [Datasets](concept-azure-machine-learning-architecture.md#datasets-and-datastores) vereinfachen die Verwaltung der Daten, die Sie zum Modelltraining und zur die Pipelineerstellung verwenden.
+ Nachdem Sie über ein Modell verfügen, das Sie bereitstellen möchten, können Sie ein registriertes Modell erstellen.
+ Verwenden Sie das registrierte Modell und ein Bewertungsskript, um eine [Bereitstellung](concept-azure-machine-learning-architecture.md#deployment) zu erstellen.

## <a name="tools-for-workspace-interaction"></a>Tools zum Arbeiten mit Ihrem Arbeitsbereich

Sie können auf folgende Arten mit Ihrem Arbeitsbereich arbeiten:

+ Im Web:
    + Das [Azure-Portal](https://portal.azure.com)
    + Die [grafische Benutzeroberfläche (Vorschau)](ui-concept-visual-interface.md)
+ In Python über das Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Über die Befehlszeile mit der [CLI-Erweiterung](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli) für Azure Machine Learning

## <a name="machine-learning-with-a-workspace"></a>Maschinelles Lernen mit einem Arbeitsbereich

Für Aufgaben für maschinelles Lernen werden Artefakte aus Ihrem Arbeitsbereich gelesen oder in diesen geschrieben. 

+ Ausführen eines Experiments, um ein Modell zu trainieren: Die Ergebnisse der Experimentausführung werden in den Arbeitsbereich geschrieben.
+ Verwenden von automatisiertem ML, um ein Modell zu trainieren: Die Trainingsergebnisse werden in den Arbeitsbereich geschrieben.
+ Registrieren eines Modells im Arbeitsbereich.
+ Bereitstellen eines Modells: Das registrierte Modell wird verwendet, um eine Bereitstellung zu erstellen.
+ Erstellen und Ausführen von wiederverwendbaren Workflows.
+ Anzeigen von Machine Learning-Artefakten, z. B. Experimente, Pipelines, Modelle, Bereitstellungen.
+ Nachverfolgen und Überwachen von Modellen.

## <a name="workspace-management"></a>Arbeitsbereichsverwaltung

Sie können auch die folgenden Arbeitsbereichsverwaltungsaufgaben ausführen:

| Arbeitsbereichsverwaltungsaufgabe   | Portal              | SDK        | Befehlszeilenschnittstelle (CLI)        |
|---------------------------|------------------|------------|------------|
| Erstellen eines Arbeitsbereichs        | **&check;**     | **&check;** | **&check;** |
| Erstellen und Verwalten von Computeressourcen    | **&check;**   | **&check;** |  **&check;**   |
| Verwalten des Arbeitsbereichszugriffs    | **&check;**   | |  **&check;**    |
| Erstellen einer Notebook-VM | **&check;**   | |     |

Führen Sie erste Schritte mit dem Dienst aus, indem Sie [einen Arbeitsbereich erstellen](setup-create-workspace.md).

## <a name="resources"></a> Zugeordnete Ressourcen

Wenn Sie einen neuen Arbeitsbereich erstellen, werden darin automatisch mehrere Azure-Ressourcen erstellt, die vom Arbeitsbereich verwendet werden:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Registriert die Docker-Container, die Sie während des Trainings und bei der Modellbereitstellung verwenden. Um Kosten zu minimieren, wird ACR **verzögert geladen**, bis Bereitstellungsimages erstellt sind.
+ [Azure Storage-Konto](https://azure.microsoft.com/services/storage/): Wird als Standarddatenspeicher für den Arbeitsbereich verwendet.  Hier werden auch Jupyter-Notebooks gespeichert, die mit Ihren Notebook-VMs verwendet werden.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Speichert Überwachungsinformationen zu Ihren Modellen.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Speichert Geheimnisse, die von Computezielen verwendet werden, sowie andere vertrauliche Informationen, die vom Arbeitsbereich benötigt werden.

> [!NOTE]
> Neben dem Erstellen neuer Versionen können Sie auch vorhandene Azure-Dienste verwenden.

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit Azure Machine Learning Service finden Sie unter:

+ [Was ist der Azure Machine Learning-Dienst?](overview-what-is-azure-ml.md)
+ [Erstellen eines Arbeitsbereichs](setup-create-workspace.md)
+ [Verwalten eines Arbeitsbereichs](how-to-manage-workspace.md)
+ [Tutorial: Trainieren eines Bildklassifizierungsmodells mit dem Azure Machine Learning-Dienst](tutorial-train-models-with-aml.md)
