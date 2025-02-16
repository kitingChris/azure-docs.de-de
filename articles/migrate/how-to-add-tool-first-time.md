---
title: Erstes Hinzufügen eines Bewertungs-/Migrationstools in Azure Migrate| Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie ein Azure Migrate-Projekt erstellt und ein Bewertungs-/Migrationstool hinzufügen.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b226f7c5879673b573133cde45db78d8d1f2fffa
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810289"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Erstes Hinzufügen eines Bewertungs-/Migrationstools

In diesem Artikel wird beschrieben, wie Sie zum ersten Mal ein Bewertungs- oder Migrationstool zu einem [Azure Migrate](migrate-overview.md)-Projekt hinzufügen.  
Azure Migrate ist ein zentraler Hub zum Nachverfolgen der Ermittlung, Bewertung und Migration Ihrer lokalen Apps und Workloads sowie von VMs in der privaten/öffentlichen Cloud zu Azure. Der Hub stellt Azure Migrate-Tools für die Bewertung und Migration sowie [Angebote](migrate-services-overview.md#isv-integration) von unabhängigen Drittanbietern (Independent Software Vendors, ISVs) bereit. 

## <a name="create-a-project-and-add-a-tool"></a>Erstellen eines Projekts und Hinzufügen eines Tools

Richten Sie ein neues Azure Migrate-Projekt in einem Azure-Abonnement ein, und fügen Sie ein Tool hinzu.

- Ein Azure Migrate-Projekt wird zum Speichern von Ermittlungs-, Bewertungs- und Migrationsmetadaten verwendet, die in der von Ihnen bewerteten oder migrierten Umgebung gesammelt werden. 
- In einem Projekt können Sie ermittelte Objekte nachverfolgen und die Bewertung und Migration orchestrieren.

1. Wählen Sie im Azure-Portal **Alle Dienste** aus, und suchen Sie nach **Azure Migrate**.
2. Wählen Sie unter **Dienste** die Option **Azure Migrate** aus.

    ![Einrichten von Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Klicken Sie in der **Übersicht** auf **Server bewerten und migrieren**.
4. Klicken Sie unter **Server ermitteln, bewerten und migrieren** auf **Server bewerten und migrieren**.

    ![Ermitteln und Bewerten von Servern](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Klicken Sie unter **Server ermitteln, bewerten und migrieren** auf **Tools hinzufügen**.
2. Wählen Sie unter **Projekt migrieren** Ihr Azure-Abonnement aus, und erstellen Sie bei Bedarf eine Ressourcengruppe.
3. Geben Sie unter **Projektdetails** den Projektnamen und die geografische Region an, in der Sie das Projekt erstellen möchten. 

    ![Erstellen eines Azure Migrate-Projekts](./media/how-to-add-tool-first-time/migrate-project.png)

    Sie können Azure Migrate-Projekte in den unten angegebenen geografischen Regionen erstellen.

    **Geografie** | **Region des Speicherorts**
    --- | ---
    Asien | „Asien, Südosten“ und „Asien, Osten“
    Europa | „Europa, Süden“ oder „Europa, Westen“
    Vereinigtes Königreich | „Vereinigtes Königreich, Süden“ oder „Vereinigtes Königreich, Westen“
    USA | „USA, Mitte“ oder „USA, Westen 2“

    Die für das Projekt angegebene Region wird nur zum Speichern der Metadaten verwendet, die von den lokalen VMs erfasst werden. Bei der tatsächlichen Migration kann eine beliebige Zielregion ausgewählt werden.

4. Klicken Sie auf **Weiter**, und fügen Sie ein Bewertungs- oder Migrationstool hinzu.

    > [!NOTE]
    > Wenn Sie ein Projekt erstellen, müssen Sie mindestens ein Bewertungs- oder Migrationstool hinzufügen.

5. Fügen Sie unter **Bewertungstool auswählen** ein Bewertungstool hinzu. Wenn Sie kein Bewertungstool benötigen, wählen Sie die Option **Hinzufügen eines Bewertungstools vorerst überspringen** > **Weiter** aus. 
2. Fügen Sie unter **Migrationstool auswählen** nach Bedarf ein Migrationstool hinzu. Wenn Sie zum jetzigen Zeitpunkt kein Migrationstool benötigen, wählen Sie die Option **Hinzufügen eines Migrationstools vorerst überspringen** > **Weiter** aus.
3. Überprüfen Sie die Einstellungen unter **Überprüfen + Tools hinzufügen**, und klicken Sie auf **Tools hinzufügen**.

Nachdem Sie das Projekt erstellt haben, können Sie zusätzliche Tools für Bewertung und Migration von Servern, Workloads, Datenbanken und Web-Apps auswählen.

## <a name="create-additional-projects"></a>Erstellen zusätzlicher Projekte

Unter bestimmten Umständen müssen Sie möglicherweise zusätzliche Azure Migrate-Projekte erstellen. Beispielsweise, wenn Sie über Rechenzentren in unterschiedlichen geografischen Regionen verfügen oder wenn Sie Metadaten in einer anderen geografischen Region speichern müssen. Erstellen Sie wie folgt ein zusätzliches Projekt:

1. Klicken Sie im Azure Migrate-Projekt auf **Server** oder **Datenbanken**.
2. Klicken Sie in der oberen rechten Ecke neben dem Namen des aktuellen Projekts auf **Ändern**.
3. Wählen Sie unter **Einstellungen** die Option **Hier klicken, um ein neues Projekt zu erstellen** aus.
4. Erstellen Sie ein neues Projekt, und fügen Sie ein neues Tool hinzu, wie im vorherigen Verfahren beschrieben.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie zusätzliche Tools für [Bewertung](how-to-assess.md) und [Migration](how-to-migrate.md) hinzufügen. 
