---
title: Beispiel – Blaupause PCI-DSS v3.2.1 – Steuerungszuordnung
description: Steuerungszuordnung des Blaupausenbeispiels Payment Card Industry Data Security Standard v3.2.1 zu Azure Policy und RBAC.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 38b1cc6249da98e11167416c8e18d06de1645679
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540944"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Steuerungszuordnung des Blaupausenbeispiels PCI-DSS v3.2.1

In diesem Artikel wird erläutert, wie das Blaupausenbeispiel PCI-DSS v3.2.1 von Azure Blueprints den Steuerungen von PCI-DSS v3.2.1 zugeordnet wird. Weitere Informationen zu den Steuerungen finden Sie unter [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Die folgenden Zuordnungen gelten für die Steuerungen unter **PCI-DSS v3.2.1:2018**. Über den rechten Navigationsbereich können Sie direkt zu einer bestimmten Steuerungszuordnung springen. Viele der zugeordneten Steuerungen werden mit einer [Azure Policy](../../../policy/overview.md)-Initiative implementiert. Zum Anzeigen der vollständigen Initiative öffnen Sie **Richtlinie** im Azure-Portal und wählen dann die Seite **Definitionen** aus. Suchen Sie anschließend die integrierte Richtlinieninitiative **[Vorschau] PCI v3.2.1:2018-Steuerungen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen**, und wählen Sie sie aus.

## <a name="132-and-134-boundary-protection"></a>1.3.2 und 1.3.4 Schutz von Grenzen

Mit dieser Blaupause können Sie Netzwerke verwalten und steuern, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die Netzwerksicherheitsgruppen mit wenig einschränkenden Regeln überwachen. Regeln, die zu wenig einschränkend sind, können einen unbeabsichtigten Netzwerkzugriff ermöglichen und sollten überprüft werden. Mit dieser Blaupause wird auch eine Azure Policy-Definition zugewiesen, die nicht geschützte Endpunkte, Anwendungen und Speicherkonten überwacht. Endpunkte und Anwendungen, die nicht durch eine Firewall geschützt sind, sowie Speicherkonten mit uneingeschränktem Zugriff können einen unbeabsichtigten Zugriff auf Informationen im Informationssystem ermöglichen.

- Nicht eingeschränkten Netzwerkzugriff auf Speicherkonten überwachen
- Zugriff über Endpunkt mit Internetzugriff sollte eingeschränkt werden

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h und 6.5.3 Kryptografischer Schutz

Die Blaupause hilft Ihnen, Ihre Richtlinie für die Verwendung von Kryptokontrollen durchzusetzen, indem sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweist, die bestimmte Kryptokontrollen durchsetzen und die Verwendung schwacher kryptographischer Einstellungen überwachen. Wenn Sie wissen, wo Ihre Azure-Ressourcen möglicherweise nicht optimale kryptografische Konfigurationen aufweisen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Ressourcen entsprechend Ihrer Richtlinie zur Informationssicherheit konfiguriert sind. Die von dieser Blaupause zugewiesenen Richtlinien erfordern insbesondere eine transparente Datenverschlüsselung für SQL-Datenbank-Instanzen, die Überwachung fehlender Verschlüsselung für Speicherkonten und Automation-Kontovariablen. Es gibt auch Richtlinien, die sich auf das Überwachen unsicherer Verbindungen mit Speicherkonten, Funktions-Apps, WebApp, API-Apps und Redis Cache beziehen und die unverschlüsselte Service Fabric-Kommunikation überwachen.

- Zugriff auf Funktions-App nur über HTTPS gestatten
- Zugriff auf Webanwendung nur über HTTPS gestatten
- Auf API-Apps sollte nur über HTTPS zugegriffen werden können
- Nicht verschlüsselte SQL-Datenbank in Azure Security Center überwachen
- Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden.
- Automation-Kontovariablen sollten verschlüsselt werden.
- Nur sichere Verbindungen mit Ihrer Redis Cache-Instanz sollten aktiviert werden
- Sichere Übertragung in Speicherkonten sollte aktiviert werden.
- Service Fabric Cluster sollten die Eigenschaft ClusterProtectionLevel auf EncryptAndSign setzen
- Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.
- Bereitstellen der transparenten SQL DB-Datenbankverschlüsselung

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 und 11.2.1 Überprüfung auf Sicherheitsrisiken und Systemupdates

Mit dieser Blaupause können Sie Sicherheitsrisiken im Informationssystem verwalten, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die fehlende Systemupdates, Sicherheitsrisiken des Betriebssystems, SQL-Sicherheitsrisiken und Sicherheitsrisiken von virtuellen Computern in Azure Security Center überwachen. Azure Security Center umfasst Funktionen zur Berichterstellung, über die Sie in Echtzeit Einblick in den Sicherheitsstatus von bereitgestellten Azure-Ressourcen erhalten.

- Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen
- Bereitstellung der standardmäßigen Microsoft IaaSAntimalware-Erweiterung für Windows Server
- Bereitstellen von Bedrohungserkennung auf SQL-Servern
- Systemupdates sollten auf Ihren Computern installiert sein.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.
- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 und 7.1.3 Aufgabentrennung

Bei nur einem Azure-Abonnementbesitzer ist keine administrative Redundanz möglich. Im umgekehrten Fall können zu viele Azure-Abonnementbesitzer das Potenzial einer Sicherheitsverletzung durch ein kompromittiertes Besitzerkonto erhöhen. Mit dieser Blaupause können Sie eine angemessene Anzahl von Azure-Abonnementbesitzern beibehalten, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die die Anzahl der Besitzer für Azure-Abonnements überwachen. Über die Verwaltung von Berechtigungen für Abonnementbesitzer können Sie eine entsprechende Aufgabentrennung implementieren.

- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.
- Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein. 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a und 8.3.1.b Verwaltung von Rechten für den privilegierten Zugriff

Mit dieser Blaupause können Sie Rechte für den privilegierten Zugriff einschränken und steuern, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, um externe Konten mit Besitzer-, Schreib- und/oder Leseberechtigungen und Mitarbeiterkonten mit Besitzer- und/oder Schreibberechtigungen ohne aktivierte mehrstufige Authentifizierung zu überwachen. In Azure ist die rollenbasierte Zugriffssteuerung (RBAC) zur Verwaltung des Zugriffs auf Azure-Ressourcen implementiert. Wenn Sie wissen, wo benutzerdefinierte RBAC-Regeln implementiert sind, können Sie den Bedarf und die ordnungsgemäße Implementierung überprüfen, da benutzerdefinierte RBAC-Regeln fehleranfällig sind. Diese Blaupause weist auch [Azure Policy](../../../policy/overview.md)-Definitionen zu, um die Verwendung der Azure Active Directory-Authentifizierung für SQL-Server-Instanzen zu überwachen. Die Verwendung der Azure Active Directory-Authentifizierung vereinfacht die Verwaltung von Berechtigungen und zentralisiert die Identitätsverwaltung von Datenbankbenutzern und anderen Microsoft  
-Diensten.
 
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden
- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden
- MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein
- Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden
- Verwendung benutzerdefinierter RBAC-Regeln überwachen

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 und 8.1.5 Ansatz der geringsten Rechte und Überprüfung der Benutzerzugriffsrechte

Azure implementiert eine rollenbasierte Zugriffskontrolle (RBAC), die Ihnen hilft, zu verwalten, wer Zugriff auf Ressourcen in Azure hat. Über das Azure-Portal können Sie überprüfen, wer Zugriff auf Azure-Ressourcen und die zugehörigen Berechtigungen hat. Mit dieser Blaupause werden [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen, um Konten zu überwachen, die für die Überprüfung priorisiert sind, einschließlich veralteter Konten und externer Konten mit erhöhten Rechten.

- Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 Entfernung oder Anpassung von Zugriffsrechten

In Azure ist die rollenbasierte Zugriffssteuerung (RBAC) zur Verwaltung des Zugriffs auf Azure-Ressourcen implementiert. Mithilfe von Azure Active Directory und RBAC können Sie Benutzerrollen aktualisieren, um Organisationsänderungen umzusetzen. Bei Bedarf kann die Anmeldung für Konten blockiert werden (oder Konten können entfernt werden), wodurch die Zugriffsrechte für Azure-Ressourcen sofort entfernt werden. Diese Blaupause weist [Azure Policy](../../../policy/overview.md)-Definitionen zur Überprüfung des veralteten Kontos zu, die bei der Entfernung berücksichtigt werden sollten.

- Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b und 8.2.5 Kennwortbasierte Authentifizierung

Diese Blaupause hilft Ihnen bei der Durchsetzung sicherer Kennwörter, indem sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweist, die Windows-VMs überwachen, die keine Mindeststärke und andere Kennwortanforderungen durchsetzen. Aufgrund der Informationen zu virtuellen Computern, die gegen die Richtlinie zur Kennwortsicherheit verstoßen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Kennwörter für alle Benutzerkonten auf virtuellen Computern mit der Richtlinie konform sind.

- [Vorschau]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Vorschau]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Vorschau]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Vorschau]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Vorschau]: Audit Windows VMs that allow re-use of the previous 24 passwords
- [Vorschau]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords

