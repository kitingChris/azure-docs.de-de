---
title: 'Kommerzielle Transaktionen ‎im Marketplace: Möglichkeiten und Überlegungen | Azure'
description: In diesem Artikel werden Überlegungen zur Preisfindung, Abrechnung, Rechnungsstellung und Auszahlung für einen Angebotstyp für die Veröffentlichungsoption „Transaktion“ beschrieben.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.topic: article
ms.date: 10/29/2018
ms.author: pabutler
ms.openlocfilehash: c58a36988e3aee9b122446e3ee3c4878a582b8ad
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871030"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Transaktionen auf dem kommerziellen Marketplace: Funktionen und Überlegungen

In diesem Artikel werden die folgenden Themen zum Handel auf dem kommerziellen Marketplace behandelt:

* Veröffentlichungsoptionen in Marketplace
* Allgemeine Übersicht über die Veröffentlichungsoption „Transaktion“
* Abrechnungsmodelle für die Veröffentlichungsoption „Transaktion“
* Anforderungen für die Veröffentlichungsoption „Transaktion“

## <a name="marketplace-publishing-options"></a>Veröffentlichungsoptionen in Marketplace

Die folgenden Veröffentlichungsoptionen stehen Herausgebern des kommerziellen Marketplace zur Verfügung.

### <a name="list--trial-publishing-options"></a>Veröffentlichungsoptionen „Listung“ und „Testversion“

Herausgeber können die Veröffentlichungsoptionen „Listung“, „Testversion“ und „BYOL“ für Werbezwecke und zur Neukundengewinnung nutzen. Bei diesen Veröffentlichungsoptionen nimmt Microsoft nicht direkt an den Softwarelizenztransaktionen des Herausgebers teil, und es fallen keine damit verbundenen Transaktionsgebühren an. Herausgeber sind für die Unterstützung aller Aspekte der Softwarelizenztransaktion verantwortlich, einschließlich, aber nicht beschränkt auf: Auftrag, Auftragsabwicklung, Messung, Abrechnung, Rechnungsstellung, Zahlung und Inkasso. Bei den Veröffentlichungsoptionen „Listung“ und „Testversion“ behalten Herausgeber 100 % der vom Kunden eingezogenen Lizenzgebühren für Software ein. 

### <a name="transact-publishing-option"></a>Veröffentlichungsoption „Transaktion“

Zusätzlich zu den Veröffentlichungsoptionen „Listung“ und „Testversion“ steht Herausgebern die Veröffentlichungsoption „Transaktion“ zur Verfügung. Hierbei werden die global verfügbaren E-Commerce-Funktionen von Microsoft genutzt, und Microsoft kann Marketplace-Cloudtransaktionen im Namen des Herausgebers hosten.

## <a name="transact-general-overview"></a>Allgemeine Übersicht über die Veröffentlichungsoption „Transaktion“

Bei Verwendung der Veröffentlichungsoption „Transaktion“ ermöglicht Microsoft den Verkauf von Software von Drittanbietern und die Bereitstellung einiger Angebotstypen für das Azure-Abonnement des Kunden. Der Herausgeber muss bei der Auswahl eines Abrechnungsmodells und Angebotstyps die Abrechnung der Infrastrukturgebühren und der eigenen Softwarelizenzgebühren des Herausgebers berücksichtigen.

Die Veröffentlichungsoption „Transaktion“ wird derzeit für die folgenden Angebotstypen unterstützt: virtuelle Computer, Azure-Anwendungen und SaaS-Apps.


