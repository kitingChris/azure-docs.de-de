---
title: 'Web Application Firewall: Anforderungsgrößenlimits und Ausschlusslisten in Azure Application Gateway – Azure-Portal'
description: Dieser Artikel enthält Informationen zur Konfiguration von Anforderungsgrößenlimits und Ausschlusslisten für die Web Application Firewall (WAF) in Application Gateway mit dem Azure-Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/15/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 272c6d2de23b1e89caef3f9bee20a96c5c196cde
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275180"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>WAF-Anforderungsgrößenlimits und Ausschlusslisten

Die Web Application Firewall (WAF) von Azure Application Gateway bietet Schutz für Webanwendungen. Dieser Artikel beschreibt die Konfiguration von WAF-Anforderungsgrößenlimits und -Ausschlusslisten.

## <a name="waf-request-size-limits"></a>WAF-Anforderungsgrößenlimits

![Anforderungsgrößenlimits](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Die Web Application Firewall ermöglicht Ihnen das Konfigurieren von Anforderungsgrößenlimits mit Ober- und Untergrenzen. Die folgenden zwei Konfigurationen für Größenlimits sind verfügbar:

- Der Wert im Feld für die maximale Größe des Anforderungstexts wird in Kilobyte angegeben und bestimmt das Anforderungsgrößenlimit insgesamt mit Ausnahme von Dateiuploads. In diesem Feld können Werte von mindestens 1 KB bis maximal 128 KB angegeben werden. Der Standardwert für die Größe des Anforderungstexts beträgt 128 KB.
- Der Wert im Feld für das Dateiuploadlimit wird in MB angegeben und bestimmt die maximal zulässige Größe für Dateiuploads. Für dieses Feld kann ein Mindestwert von 1 MB und ein Höchstwert von 500 MB für große SKU-Instanzen angegeben werden. Für mittlere SKU-Instanzen gilt ein Höchstwert von 100 MB. Der Standardwert für die Größe von Dateiuploads beträgt 100 MB.

Die WAF bietet außerdem einen konfigurierbaren Knopf zum Aktivieren bzw. Deaktivieren der Anforderungstextüberprüfung. Standardmäßig ist die Überprüfung des Anforderungstexts aktiviert. Ist die Überprüfung des Anforderungstexts deaktiviert, wertet die WAF die Inhalte des HTTP-Nachrichtentexts nicht aus. In diesem Fall erzwingt die WAF weiterhin WAF-Regeln für Header, Cookies und den URI. Ist die Überprüfung des Anforderungstexts deaktiviert, ist das Feld für die maximale Größe des Anforderungstexts nicht verfügbar, und es kann kein Wert festgelegt werden. Wenn Sie die Überprüfung des Anforderungstexts deaktivieren, können Nachrichten, die größer als 128 KB sind, an die WAF gesendet werden, aber der Nachrichtentext wird nicht auf Sicherheitsrisiken überprüft.

## <a name="waf-exclusion-lists"></a>WAF-Ausschlusslisten

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Mit WAF-Ausschlusslisten können Sie bestimmte Anforderungsattribute in einer WAF-Auswertung weglassen. Ein gängiges Beispiel sind von Active Directory eingefügte Token, die für Authentifizierungs- oder Kennwortfelder verwendet werden. Diese Attribute enthalten häufig Sonderzeichen, die ein falsch positives Ergebnis von den WAF-Regeln auslösen können. Ein zur WAF-Ausschlussliste hinzugefügtes Attribut wird von konfigurierten und aktiven WAF-Regeln nicht berücksichtigt. Ausschlusslisten gelten global.

Die folgenden Attribute können zu Ausschlusslisten hinzugefügt werden. Die Werte des von Ihnen ausgewählten Felds werden nicht im Zusammenhang mit WAF-Regeln bewertet. Die Ausschlusslisten entfernen die Überprüfung des Feldwerts.

* Anforderungsheader
* Anforderungscookies
* Ein Anforderungsattributname (Argument) kann als Ausschlusselement hinzugefügt werden. Beispiele hierfür sind:

   * Name des Formularfelds
   * XML-Entität
   * JSON-Entität
   * URL-Abfragezeichenfolgenargument

Sie können eine exakte Übereinstimmung für Anforderungsheader, Test, Cookies oder Abfragezeichenfolgenattribute angeben.  Oder Sie können optional teilweise Übereinstimmungen angeben. Der Ausschluss bezieht sich immer auf ein Headerfeld, niemals auf seinen Wert. Ausschlussregeln sind global gültig und gelten für alle Seiten und Regeln.

Für Übereinstimmungskriterien werden die folgenden Operatoren unterstützt:

- **Equals** (gleich):  Dieser Operator wird für exakte Übereinstimmungen verwendet. Verwenden Sie beispielsweise zum Auswählen eines Headers namens **bearerToken** den Operator „equals“ mit dem Selektor **bearerToken**.
- **Starts with** (Beginnt mit): Dieser Operator gleicht alle Felder ab, die mit dem angegebenen Selektorwert beginnen.
- **Ends with** (Endet mit):  Dieser Operator gleicht alle Anforderungsfelder ab, die auf den angegebenen Selektorwert enden.
- **Contains** (Enthält): Dieser Operator gleicht alle Anforderungsfelder ab, die den angegebenen Selektorwert enthalten.
- **Gleich beliebige:** Dieser Operator entspricht allen angeforderten Feldern. * wird zum Selektorwert.

In allen Fällen muss beim Abgleich die Groß-/Kleinschreibung beachtet werden, und reguläre Ausdrücke sind nicht als Selektor zulässig.

### <a name="examples"></a>Beispiele

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Die folgenden Beispiele veranschaulichen die Verwendung von Ausschlüssen.

### <a name="example-1"></a>Beispiel 1

In diesem Beispiel möchten Sie den Header „User-Agent“ (Benutzer-Agent) ausschließen. Der Anforderungsheader „User-Agent“ (Benutzer-Agent) umfasst eine charakteristische Zeichenfolge, die es den Netzwerkprotokollpeers ermöglicht, den Anwendungstyp, das Betriebssystem, den Softwarehersteller oder die Softwareversion des anfordernden Softwarebenutzer-Agent zu ermitteln. Weitere Informationen finden Sie unter [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent) (Benutzer-Agent).

Es gibt alle möglichen Gründe, die Auswertung dieses Headers zu deaktivieren. Die WAF könnte eine Zeichenfolge als schädlich einstufen. Beispielsweise den klassischen SQL-Angriff „x=x“ in einer Zeichenfolge. In einigen Fällen kann dies berechtigter Datenverkehr sein. Sie müssen diesen Header daher möglicherweise von der WAF-Auswertung ausschließen.

Das folgende Azure PowerShell-Cmdlet schließt den Header „User-Agent“ (Benutzer-Agent) von der Auswertung aus:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>Beispiel 2

Dieses Beispiel schließt den Wert im Parameter *user* aus, der in der Anforderung über die URL übergeben wird. Nehmen wir beispielsweise Folgendes an: In Ihrer Umgebung enthält das Benutzerfeld üblicherweise eine Zeichenfolge, die von der WAF als schädlich eingestuft und daher von dieser blockiert wird.  Sie können in diesem Fall den Benutzerparameter ausschließen, sodass die WAF in dem Feld keine Auswertung vornimmt.

Das folgende Azure PowerShell-Cmdlet schließt den Benutzerparameter von der Auswertung aus:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "user"
```
Wenn also die URL **http://www.contoso.com/?user=fdafdasfda** an die WAF übergeben wird, wertet diese die Zeichenfolge **fdafdasfda** nicht aus.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren Ihrer WAF-Einstellungen können Sie sich darüber informieren, wie Sie WAF-Protokolle anzeigen. Weitere Informationen finden Sie unter [Application Gateway-Diagnose](application-gateway-diagnostics.md#diagnostic-logging).
