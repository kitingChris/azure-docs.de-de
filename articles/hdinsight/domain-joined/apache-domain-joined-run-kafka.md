---
title: 'Tutorial: Konfigurieren von Apache Kafka-Richtlinien in HDInsight mit dem Enterprise-Sicherheitspaket – Azure'
description: Tutorial – Erfahren Sie, wie Sie Apache Ranger-Richtlinien für Kafka in Azure HDInsight mit dem Enterprise-Sicherheitspaket konfigurieren.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: ba16a975aa3b1e60393006ef49a7e422c572931e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441370"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Tutorial: Konfigurieren von Apache Kafka-Richtlinien in HDInsight mit dem Enterprise-Sicherheitspaket (Vorschau)

Hier erfahren Sie, wie Sie Apache Ranger-Richtlinien für Apache Kafka-Cluster mit dem Enterprise-Sicherheitspaket (ESP) konfigurieren. Es werden Verbindungen von ESP-Clustern mit einer Domäne hergestellt, sodass Benutzer sich mit Anmeldeinformationen für die Domäne authentifizieren können. In diesem Tutorial erstellen Sie zwei Ranger-Richtlinien, um den Zugriff auf die Themen `sales` und `marketingspend` einzuschränken.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von Domänenbenutzern
> * Erstellen von Ranger-Richtlinien
> * Erstellen von Themen in einem Kafka-Cluster
> * Testen von Ranger-Richtlinien

## <a name="prerequisite"></a>Voraussetzung

