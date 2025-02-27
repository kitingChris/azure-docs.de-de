---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: e8daaaf5b6b15eb3095f11e94c707a33b4b18e28
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177778"
---
Wählen Sie **Build** aus. Wählen Sie im dann geöffneten Dialogfeld einen Ordner aus, in den das Xcode-Projekt exportiert wird.

Nach Abschluss des Exports wird ein Ordner angezeigt, der das exportierte Xcode-Projekt enthält.

> [!NOTE]
> Wenn ein Fenster mit der Auswahl zwischen „Ersetzen“ und „Anfügen“ angezeigt wird, empfiehlt es sich, **Anfügen** auszuwählen, da dies schneller ist. Die Auswahl von **Ersetzen** sollte nur erforderlich sein, wenn Sie Ressourcen in Ihrer Szene ändern (wenn Sie beispielsweise Beziehungen zwischen übergeordneten und untergeordneten Elementen hinzufügen, entfernen oder ändern oder wenn Sie Eigenschaften hinzufügen, entfernen oder ändern). Wenn Sie lediglich Änderungen am Quellcode vornehmen, sollte **Anfügen** ausreichend sein.

### <a name="open-the-xcode-project"></a>Öffnen des Xcode-Projekts

Führen Sie im Ordner des exportierten Xcode-Projekts den folgenden Befehl im Terminal aus, um die erforderlichen CocoaPods für das Projekt zu installieren:

```bash
pod install --repo-update
```

Nun können Sie `Unity-iPhone.xcworkspace` öffnen, um das Projekt in Xcode zu öffnen:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Sollte ein Fehler vom Typ `library not found for -lPods-Unity-iPhone` auftreten, haben Sie wahrscheinlich anstelle der Datei vom Typ `.xcworkspace` die Datei vom Typ `.xcodeproj` geöffnet. 

Wählen Sie den Hauptknoten **Unity-iPhone** aus, um die Projekteinstellungen anzuzeigen, und wählen Sie dann die Registerkarte **General** (Allgemein) aus.

Vergewissern Sie sich unter **Signing** (Signierung), dass **Automatically manage signing** (Signierung automatisch verwalten) aktiviert ist. Wenn dies nicht der Fall ist, aktivieren Sie diese Option, und wählen Sie im dann angezeigten Dialogfeld die Option **Enable Automatic** (Automatik aktivieren) aus, um die Buildeinstellungen zurückzusetzen.

Vergewissern Sie sich unter **Deployment Info** (Bereitstellungsinformationen), dass das Bereitstellungsziel (**Deployment Target**) auf `11.0` festgelegt ist.

### <a name="deploy-the-app-to-your-ios-device"></a>Bereitstellen der App auf Ihrem iOS-Gerät

Verbinden Sie das iOS-Gerät mit dem Macintosh-Computer, und legen Sie das **aktive Schema** auf Ihr iOS-Gerät fest.

![Auswählen des Geräts](./media/spatial-anchors-unity/select-device.png)

Wählen Sie **Build and then run the current scheme** (Aktuelles Schema erstellen und dann ausführen) aus.

![Bereitstellen und Ausführen](./media/spatial-anchors-unity/deploy-run.png)