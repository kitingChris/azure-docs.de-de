---
title: 'Netzwerkleistungsmonitor-Lösung in Azure: häufig gestellte Fragen | Microsoft-Dokumentation'
description: Dieser Artikel umfasst die häufig gestellten Fragen zum Netzwerkleistungsmonitor in Azure. Mit dem Netzwerkleistungsmonitor (NPM) können Sie die Leistung Ihrer Netzwerke nahezu in Echtzeit überwachen und Leistungsengpässe im Netzwerk erkennen und lokalisieren.
services: log-analytics
documentationcenter: ''
author: vinynigam
manager: agummadi
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/12/2018
ms.author: vinigam
ms.openlocfilehash: 71eb789c92452353029613265fe97411c8c00649
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706330"
---
# <a name="network-performance-monitor-solution-faq"></a>Netzwerkleistungsmonitor-Lösung: häufig gestellte Fragen

![Symbol des Netzwerkleistungsmonitors](media/network-performance-monitor-faq/npm-symbol.png)

Dieser Artikel umfasst die häufig gestellten Fragen (FAQs) zum Netzwerkleistungsmonitor (NPM) in Azure.

Der [Netzwerkleistungsmonitor](/azure/networking/network-monitoring-overview) ist eine cloudbasierte [hybride Netzwerküberwachungslösung](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md), mit der Sie die Netzwerkleistung zwischen verschiedenen Punkten in Ihrer Netzwerkinfrastruktur überwachen können. Zudem können Sie die Netzwerkkonnektivität mit [Dienst- und Anwendungsendpunkten](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) und [die Leistung von Azure ExpressRoute](../../azure-monitor/insights/network-performance-monitor-expressroute.md) überwachen. 

Der Netzwerkleistungsmonitor erkennt Netzwerkprobleme wie ins Nichts führenden Datenverkehr (Blackholing), Routingfehler und Probleme, die mit herkömmlichen Netzwerküberwachungsmethoden nicht erkannt werden können. Die Lösung generiert Warnungen und benachrichtigt Sie, sobald ein Schwellenwert für eine Netzwerkverbindung überschritten wird. Sie gewährleistet außerdem das rechtzeitige Erkennen von Leistungsproblemen im Netzwerk und ordnet die Ursache des Problems einem bestimmten Netzwerksegment oder Gerät zu. 

Weitere Informationen zu den verschiedenen vom [Netzwerkleistungsmonitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) unterstützten Funktionen sind online verfügbar.

## <a name="set-up-and-configure-agents"></a>Einrichten und Konfigurieren von Agents

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Welche Plattformanforderungen gelten für die vom Netzwerkleistungsmonitor zur Überwachung verwendeten Knoten?
Nachfolgend sind die Plattformanforderungen für die verschiedenen Funktionen des Netzwerkleistungsmonitors aufgeführt:

- Die Funktionen „Systemmonitor“ und „Dienstkonnektivitätsmonitor“ des Netzwerkleistungsmonitors unterstützen Windows Server sowie Windows-Desktop- und Windows-Clientbetriebssysteme. Windows Server-Betriebssystemversionen werden ab 2008 SP1 unterstützt. Die unterstützten Desktop- und Clientversionen von Windows sind Windows 10, Windows 8.1, Windows 8 und Windows 7. 
- Die Funktion „ExpressRoute-Monitor“ des Netzwerkleistungsmonitors unterstützt nur das Windows Server-Betriebssystem (2008 SP1 oder höher).

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Kann ich Linux-Computer als Überwachungsknoten im Netzwerkleistungsmonitor verwenden?
Die Funktion zum Überwachen von Netzwerken mithilfe von Linux-basierten Knoten befindet sich derzeit in der Vorschau. Wenden Sie sich an Ihren Account Manager, um mehr zu erfahren. Linux-Agents bieten die Überwachungsfunktion nur für die Funktion „Systemmonitor“ des Netzwerkleistungsmonitors und sind für die Funktionen „Dienstkonnektivitätsmonitor“ und „ExpressRoute-Monitor“ nicht verfügbar.

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Welche Größenanforderungen gelten für die vom Netzwerkleistungsmonitor zur Überwachung verwendeten Knoten?
Zur Ausführung der Netzwerkleistungsmonitor-Lösung auf virtuellen Knotencomputern zum Überwachen von Netzwerken müssen die Knoten mindestens einen Speicher von 500 MB und einen Kern aufweisen. Zum Ausführen des Netzwerkleistungsmonitors müssen Sie keine separaten Knoten verwenden. Die Lösung kann auf Knoten ausgeführt werden, auf denen andere Workloads ausgeführt werden. Die Lösung bietet die Möglichkeit, den Überwachungsprozess zu beenden, falls er mehr als 5% der CPU-Ressourcen nutzt.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Soll ich zur Verwendung des Netzwerkleistungsmonitors die Knoten als Direkt-Agent oder über System Center Operations Manager verbinden?
Die beiden Funktionen „Systemmonitor“ und „Dienstkonnektivitätsmonitor“ unterstützen Knoten, die [als Direkt-Agent](../../azure-monitor/platform/agent-windows.md) sowie [über Operations Manager](../../azure-monitor/platform/om-agents.md) verbunden werden.

