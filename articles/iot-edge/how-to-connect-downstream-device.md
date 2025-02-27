---
title: Verbinden nachgeschalteter Geräte – Azure IoT Edge | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie nachgeschaltete oder Blattgeräte für eine Verbindung mit Azure IoT Edge-Gatewaygeräten konfigurieren.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7a66355ca1a0c9c2c144f04cd944efe22467d3ae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058516"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Verbinden eines nachgeschalteten Geräts mit einem Azure IoT Edge-Gateway

Dieser Artikel enthält Anweisungen zum Herstellen einer vertrauenswürdigen Verbindung zwischen nachgeschalteten Geräten und transparenten IoT Edge-Gateways. In einem transparenten Gatewayszenario kann mindestens ein Gerät Nachrichten über ein einziges Gatewaygerät weiterleiten, das die Verbindung zu IoT Hub aufrechterhält. Ein nachgeschaltetes Gerät kann eine beliebige Anwendung oder Plattform sein, deren Identität mit dem [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub)-Clouddienst erstellt wurde. Oft verwenden diese Anwendungen das [Azure IoT-Geräte-SDK](../iot-hub/iot-hub-devguide-sdks.md). Ein nachgeschaltetes Gerät kann sogar eine Anwendung sein, die auf dem IoT Edge-Gatewaygerät selbst ausgeführt wird. 

Es gibt drei allgemeine Schritte zum Einrichten einer erfolgreichen Verbindung für ein transparentes Gateway. In diesem Artikel wird der dritte Schritt behandelt:

