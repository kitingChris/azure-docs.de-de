---
title: Herstellen einer Verbindung mit dem FTP-Server – Azure Logic Apps
description: Erstellen, Überwachen und Verwalten von Dateien auf einem FTP-Server mit Azure-Logik-Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 66f1d726dcfa1a077abbff0d9f028036db43cc25
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293095"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Erstellen, Überwachen und Verwalten von FTP-Dateien mithilfe von Azure Logic Apps

Mit Azure Logic Apps und dem FTP-Connector können Sie automatisierte Aufgaben und Workflows erstellen, die Dateien über Ihr Konto auf einem FTP-Server erstellen, überwachen, senden und empfangen sowie weitere Aktionen ausführen, z. B. folgende:

* Sie können die Vorgänge überwachen, wenn Dateien hinzugefügt oder geändert werden.
* Sie können Dateien abrufen, erstellen, kopieren, aktualisieren, auflisten und löschen.
* Sie können Dateiinhalte und Metadaten abrufen.
* Sie können Archive in Ordner extrahieren.

Sie können Trigger verwenden, die Antworten von Ihrem FTP-Server erhalten und die Ausgabe für andere Aktionen verfügbar machen. Sie können Ausführungsaktionen in Ihren Logik-Apps verwenden, um Dateien auf Ihrem FTP-Server zu verwalten. Sie können die Ausgaben von FTP-Aktionen auch in anderen Aktionen verwenden. Wenn Sie z.B. regelmäßig Dateien von Ihrem FTP-Server abrufen, können Sie mithilfe des Office 365 Outlook-Connectors oder mithilfe des Outlook.com-Connectors E-Mails zu diesen Dateien und ihren Inhalten senden. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="limits"></a>Einschränkungen

* Der FTP-Connector unterstützt nur explizites FTP über SSL (FTPS) und ist nicht kompatibel mit implizitem FTPS.

* Standardmäßig können FTP-Aktionen Dateien mit einer Größe von *50 MB oder kleiner* lesen oder schreiben. FTP-Aktionen unterstützen die [Nachrichtensegmentierung](../logic-apps/logic-apps-handle-large-messages.md), um Dateien zu verarbeiten, die größer als 50 MB sind. Die Aktion **Get file content** (Dateiinhalt abrufen) verwendet implizit die Nachrichtensegmentierung.

* FTP-Trigger unterstützen keine Segmentierung. Trigger wählen beim Anfordern von Dateiinhalten nur Dateien aus, die 50 MB oder kleiner sind. Befolgen Sie das folgende Muster, um Dateien abzurufen, die größer als 50 MB sind:

  * Verwenden Sie einen FTP-Trigger, der Dateieigenschaften wie beispielsweise **When a file is added or modified (properties only) (Wenn eine Datei hinzugefügt oder geändert wird (nur Eigenschaften))** .

  * Auf den Trigger muss die FTP-Aktion **Get file content** (Dateiinhalt abrufen) folgen, die die vollständige Datei liest und implizit Segmentierung verwendet.

## <a name="how-ftp-triggers-work"></a>Funktionsweise von FTP-Triggern

Die FTP-Trigger rufen das FTP-Dateisystem ab und suchen nach jeder Datei, die seit dem letzten Abruf geändert wurde. Bei einigen Tools können Sie den Zeitstempel beibehalten, wenn sich die Dateien ändern. In diesen Fällen müssen Sie diese Funktion deaktivieren, sodass der Trigger arbeiten kann. Hier sind einige gängige Einstellungen:

| SFTP-Client | Aktion |
|-------------|--------|
| Winscp | Navigieren Sie zu **Optionen** > **Voreinstellungen** > **Übertragen** > **Bearbeiten** > **Zeitstempel beibehalten** > **Deaktivieren**. |
| FileZilla | Wechseln Sie zu **Übertragung** > **Änderungszeitpunkt der übertragenen Dateien beibehalten** > **Deaktivieren**. |
|||

Wenn ein Trigger eine neue Datei findet, überprüft er, ob die neue Datei vollständig ist und nicht nur teilweise geschrieben wurde. Zum Beispiel werden bei einer Datei möglicherweise gerade Änderungen vorgenommen, wenn der Trigger den Dateiserver überprüft. Um zu vermeiden, dass eine nur zum Teil geschriebene Datei zurückgegeben wird, vermerkt der Trigger den Zeitstempel für die Datei mit den kürzlichen Änderungen, gibt diese Datei jedoch nicht sofort zurück. Der Trigger gibt die Datei erst dann zurück, wenn der Server erneut abgerufen wird. Dieses Verhalten kann in manchen Fällen zu Verzögerungen führen, die bis zu zweimal länger als das Abrufintervall des Triggers sein können.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Die Adresse Ihres FTP-Hostservers und die zugehörigen Anmeldeinformationen.

  Der FTP-Connector erfordert, dass Ihr FTP-Server über das Internet erreichbar ist und für den Betrieb im Modus *Passiv* eingerichtet ist. Über Ihre Anmeldeinformationen kann mit Ihrer Logik-App eine Verbindung mit Ihrem FTP-Konto hergestellt sowie auf das Konto zugegriffen werden.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr FTP-Konto zugreifen möchten. Um mit einem FTP-Trigger zu beginnen, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine FTP-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem anderen Trigger, z. B. dem **Wiederholungstrigger**.