Für die Funktion „ExpressRoute-Monitor“ sollten die Azure-Knoten nur als Direkt-Agents verbunden werden. Azure-Knoten, die über Operations Manager verbunden werden, werden nicht unterstützt. Lokale Knoten werden zur Überwachung einer ExpressRoute-Leitung unterstützt, wenn sie als Direkt-Agents und über Operations Manager verbunden sind.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Welches Protokoll, TCP oder ICMP, sollte für die Überwachung ausgewählt werden?
Wenn Sie Ihr Netzwerk mithilfe Windows Server-basierter Knoten überwachen, sollten Sie TCP als Überwachungsprotokoll verwenden, da es eine höhere Genauigkeit bietet. 

ICMP wird für Windows-Knoten mit Desktop-/Clientbetriebssystemen empfohlen. Auf dieser Plattform können keine TCP-Daten über RAW-Sockets gesendet werden, die NPM zum Ermitteln der Netzwerktopologie verwendet.

Weitere Informationen zu den jeweiligen Vorteilen der einzelnen Protokolle finden Sie [hier](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Wie kann ich einen Knoten zur Unterstützung der Überwachung mithilfe des TCP-Protokolls konfigurieren?
Damit der Knoten die Überwachung mithilfe des TCP-Protokolls unterstützt, ist Folgendes zu beachten: 
* Stellen Sie sicher, dass als Plattform für den Knoten Windows Server (2008 SP1 oder höher) verwendet wird.
* Führen Sie das PowerShell-Skript [EnableRules.ps1](https://aka.ms/npmpowershellscript) auf dem Knoten aus. Weitere Informationen finden Sie in diesen [Anweisungen](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring).


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Wie kann ich den vom Netzwerkleistungsmonitor zur Überwachung verwendeten TCP-Port ändern?
Sie können den vom Netzwerkleistungsmonitor zur Überwachung verwendeten TCP-Port ändern, indem Sie das Skript [EnableRules.ps1](https://aka.ms/npmpowershellscript) ausführen. Sie müssen die Nummer des zu verwendenden Ports als Parameter eingeben. Um TCP beispielsweise an Port 8060 zu aktivieren, führen Sie `EnableRules.ps1 8060` aus. Stellen Sie sicher, dass Sie den gleichen TCP-Port auf allen zur Überwachung verwendeten Knoten verwenden.

Das Skript konfiguriert nur die lokale Windows-Firewall. Wenn Sie Netzwerkfirewall- oder Netzwerksicherheitsgruppen-Regeln (NSG) definiert haben, achten Sie darauf, dass sie den Datenverkehr für den vom Netzwerkleistungsmonitor verwendeten TCP-Port zulassen.

### <a name="how-many-agents-should-i-use"></a>Wie viele Agents sollte ich verwenden?
Sie sollten mindestens einen Agent für jedes Subnetz verwenden, das Sie überwachen möchten.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Was ist die maximale Anzahl von Agents, die ich verwenden kann, bevor der Fehler „...Sie haben Ihr Konfigurationslimit erreicht“ angezeigt wird?
NPM beschränkt die Anzahl von IPs auf 5.000 IPs pro Arbeitsbereich. Wenn ein Knoten sowohl IPv4- als auch IPv6-Adressen besitzt, wird dies als 2 IPs für diesen Knoten gezählt. Daher würde dieser Grenzwert von 5.000 IPs die Obergrenze für die Anzahl der Agents bestimmen. Sie können die inaktiven Agents von der Registerkarte „Knoten“ in „NPM“ >> „Konfigurieren“ löschen. NPM pflegt außerdem einen Verlauf aller IPs, die jemals der VM zugwiesen wurden, die den Agent hostet, und diese werden auch als separate IPs gezählt, die zu dieser Obergrenze von 5.000 IPs beitragen. Um IPs für Ihren Arbeitsbereich freizugeben, können Sie die Seite „Knoten“ verwenden, um die IPs zu löschen, die nicht verwendet werden.

## <a name="monitoring"></a>Überwachung

### <a name="how-are-loss-and-latency-calculated"></a>Wie werden Verlust und Latenz berechnet?
Quell-Agents senden TCP SYN-Anforderungen (wenn TCP als Protokoll zur Überwachung ausgewählt ist) oder ICMP ECHO-Anforderungen (wenn ICMP als Protokoll zur Überwachung ausgewählt ist) in regelmäßigen Abständen an die Ziel-IP-Adresse, um sicherzustellen, dass alle Pfade zwischen der Kombination aus Quell- und Ziel-IP-Adresse abgedeckt sind. Der Prozentsatz der empfangenen Pakete und die Roundtripzeit der Pakete werden gemessen, um Verlust und Latenz der einzelnen Pfade zu berechnen. Diese Daten werden für das Abrufintervall und für alle Pfade aggregiert, um die aggregierten Werte von Verlust und Latenz für die IP-Kombination für das bestimmte Abrufintervall zu erhalten.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Mit welcher Häufigkeit sendet der Quell-Agent Pakete zur Überwachung an das Ziel?
Für die Funktionen Systemmonitor und ExpressRoute-Monitor sendet die Quelle alle 5 Sekunden Pakete und erfasst die Netzwerkmessungen. Diese Daten werden über ein Abrufintervall von 3 Minuten aggregiert, um die Durchschnitts- und Spitzenwerte von Verlust und Latenz zu berechnen. Für die Funktion Dienstkonnektivitätsmonitor ergibt sich die Häufigkeit des Sendens der Pakete zur Netzwerkmessung durch die Häufigkeit, die der Benutzer beim Konfigurieren des Tests für den jeweiligen Test eingibt.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Wie viele Pakete werden zur Überwachung gesendet?
Die Anzahl der Pakete, die in einem Abruf vom Quell-Agent an das Ziel gesendet werden, ist anpassbar und wird durch den proprietären Algorithmus festgelegt, der sich bei verschiedenen Netzwerktopologien unterscheiden kann. Je höher die Anzahl der Netzwerkpfade zwischen der Kombination aus Quell- und Ziel-ID, desto höher die Anzahl der gesendeten Pakete. Das System stellt sicher, dass alle Pfade zwischen der Kombination aus Quell- und Ziel-ID abgedeckt sind.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Wie ermittelt der Netzwerkleistungsmonitor die Netzwerktopologie zwischen der Quelle und dem Ziel?
Der Netzwerkleistungsmonitor verwendet einen proprietären Algorithmus basierend auf Traceroute, um alle Pfade und Hops zwischen der Quelle und dem Ziel zu ermitteln.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Bietet der Netzwerkleistungsmonitor Informationen auf Routing- und Switchingebene? 
Obwohl der Netzwerkleistungsmonitor alle möglichen Routen zwischen dem Quell-Agent und dem Ziel erkennen kann, sind die jeweiligen Routen der Pakete, die von den spezifischen Workloads gesendet wurden, nicht zu entnehmen. Mit der Lösung können Sie die Pfade und die zugrunde liegenden Netzwerkhops ermitteln, die zu einer höheren Latenz als erwartet führen.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Warum sind einige Pfade fehlerhaft?
Zwischen der Quell- und der Ziel-ID können verschiedene Netzwerkpfade vorhanden sein, und die einzelnen Pfade können unterschiedliche Werte für Verlust und Latenz aufweisen. Der Netzwerkleistungsmonitor markiert die Pfade als fehlerhaft (rot gekennzeichnet), deren Werte für Verlust und/oder Latenz größer sind als der entsprechende in der Überwachungskonfiguration festgelegte Schwellenwert.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Was bedeutet ein rot gekennzeichneter Hop in der Netzwerktopologiekarte?
Wenn ein Hop rot gekennzeichnet ist, heißt das, dass er Teil mindestens eines fehlerhaften Pfads ist. Der Netzwerkleistungsmonitor markiert die Pfade nur als fehlerhaft, der Integritätsstatus der einzelnen Pfade wird jedoch nicht gesondert angegeben. Die problematischen Hops können Sie ermitteln, indem Sie die Latenz der einzelnen Hops anzeigen und die Hops isolieren, deren Latenz höher als erwartet ausfällt.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Wie funktioniert die Fehlerlokalisierung im Systemmonitor?
Der Netzwerkleistungsmonitor verwendet eine Wahrscheinlichkeitsmethode, um den einzelnen Netzwerkpfaden, Netzwerksegmenten und den zugehörigen Netzwerkhops basierend auf der Anzahl der fehlerhaften Pfade, zu denen sie gehören, Fehlerwahrscheinlichkeiten zuzuweisen. Wenn die Netzwerksegmente und Netzwerkhops zu einer höheren Anzahl von fehlerhaften Pfaden gehören, steigt auch die zugewiesene Fehlerwahrscheinlichkeit. Dieser Algorithmus lässt sich am besten bei vielen Knoten anwenden, die über einen NPM-Agent miteinander verbunden sind, da sich dadurch die Datenpunkte zur Berechnung der Fehlerwahrscheinlichkeiten erhöhen.

### <a name="how-can-i-create-alerts-in-npm"></a>Wie kann ich Warnungen im Netzwerkleistungsmonitor erstellen?
Entsprechende ausführliche Anweisungen finden Sie im [Abschnitt „Warnungen“ in der Dokumentation](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts).

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Kann der Netzwerkleistungsmonitor Router und Server als einzelne Geräte überwachen?
Der Netzwerkleistungsmonitor ermittelt nur die IP-Adresse und den Hostnamen der zugrunde liegenden Netzwerkhops (Switches, Router, Server usw.) zwischen der Quell- und der Ziel-IP-Adresse. Zudem wird die Latenz zwischen diesen identifizierten Hops ermittelt. Diese zugrunde liegenden Hops werden nicht einzeln überwacht.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Kann mit dem Netzwerkleistungsmonitor die Netzwerkkonnektivität zwischen Azure und AWS überwacht werden?
Ja. Weitere Informationen finden Sie im Artikel [Monitor Azure, AWS, and on-premises networks using NPM](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) (Überwachen von Azure, AWS und lokalen Netzwerken mit dem Netzwerkleistungsmonitor).

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Gibt die ExpressRoute-Bandbreitennutzung die eingehende oder die ausgehende Bandbreite an?
Die Bandbreitennutzung ist die Summe der eingehenden und ausgehenden Bandbreite. Sie wird in Bit/s angegeben.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Können Informationen zur eingehenden und ausgehenden Bandbreite für ExpressRoute erfasst werden?
Eingehende und ausgehende Werte können für die primäre und die sekundäre Bandbreite erfasst werden.

Verwenden Sie die folgende Abfrage in der Protokollsuche, um Informationen auf Peeringebene abzurufen.

    NetworkMonitoring 
    | where SubType == "ExpressRoutePeeringUtilization"
    | project CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Verwenden Sie die folgende Abfrage, um Informationen auf Verbindungsebene abzurufen. 

    NetworkMonitoring 
    | where SubType == "ExpressRouteCircuitUtilization"
    | project CircuitName,PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Welche Regionen werden für den Systemmonitor des Netzwerkleistungsmonitors unterstützt?
Der Netzwerkleistungsmonitor kann die Konnektivität zwischen Netzwerken in jedem Teil der Welt von einem Arbeitsbereich aus überwachen, der in einer der [unterstützten Regionen](../../azure-monitor/insights/network-performance-monitor.md#supported-regions) gehostet wird.

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Welche Regionen werden für den Dienstkonnektivitätsmonitor des Netzwerkleistungsmonitors unterstützt?
Der Netzwerkleistungsmonitor kann die Konnektivität mit Diensten in jedem Teil der Welt von einem Arbeitsbereich aus überwachen, der in einer der [unterstützten Regionen](../../azure-monitor/insights/network-performance-monitor.md#supported-regions) gehostet wird.

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Welche Regionen werden für den ExpressRoute-Monitor des Netzwerkleistungsmonitors unterstützt?
Der Netzwerkleistungsmonitor kann die ExpressRoute-Leitungen in jeder Azure-Region überwachen. Zur Integration in den Netzwerkleistungsmonitor benötigen Sie einen Log Analytics-Arbeitsbereich, der in einer der [unterstützten Regionen](/azure/expressroute/how-to-npm) gehostet werden muss.

## <a name="troubleshoot"></a>Problembehandlung

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Warum sind einige Hops in der Netzwerktopologieansicht als unbekannt markiert?
Der Netzwerkleistungsmonitor verwendet eine geänderte Version von Traceroute, um die Topologie zwischen dem Quell-Agent und dem Ziel zu ermitteln. Ein unbekannter Hop gibt an, dass der Netzwerkhop nicht auf die Traceroute-Anforderung des Quell-Agents reagiert hat. Wenn drei aufeinanderfolgende Netzwerkhops nicht auf die Traceroute-Anforderung des Agents reagieren, markiert die Lösung die nicht reagierenden Hops als unbekannt und versucht nicht, weitere Hops zu ermitteln.

Ein Hop reagiert in einem der folgenden Szenarien möglicherweise nicht auf eine Traceroute-Anforderung:

* Die Router wurden so konfiguriert, dass ihre Identität nicht angezeigt wird.
* Die Netzwerkgeräte lassen keinen ICMP_TTL_EXCEEDED-Datenverkehr zu.
* Die ICMP_TTL_EXCEEDED-Antwort vom Netzwerkgerät wird durch eine Firewall blockiert.

### <a name="why-does-my-link-show-unhealthy-but-the-topology-does-not"></a>Warum wird mein Link als fehlerhaft angezeigt, aber die Topologie nicht? 
NPM überwacht End-to-End-Verlust, Latenz und Topologie mit unterschiedlichen Intervallen. Verlust und Latenz werden einmal alle 5 Sekunden gemessen und alle drei Minuten aggregiert (für Systemmonitor und ExpressRoute-Monitor), während die Topologie alle 10 Minuten mit Traceroute berechnet wird. Die Topologie wird z.B. zwischen 3:44 Uhr und 4:04 Uhr drei Mal aktualisiert (3:44 Uhr, 3:54 Uhr, 4:04 Uhr), aber Verlust und Latenz sieben Mal (3:44 Uhr, 3:47 Uhr, 3:50 Uhr, 3:53 Uhr, 3:56 Uhr, 3:59 Uhr, 4:02 Uhr). Die um 3:54 Uhr generierte Topologie wird für Verlust und Latenz gerendert, die um 3:56 Uhr, 3:59 Uhr und 4:02 Uhr berechnet wird. Nehmen wir an, Sie erhalten eine Warnung, dass Ihre Expressroute-Verbindung um 3:59 Uhr fehlerhaft war. Sie melden sich beim NPM an und versuchen, die Topologiezeit auf 3:59 Uhr festzulegen. NPM rendert die um 3:54 Uhr generierte Topologie. Um die letzte bekannte Topologie Ihres Netzwerks zu verstehen, vergleichen Sie die Felder TimeProcessed (Zeit, zu der Verlust und Latenz berechnet wurden) und TracerouteCompletedTime (Zeit, zu der die Topologie berechnet wurde). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Was ist der Unterschied zwischen den Feldern E2EMedianLatency und AvgHopLatencyList in der Tabelle NetworkMonitoring?
E2EMedianLatency ist die Latenz, die alle drei Minuten nach Aggregieren der Ergebnisse der TCP-Ping-Tests aktualisiert wird, während AvgHopLatencyList alle 10 Minuten auf Basis von Traceroute aktualisiert wird. Um die genaue Zeit zu kennen, zu der E2EMedianLatency berechnet wurde, verwenden Sie das Feld TimeProcessed. Um die genaue Zeit zu kennen, zu der Traceroute AvgHopLatencyList abgeschlossen und aktualisiert hat, verwenden Sie das Feld TracerouteCompletedTime.

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Warum unterscheiden sich Hop-by-Hop-Latenzzahlen von HopLatencyValues? 
HopLatencyValues sind die Quelle für den Endpunkt.
Beispiel: Hops – A, B, C. AvgHopLatency – 10, 15, 20. Dies bedeutet: Quelle zu A Latenz = 10, Quelle zu B Latenz = 15 und Quelle zu C Latenz = 20. Die Benutzeroberfläche berechnet die A-B-Hop-Latenz in der Topologie als 5.

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>Die Lösung zeigt einen Verlust von 100 % an, obwohl eine Verbindung zwischen der Quelle und dem Ziel besteht.
Das kann der Fall sein, wenn die Hostfirewall oder die zwischengeschaltete Firewall (Netzwerkfirewall oder Azure-NSG) die Kommunikation zwischen dem Quell-Agent und dem Ziel über den Port blockiert, der vom Netzwerkleistungsmonitor zur Überwachung verwendet wird (standardmäßig handelt es sich um Port 8084, es sei denn, der Kunde hat dies geändert).

* Um sicherzustellen, dass die Hostfirewall die Kommunikation am entsprechenden Port nicht blockiert, überprüfen Sie den Integritätsstatus der Quell- und Zielknoten in der folgenden Ansicht: „Netzwerkleistungsmonitor -> Konfiguration -> Knoten“. 
  Wenn sie fehlerhaft sind, sehen Sie sich die Anweisungen an, und nehmen Sie Korrekturmaßnahmen vor. Wenn die Knoten fehlerfrei sind, fahren Sie mit dem nächsten Schritt fort. .
* Um sicherzustellen, dass keine zwischengeschaltete Netzwerkfirewall oder Azure-NSG die Kommunikation am entsprechenden Port blockiert, verwenden Sie wie folgt das Drittanbieterhilfsprogramm PsPing:
  * Das Hilfsprogramm PsPing ist [hier](https://technet.microsoft.com/sysinternals/psping.aspx) als Download verfügbar. 
  * Führen Sie im Quellknoten den folgenden Befehl aus.
    * psping -n 15 \<IP-Adresse des Zielknotens\>:<Portnummer>. Standardmäßig verwendet der Netzwerkleistungsmonitor Port 8084. Wenn Sie den Port explizit über das Skript „EnableRules.ps1“ geändert haben, geben Sie die verwendete benutzerdefinierte Portnummer ein. Dies ist ein Ping vom Azure-Computer an den lokalen Computer.
* Überprüfen Sie, ob die Pings erfolgreich ausgeführt werden. Wenn dies nicht der Fall ist, bedeutet das, dass eine zwischengeschaltete Netzwerkfirewall oder Azure-NSG den Datenverkehr an diesem Port blockiert.
* Führen Sie nun den Befehl von der Zielknoten-ID zur Quellknoten-ID aus.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Es ist Verlust von Knoten A zu Knoten B zu verzeichnen, jedoch nicht von Knoten B zu Knoten A. Warum?
Da sich die Netzwerkpfade von A zu B von den Netzwerkpfaden von B zu A unterscheiden können, können sich unterschiedliche Werte für Verlust und Latenz ergeben.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Warum werden nicht alle meine ExpressRoute-Leitungen und -Peeringverbindungen ermittelt?
NPM erkennt jetzt ExpressRoute-Leitungen und -Peeringverbindungen in allen Abonnements, auf die der Benutzer Zugriff hat. Wählen Sie alle Abonnements aus, in denen Ihre Express Route-Ressourcen verknüpft sind, und aktivieren Sie die Überwachung für jede ermittelte Ressource. NPM sucht nach Verbindungsobjekten, wenn privates Peering ermittelt wird. Überprüfen Sie daher, ob ein VNET mit Ihrem Peering verbunden ist.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>In der Funktion ExpressRoute-Monitor wird die Diagnosemeldung „Traffic is not passing through ANY circuit“ (Der Datenverkehr wird über KEINE Verbindung geleitet) angezeigt. Was bedeutet das?

In einem möglichen Szenario besteht eine fehlerfreie Verbindung zwischen den lokalen und Azure-Knoten, der Datenverkehr wird jedoch nicht über die ExpressRoute-Leitung geleitet, die zur Überwachung durch den Netzwerkleistungsmonitor konfiguriert ist. 

Möglich sind folgende Ursachen:

* Die ExpressRoute-Leitung ist nicht verfügbar.
* Die Routenfilter sind so konfiguriert, dass andere Routen (z.B. eine VPN-Verbindung oder eine andere ExpressRoute-Leitung) Priorität vor der vorgesehenen ExpressRoute-Leitung haben. 
* Die zur Überwachung der ExpressRoute-Leitung in der Überwachungskonfiguration ausgewählten lokalen und Azure-Knoten sind über die vorgesehene ExpressRoute-Leitung nicht miteinander verbunden. Vergewissern Sie sich, dass Sie die richtigen Knoten ausgewählt haben, die über die zu überwachende ExpressRoute-Leitung miteinander verbunden sind.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Beim Konfigurieren der Überwachung der ExpressRoute-Leitung werden die Azure-Knoten nicht erkannt
Dies kann der Fall sein, wenn die Azure-Knoten über Operations Manager verbunden werden. Mit der Funktion „ExpressRoute-Monitor“ werden nur die Azure-Knoten unterstützt, die als Direkt-Agents verbunden werden.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Ich kann keine ExpressRoute-Leitungen im OMS-Portal ermitteln
Obwohl der Netzwerkleistungsmonitor über das Azure-Portal und über das OMS-Portal verwendet werden kann, wird die Ermittlung von Leitungen in der Funktion ExpressRoute-Monitor nur über das Azure-Portal ausgeführt. Wenn die Leitungen über das Azure-Portal ermittelt wurden, kann die Funktion in beiden Portalen verwendet werden. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>In der Funktion Dienstkonnektivitätsmonitor werden Dienstantwortzeit, Netzwerkverlust und Latenz als nicht verfügbar angezeigt
Dies ist in folgenden Fällen möglich:

* Der Dienst ist nicht verfügbar.
* Der Knoten, der zum Überprüfen der Netzwerkkonnektivität mit dem Dienst verwendet wird, ist nicht verfügbar.
* In die Testkonfiguration wurde ein falsches Ziel eingegeben.
* Der Knoten verfügt nicht über Netzwerkkonnektivität.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>In der Funktion Dienstkonnektivitätsmonitor wird eine gültige Dienstantwortzeit angezeigt, Netzwerkverlust und Latenz werden jedoch als nicht verfügbar angezeigt
 Dies ist in folgenden Fällen möglich:

* Wenn es sich bei dem Knoten, der zum Überprüfen der Netzwerkkonnektivität mit dem Dienst verwendet wird, um einen Windows-Clientcomputer handelt, blockiert der Zieldienst möglicherweise ICMP-Anforderungen, oder eine Netzwerkfirewall blockiert ICMP-Anforderungen des Knotens.
* Das Kontrollkästchen „Netzwerkmessungen durchführen“ ist in der Testkonfiguration deaktiviert.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>In der Funktion Dienstkonnektivitätsmonitor wird die Dienstantwortzeit als nicht verfügbar angezeigt, es sind jedoch gültige Angaben zu Netzwerkverlust und Latenz verfügbar
Dies kann der Fall sein, wenn der Zieldienst keine Webanwendung ist, der Test aber als Webtest konfiguriert ist. Bearbeiten Sie die Testkonfiguration, und wählen Sie für den Testtyp „Netzwerk“ anstelle von „Web“ aus.

## <a name="miscellaneous"></a>Verschiedenes

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Ist die Leistung auf dem zur Überwachung verwendeten Knoten beeinträchtigt?
Der Prozess des Netzwerkleistungsmonitors ist so konfiguriert, dass er beendet wird, wenn mehr als 5 % der CPU-Ressourcen des Hosts genutzt werden. Dadurch wird sichergestellt, dass die Knoten weiterhin für die üblichen Workloads verwendet werden können, ohne dass die Leistung beeinträchtigt wird.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>Bearbeitet der Netzwerkleistungsmonitor die Firewallregeln für die Überwachung?
Der Netzwerkleistungsmonitor erstellt nur eine lokale Windows-Firewallregel auf den Knoten, auf denen das PowerShell-Skript „EnableRules.ps1“ ausgeführt wird, sodass die Agents TCP-Verbindungen untereinander am angegebenen Port erstellen können. Die Lösung ändert keine Netzwerkfirewall- oder Netzwerksicherheitsgruppen-Regeln (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Wie kann ich die Integrität der Knoten überprüfen, die zur Überwachung verwendet werden?
Sie können den Integritätsstatus der zur Überwachung verwendeten Knoten in der folgenden Ansicht anzeigen: „Netzwerkleistungsmonitor -> Konfiguration -> Knoten“. Wenn ein Knoten fehlerhaft ist, können Sie die Fehlerdetails anzeigen und die empfohlene Aktion ausführen.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Kann der Netzwerkleistungsmonitor Latenzzeiten in Mikrosekunden melden?
Der Netzwerkleistungsmonitor rundet die Latenzzeiten in der Benutzeroberfläche und in Millisekunden. Die gleichen Daten werden mit einer höheren Granularität (manchmal mit bis zu vier Dezimalstellen) gespeichert.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Netzwerkleistungsmonitor finden Sie unter [Netzwerkleistungsmonitor-Lösung in Azure](../../azure-monitor/insights/network-performance-monitor.md).