## <a name="103-and-1054-audit-generation"></a>10.3 und 10.5.4 Generierung von Überwachungsdatensätzen

Diese Blaupause hilft Ihnen, sicherzustellen, dass Systemereignisse protokolliert werden, indem Sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweisen, die die Einstellungen des Überwachungsprotokolls auf Azure-Ressourcen überprüfen.
Diagnoseprotokolle bieten Einblicke in Vorgänge, die in Azure-Ressourcen ausgeführt werden. Azure-Protokolle basieren auf synchronisierten internen Uhren, um eine zeitkorrelierte Aufzeichnung von Ereignissen über Ressourcen hinweg zu erstellen.

- Überwachen nicht überwachter SQL-Server-Instanzen in Azure Security Center
- Überwachen der Diagnoseeinstellung
- Überwachungseinstellungen auf SQL Server-Ebene überwachen
- Bereitstellen von Überwachung auf SQL-Server-Instanzen
- Speicherkonten sollten zu neuen Azure Resource Manager-Ressourcen migriert werden.
- VMs sollten zu neuen Azure Resource Manager-Ressourcen migriert werden

## <a name="1236-and-1237-information-security"></a>12.3.6 und 12.3.7 Informationssicherheit

Diese Blaupause hilft Ihnen bei der Verwaltung und Steuerung Ihres Netzwerks durch Zuweisung von [Azure Policy](../../../policy/overview.md)-Definitionen, die die zulässigen Netzwerkadressen und die genehmigten Unternehmensprodukte, die für die Umwelt zugelassen sind, überwachen. Diese können von jedem Unternehmen über die Richtlinienparameter innerhalb jeder dieser Richtlinien angepasst werden.

- Allowed locations (Zulässige Speicherorte)
- Zulässige Speicherorte für Ressourcengruppen

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun die Steuerungszuordnung der PCI-DSS v3.2.1-Blaupause angesehen haben, lesen Sie die folgenden Artikel, um eine Übersicht zu erhalten und zu erfahren, wie Sie dieses Beispiel bereitstellen:

> [!div class="nextstepaction"]
> [Steuerungszuordnung des Blaupausenbeispiels PCI-DSS v3.2.1](./index.md)
> [PCI-DSS v3.2.1-Blaupause – Bereitstellungsschritte](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