![[Abwickeln von Geschäften zwischen Unternehmen in Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Abrechnung von Infrastrukturkosten

**Für virtuelle Computer und Azure-Anwendungen**

Für virtuelle Computer und Azure-Anwendungen werden die Nutzungsgebühren für Azure-Infrastruktur im Azure-Abonnement des Kunden in Rechnung gestellt.  Auf der Rechnung des Kunden werden die Infrastrukturnutzungsgebühren separat von den Lizenzgebühren des Softwareanbieters ausgewiesen.

**Für SaaS-Apps**

Bei SaaS-Apps muss der Herausgeber die Nutzungsgebühren für Azure-Infrastruktur und Softwarelizenzgebühren als einen Kostenposten verrechnen.  Diese werden dem Kunden als Pauschalgebühr angegeben. Die Nutzung der Azure-Infrastruktur wird verwaltet und dem Partner direkt in Rechnung gestellt.  Tatsächliche Nutzungsgebühren für die Infrastruktur werden dem Kunden nicht angezeigt.  Herausgeber entscheiden sich in der Regel dafür, die Nutzungsgebühren für Azure-Infrastruktur in ihren Softwarelizenzpreis einfließen zu lassen.  Softwarelizenzgebühren werden nicht gemessen oder nutzungsabhängig berechnet.

## <a name="transact-billing-models"></a>Abrechnungsmodelle für die Veröffentlichungsoption „Transaktion“

Abhängig von der verwendeten Transaktionsoption können die Softwarelizenzgebühren des Herausgebers wie folgt ausgewiesen werden:  

* Kostenlos: Keine Kosten für Softwarelizenzen. 

* BYOL (Bring-Your-Own-License, Verwendung Ihrer eigenen Lizenz): Alle anfallenden Gebühren für Softwarelizenzen werden direkt zwischen Herausgeber und Kunde geregelt. Microsoft wickelt nur die Nutzungsgebühren für Azure-Infrastruktur ab. (Nur für virtuelle Computer und Azure-Anwendungen.)

* Nutzungsbasierte Bezahlung: Softwarelizenzgebühren werden basierend auf der verwendeten Azure-Infrastruktur als Preis pro Stunde und pro Kern (vCPU) ausgewiesen. Dies gilt nur für virtuelle Computer und Azure-Anwendungen.

* • Abonnementpreise: Softwarelizenzgebühren fallen als monatliche oder jährliche wiederkehrende Gebühren an, die als Pauschalgebühr oder pro Arbeitsplatz berechnet werden. Dies gilt nur für SaaS-Apps und verwaltete Azure-Anwendungen.

* Kostenlose Testversion der Software: Keine Kosten für Softwarelizenzen für 30 bzw. 90 Tage.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preise für die Modelle „Kostenlos“ und „BYOL“ (Bring-Your-Own-License, Verwendung Ihrer eigenen Lizenz)

Bei der Veröffentlichung eines kostenlosen oder BYOL- Transaktionsangebots spielt Microsoft keine Rolle bei der Abwicklung des Verkaufsvorgangs für Ihre Softwarelizenzgebühren. Wie bei den Veröffentlichungsoptionen „Listung“ und „Testversion“ behält der Herausgeber 100 % der Softwarelizenzgebühren ein. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Nutzungsbasierte Bezahlung und Abonnementpreis (standortbasiert)

Preise für nutzungsbasierte Bezahlung und Abonnement: Bei der Veröffentlichung eines Transaktionsangebots mit dem Modell „Nutzungsbasierte Bezahlung“ oder „Abonnement“ stellt Microsoft die Technologie und Dienste zur Verfügung, um Käufe, Rückgaben und Rückbuchungen von Softwarelizenzen zu verarbeiten. In diesem Szenario autorisiert der Herausgeber Microsoft, als Vermittler für diese Zwecke zu fungieren. Der Herausgeber erlaubt Microsoft die Abwicklung der Softwarelizenzierungstransaktion, wobei er seine Kennzeichnung als Verkäufer, Anbieter, Distributor und Lizenzgeber beibehält.

Microsoft ermöglicht Kunden, Software des Herausgebers zu bestellen, zu lizenzieren und zu nutzen, wobei die Bedingungen des kommerziellen Marketplace von Microsoft und des Endbenutzerlizenzvertrags des Herausgebers gelten. Herausgeber müssen ihren Endbenutzerlizenzvertrag bereitstellen oder beim Erstellen des Angebots den [Standardvertrag](https://docs.microsoft.com/azure/marketplace/standard-contract) auswählen.


### <a name="free-software-trials"></a>Kostenlose Testversionen

Bei Veröffentlichungsszenarien des Typs „Transaktion“ kann der Herausgeber eine Softwarelizenz 30 oder 90 Tage kostenlos zur Verfügung stellen. Diese Rabattierungsmöglichkeit schließt nicht die Kosten für die Nutzung der Azure-Infrastruktur ein, die durch die Nutzung der Partnerlösung verursacht wird.

### <a name="private-offers"></a>Private Angebote

Zusätzlich zur Verwendung von Angebotstypen und Abrechnungsmodellen zur Verrechnung eines Angebots können Herausgeber ein vollständiges privates Angebot mit ausgehandelten, transaktionsspezifischen Preisen oder kundenspezifischen Konfigurationen anbieten. Private Angebote werden von allen drei Veröffentlichungsoptionen des Typs „Transaktion“ unterstützt.

Diese Option ermöglicht höhere oder niedrigere Preise als für das öffentlich verfügbare Angebot. Private Angebote können genutzt werden, um Rabatte zu gewähren oder eine Prämie zu einem Angebot hinzuzufügen. Private Angebote können einem oder mehreren Kunden bereitgestellt werden, indem sie ihr Azure-Abonnement auf Angebotsebene auf die Zulassungsliste setzen.


### <a name="examples"></a>Beispiele

**Nutzungsbasierte Bezahlung** 

* Wenn Sie die Option mit nutzungsbasierter Bezahlung aktivieren, sieht die Kostenstruktur folgendermaßen aus.

|Ihre Lizenzkosten  | 1,00 USD pro Stunde  |
|---------|---------|
|Azure-Nutzungskosten (D1/1-Kern)    |   0,14 USD pro Stunde     |
|*Microsoft führt die Abrechnung mit dem Kunden durch*    |  *1,14 USD pro Stunde*       |

* In diesem Szenario berechnet Microsoft 1,14 USD pro Stunde für die Verwendung Ihres veröffentlichten VM-Images.

|Berechnung durch Microsoft  | 1,14 USD pro Stunde  |
|---------|---------|
|Microsoft zahlt Ihnen 80% Ihrer Lizenzkosten|   0,80 USD pro Stunde     |
|Microsoft behält 20% Ihrer Lizenzkosten ein  |  0,20 USD pro Stunde       |
|Microsoft behält die Kosten der Azure-Nutzung zu 100 % ein | 0,14 USD pro Stunde |

**BYOL (Bring-Your-Own-License, Verwendung Ihrer eigenen Lizenz)**

* Wenn Sie die BYOL-Option aktivieren, sieht die Kostenstruktur folgendermaßen aus.

|Ihre Lizenzkosten  | Ausgehandelte und von Ihnen in Rechnung gestellte Lizenzgebühr  |
|---------|---------|
|Azure-Nutzungskosten (D1/1-Kern)    |   0,14 USD pro Stunde     |
|*Microsoft führt die Abrechnung mit dem Kunden durch*    |  *0,14 USD pro Stunde*       |

* In diesem Szenario berechnet Microsoft 0,14 USD pro Stunde für die Verwendung Ihres veröffentlichten VM-Images.

|Berechnung durch Microsoft  | 0,14 USD pro Stunde  |
|---------|---------|
|Microsoft behält die Kosten der Azure-Nutzung ein    |   0,14 USD pro Stunde     |
|Microsoft behält 0% Ihrer Lizenzkosten ein   |  0,00 USD pro Stunde       |

**SaaS-App-Abonnement**

Diese Option muss für den Verkauf über Microsoft konfiguriert sein und kann per Pauschalpreis oder pro Benutzer monatlich oder jährlich berechnet werden.
•   Wenn Sie für ein SaaS-Angebot die Option „Über Microsoft verkaufen“ aktivieren, verfügen Sie über die folgende Kostenstruktur.

|Ihre Lizenzkosten       | 100,00 USD pro Monat  |
|--------------|---------|
|Azure-Nutzungskosten (D1/1-Kern)    | Abrechnung direkt mit dem Herausgeber, nicht mit dem Kunden |
|*Microsoft führt die Abrechnung mit dem Kunden durch*    |  *100,00 USD pro Monat (Hinweis: Herausgeber muss alle anfallenden oder weiterzugebenden Infrastrukturkosten in der Lizenzgebühr berücksichtigen)*  |

* In diesem Szenario stellt Microsoft 100,00 USD für Ihre Softwarelizenz in Rechnung und zahlt 80,00 USD an den Herausgeber aus.
* Für Partner, die für die reduzierte Marketplace-Dienstgebühr qualifiziert sind, wird von Mai 2019 bis Juni 2020 eine reduzierte Transaktionsgebühr für die SaaS-Angebote angezeigt. In diesem Szenario stellt Microsoft 100,00 USD für Ihre Softwarelizenz in Rechnung und zahlt 90,00 USD an den Herausgeber aus.

|Berechnung durch Microsoft  | 100,00 USD pro Monat  |
|---------|---------|
|Microsoft zahlt Ihnen 80% Ihrer Lizenzkosten <br> \* Für jede qualifizierte SaaS-App zahlt Microsoft 90 % Ihrer Lizenzkosten.   |   80,00 USD pro Monat <br> \* 90,00 USD pro Monat    |
|Microsoft behält 20% Ihrer Lizenzkosten ein <br> \* Für jede qualifizierte SaaS-App behält Microsoft 10 % Ihrer Lizenzkosten.  |  20,00 USD pro Monat <br> \* 10,00 USD     |

* **Reduzierte Marketplace-Dienstgebühr:** Für SaaS-Produkte, die Sie in unserem kommerziellen Marketplace veröffentlichen, reduziert Microsoft die Marketplace-Dienstgebühr von 20 % (wie in der Microsoft-Herausgebervereinbarung angegeben) auf 10 %.  Damit Ihr Produkt qualifiziert ist, muss mindestens eines Ihrer Produkte von Microsoft als bereit für IP-Co-Selling oder als priorisiert für IP-Co-Selling gekennzeichnet sein. Die Berechtigung muss mindestens fünf (5) Arbeitstage vor dem Ende des jeweiligen Kalendermonats bestehen, damit die reduzierte Marketplace-Dienstgebühr für den Monat in Anspruch genommen werden kann. Die reduzierte Marketplace-Dienstgebühr gilt nicht für virtuelle Computer, verwaltete Apps oder andere Produkte, die über unseren kommerziellen Marketplace zur Verfügung gestellt werden.  Die reduzierte Marketplace-Dienstgebühr ist für qualifizierte Angebote verfügbar, wobei die Lizenzgebühren von Microsoft zwischen dem 1. Mai 2019 und dem 30. Juni 2020 vereinnahmt werden.  Nach diesem Zeitpunkt gilt für die Marketplace-Dienstgebühr wieder der normale Betrag.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Rechnungsstellung für den Kunden, Zahlung, Abrechnung und Inkasso

**Rechnungsstellung und Zahlung**

Der Herausgeber kann die vom Kunden bevorzugte Rechnungsstellungsmethode verwenden, um Abonnement- oder nutzungsbasierte Softwarelizenzgebühren zu entrichten.

**Enterprise Agreement** 

Wenn die vom Kunden bevorzugte Rechnungsstellungsmethode „Microsoft Enterprise Agreement“ ist, werden Ihre Softwarelizenzgebühren mit dieser Rechnungsstellungsmethode als Postenkosten abgerechnet, und zwar getrennt von allen Azure-spezifischen Nutzungskosten.

**Kreditkarten und monatliche Rechnung** 

Kunden können auch per Kreditkarte und monatlicher Rechnung bezahlen. In diesem Fall werden Ihre Softwarelizenzgebühren genau wie beim Enterprise Agreement-Szenario als Postenkosten abgerechnet, und zwar getrennt von allen Azure-spezifischen Nutzungskosten.

Beispiel, wenn der Kunde mit einer Kreditkarte bezahlt:

|BESCHREIBUNG    |    Date  |
|----------|----------|
|Auftragszeitraum   | 15. August 2018 bis 30. August 2018 |
|Laufzeitende (Monat)   | 30. August 2018 |
|Abrechnungsdatum | 1\. September 2018 |
|Zahlungsdatum des Kunden | 1\. September 2018 |
|Treuhandperiode (nur Kreditkarten, 30 Tage) | 1\. September 2018 bis 30. September 2018 |
|Start der Forderungslaufzeit | 1\. September 2018 |
|Ende der Forderungslaufzeit (max. 30 Tage) | 30. September 2018 |
|Datum der Berechnung der Auszahlung (monatlich am 15.) | 1\. Oktober 2018 |
|Auszahlungsdatum | 15. Oktober 2018 |

Wenn der Kunde im Enterprise Agreement-Rahmen bezahlt:

| BESCHREIBUNG |    Date  |
|----------|----------|
|Auftragszeitraum | 15. August 2018 bis 30. August 2018 |
|Laufzeitende (Quartal) | 30. September 2018 |
|Abrechnungsdatum | 15. Oktober 2018 |
|Treuhandperiode (nur Kreditkarten, 30 Tage) | – |
|Start der Forderungslaufzeit | 15. Oktober 2018 |
|Ende der Forderungslaufzeit (max. 90 Tage) | 15. Januar 2019 |
|Zahlungsdatum des Kunden | 30. Dezember 2018 |
|Datum der Berechnung der Auszahlung (monatlich am 15.) | 15. Januar 2019 |
|Auszahlungsdatum | 15. Februar 2019 |

**Kostenlose Guthaben und finanzielle Verpflichtung** 

Einige Kunden entscheiden sich dafür, Azure mit einer finanziellen Verpflichtung im Enterprise Agreement-Rahmen im Voraus zu bezahlen oder erhalten kostenlose Guthaben für die Nutzung mit Azure. Obwohl diese Guthaben zur Bezahlung der Azure-Nutzung verwendet werden können, dürfen sie nicht zur Zahlung von Softwarelizenzgebühren für Herausgeber verwendet werden.

**Abrechnung und Inkasso** 

Die Abrechnung der Softwarelizenz des Herausgebers erfolgt nach der vom Kunden gewählten Methode der Rechnungsstellung und folgt dem Zeitrahmen der Rechnungsstellung. Kunden ohne Enterprise Agreement werden Marketplace-Softwarelizenzen monatlich in Rechnung gestellt. Kunden mit Enterprise Agreement wird monatlich eine Rechnung gestellt, die vierteljährlich ausgefertigt wird.

Bei Auswahl der Preismodelle „Abonnement“ oder „Nutzungsbasierte Bezahlung“ fungiert Microsoft als Vertreter des Herausgebers und ist für alle Aspekte der Abrechnung, Zahlung und des Inkassos verantwortlich.

### <a name="publisher-payout-and-reporting"></a>Auszahlung an den Herausgeber und Berichterstellung

* Alle Softwarelizenzgebühren, die von Microsoft als Vertreter erhoben werden, unterliegen einer Transaktionsgebühr von 20 %, sofern nicht anders angegeben, und werden zum Zeitpunkt der Auszahlung an den Herausgeber einbehalten.

* Kunden kaufen in der Regel im Rahmen eines Enterprise Agreement oder eines Vertrags mit nutzungsbasierter Bezahlung per Kreditkarte. Der Vertragstyp bestimmt den Zeitpunkt für Abrechnung, Rechnungsstellung, Inkasso und Auszahlung.

>[!NOTE] 
>Alle Berichte und Ergebnisse für die Veröffentlichungsoption „Transaktion“ sind im Abschnitt „Erkenntnisse“ im Cloud-Partnerportal oder im Abschnitt „Analyse“ in Partner Center verfügbar.

#### <a name="billing-questions-and-support"></a>Fragen zu Abrechnung und Support

Weitere Informationen und gesetzliche Richtlinien finden Sie unter [Publisher Agreement](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (im Cloud-Partnerportal verfügbar).

Hilfe zu Abrechnungsfragen erhalten Sie beim [Support für Herausgeber auf dem kommerziellen Marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Anforderungen für die Veröffentlichungsoption „Transaktion“

In diesem Abschnitt werden die Transaktionsanforderungen für verschiedene Angebotstypen behandelt.

### <a name="requirements-for-all-offer-types"></a>Anforderungen für alle Angebotstypen

- Für die Veröffentlichungsoption „Transaktion“ sind unabhängig vom Preismodell des Angebots ein Microsoft-Konto und Finanzinformationen erforderlich.
- Obligatorische Finanzinformationen sind beispielsweise ein Auszahlungskonto und ein Steuerprofil.

Weitere Informationen zur Einrichtung dieser Konten finden Sie unter [Verwalten Ihres Kontos im kommerziellen Marketplace in Partner Center – Finanzielle Details](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Anforderungen für bestimmte Angebotstypen

Die Veröffentlichungsoption „Transaktion“ ist nur für die folgenden Typen von Marketplace-Angeboten verfügbar: 

**Virtueller Computer** 

Wählen Sie aus den Preismodellen „Kostenlos“, „Bring-Your-Own-License“ oder „Nutzungsbasierte Bezahlung“ aus, und weisen Sie sie als auf Angebotsebene definierte SKUs aus. Auf der Azure-Rechnung des Kunden weist Microsoft die Softwarelizenzgebühren des Herausgebers getrennt von den zugrunde liegenden Azure-Infrastrukturgebühren aus. Azure-Infrastrukturgebühren hängen von der Verwendung der Software des Herausgebers ab.

**Azure-Anwendungen: Lösungsvorlage oder verwaltete App** 

Muss einen oder mehrere virtuelle Computer bereitstellen und zieht sich durch die Summe der Preise für virtuelle Computer. Für verwaltete Apps mit einem Einzelplan kann anstelle der Preise für virtuelle Computer ein pauschales Monatsabonnement als Preismodell gewählt werden. In einigen Fällen werden dem Kunden die Nutzungsgebühren der Azure-Infrastruktur getrennt von den Softwarelizenzgebühren in Rechnung gestellt – aber auf derselben Abrechnung. Wenn Sie allerdings ein Angebot für eine verwaltete App für ISV-Infrastrukturgebühren konfigurieren, werden die Azure-Ressourcen dem Herausgeber berechnet. Für den Kunden wird eine Pauschalgebühr verwendet, die die Kosten für die Infrastruktur, Softwarelizenzen und Verwaltungsdienste abdeckt.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die erforderlichen Berechtigungen in den Veröffentlichungsoptionen des entsprechenden Angebotstypabschnitts, um Auswahl und Konfiguration Ihres Angebots abzuschließen.
* Entnehmen Sie den Veröffentlichungsmustern der Storefronts Beispiele für die Zuordnung ihrer Lösung zu einem Angebotstyp und einer Konfiguration.