## <a name="connect-to-ftp"></a>Herstellen einer FTP-Verbindung

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Geben Sie im Fall einer leeren Logik-App im Suchfeld die Zeichenfolge „ftp“ als Filter ein. Wählen Sie in der Triggerliste den gewünschten Trigger aus.

   Oder

   Wählen Sie für vorhandene Logik-Apps im letzten Schritt zum Hinzufügen einer Aktion die Option **Neuer Schritt** und dann **Aktion hinzufügen** aus. Geben Sie im Suchfeld den Begriff „ftp“ als Filter ein. Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) aus, und klicken Sie auf **Aktion hinzufügen**.

1. Geben Sie die erforderlichen Informationen zu Ihrer Verbindung ein, und wählen Sie dann **Erstellen** aus.

1. Geben Sie die erforderlichen Details für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="examples"></a>Beispiele

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>FTP-Trigger: When a file is added or modified (Wenn eine Datei hinzugefügt oder geändert wird)

Dieser Trigger startet einen Logik-App-Workflow, wenn er erkennt, dass eine Datei auf einem FTP-Server hinzugefügt oder geändert wurde. Sie können z.B. eine Bedingung hinzufügen, die den Inhalt der Datei überprüft und entscheidet, ob dieser Inhalt abgerufen wird. Die Entscheidung basiert darauf, ob der Inhalt eine bestimmte Bedingung erfüllt oder nicht. Zum Schluss können Sie eine Aktion hinzufügen, die den Inhalt der Datei abruft und in einem Ordner auf dem SFTP-Server ablegt.

**Beispiel für Unternehmen**: Sie können mit diesem Trigger beispielsweise einen FTP-Ordner auf neue Dateien überwachen, in denen Kundenbestellungen beschrieben werden. Dann können Sie eine FTP-Aktion wie **Dateiinhalt abrufen** verwenden, um den Inhalt einer Bestellung zur weiteren Verarbeitung und Speicherung in einer Bestelldatenbank abzurufen.

Das folgende Beispiel zeigt diesen Trigger: **Wenn eine Datei hinzugefügt oder geändert wird**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Geben Sie im Fall einer leeren Logik-App im Suchfeld die Zeichenfolge „ftp“ als Filter ein. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Wenn eine Datei hinzugefügt oder geändert wird – FTP**

   ![Suchen und Auswählen des FTP-Triggers](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Geben Sie die erforderlichen Informationen zu Ihrer Verbindung ein, und wählen Sie dann **Erstellen** aus.

   Standardmäßig werden mit diesem Connector Dateien im Textformat übertragen. Wählen Sie beispielsweise in Fällen mit Codierung **Binary Transport** (Binäre Übertragung) aus, um Dateien im Binärformat zu übertragen.

   ![Erstellen einer FTP-Serververbindung](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Wählen Sie neben dem Feld **Ordner** das Ordnersymbol aus, damit eine Liste angezeigt wird. Um den Ordner zu finden, den Sie auf neue oder bearbeitete Dateien überwachen möchten, wählen Sie den Pfeil nach rechts ( **>** ) aus, navigieren zu diesem Ordner, und wählen Sie dann den Ordner aus.

   ![Suchen und Auswählen des zu überwachenden Ordners](./media/connectors-create-api-ftp/select-folder.png)  

   Der ausgewählte Ordner wird im Feld **Ordner** angezeigt.

   ![Ausgewählter Ordner](./media/connectors-create-api-ftp/selected-folder.png)  

Nachdem Ihre Logik-App jetzt über einen Trigger verfügt, fügen Sie die Aktionen hinzu, die Sie ausführen möchten, wenn Ihre Logik-App eine neue oder bearbeitete Datei findet. In diesem Beispiel können Sie eine FTP-Aktion hinzufügen, die den neuen oder aktualisierten Inhalt abruft.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>FTP-Aktion: Inhalte abrufen

Diese Aktion ruft den Inhalt einer Datei auf einem FTP-Server ab, wenn diese Datei hinzugefügt oder aktualisiert wird. So können Sie z. B. den Trigger aus dem vorherigen Beispiel und eine Aktion hinzufügen, die den Inhalt der Datei abruft, nachdem diese Datei hinzugefügt oder bearbeitet wurde.

Hier sehen Sie ein Beispiel für diese Aktion: **Abrufen von Inhalten**

1. Wählen Sie unter dem Trigger oder anderen Aktionen **Neuer Schritt** aus.

1. Geben Sie im Suchfeld den Begriff „ftp“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Dateiinhalte abrufen - FTP**

   ![Auswählen einer FTP-Aktion](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Wenn Sie bereits über eine Verbindung zu Ihrem FTP-Server und Konto verfügen, wechseln Sie zum nächsten Schritt. Andernfalls stellen Sie die erforderlichen Informationen zu dieser Verbindung bereit, und wählen Sie dann **Erstellen** aus:

   ![Erstellen einer FTP-Serververbindung](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Nachdem die Aktion **Dateiinhalt abrufen** geöffnet wurde, klicken Sie in das Feld **Datei**, sodass die dynamische Inhaltsliste angezeigt wird. Sie können jetzt Eigenschaften für die Ausgaben aus den vorherigen Schritten auswählen. Wählen Sie in der Liste der dynamischen Inhalte die Eigenschaft **Dateiinhalt** aus, die den Inhalt für die hinzugefügte oder aktualisierte Datei enthält.  

   ![Suchen und Auswählen der Datei](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   Die Eigenschaft **Dateiinhalt** wird jetzt im Feld **Datei** angezeigt.

   ![Ausgewählte „Dateiinhalt“-Eigenschaft](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Speichern Sie Ihre Logik-App. Um Ihren Workflow zu testen, fügen Sie eine Datei zu dem FTP-Ordner hinzu, den Ihre Logik-App jetzt überwacht.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite des Connectors](/connectors/ftpconnector/).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
