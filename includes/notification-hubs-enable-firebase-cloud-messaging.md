---
title: include file
description: include file
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: fef6122eceda213fb6353ada53033d0d1e27fd7e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509118"
---
1. Melden Sie sich bei der [Firebase-Konsole](https://firebase.google.com/console/) an. Erstellen Sie ein neues Firebase-Projekt, falls Sie noch keins besitzen.
2. Klicken Sie nach der Erstellung Ihres Projekts auf **Add Firebase to your Android app** (Firebase der Android-App hinzufügen). 

    ![Hinzufügen von Firebase zu Ihrer Android-App](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Führen Sie auf der Seite **Add Firebase to your Android app** (Firebase der Android-App hinzufügen) die folgenden Schritte aus: 
    1. Kopieren Sie für **Android package name** (Name des Android-Pakets) den Wert von **applicationId** in die Datei „build.gradle“ Ihrer Anwendung. In diesem Beispiel lautet er `com.fabrikam.fcmtutorial1app`. 

        ![Angeben des Paketnamens](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Wählen Sie **Register app** (App registrieren) aus. 
4. Wählen Sie **Download google-services.json** (google-services.json herunterladen) aus, speichern Sie die Datei im Ordner **app** des Projekts, und klicken Sie dann auf **Next** (Weiter). 

    ![Herunterladen von „google-services.json“](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Nehmen Sie an Ihrem Projekt in Android Studio die folgenden **Konfigurationsänderungen** vor. 
    1.  Fügen Sie der Datei „build.gradle“ auf Projektebene (&lt;Projekt&gt;/build.gradle) im Abschnitt **dependencies** die folgende Anweisung hinzu. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Fügen Sie der Datei „build.gradle“ auf App-Ebene (&lt;Projekt&gt;/&lt;App-Modul&gt;/build.gradle) im Abschnitt **dependencies** die folgende Anweisung hinzu. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Fügen Sie am Ende der Datei „build.gradle“ auf App-Ebene hinter dem Abschnitt „dependenices“ die folgende Zeile hinzu. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Wählen Sie auf der Symbolleiste **Sync Now*** (Jetzt synchronisieren) aus. 
 
        ![Konfigurationsänderungen an „build.gradle“](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Klicken Sie auf **Weiter**. 
7. Klicken Sie auf **Diesen Schritt überspringen**. 

    ![Den letzten Schritt überspringen](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Klicken Sie in der Firebase-Konsole auf das Zahnrad für Ihr Projekt. Klicken Sie dann auf **Projekteinstellungen**.

    ![Klicken auf „Projekteinstellungen“](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Wenn Sie die Datei „google-services.json“ nicht in den Ordner **app** Ihres Android Studio-Projekts heruntergeladen haben, können Sie dies auf dieser Seite tun. 
5. Wechseln Sie oben zur Registerkarte **Cloud Messaging**. 
6. Kopieren und speichern Sie den **Serverschlüssel** für eine spätere Verwendung. Verwenden Sie diesen Wert zum Konfigurieren Ihres Hubs.