1. Das Gatewaygerät muss eine sichere Verbindung mit nachgeschalteten Geräten herstellen, Nachrichten von nachgeschalteten Geräten empfangen und Nachrichten an das richtige Ziel weiterleiten. Weitere Informationen finden Sie unter [Konfigurieren eines IoT Edge-Geräts als transparentes Gateway](how-to-create-transparent-gateway.md).
2. Das nachgeschaltete Gerät benötigt eine Geräteidentität, damit es sich bei IoT Hub authentifizieren kann und weiß, dass es über sein Gatewaygerät kommunizieren kann. Weitere Informationen finden Sie unter [Authentifizieren eines nachgeschalteten Geräts bei Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **Das nachgeschaltete Gerät muss eine sichere Verbindung mit seinem Gatewaygerät herstellen können.**

Dieser Artikel erläutert häufige Probleme beim Verbinden nachgeschalteter Geräte und führt Sie durch ihre Einrichtung. Behandelt werden die folgenden Themen: 

* Grundlagen von Transport Layer Security (TLS) und Zertifikaten 
* Funktionsweise von TLS-Bibliotheken mit verschiedenen Betriebssystemen und Verarbeiten von Zertifikaten durch die Betriebssysteme
* Azure IoT-Beispiele für mehrere Sprachen zum Einstieg 

In diesem Artikel beziehen sich die Begriffe *Gateway* und *IoT Edge-Gateway* auf ein IoT Edge-Gerät, das als transparentes Gateway konfiguriert wurde. 

## <a name="prepare-a-downstream-device"></a>Vorbereiten eines nachgeschalteten Geräts

Ein nachgeschaltetes Gerät kann eine beliebige Anwendung oder Plattform sein, deren Identität mit dem [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub)-Clouddienst erstellt wurde. Oft verwenden diese Anwendungen das [Azure IoT-Geräte-SDK](../iot-hub/iot-hub-devguide-sdks.md). Ein nachgeschaltetes Gerät kann sogar eine Anwendung sein, die auf dem IoT Edge-Gatewaygerät selbst ausgeführt wird. 

Zum Verbinden eines nachgeschalteten Geräts mit einem Azure IoT Edge-Gateway ist Folgendes erforderlich:

* Ein Geräte oder eine Anwendung, die mit einer IoT Hub-Geräte-Verbindungszeichenfolge konfiguriert ist, der Informationen für die Verbindung mit dem Gateway angefügt sind. 

    Dieser Schritt wird unter [Authentifizieren eines nachgeschalteten Geräts bei Azure IoT Hub](how-to-authenticate-downstream-device.md) erläutert.

* Das Gerät oder die Anwendung muss dem Zertifikat der **Stammzertifizierungsstelle** des Gateways vertrauen, um die TLS-Verbindungen mit dem Gatewaygerät zu überprüfen. 

    Dieser Schritt wird im weiteren Verlauf dieses Artikels ausführlich erläutert. Dieser Schritt kann auf zwei Arten ausgeführt werden: durch Installation des Zertifizierungsstellenzertifikats (ZS-Zertifikat) im Zertifikatspeicher des Betriebssystems oder (für bestimmte Sprachen) durch Verweisen auf das Zertifikat in Anwendungen mit Azure IoT SDKs.

## <a name="tls-and-certificate-fundamentals"></a>Grundlagen von TLS und Zertifikaten

Die sichere Verbindung nachgeschalteter Geräte mit IoT Edge unterliegt den gleichen Herausforderungen wie jede andere sichere Client/Server-Kommunikation, die über das Internet erfolgt. Ein Client und ein Server kommunizieren mithilfe von [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) sicher über das Internet. TLS wird mit Standardkonstrukten der [Public Key-Infrastruktur](https://en.wikipedia.org/wiki/Public_key_infrastructure) erstellt, die als Zertifikate bezeichnet werden. TLS ist ziemlich raffiniert und deckt zahlreiche Themen in Hinblick auf die Sicherung von zwei Endpunkten ab. In diesem Abschnitt sind die für Sie relevanten Konzepte zusammengefasst, um eine sichere Verbindung von Geräten mit einem IoT Edge-Gateway herzustellen.

Wenn sich ein Client mit einem Server verbindet, präsentiert der Server eine Kette von Zertifikaten, die als *Serverzertifikatkette* bezeichnet wird. Eine Zertifikatkette besteht i.d.R. aus einem Stammzertifizierungsstellen-Zertifikat, mindestens einem Zwischen-ZS-Zertifikat und dem Zertifikat des Servers selbst. Ein Client stellt eine Vertrauensstellung mit einem Server her, indem er die gesamte Serverzertifikatkette kryptographisch überprüft. Diese Clientüberprüfung der Serverzertifikatkette wird als *Serverkettenüberprüfung* bezeichnet. Der Client fordert den Dienst in einem Prozess namens *Eigentumsnachweis* kryptografisch auf, den Besitz des privaten Schlüssels, der dem Serverzertifikat zugeordnet ist, nachzuweisen. Die Kombination aus Serverkettenüberprüfung und Eigentumsnachweis wird als *Serverauthentifizierung* bezeichnet. Zum Überprüfen einer Serverzertifikatkette benötigt der Client eine Kopie des Stamm-ZS-Zertifikats, mit dem das Serverzertifikat erstellt (oder ausgestellt) wurde. Normalerweise ist ein Browser beim Verbinden mit Websites mit häufig verwendeten ZS-Zertifikaten vorkonfiguriert, um den Workflow des Clients zu vereinfachen. 

Wenn sich ein Gerät mit Azure IoT Hub verbindet, ist das Gerät der Client und der IoT Hub-Clouddienst der Server. Der IoT Hub-Clouddienst wird durch ein Stamm-ZS-Zertifikat namens **Baltimore CyberTrust Root** gesichert, das öffentlich verfügbar und weitverbreitet ist. Da das IoT Hub-ZS-Zertifikat auf den meisten Geräten bereits installiert ist, wird es von vielen TLS-Implementierungen (OpenSSL, Schannel, LibreSSL) bei der Serverzertifikatüberprüfung automatisch verwendet. Ein Gerät, das sich erfolgreich mit IoT Hub verbinden kann, hat möglicherweise Probleme beim Verbinden mit einem IoT Edge-Gateway.

Wenn sich ein Gerät mit einem IoT Edge-Gateway verbindet, ist das nachgeschaltete Gerät der Client und das Gatewaygerät der Server. Mit Azure IoT Edge können Operatoren (und Benutzer) Gatewayzertifikatketten nach Belieben erstellen. Der Operator hat die Möglichkeit, ein öffentliches ZS-Zertifikat wie Baltimore oder ein selbstsigniertes (oder internes) Stamm-ZS-Zertifikat zu verwenden. Öffentliche ZS-Zertifikate sind oft mit Kosten verbunden und werden deswegen i.d.R. in Produktionsszenarien eingesetzt. Selbstsignierte ZS-Zertifikate werden für Entwicklung und Tests bevorzugt. Bei den in der Einführung aufgelisteten Artikeln zum Einrichten transparenter Gateways werden selbstsignierte Stamm-ZS-Zertifikate verwendet. 

Wenn Sie ein selbstsigniertes Stamm-ZS-Zertifikat für ein IoT Edge-Gateway verwenden, muss es auf allen nachgeschalteten Geräten installiert oder bereitgestellt werden, die sich mit dem Gateway verbinden wollen. 

![Gatewayzertifikateinrichtung](./media/how-to-create-transparent-gateway/gateway-setup.png)

Weitere Informationen zu IoT Edge-Zertifikaten und einigen Auswirkungen auf die Produktion finden Sie unter [Details zur Verwendung von IoT Edge-Zertifikaten](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Bereitstellen des Stamm-ZS-Zertifikats

Um die Zertifikate des Gatewaygeräts zu überprüfen, muss das nachgeschaltete Gerät über eine eigene Kopie des Stamm-ZS-Zertifikats verfügen. Wenn Sie die im IoT Edge-Git-Repository bereitgestellten Skripts zum Erstellen von Testzertifikaten verwendet haben, hat das Stamm-ZS-Zertifikat den Namen **azure-iot-test-only.root.ca.cert.pem**. Verschieben Sie diese Zertifikatdatei in ein beliebiges Verzeichnis auf dem nachgeschalteten Gerät, wenn dies noch im Rahmen der anderen Vorbereitungsschritte für nachgeschaltete Geräte geschehen ist. Sie können einen Dienst wie [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) oder eine Funktion wie [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) zum Verschieben der Zertifikatdatei verwenden.

## <a name="install-certificates-in-the-os"></a>Installieren von Zertifikaten im Betriebssystem

Die Installation des Stamm-ZS-Zertifikats im Zertifikatspeicher des Betriebssystems ermöglicht den meisten Anwendungen die Verwendung des Stamm-ZS-Zertifikats. Es gibt einige Ausnahmen, z.B. die NodeJS-Anwendungen, die nicht den Zertifikatspeicher des Betriebssystems verwenden, sondern den internen Zertifikatspeicher der Node-Runtime. Wenn Sie das Zertifikat nicht auf Betriebssystemebene installieren können, fahren Sie mit [Verwenden von Zertifikaten mit Azure IoT SDKs](#use-certificates-with-azure-iot-sdks) fort. 

### <a name="ubuntu"></a>Ubuntu

Die folgenden Befehle sind ein Beispiel für die Installation eines ZS-Zertifikats auf einem Ubuntu-Host. In diesem Beispiel wird davon ausgegangen, dass Sie das Zertifikat **azure-iot-test-only.root.ca.cert.pem** aus den Artikeln über die Voraussetzungen verwenden und das Zertifikat an einen Speicherort auf dem nachgeschalteten Gerät kopiert haben.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Es sollte die Meldung „Updating certificates in /etc/ssl/certs 1 added, 0 removed; done“ (Zertifikate in /etc/ssl/certs werden aktualisiert... 1 hinzugefügt, 0 entfernt; fertig) angezeigt werden.

### <a name="windows"></a>Windows

Die folgenden Schritte sind ein Beispiel für die Installation eines ZS-Zertifikats auf einem Windows-Host. In diesem Beispiel wird davon ausgegangen, dass Sie das Zertifikat **azure-iot-test-only.root.ca.cert.pem** aus den Artikeln über die Voraussetzungen verwenden und das Zertifikat an einen Speicherort auf dem nachgeschalteten Gerät kopiert haben.

1. Suchen Sie im Startmenü nach **Computerzertifikate verwalten**, und wählen Sie die Option aus. Daraufhin wird das Hilfsprogramm **certlm** geöffnet.
2. Navigieren Sie zu **Zertifikate – Lokaler Computer** > **Vertrauenswürdige Stammzertifizierungsstellen**.
3. Klicken Sie mit der rechten Maustaste auf **Zertifikat**, und wählen Sie **Alle Aufgaben** > **Importieren** aus. Der Zertifikatimport-Assistent wird gestartet. 
4. Folgen Sie den Anweisungen und importieren Sie die Zertifikatsdatei `<path>/azure-iot-test-only.root.ca.cert.pem`. Wenn Sie damit fertig sind, sollte die Meldung „Der Import war erfolgreich.“ angezeigt werden. 

Sie können Zertifikate auch programmgesteuert über .NET-APIs installieren (siehe .NET-Beispiel weiter unten in diesem Artikel). 

I.d.R. verwenden Anwendungen den von Windows bereitgestellten TLS-Stapel namens [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel), um eine sichere Verbindung über TLS herzustellen. Schannel *erfordert*, dass alle Zertifikate im Windows-Zertifikatspeicher installiert werden, bevor eine TLS-Verbindung hergestellt werden kann.

## <a name="use-certificates-with-azure-iot-sdks"></a>Verwenden von Zertifikaten mit Azure IoT SDKs

In diesem Abschnitt wird beschrieben, wie sich die Azure IoT SDKs mithilfe einfacher Beispielanwendungen mit einem IoT Edge-Gerät verbinden. Das Ziel aller Beispielanwendungen ist es, den Geräteclient zu verbinden und Telemetrienachrichten an das Gateway zu senden, dann die Verbindung zu schließen und zu beenden. 

Zwei Voraussetzungen müssen erfüllt sein, bevor Sie die Anwendungsbeispiele verwenden können:

* Die IoT Hub-Verbindungszeichenfolge Ihres nachgeschalteten Geräts, muss so geändert sein, dass sie auf das Gatewaygerät verweist, und sämtliche Zertifikate müssen vorhanden sein, die erforderlich sind, um Ihre nachgeschaltetes Gerät beim IoT Hub zu authentifizieren. Weitere Informationen finden Sie unter [Authentifizieren eines nachgeschalteten Geräts bei Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Sie haben den vollständigen Pfad zum Stamm-ZS-Zertifikat kopiert und auf Ihrem nachgeschalteten Gerät gespeichert.

    Beispiel: `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Dieser Abschnitt enthält eine Beispielanwendung zum Verbinden eines Azure IoT-NodeJS-Geräteclients mit einem IoT Edge-Gateway. Für NodeJS-Anwendungen müssen Sie das Stamm-ZS-Zertifikat auf Anwendungsebene installieren, wie es hier gezeigt ist. NodeJS-Anwendungen verwenden nicht den Zertifikatspeicher des Systems. 

1. Rufen Sie das Beispiel für **edge_downstream_device.js** aus dem [Repository für das Azure IoT-Geräte-SDK für Node.js-Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) ab. 
2. Stellen Sie sicher, dass alle Voraussetzungen zum Ausführen des Beispiels erfüllt sind, indem Sie die Datei **readme.md** überprüfen. 
3. Aktualisieren Sie in der Datei „edge_downstream_device_device.js“ die Variablen **connectionString** und **edge_ca_cert_path**. 
4. In der SDK-Dokumentation finden Sie Anweisungen zum Ausführen des Beispiels auf Ihrem Gerät. 

Zum besseren Verständnis dieses Beispiels zeigt der folgende Codeausschnitt, wie das Client-SDK die Zertifikatsdatei liest und damit eine sichere TLS-Verbindung herstellt: 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Dieser Abschnitt stellt eine Beispielanwendung zum Verbinden eines Azure IoT-.NET-Geräteclients mit einem IoT Edge-Gateway vor. .NET-Anwendungen können automatisch alle installierten Zertifikate im Systemzertifikatspeicher sowohl auf Linux- als auch auf Windows-Hosts verwenden.

1. Rufen Sie das Beispiel für **EdgeDownstreamDevice** aus dem Ordner [IoT Edge-.NET-Beispiele](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice) ab. 
2. Stellen Sie sicher, dass alle Voraussetzungen zum Ausführen des Beispiels erfüllt sind, indem Sie die Datei **readme.md** überprüfen. 
3. Aktualisieren Sie in der Datei **Properties/launchSettings.json** die Variablen **DEVICE_CONNECTION_STRING** und **CA_CERTIFICATE_PATH**. Wenn Sie das im vertrauenswürdigen Zertifikatspeicher auf dem Hostsystem installierte Zertifikat verwenden möchten, lassen Sie diese Variable leer. 
4. In der SDK-Dokumentation finden Sie Anweisungen zum Ausführen des Beispiels auf Ihrem Gerät. 

Um ein vertrauenswürdiges Zertifikat programmgesteuert über eine .NET-Anwendung im Zertifikatspeicher zu installieren, verwenden Sie die Funktion **InstallCACert()** in der Datei **EdgeDownstreamDevice/Program.cs**. Dieser Vorgang ist idempotent, sodass er mehrfach mit den gleichen Werten ohne weitere Auswirkungen ausgeführt werden kann. 

### <a name="c"></a>C

Dieser Abschnitt stellt eine Beispielanwendung zum Verbinden eines Azure IoT-C-Geräteclients mit einem IoT Edge-Gateway vor. Das C SDK ist mit vielen TLS-Bibliotheken kompatibel, darunter OpenSSL, WolfSSL und Schannel. Weitere Informationen finden Sie unter [Azure IoT SDK für C](https://github.com/Azure/azure-iot-sdk-c). 

1. Rufen Sie die Anwendung **iotedge_downstream_device_sample** aus den [Beispielen für das Azure IoT-Geräte-SDK für C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples) ab. 
2. Stellen Sie sicher, dass alle Voraussetzungen zum Ausführen des Beispiels erfüllt sind, indem Sie die Datei **readme.md** überprüfen. 
3. Aktualisieren Sie in der Datei „iotedge_downstream_device_sample.c“ die Variablen **connectionString** und **edge_ca_cert_path**. 
4. In der SDK-Dokumentation finden Sie Anweisungen zum Ausführen des Beispiels auf Ihrem Gerät. 

Das Azure IoT-Geräte-SDK für C bietet die Möglichkeit, beim Einrichten des Clients ein ZS-Zertifikat zu registrieren. Dieser Vorgang installiert das Zertifikat nicht, sondern verwendet ein Zeichenfolgenformat des Zertifikats im Speicher. Das gespeicherte Zertifikat wird dem zugrunde liegenden TLS-Stapel beim Verbindungsaufbau bereitgestellt. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Wenn Sie weder OpenSSL noch andere TLS-Bibliotheken verwenden, nutzt das SDK auf Windows-Hosts standardmäßig Schannel. Damit Schannel funktioniert, muss das IoT Edge-Stamm-ZS-Zertifikat im Windows-Zertifikatspeicher installiert und nicht mit Vorgang `IoTHubDeviceClient_SetOption` eingerichtet werden. 

### <a name="java"></a>Java

Dieser Abschnitt stellt eine Beispielanwendung zum Verbinden eines Azure IoT-Java-Geräteclients mit einem IoT Edge-Gateway vor. 

1. Rufen Sie das Beispiel für **Send-event** aus [den Beispielen für das Azure IoT-Geräte-SDK für Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples) ab. 
2. Stellen Sie sicher, dass alle Voraussetzungen zum Ausführen des Beispiels erfüllt sind, indem Sie die Datei **readme.md** überprüfen. 
3. In der SDK-Dokumentation finden Sie Anweisungen zum Ausführen des Beispiels auf Ihrem Gerät.

### <a name="python"></a>Python

Dieser Abschnitt stellt eine Beispielanwendung zum Verbinden eines Azure IoT-Python-Geräteclients mit einem IoT Edge-Gateway vor. 

1. Rufen Sie das Beispiel für **edge_downstream_client** aus [den Beispielen für das Azure IoT-Geräte-SDK für Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples) ab. 
2. Stellen Sie sicher, dass alle Voraussetzungen zum Ausführen des Beispiels erfüllt sind, indem Sie die Datei **readme.md** überprüfen. 
3. Aktualisieren Sie in der Datei „edge_downstream_client.py“ die Variablen **CONNECTION_STRING** und **TRUSTED_ROOT_CA_CERTIFICATE_PATH**. 
4. In der SDK-Dokumentation finden Sie Anweisungen zum Ausführen des Beispiels auf Ihrem Gerät. 


## <a name="test-the-gateway-connection"></a>Testen der Gatewayverbindung

Dies ist ein Beispielbefehl, der testet, ob alles ordnungsgemäß eingerichtet wurde. Es sollte die Meldung „Überprüfung OK“ angezeigt werden.

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="troubleshoot-the-gateway-connection"></a>Problembehandlung bei der Gatewayverbindung

Wenn bei Ihrem Blattgerät eine zeitweilige Verbindung mit dessen Gatewaygerät besteht, probieren Sie die folgenden Schritte zur Lösung aus. 

1. Ist der Hostname des Gateways in der Verbindungszeichenfolge mit dem Hostnamenwert in der IoT Edge-Datei „config.yaml“ auf dem Gatewaygerät identisch?
2. Kann der Hostname des Gateways in eine IP-Adresse aufgelöst werden? Sie können zeitweilige Verbindungen entweder über DNS oder durch Hinzufügen eines Eintrags in die Hostdatei auf dem Blattgerät lösen.
3. Sind in Ihrer Firewall Kommunikationsports geöffnet? Auf der Grundlage des verwendeten Protokolls (MQTTS:8883/AMQPS:5671/HTTPS:433) muss Kommunikation zwischen dem nachgeschalteten Gerät und dem transparenten IoT Edge möglich sein.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie IoT Edge [Offlinefunktionen](offline-capabilities.md) für nachgeschaltete Geräte erweitern kann. 
