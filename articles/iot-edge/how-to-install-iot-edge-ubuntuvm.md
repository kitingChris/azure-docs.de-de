---
title: Ausführen von virtuellen Computern vom Typ „Azure IoT Edge unter Ubuntu“ | Microsoft-Dokumentation
description: Anweisungen zur Einrichtung von Azure IoT Edge auf Azure Marketplace-VMs unter Ubuntu 16.04
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: 8275bceca1a18f49eb7eeece66a3866d77c47635
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796172"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Ausführen von virtuellen Computern vom Typ „Azure IoT Edge unter Ubuntu“

Die Azure IoT Edge-Runtime verwandelt ein Gerät in ein IoT Edge-Gerät. Die Runtime kann auf verschiedensten Geräten bereitgestellt werden – vom kleinen Raspberry Pi bis hin zum großen industriellen Server. Wenn ein Gerät mit der IoT Edge-Runtime konfiguriert wurde, können Sie darauf Geschäftslogik aus der Cloud bereitstellen.

Weitere Informationen zur Funktionsweise und zu den Komponenten der IoT Edge-Runtime finden Sie unter [Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur](iot-edge-runtime.md).

In diesem Artikel wird erläutert, wie Sie die Azure IoT Edge-Runtime auf einem virtuellen Computer unter Ubuntu 16.04 unter Verwendung des vorkonfigurierten [Azure Marketplace-Angebots „Azure IoT Edge unter Ubuntu“](https://aka.ms/azure-iot-edge-ubuntuvm) ausführen. 

Beim ersten Start wird vom virtuellen Computer vom Typ „Azure IoT Edge unter Ubuntu“ die aktuelle Version der Azure IoT Edge-Runtime vorinstalliert. Diese Version enthält darüber hinaus ein Skript zum Festlegen der Verbindungszeichenfolge und zum anschließenden Neustarten der Runtime. Diese Vorgänge können remote über das Azure-VM-Portal oder die Azure-Befehlszeile ausgelöst werden, sodass Sie das IoT Edge-Gerät einfach konfigurieren und verbinden können, ohne eine SSH- oder Remotedesktopsitzung starten zu müssen. Dieses Skript wartet mit dem Festlegen der Verbindungszeichenfolge, bis der IoT Edge-Client vollständig installiert wurde, damit dieser Schritt nicht in die Automatisierung aufgenommen werden muss.

## <a name="deploy-from-the-azure-marketplace"></a>Bereitstellen über den Azure Marketplace
1.  Navigieren Sie zum Marketplace-Angebot [Azure IoT Edge unter Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm), oder suchen Sie im [Azure Marketplace](https://azuremarketplace.microsoft.com/) nach „Azure IoT Edge unter Ubuntu“.
2.  Wählen Sie **JETZT ANFORDERN** und im nächsten Dialogfeld **Weiter**.
3.  Wählen Sie im Azure-Portal **Erstellen**, und befolgen Sie die Anweisungen des Assistenten zum Bereitstellen des virtuellen Computers. 
    *   Wenn Sie zum ersten Mal einen virtuellen Computer verwenden, ist es am einfachsten, ein Kennwort zu verwenden und im Menü für den öffentlichen eingehenden Port SSH zu aktivieren. 
    *   Bei einer ressourcenintensiven Workload sollten Sie mehr CPUs und/oder Arbeitsspeicher hinzufügen, um die VM-Größe zu erhöhen.
4.  Konfigurieren Sie den virtuellen Computer nach seiner Bereitstellung, sodass er eine Verbindung mit Ihrer IoT Hub-Instanz herstellen kann:
    1.  Kopieren Sie die Geräte-Verbindungszeichenfolge von Ihrem IoT Edge-Gerät, das Sie in Ihrer IoT Hub-Instanz erstellt haben. (Befolgen Sie die Schrittanleitung unter [Registrieren eines neuen Azure IoT Edge-Geräts über das Azure-Portal](how-to-register-device-portal.md), wenn Sie mit diesem Prozess nicht vertraut sind.)
    1.  Wählen Sie die neu erstellte VM-Ressource im Azure-Portal aus, und öffnen Sie die Option **Befehl ausführen**.
    1.  Wählen Sie die Option **RunShellScript** aus.
    1.  Führen Sie über das Befehlsfenster das folgende Skript mit Ihrer Geräte-Verbindungszeichenfolge aus: `/etc/iotedge/configedge.sh “{device_connection_string}”`.
    1.  Wählen Sie **Ausführen** aus.
    1.  Warten Sie einige Minuten. Auf dem Bildschirm sollte dann eine Meldung mit dem Hinweis angezeigt werden, dass die Verbindungszeichenfolge festgelegt wurde.


## <a name="deploy-from-the-azure-portal"></a>Bereitstellen über das Azure-Portal
Suchen Sie im Azure-Portal nach „Azure IoT Edge“, und wählen Sie **Ubuntu Server 16.04 LTS + Azure IoT Edge-Runtime** aus, um den VM-Erstellungsworkflow zu starten. Führen Sie dann die Schritte 3 und 4 aus den Anweisungen unter „Bereitstellen über den Azure Marketplace“ aus.

## <a name="deploy-from-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle

1. Wenn Sie die Azure-Befehlszeilenschnittstelle auf Ihrem Desktop verwenden, melden Sie sich zuerst an:

   ```azurecli-interactive
   az login
   ```
    
1. Wenn Sie über mehrere Abonnements verfügen, wählen Sie das gewünschte Abonnement aus:
   1. Listen Sie Ihre Abonnements auf:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Kopieren Sie das SubscriptionID-Feld für das zu verwendende Abonnement.

   1. Legen Sie Ihr Arbeitsabonnement mit der soeben kopierten ID fest:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Erstellen Sie eine neue Ressourcengruppe (oder geben Sie in den nächsten Schritten eine vorhandene Ressourcengruppe an):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Akzeptieren Sie die Nutzungsbedingungen für den virtuellen Computer. Wenn Sie die Bedingungen zunächst durchlesen möchten, führen Sie die in [Bereitstellen über den Azure Marketplace](#deploy-from-the-azure-marketplace) beschriebenen Schritte aus.

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Erstellen Sie einen neuen virtuellen Computer:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Legen Sie die Verbindungszeichenfolge fest. (Befolgen Sie die Schrittanleitung unter [Registrieren eines neuen Azure IoT Edge-Geräts mithilfe der Azure-Befehlszeilenschnittstelle](how-to-register-device-cli.md), wenn Sie mit diesem Prozess nicht vertraut sind.)

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Wenn Sie nach der Einrichtung eine SSH-Verbindung mit diesem virtuellen Computer herstellen möchten, verwenden Sie „publicIpAddress“ mit dem folgenden Befehl: `ssh azureuser@{publicIpAddress}`.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun ein IoT Edge-Gerät für die installierte Runtime bereitgestellt haben, können Sie [IoT Edge-Module bereitstellen](how-to-deploy-modules-portal.md).

Wenn Sie Probleme mit der ordnungsgemäßen Installation der IoT Edge-Runtime haben, lesen Sie die Informationen auf der Seite [Problembehandlung](troubleshoot.md).

Weitere Informationen zum Aktualisieren einer vorhandenen Installation auf die aktuelle Version von IoT Edge finden Sie unter [Aktualisieren des IoT Edge-Sicherheitsdaemons und der Runtime](how-to-update-iot-edge.md).
