---
title: Einrichten von Stagingumgebungen für Web-Apps in Azure App Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Stagingveröffentlichungen Ihrer Web-Apps in Azure App Service verwenden.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: fd488d475e24bc1aeebfa49b9d81b04ebae449ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445597"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Einrichten von Stagingumgebungen in Azure App Service
<a name="Overview"></a>

Im Tarif **Standard**, **Premium** oder **I** des App Service-Plans können Sie für die Bereitstellung Ihrer Web-App, Ihrer Web-App unter Linux, Ihres mobilen Back-Ends oder Ihrer API-App in [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) anstelle des Standardproduktionsslots einen separaten Bereitstellungsslot verwenden. Bereitstellungsslots sind aktive Apps mit eigenen Hostnamen. Elemente für App-Inhalte und -Konfigurationen können zwischen zwei Bereitstellungsslots, einschließlich des Produktionsslots, ausgetauscht werden. 

Die Bereitstellung von Anwendungen in einem produktionsfremden Slot hat folgende Vorteile:

* Sie können App-Änderungen in einem Stagingbereitstellungsslot überprüfen, bevor Sie die App in den Produktionsslot überführen.
* Wenn Sie eine App zuerst in einem Slot bereitstellen und dann in den Produktionsslot überführen, ist sichergestellt, dass alle Instanzen erst nach einer Anlaufzeit in den Produktionsslot übernommen werden. Dadurch vermeiden Sie Ausfallzeiten bei der Bereitstellung der App. Die Umleitung des Datenverkehrs erfolgt übergangslos, und es gehen keine Anforderungen aufgrund des Tauschs verloren. Der gesamte Workflow kann durch Konfigurieren von [Automatisch tauschen](#Auto-Swap) automatisiert werden, wenn keine Überprüfung vor dem Tausch erforderlich ist.
* Nach der Überführung enthält der Slot mit der vorherigen Staging-App die vorherige Produktions-App. Wenn die in den Produktionsslot überführten Änderungen nicht Ihren Erwartungen entsprechen, können Sie den gleichen Tausch sofort noch einmal vornehmen, um Ihre „letzte als funktionierend bekannte Website“ zurückzuerhalten.

Jeder App Service-Plantarif unterstützt eine andere Anzahl von Bereitstellungsslots. Für die Nutzung von Bereitstellungsslots fallen keine zusätzlichen Gebühren an. Informationen zur unterstützten Slotanzahl in Ihrem App-Tarif finden Sie unter [App Service-Grenzwerte](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). 

Wenn Sie Ihre App auf einen anderen Tarif skalieren möchten, muss der Zieltarif die Anzahl von Slots unterstützen, die Ihre App bereits verwendet. Falls Ihre App also beispielsweise mehr als fünf Slots besitzt, können Sie sie nicht auf den Tarif **Standard** herunterskalieren, da der Tarif **Standard** nur fünf Bereitstellungsslots unterstützt. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Hinzufügen eines Slots
Die App muss im Tarif **Standard**, **Premium** oder **I*** ausgeführt werden, um mehrere Bereitstellungsslots aktivieren zu können.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) die Seite [Ressourcen](../azure-resource-manager/manage-resources-portal.md#manage-resources) Ihrer App.

2. Wählen Sie im linken Bereich **Bereitstellungsslots** > **Slot hinzufügen** aus.
   
    ![Neuen Bereitstellungsslot hinzufügen](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Wenn die App nicht bereits im Tarif **Standard**, **Premium** oder **I** ausgeführt wird, wird eine Meldung angezeigt, in der auf die unterstützten Tarife für die Veröffentlichung in einer Stagingumgebung hingewiesen wird. An diesem Punkt können Sie **Upgrade** auswählen und zur Registerkarte **Skalierung** der App navigieren, bevor Sie den Vorgang fortsetzen.
   > 

3. Weisen Sie dem Slot im Dialogfeld **Slot hinzufügen** einen Namen zu, und wählen Sie aus, ob Sie die App-Konfiguration eines anderen Bereitstellungsslots klonen möchten. Wählen Sie **Hinzufügen** aus, um den Vorgang fortzusetzen.
   
    ![Konfigurationsquelle](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Sie können die Konfiguration eines beliebigen bereits vorhandenen Slots klonen. Zu den Einstellungen, die geklont werden können, zählen App-Einstellungen, Verbindungszeichenfolgen, Sprachframework-Versionen, Websockets, die HTTP-Version und die Bitanzahl der Plattform.

4. Wählen Sie nach dem Hinzufügen des Slots **Schließen** aus, um das Dialogfeld zu schließen. Der neue Slot wird nun auf der Seite **Bereitstellungsslots** angezeigt. **Datenverkehr %** ist für den neuen Slot standardmäßig auf „0“ festgelegt, sodass der gesamte Kundendatenverkehr an den Produktionsslot weitergeleitet wird.

5. Wählen Sie den neuen Bereitstellungsslot aus, um die Ressourcenseite dieses Slots zu öffnen.
   
    ![Titel des Bereitstellungsslots](./media/web-sites-staged-publishing/StagingTitle.png)

    Der Stagingslot verfügt genau wie jede andere App Service-App über eine Verwaltungsseite. Sie können die Konfiguration des Slots ändern. Am oberen Seitenrand wird der Name des Slots angezeigt, um Sie daran zu erinnern, dass es sich um den Bereitstellungsslot handelt.

6. Wählen Sie auf der Ressourcenseite des Slots die App-URL aus. Der Bereitstellungsslot besitzt einen eigenen Hostnamen und ist außerdem eine Live-App. Informationen zum Beschränken des öffentlichen Zugriffs auf den Bereitstellungsslot finden Sie unter [Statische Azure App Service-IP-Einschränkungen](app-service-ip-restrictions.md).

Der neue Bereitstellungsslot hat keinen Inhalt. Das gilt auch, wenn Sie die Einstellungen eines anderen Slots klonen. Sie können beispielsweise [Git verwenden, um etwas in diesem Slot zu veröffentlichen](app-service-deploy-local-git.md). Die Bereitstellung im Slot kann aus einem anderen Repository-Branch oder aus einem anderen Repository erfolgen. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Was geschieht bei einem Austausch?

### <a name="swap-operation-steps"></a>Schritte bei einem Austausch

Wenn Sie zwei Slots austauschen (in der Regel, um aus einem Stagingslot einen Produktionsslot zu machen), werden folgende Schritte ausgeführt, um Downtime im Zielslot zu verhindern:

1. App Service wendet die folgenden Einstellungen aus dem Zielslot (also beispielsweise dem Produktionsslot) auf alle Instanzen des Quellslot an: 
    - [Slotspezifische](#which-settings-are-swapped) App-Einstellungen und Verbindungszeichenfolgen (sofern zutreffend)
    - Einstellungen für [Continuous Deployment](deploy-continuous-deployment.md) (sofern aktiviert)
    - Einstellungen für die [App Service-Authentifizierung](overview-authentication-authorization.md) (sofern aktiviert)
    
    In jedem dieser Fälle wird ein Neustart aller Instanzen im Quellslot ausgelöst. Beim [Austauschen mit Vorschau](#Multi-Phase) ist dies das Ende der ersten Phase. Der Austauschvorgang wird angehalten, und Sie können sich vergewissern, dass der Quellslot mit den Einstellungen des Zielslots ordnungsgemäß funktioniert.

1. App Service wartet, bis jede Instanz im Quellslot neu gestartet wurde. Sollte eine Instanz nicht erfolgreich neu gestartet werden, werden sämtliche Änderungen am Quellslot zurückgesetzt, und der Austauschvorgang wird beendet.

1. Wenn [lokaler Cache](overview-local-cache.md) aktiviert ist, löst App Service die Initialisierung des lokalen Caches aus. Hierzu wird eine HTTP-Anforderung an den Anwendungsstamm („/“) in jeder Instanz des Quellslots gerichtet. Daraufhin wird gewartet, bis die einzelnen Instanzen eine HTTP-Antwort zurückgeben. Die Initialisierung des lokalen Caches hat einen weiteren Neustart der einzelnen Instanzen zur Folge.

1. Wenn [Automatisch tauschen](#Auto-Swap) mit [benutzerdefinierter Aufwärmphase](#Warm-up) aktiviert ist, löst App Service die [Anwendungsinitiierung](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) aus. Hierzu wird eine HTTP-Anforderung an den Anwendungsstamm („/“) in jeder Instanz des Quellslots gerichtet.

    Ohne Angabe von `applicationInitialization` löst App Service eine HTTP-Anforderung an den Anwendungsstamm des Quellslots in jeder Instanz aus. 
    
    Wenn eine Instanz eine HTTP-Antwort zurückgibt, wird sie vorbereitet betrachtet.

1. Nach erfolgreicher Vorbereitung aller Instanzen im Quellslot vertauscht App Service die Routingregeln der beiden Slots, um die beiden Slots auszutauschen. Nach diesem Schritt befindet sich die App, die zuvor im Quellslot vorbereitet wurde, im Zielslot (also beispielsweise im Produktionsslot).

1. Nachdem der Quellslot nun die App vor dem Austausch enthält, die sich zuvor im Zielslot befand, führt App Service den gleichen Vorgang erneut aus (also Anwenden aller Einstellungen und Neustarten der Instanzen).

In jeder Phase des Austauschvorgangs finden sämtliche Vorgänge zur Initialisierung der ausgetauschten Apps im Quellslot statt. Der Zielslot bleibt während der gesamten Vorbereitung des Quellslots online – unabhängig davon, ob der Austausch erfolgreich ist. Wenn Sie einen Stagingslot und den Produktionsslot austauschen möchten, muss der Produktionsslot immer der Zielslot sein. So ist sichergestellt, dass Ihre Produktions-App durch den Austauschvorgang nicht beeinträchtigt wird.

### <a name="which-settings-are-swapped"></a>Welche Einstellungen werden ausgetauscht?
Wenn Sie die Konfiguration von einem anderen Bereitstellungsslot klonen, kann die geklonte Konfiguration bearbeitet werden. Bei einem Austausch werden einige Konfigurationselemente zusammen mit dem Inhalt überführt (nicht slotspezifisch), während andere Konfigurationselemente nach einem Austausch im gleichen Slot verbleiben (slotspezifisch). Im Anschluss sind die Einstellungen aufgeführt, die sich beim Austauschen der Slots ändert.

**Einstellungen, die ausgetauscht werden**:

* Allgemeine Einstellungen (z. B. Framework-Version, 32/64-Bit-Angabe, WebSockets)
* App-Einstellungen (können so konfiguriert werden, dass sie beim Slot verbleiben)
* Verbindungszeichenfolgen (können so konfiguriert werden, dass sie beim Slot verbleiben)
* Handlerzuordnungen
* Überwachungs- und Diagnoseeinstellungen
* Öffentliche Zertifikate
* WebJobs-Inhalte
* Hybridverbindungen*
* Virtual Network-Integration*
* Dienstendpunkte*
* Azure Content Delivery Network*

Für mit einem Sternchen (*) gekennzeichnete Features ist eine feste Slotzuordnung geplant. 

**Einstellungen, die nicht ausgetauscht werden**:

* Veröffentlichungsendpunkte
* Benutzerdefinierte Domänennamen
* Private Zertifikate und SSL-Bindungen
* Skalierungseinstellungen
* WebJobs-Planer
* IP-Einschränkungen
* Always On
* Protokolleinstellungen (HTTPS, TLS-Version, Clientzertifikate)
* Einstellungen für das Diagnoseprotokoll
* Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->

Wenn Sie eine App-Einstellung oder eine Verbindungszeichenfolge fest einem bestimmten Slot zuordnen möchten, sodass sie nicht ausgetauscht wird, navigieren Sie zur **** Konfigurationsseite für den entsprechenden Slot. Aktivieren Sie nach dem Hinzufügen oder Bearbeiten einer Einstellung das Kontrollkästchen **Bereitstellungssloteinstellung**. Ist dieses Kontrollkästchen aktiviert, wird die Einstellung nicht ausgetauscht. 

![Sloteinstellung](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Austauschen von zwei Slots 
Bereitstellungsslots können auf der Seite **Bereitstellungsslots** Ihrer App sowie auf der Seite **Übersicht** ausgetauscht werden. Technische Details zum Slotaustauch finden Sie unter [Was geschieht bei einem Austausch?](#AboutConfiguration).

> [!IMPORTANT]
> Vergewissern Sie sich vor der Überführung einer App aus einem Bereitstellungsslot in die Produktion, dass der Produktionsslot der Zielslot ist und dass alle Einstellungen im Quellslot exakt so konfiguriert sind, wie sie in der Produktion konfiguriert sein sollen.
> 
> 

So tauschen Sie Bereitstellungsslots aus:

1. Wählen Sie auf der Seite **Bereitstellungsslots** Ihrer App die Option **Austauschen** aus.
   
    ![Schaltfläche „Austauschen“](./media/web-sites-staged-publishing/SwapButtonBar.png)

    Im Dialogfeld **Austauschen** werden Einstellungen der ausgewählten Quell- und Zielslots angezeigt, die ausgetauscht werden.

2. Wählen Sie die gewünschten Slots für **Quelle** und **Ziel** aus. Bei dem Ziel handelt es sich in der Regel um einen Produktionsslot. Wählen Sie auch die Registerkarten **Quelländerungen** und **Zieländerungen** aus, und vergewissern Sie sich, dass die Konfigurationsänderungen Ihren Erwartungen entsprechen. Wählen Sie anschließend **Austauschen** aus, um die Slots umgehend auszutauschen.

    ![Vollständiger Austausch](./media/web-sites-staged-publishing/SwapImmediately.png)

    Wenn Sie sich vor dem tatsächlichen Austausch ansehen möchten, wie der Zielslot mit den neuen Einstellungen funktioniert, wählen Sie nicht **Austauschen** aus, sondern führen Sie die Schritte unter [Mit Vorschau austauschen](#Multi-Phase) aus.

3. Wählen Sie abschließend **Schließen** aus, um das Dialogfeld zu schließen.

Informationen zur Problembehandlung finden Sie bei Bedarf unter [Behandeln von Problemen beim Austausch](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Mit Vorschau austauschen (Austausch mit mehreren Phasen)

> [!NOTE]
> „Mit Vorschau austauschen“ wird in Web-Apps unter Linux nicht unterstützt.

Vergewissern Sie sich, dass die App mit den ausgetauschten Einstellungen funktioniert, bevor Sie einen Austausch mit dem Produktionsslot als Zielslot durchführen. Der Quellslot wird außerdem vor Abschluss des Austauschs vorbereitet, was für unternehmenskritische Anwendungen von Vorteil ist.

Bei einem Austausch mit Vorschau führt App Service den gleichen [Austauschvorgang](#AboutConfiguration) durch, pausiert jedoch nach dem ersten Schritt. Daraufhin können Sie das Ergebnis vor Abschluss des Austauschs im Stagingslot überprüfen. 

Wenn Sie den Austausch abbrechen, wendet App Service erneut Konfigurationselemente auf den Quellslot an.

So führen Sie einen Austausch mit Vorschau durch:

1. Führen Sie die Schritte unter [Überführen von Bereitstellungsslots](#Swap) aus, aktivieren Sie dabei jedoch das Kontrollkästchen **Tausch mit Vorschau ausführen**.

    ![Austauschen mit Vorschau](./media/web-sites-staged-publishing/SwapWithPreview.png)

    Das Dialogfeld zeigt, wie sich die Konfiguration im Quellslot ändert (Phase 1) und wie sich der Quell- und der Zielslot ändern (Phase 2).

2. Wenn Sie so weit sind, wählen Sie **Austausch starten** aus.

    Nach Abschluss der ersten Phase wird im Dialogfeld eine entsprechende Benachrichtigung angezeigt. Navigieren Sie zu `https://<app_name>-<source-slot-name>.azurewebsites.net`, um sich eine Vorschau des Austauschs im Quellslot anzusehen. 

3. Wenn Sie bereit sind, den ausstehenden Austausch abzuschließen, wählen Sie unter **Austauschaktion** die Option **Austausch abschließen** und anschließend **Austausch abschließen** aus.

    Wenn Sie einen ausstehenden Austausch abbrechen möchten, wählen Sie stattdessen **Austausch abbrechen** aus.

4. Wählen Sie abschließend **Schließen** aus, um das Dialogfeld zu schließen.

Informationen zur Problembehandlung finden Sie bei Bedarf unter [Behandeln von Problemen beim Austausch](#troubleshoot-swaps).

Informationen zum Automatisieren eines mehrstufigen Austauschs finden Sie unter [Automatisieren mit PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Ausführen eines Rollbacks für einen Austausch
Sollten nach einem Slotaustausch Fehler im Zielslot (etwa im Produktionsslot) auftreten, stellen Sie für die Slots den Zustand vor dem Austausch wieder her, indem Sie die beiden gleichen Slots sofort erneut austauschen.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurieren des automatischen Austauschs

> [!NOTE]
> Automatisches Austauschen wird in Web-Apps unter Linux nicht unterstützt.

Das Feature „Automatisch tauschen“ ermöglicht die Optimierung von Azure DevOps-Szenarien, bei denen Ihre App kontinuierlich ohne Kaltstarts und ohne Downtime für App-Kunden bereitgestellt werden soll. Wenn automatisches Austauschen für die Überprüfung eines Slots in die Produktion aktiviert ist, gilt Folgendes: Sobald Sie Ihre Codeänderungen an diesen Slot pushen, [überführt App Service die App automatisch in die Produktion](#swap-operation-steps), nachdem sie im Quellslot vorbereitet wurde.

   > [!NOTE]
   > Es empfiehlt sich, das Feature „Automatisch tauschen“ zunächst in einem produktionsfremden Zielslot zu testen, bevor Sie es für den Produktionsslot konfigurieren.
   > 

So konfigurieren Sie automatisches Austauschen:

1. Navigieren Sie zur Ressourcenseite Ihrer App. Wählen Sie **Bereitstellungsslots** >  *\<gewünschter Quellslot>*  > **Konfiguration** > **Allgemeine Einstellungen** aus.
   
2. Legen Sie **Automatischer Tausch aktiviert** auf **Ein** fest. Wählen Sie anschließend unter **Bereitstellungsslot für automatischen Tausch** den gewünschten Zielslot und danach auf der Befehlsleiste die Option **Speichern** aus. 
   
    ![Konfigurationsoptionen für den automatischen Austausch](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Führen Sie einen Codepushvorgang an den Quellslot aus. Der automatische Austausch erfolgt nach kurzer Zeit, und die URL Ihres Zielslots spiegelt die Änderung wider.

Informationen zur Problembehandlung finden Sie bei Bedarf unter [Behandeln von Problemen beim Austausch](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Angeben der benutzerdefinierten Aufwärmphase
Bei Verwendung des Features [Automatisch tauschen](#Auto-Swap) müssen für einige Apps vor dem Austausch unter Umständen benutzerdefinierte Vorbereitungsschritte ausgeführt werden. Mithilfe des Konfigurationselements `applicationInitialization` in „web.config“ können Sie benutzerdefinierte Initialisierungsaktionen angeben. Der [Austausch](#AboutConfiguration) mit dem Zielslot erfolgt dann erst nach Abschluss dieser benutzerdefinierten Aufwärmphase. Hier sehen Sie ein Beispielfragment aus „web.config“:

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Weitere Informationen zum Anpassen des `applicationInitialization`-Elements finden Sie unter [Häufigste Bereitstellungsfehler beim Slotaustausch und Vorgehensweise zu deren Behebung](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Sie können das Aufwärmverhalten ferner mithilfe folgender [App-Einstellungen](configure-common.md) anpassen:

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Der zu pingende Pfad, um Ihre Website vorzubereiten. Fügen Sie diese App-Einstellung durch Angeben eines benutzerdefinierten Pfads hinzu, der mit einem Schrägstrich als Wert beginnt. Ein Beispiel ist `/statuscheck`. Standardwert: `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Gültige HTTP-Antwortcodes für den Aufwärmvorgang. Fügen Sie diese App-Einstellung mit einer durch Trennzeichen getrennten Liste mit HTTP-Codes hinzu. Beispiel: `200,202`. Ist der zurückgegebene Statuscode nicht in der Liste enthalten, werden die Vorbereitungs- und Austauschvorgänge beendet. Standardmäßig sind alle Antwortcodes gültig.

Informationen zur Problembehandlung finden Sie bei Bedarf unter [Behandeln von Problemen beim Austausch](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Überwachen eines Austauschs

Bei länger dauernden [Austauschvorgängen](#AboutConfiguration) können Sie sich anhand des [Aktivitätsprotokolls](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) über den Austauschvorgang informieren.

Wählen Sie im Portal auf der Ressourcenseite Ihrer App im linken Bereich die Option **Aktivitätsprotokoll** aus.

Ein Austauschvorgang wird in der Protokollabfrage als `Swap Web App Slots` angezeigt. Sie können die Abfrage erweitern und Untervorgänge oder Fehler auswählen, um die entsprechenden Details anzuzeigen.

## <a name="route-traffic"></a>Weiterleiten von Datenverkehr

Standardmäßig werden alle an die Produktions-URL Ihrer App (`http://<app_name>.azurewebsites.net`) gerichteten Clientanforderungen an den Produktionsslot weitergeleitet. Sie können einen Teil des Datenverkehrs an einen anderen Slot weiterleiten. Dieses Feature ist hilfreich, wenn Sie Benutzerfeedback zu einem neuen Update benötigen, das noch nicht für die Veröffentlichung in der Produktionsumgebung bereit ist.

### <a name="route-production-traffic-automatically"></a>Automatisches Weiterleiten von Produktionsdatenverkehr

So leiten Sie Produktionsdatenverkehr automatisch weiter:

1. Navigieren Sie zur Ressourcenseite Ihrer App, und wählen Sie **Bereitstellungsslots** aus.

2. Geben Sie in der Spalte **Datenverkehr %** des gewünschten Zielslots für die Weiterleitung durch einen Prozentwert (zwischen 0 und 100) an, welcher Anteil des gesamten Datenverkehrs weitergeleitet werden soll. Wählen Sie **Speichern** aus.

    ![Festlegen eines Prozentwerts für den Datenverkehr](./media/web-sites-staged-publishing/RouteTraffic.png)

Nach dem Speichern der Einstellung wird der angegebene Prozentsatz von Clients nach dem Zufallsprinzip an den produktionsfremden Slot weitergeleitet. 

Sobald ein Client automatisch an einen bestimmten Slot weitergeleitet wird, ist er für die Dauer der Clientsitzung auf diesen Slot festgelegt. Im Clientbrowser sehen Sie anhand des Cookies `x-ms-routing-name` in Ihren HTTP-Headern, mit welchem Slot Ihre Sitzung verknüpft ist. Anforderungen, die an den Stagingslot weitergeleitet werden, haben das Cookie `x-ms-routing-name=staging`. Anforderungen, die an den Produktionsslot weitergeleitet werden, haben das Cookie `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Manuelles Weiterleiten von Produktionsdatenverkehr

Neben dem automatischen Datenverkehrsrouting kann App Service Anforderungen auch an einen bestimmten Slot weiterleiten. Dies ist hilfreich, wenn Sie Ihren Benutzern ermöglichen möchten, Ihre Beta-App zu nutzen oder die Nutzung zu beenden. Zur manuellen Weiterleitung von Produktionsdatenverkehr wird der Abfrageparameter `x-ms-routing-name` verwendet.

Damit Benutzer die Nutzung Ihrer Beta-App beenden können, können Sie z. B. folgenden Link auf Ihrer Webseite bereitstellen:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Die Zeichenfolge `x-ms-routing-name=self` gibt den Produktionsslot an. Wenn der Clientbrowser auf den Link zugreift, wird er zum Produktionsslot weitergeleitet. Jede nachfolgende Anforderung besitzt das Cookie `x-ms-routing-name=self`, das die Sitzung mit dem Produktionsslot verknüpft.

Damit Benutzer Ihre Beta-App nutzen können, müssen Sie den gleichen Abfrageparameter auf den Namen des produktionsfremden Slots festlegen. Hier sehen Sie ein Beispiel:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Standardmäßig erhalten neue Slots eine Routingregel von `0%`, die in grau dargestellt. Wenn Sie diesen Wert explizit auf `0%` festlegen (als schwarzer Text dargestellt), können Ihre Benutzer manuell unter Verwendung des Abfrageparameters `x-ms-routing-name` auf den Stagingslot zugreifen. Sie werden jedoch nicht automatisch an den Slot weitergeleitet, da der Prozentsatz für die Weiterleitung auf „0“ festgelegt ist. Dies ist ein erweitertes Szenario, in dem Sie Ihren Stagingslot vor der Öffentlichkeit verbergen können, während Sie gleichzeitig zulassen, dass interne Teams Änderungen am Slot testen.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Löschen eines Slots

Navigieren Sie zur Ressourcenseite Ihrer App. Wählen Sie **Bereitstellungsslots** >  *\<zu löschender Slot>*  > **Übersicht** aus. Wählen Sie auf der Befehlsleiste die Option **Löschen** aus.  

![Löschen eines Bereitstellungsslots](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatisieren mit PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell ist ein Modul, das Cmdlets für die Verwaltung von Azure über Windows PowerShell bietet, einschließlich Unterstützung der Verwaltung von Bereitstellungsslots für Azure App Service.

Informationen zum Installieren und Konfigurieren von Azure PowerShell sowie zur Authentifizierung von Azure PowerShell mit Ihrem Azure-Abonnement finden Sie unter [Installieren und Konfigurieren von Microsoft Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Erstellen einer Web-App
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Erstellen eines Slots
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Initiieren eines Austauschs mit Vorschau (Austausch mit mehreren Phasen) und Anwenden der Konfiguration des Zielslots auf den Quellslot
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Abbrechen eines ausstehenden Austauschs (Austausch mit Vorschau) und Wiederherstellen der Konfiguration des Quellslots
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Überführen von Bereitstellungsslots
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Überwachen von Austauschereignissen im Aktivitätsprotokoll
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Löschen eines Slots
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatisieren mithilfe der Befehlszeilenschnittstelle

Informationen zu Befehlen der [Azure CLI](https://github.com/Azure/azure-cli) für Bereitstellungsslots finden Sie unter [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Behandeln von Problemen beim Austausch

Sollte bei einem [Slotaustausch](#AboutConfiguration) ein Fehler auftreten, wird er in *D:\home\LogFiles\eventlog.xml* protokolliert. Darüber hinaus wird der Fehler auch im anwendungsspezifischen Fehlerprotokoll protokolliert.

Im Anschluss folgen einige allgemeine Austauschfehler:

- Eine HTTP-Anforderung an den Anwendungsstamm ist zeitgesteuert. Der Austauschvorgang wartet bei jeder HTTP-Anforderung 90 Sekunden und führt bis zu fünf Wiederholungen durch. Sollte bei allen Wiederholungen ein Timeout auftreten, wird der Austauschvorgang beendet.

- Die Initialisierung des lokalen Caches ist möglicherweise nicht erfolgreich, wenn die App-Inhalte das für den lokalen Cache angegebene Kontingent des lokalen Datenträgers übersteigen. Weitere Informationen finden Sie in der [Übersicht über den lokalen Cache von Azure App Service](overview-local-cache.md).

- Während der [benutzerdefinierten Aufwärmphase](#Warm-up) werden die HTTP-Anforderungen intern (also nicht über die externe URL) ausgeführt. Sie sind möglicherweise nicht erfolgreich, wenn *web.config* bestimmte URL Rewrite-Regeln enthält. So können beispielsweise Regeln zur Umleitung von Domänennamen oder zur Erzwingung von HTTPS dazu führen, dass vorbereitende Anforderungen den App-Code nicht erreichen. Ändern Sie zur Umgehung dieses Problems Ihre Rewrite-Regeln, indem Sie die beiden folgenden Bedingungen hinzufügen:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Auch ohne benutzerdefinierte Aufwärmphase können HTTP-Anforderungen durch die URL Rewrite-Regel blockiert werden. Ändern Sie zur Umgehung dieses Problems Ihre Rewrite-Regeln, indem Sie die folgende Bedingung hinzufügen:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Einige [IP-Einschränkungsregeln](app-service-ip-restrictions.md) verhindern unter Umständen das Senden von HTTP-Anforderungen an Ihre App. IPv4-Adressbereiche, die mit `10.` und `100.` beginnen, sind interne Adressbereiche für Ihre Bereitstellung. Es empfiehlt sich, für diese Bereich die Verbindungsherstellung mit Ihrer App zuzulassen.

## <a name="next-steps"></a>Nächste Schritte
[Blockieren des Zugriffs auf produktionsfremde Slots](app-service-ip-restrictions.md)