Ein [HDInsight-Kafka-Cluster mit dem Enterprise-Sicherheitspaket](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Herstellen einer Verbindung mit der Apache Ranger-Administratoroberfläche

1. Stellen Sie in einem Browser mit der URL `https://ClusterName.azurehdinsight.net/Ranger/` eine Verbindung mit der Ranger-Administrator-Benutzeroberfläche her. Vergessen Sie nicht, `ClusterName` in den Namen des Kafka-Clusters zu ändern. Ranger-Anmeldeinformationen sind nicht identisch mit Hadoop-Clusteranmeldeinformationen. Stellen Sie die Verbindung mit der Ranger-Administratoroberfläche in einem neuen InPrivate-Browserfenster her, um zu verhindern, dass der Browser zwischengespeicherte Hadoop-Anmeldeinformationen verwendet.

2. Melden Sie sich mit Ihren Administratoranmeldeinformationen für Azure Active Directory (AD) an. Die Azure AD-Administratoranmeldeinformationen sind nicht identisch mit HDInsight-Clusteranmeldeinformationen oder Linux-HDInsight-Knoten-SSH-Anmeldeinformationen.

   ![Apache Ranger-Administratoroberfläche](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Erstellen von Domänenbenutzern

Besuchen Sie [Erstellen eines in die Domäne eingebundenen HDInsight-Clusters](./apache-domain-joined-configure-using-azure-adds.md), um Informationen zum Erstellen der Domänenbenutzer **sales_user** und **marketing_user** zu erhalten. In einem Produktionsszenario stammen Domänenbenutzer aus Ihrem Active Directory-Mandanten.

## <a name="create-ranger-policy"></a>Erstellen der Ranger-Richtlinie

Erstellen Sie eine Ranger-Richtlinie für **sales_user** und **marketing_user**.

1. Öffnen Sie die **Ranger-Administratoroberfläche**.

2. Wählen Sie unter **Kafka** **\<ClusterName>_kafka** aus. Möglicherweise ist eine vorkonfigurierte Richtlinie aufgelistet.

3. Wählen Sie **Neue Richtlinie hinzufügen** aus, und geben Sie die folgenden Werte ein:

   |Einstellung  |Empfohlener Wert  |
   |---------|---------|
   |Richtlinienname  |  hdi sales*-Richtlinie   |
   |Thema   |  sales* |
   |Benutzer auswählen  |  sales_user1 |
   |Berechtigungen  | Veröffentlichen, Nutzen, Erstellen |

   Die folgenden Platzhalter können im Themennamen enthalten sein:

   * „*“ weist auf null (0) oder mehr Vorkommen von Zeichen hin.
   * „?“ weist auf ein einzelnes Zeichen hin.

   ![Apache Ranger-Administratoroberfläche – Richtlinie erstellen](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   Warten Sie kurz, bis Ranger mit AAD synchronisiert ist, wenn unter **Benutzer auswählen** nicht automatisch ein Domänenbenutzer eingetragen wird.

4. Wählen Sie **Hinzufügen** aus, um die Richtlinie zu speichern.

5. Wählen Sie **Neue Richtlinie hinzufügen** aus, und geben Sie die folgenden Werte ein:

   |Einstellung  |Empfohlener Wert  |
   |---------|---------|
   |Richtlinienname  |  hdi marketing-Richtlinie   |
   |Thema   |  marketingspend |
   |Benutzer auswählen  |  marketing_user1 |
   |Berechtigungen  | Veröffentlichen, Nutzen, Erstellen |

   ![Apache Ranger-Administratoroberfläche – Richtlinie erstellen](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Wählen Sie **Hinzufügen** aus, um die Richtlinie zu speichern.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Erstellen von Themen in einem Kafka-Cluster mit ESP

So erstellen Sie zwei Themen, `salesevents` und `marketingspend`:

1. Führen Sie folgenden Befehl aus, um eine SSH-Verbindung mit dem Cluster zu öffnen:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Ersetzen Sie `DOMAINADMIN` durch den Administratorbenutzer für Ihren Cluster, den Sie während der [Clustererstellung](./apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp) konfiguriert haben, und ersetzen Sie `CLUSTERNAME` durch den Namen Ihres Clusters. Geben Sie bei entsprechender Aufforderung das Kennwort für das Administratorbenutzerkonto ein. Weitere Informationen zum Verwenden von `SSH` mit HDInsight finden Sie unter [Verwenden von SSH mit HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Um den Clusternamen in einer Variablen zu speichern und ein JSON-Analysehilfsprogramm (`jq`) zu installieren, verwenden Sie die folgenden Befehle. Geben Sie bei entsprechender Aufforderung den Namen des Kafka-Clusters ein.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Um die Kafka-Brokerhosts abzurufen, verwenden Sie die folgenden Befehle. Geben Sie bei entsprechender Aufforderung das Kennwort des Administratorkontos für den Cluster ein.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Vor dem Fortfahren müssen Sie unter Umständen Ihre Entwicklungsumgebung einrichten, falls Sie dies nicht bereits getan haben. Sie benötigen Komponenten wie Java JDK, Apache Maven und einen SSH-Client mit scp. Weitere Informationen finden Sie unter [Einrichtungsanweisungen](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Laden Sie die [Apache Kafka-Producer/Consumer-Beispiele mit Domäneneinbindung](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer) herunter.

1. Befolgen Sie die Schritte 2 und 3 unter **Erstellen und Bereitstellen des Beispiels** im [Tutorial: Verwenden der Apache Kafka Producer- und Consumer-APIs](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example).

1. Führen Sie die folgenden Befehle aus:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testen der Ranger-Richtlinien

Basierend auf den konfigurierten Ranger-Richtlinien kann **sales_user** für das Thema `salesevents` produzieren/es nutzen, dies gilt jedoch nicht für das Thema `marketingspend`. Umgekehrt kann **marketing_user** für das Thema `marketingspend` produzieren/es nutzen, dies gilt jedoch nicht für das Thema `salesevents`.

1. Stellen Sie eine neue SSH-Verbindung mit dem Cluster her. Führen Sie den folgenden Befehl aus, um sich als **sales_user1** anzumelden:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Führen Sie den folgenden Befehl aus:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Verwenden Sie die Broker-Namen aus dem vorherigen Abschnitt, um die folgenden Umgebungsvariable festzulegen:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Beispiel: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Führen Sie Schritt 3 im Abschnitt **Erstellen und Bereitstellen des Beispiels** unter [Tutorial: Verwenden der Apache Kafka Producer- und Consumer-APIs](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) aus, um sicherzustellen, dass `kafka-producer-consumer.jar` auch für **sales_user** verfügbar ist.

5. Überprüfen Sie, ob **sales_user1** für das Thema `salesevents` produzieren kann. Führen Sie dazu den folgenden Befehl aus:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Führen Sie den folgenden Befehl zum Nutzen des Themas `salesevents` aus:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Stellen Sie sicher, dass Sie die Nachrichten lesen können.

7. Vergewissern Sie sich, dass **sales_user1** nicht für das Thema `marketingspend` produzieren kann. Führen Sie dazu im gleichen SSH-Fenster den folgenden Befehl aus:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Ein Autorisierungsfehler tritt auf und kann ignoriert werden.

8. Beachten Sie, dass **marketing_user1** Thema `salesevents` nicht nutzen kann.

   Wiederholen Sie die Schritte 1 bis 4, aber dieses Mal als **marketing_user1**.

   Führen Sie den folgenden Befehl zum Nutzen des Themas `salesevents` aus:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Vorherige Meldungen werden nicht angezeigt.

9. Zeigen Sie die Überwachungszugriffsereignisse in der Ranger-Benutzeroberfläche an.

   ![Ranger-Benutzeroberfläche – Richtlinienüberwachung](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht mehr benötigen, gehen Sie wie folgt vor, um den erstellten Kafka-Cluster zu löschen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Geben Sie oben im **Suchfeld** den Suchbegriff **HDInsight** ein.
1. Wählen Sie unter **Dienste** die Option **HDInsight-Cluster** aus.
1. Klicken Sie in der daraufhin angezeigten Liste mit den HDInsight-Clustern neben dem Cluster, den Sie für dieses Tutorial erstellt haben, auf die Auslassungspunkte ( **...** ). 
1. Klicken Sie auf **Löschen**. Klicken Sie auf **Ja**.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bring Your Own Key für Apache Kafka](../kafka/apache-kafka-byok.md)