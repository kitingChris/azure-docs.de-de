---
title: Verwenden von Azure Data Box zum Migrieren von Daten aus einem lokalen Hadoop Distributed File System-Speicher zu Azure Storage
description: Migrieren von Daten aus einem lokalen Hadoop Distributed File System-Speicher zu Azure Storage
services: storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4445a8566c04d30cfb8743cbd33623f2e23f0dde
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595399"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Verwenden von Azure Data Box zum Migrieren von Daten aus einem lokalen Hadoop Distributed File System-Speicher zu Azure Storage

Sie können Daten aus einem lokalen HDFS-Speicher (Hadoop Distributed File System) Ihres Hadoop-Clusters zu Azure Storage (Blobspeicher oder Data Lake Storage Gen2) migrieren, indem Sie ein Data Box-Gerät verwenden. Sie können zwischen einer Data Box mit 80 TB oder einer Data Box Heavy mit 770 TB wählen.

Dieser Artikel enthält Informationen zur Durchführung dieser Aufgaben:

> [!div class="checklist"]
> * Bereiten Sie das Migrieren Ihrer Daten vor.
> * Kopieren Sie Ihre Daten auf ein Data Box- oder Data Box Heavy-Gerät.
> * Senden Sie das Gerät an Microsoft zurück.
> * Verschieben Sie die Daten in Data Lake Storage Gen2.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um die Migration durchführen zu können:

* Zwei Speicherkonten; ein Konto, für das ein hierarchischer Namespace aktiviert ist, und ein Konto ohne aktivierten Namespace.

* Einen lokalen Hadoop-Cluster, in dem Ihre Quelldaten enthalten sind.

* Ein [Azure Data Box-Gerät](https://azure.microsoft.com/services/storage/databox/).

  * [Bestellen Sie die Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) oder [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Beachten Sie beim Bestellen Ihres Geräts, dass Sie ein Speicherkonto wählen, für das hierarchische Namespaces **nicht** aktiviert sind. Der Grund ist, dass für Data Box-Geräte die direkte Erfassung in Azure Data Lake Storage Gen2 noch nicht unterstützt wird. Sie müssen den Kopiervorgang in ein Speicherkonto und dann einen zweiten Kopiervorgang in das ADLS Gen2-Konto durchführen. Die unten angegebenen Schritte enthalten hierzu eine Anleitung.

  * Verkabeln und verbinden Sie die [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) oder [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) mit einem lokalen Netzwerk.

Wenn Sie bereit sind, können wir beginnen.

## <a name="copy-your-data-to-a-data-box-device"></a>Kopieren Ihrer Daten auf ein Data Box-Gerät

Wenn Ihre Daten auf ein einzelnes Data Box-Gerät passen, kopieren Sie die Daten auf das Data Box-Gerät. 

Wenn die Menge Ihrer Daten die Kapazität des Data Box-Geräts überschreitet, verwenden Sie das [optionale Verfahren zum Aufteilen der Daten auf mehrere Data Box-Geräte](#appendix-split-data-across-multiple-data-box-devices), und führen Sie dann diesen Schritt aus. 

Zum Kopieren der Daten aus Ihrem lokalen HDFS-Speicher auf ein Data Box-Gerät richten Sie einige Dinge ein und verwenden dann das Tool [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html).

Führen Sie diese Schritte aus, um Daten über die REST-APIs des Blob-/Objektspeichers auf Ihr Data Box-Gerät zu kopieren. Über die REST-API-Schnittstelle wird das Gerät als HDFS-Speicher für Ihren Cluster angezeigt.

1. Bevor Sie die Daten per REST kopieren, sollten Sie die Sicherheits- und Verbindungsprimitive zum Herstellen einer Verbindung mit der REST-Schnittstelle auf dem Data Box- oder Data Box Heavy-Gerät identifizieren. Melden Sie sich an der lokalen Webbenutzeroberfläche von Data Box an, und navigieren Sie zur Seite **Verbindung herstellen und Daten kopieren**. Suchen Sie für das Azure-Speicherkonto für Ihr Gerät unter **Zugriffseinstellungen** nach **REST**, und wählen Sie die Option aus.

    ![Seite „Verbindung herstellen und Daten kopieren“](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Kopieren Sie im Dialogfeld „Auf Speicherkonto zugreifen und Daten hochladen“ den **Blob-Dienstendpunkt** und den **Speicherkontoschlüssel**. Lassen Sie beim Blob-Dienstendpunkt den Teil `https://` und den nachgestellten Schrägstrich weg.

    Der Endpunkt ist in diesem Fall: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Der Hostteil des verwendeten URI ist: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Ein Beispiel hierfür finden Sie in der Beschreibung der [Verbindungsherstellung mit REST per http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Dialogfeld „Auf Speicherkonto zugreifen und Daten hochladen“](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Fügen Sie `/etc/hosts` auf jedem Knoten den Endpunkt und die Data Box- bzw. Data Box Heavy-IP-Adresse hinzu.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Falls Sie für DNS einen anderen Mechanismus verwenden, sollten Sie sicherstellen, dass der Data Box-Endpunkt aufgelöst werden kann.

4. Legen Sie die Shellvariable `azjars` auf den Speicherort der JAR-Dateien `hadoop-azure` und `azure-storage` fest. Sie finden diese Dateien im Hadoop-Installationsverzeichnis.

    Um zu bestimmen, ob diese Dateien vorhanden sind, verwenden Sie den folgenden Befehl: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Ersetzen Sie den Platzhalter `<hadoop_install_dir>` durch den Pfad zu dem Verzeichnis, in dem Sie Hadoop installiert haben. Achten Sie darauf, dass Sie vollqualifizierte Pfade zu verwenden.

    Beispiele:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar``azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Erstellen Sie den Speichercontainer, den Sie beim Kopieren von Daten verwenden möchten. Sie müssen auch ein Zielverzeichnis als Teil dieses Befehls angeben. Dies kann zu diesem Zeitpunkt ein Dummyzielverzeichnis sein.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Ersetzen Sie den Platzhalter `<blob_service_endpoint>` durch den Namen Ihres Endpunkts des Blobdiensts.

    * Ersetzen Sie den Platzhalter `<account_key>` durch den Zugriffsschlüssel Ihres Speicherkontos.

    * Ersetzen Sie den Platzhalter `<container-name>` durch den Namen Ihres Containers.

    * Ersetzen Sie den Platzhalter `<destination_directory>` durch den Namen des Verzeichnisses, in das Sie Ihre Daten kopieren möchten.

6. Führen Sie einen „list“-Befehl aus, um sicherzustellen, dass Ihr Container und Verzeichnis erstellt wurden.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Ersetzen Sie den Platzhalter `<blob_service_endpoint>` durch den Namen Ihres Endpunkts des Blobdiensts.

   * Ersetzen Sie den Platzhalter `<account_key>` durch den Zugriffsschlüssel Ihres Speicherkontos.

   * Ersetzen Sie den Platzhalter `<container-name>` durch den Namen Ihres Containers.

7. Kopieren Sie Daten aus dem Hadoop-HDFS zum Data Box-Blobspeicher in den zuvor erstellten Container. Wenn das Verzeichnis, in das Sie kopieren möchten, nicht gefunden wurde, wird es vom Befehl automatisch erstellt.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Ersetzen Sie den Platzhalter `<blob_service_endpoint>` durch den Namen Ihres Endpunkts des Blobdiensts.

    * Ersetzen Sie den Platzhalter `<account_key>` durch den Zugriffsschlüssel Ihres Speicherkontos.

    * Ersetzen Sie den Platzhalter `<container-name>` durch den Namen Ihres Containers.

    * Ersetzen Sie den Platzhalter `<exlusion_filelist_file>` durch den Namen der Datei, die Ihre Liste ausgeschlossener Dateien enthält.

    * Ersetzen Sie den Platzhalter `<source_directory>` durch den Namen des Verzeichnisses, das die Daten enthält, die Sie kopieren möchten.

    * Ersetzen Sie den Platzhalter `<destination_directory>` durch den Namen des Verzeichnisses, in das Sie Ihre Daten kopieren möchten.

    Die Option `-libjars` wird verwendet, um die Datei `hadoop-azure*.jar` und die abhängige Datei `azure-storage*.jar` für `distcp` verfügbar zu machen. Dies kann für einige Cluster bereits erfolgt sein.

    Im folgenden Beispiel wird veranschaulicht, wie der Befehl `distcp` zum Kopieren von Daten verwendet wird.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Erhöhen Sie die Kopiergeschwindigkeit wie folgt:

    * Versuchen Sie, die Anzahl von Zuordnungen zu ändern. (Im obigen Beispiel werden „`m` = 4“ Zuordnungen verwendet.)

    * Versuchen Sie, mehrere `distcp`-Elemente parallel auszuführen.

    * Beachten Sie hierbei, dass für große Dateien eine bessere Leistung als für kleine Dateien erzielt wird.

## <a name="ship-the-data-box-to-microsoft"></a>Senden des Data Box-Geräts an Microsoft

Führen Sie diese Schritte aus, um das Data Box-Gerät vorzubereiten und an Microsoft zu senden.

1. [Bereiten Sie den Versand der Data Box oder Data Box Heavy vor.](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)

2. Laden Sie die BOM-Dateien herunter, nachdem die Vorbereitung des Geräts abgeschlossen ist. Sie verwenden diese BOM- bzw. Manifestdateien später, um sich zu vergewissern, dass die Daten in Azure hochgeladen wurden.

3. Fahren Sie das Gerät herunter, und entfernen Sie die Kabel.

4. Planen Sie eine Abholung durch UPS.

    * Die entsprechenden Informationen für Data Box-Geräte finden Sie unter [Zurücksenden der Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Die entsprechenden Informationen für Data Box Heavy-Geräte finden Sie unter [Zurücksenden von Azure Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Nachdem Ihr Gerät von Microsoft empfangen wurde, wird es mit dem Netzwerk des Rechenzentrums verbunden, und die Daten werden in das Speicherkonto hochgeladen (mit deaktivierten hierarchischen Namespaces), das Sie bei der Bestellung des Geräts angegeben haben. Überprüfen Sie anhand der BOM-Dateien, ob Ihre gesamten Daten in Azure hochgeladen wurden. Sie können diese Daten jetzt in ein Data Lake Storage Gen2-Speicherkonto verschieben.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Verschieben der Daten in Azure Data Lake Storage Gen2

Die Daten befinden sich bereits in Ihrem Azure Storage-Konto. Sie kopieren die Daten nun in Ihr Azure Data Lake Storage-Konto, und Sie wenden Zugriffsberechtigungen auf Dateien und Verzeichnisse an.

> [!NOTE]
> Dieser Schritt ist erforderlich, wenn Sie Azure Data Lake Storage Gen2 als Datenspeicher verwenden. Falls Sie nur ein Blobspeicherkonto ohne hierarchischen Namespace als Datenspeicher nutzen, können Sie diesen Abschnitt überspringen.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Kopieren von Daten in das Azure Data Lake Storage Gen2-Konto

Sie können Daten kopieren, indem Sie Azure Data Factory oder Ihren Azure-basierten Hadoop-Cluster verwenden.

* Informationen zum Verwenden von Azure Data Factory finden Sie unter [Laden von Daten in Azure Data Lake Storage Gen2 mit Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Geben Sie **Azure Blob Storage** als Quelle an.

* Um Ihren Azure-basierten Hadoop-Cluster zu verwenden, führen Sie diesen DistCp-Befehl aus:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Ersetzen Sie die Platzhalter `<source_account>` und `<dest_account>` durch den Namen des Quell- bzw. den Namen des Zielspeicherkontos.

    * Ersetzen Sie die Platzhalter `<source_container>` und `<dest_container>` durch den Namen des Quell- bzw. den Namen des Zielcontainers.

    * Ersetzen Sie die Platzhalter `<source_path>` und `<dest_path>` durch den Namen des Quell- bzw. den Namen des Zielverzeichnispfads.

    * Ersetzen Sie den Platzhalter `<source_account_key>` durch den Zugriffsschlüssel des Speicherkontos, das die Daten enthält.

    Mit diesem Befehl werden sowohl Daten als auch Metadaten aus Ihrem Speicherkonto in Ihr Data Lake Storage Gen2-Speicherkonto kopiert.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Erstellen eines Dienstprinzipals für Ihr Azure Data Lake Storage Gen2-Konto

Informationen, wie ein Dienstprinzipal erstellt wird, finden Sie unter [Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* Achten Sie beim Ausführen der Schritte im Abschnitt [Zuweisen der Anwendung zu einer Rolle](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) des Artikels darauf, dem Dienstprinzipal die Rolle **Mitwirkender an Storage-Blobdaten** zuzuweisen.

* Fügen Sie, wenn Sie Schritte ausführen, die im Abschnitt [Abrufen von Werten für die Anmeldung](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) des Artikels aufgeführt sind, die Werte für die Anwendungs-ID, die Mandanten-ID und den geheimen Clientschlüssel in eine Textdatei ein. Sie benötigen sie in Kürze.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Generieren einer Liste der kopierten Dateien mit deren Berechtigungen

Führen Sie im lokalen Hadoop-Cluster diesen Befehl aus:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Dieser Befehl generiert eine Liste der kopierten Dateien mit deren Berechtigungen.

> [!NOTE]
> Abhängig von der Anzahl der Dateien im Hadoop Distributed File System (HDFS) kann das Ausführen dieses Befehls lange dauern.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Generieren einer Liste der Identitäten und Zuordnen dieser Identitäten zu Azure Active Directory-Identitäten

1. Laden Sie das Skript `copy-acls.py` herunter. Informationen hierzu finden Sie in diesem Artikel im Abschnitt [Herunterladen von Hilfsskripts und Einrichten Ihres Edgeknotens, um sie auszuführen](#download-helper-scripts).

2. Führen Sie den folgenden Befehl aus, um eine Liste der eindeutigen Identitäten zu generieren.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Dieses Skript generiert eine Datei namens `id_map.json`, die die Identitäten enthält, die Sie für das Zuordnen zu AAD-basierten (Azure Active Directory) Identitäten benötigen.

3. Öffnen Sie die Datei `id_map.json` in einem Text-Editor.

4. Aktualisieren Sie für jedes JSON-Objekt, das in der Datei aufgeführt ist, das `target`-Attribut von entweder einem AAD-Benutzerprinzipalnamen oder einer Objekt-ID (OID) mit der entsprechenden zugeordneten Identität. Wenn Sie damit fertig sind, speichern Sie die Datei. Sie benötigen diese Datei im nächsten Schritt.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Anwenden von Berechtigungen auf kopierte Dateien und Anwenden von Identitätszuordnungen

Führen Sie den folgenden Befehl aus, um Berechtigungen auf die Daten anzuwenden, die Sie in das Data Lake Storage Gen2-Konto kopiert haben:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Ersetzen Sie den Platzhalter `<storage-account-name>` durch den Namen Ihres Speicherkontos.

* Ersetzen Sie den Platzhalter `<container-name>` durch den Namen Ihres Containers.

* Ersetzen Sie die Platzhalter `<application-id>` und `<client-secret>` durch die Anwendungs-ID bzw. den geheimen Clientschlüssel, die Sie notiert haben, als Sie den Dienstprinzipal erstellt haben.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Anhang: Aufteilen von Daten auf mehrere Data Box-Geräte

Bevor Sie Ihre Daten auf ein Data Box-Gerät verschieben, müssen Sie einige Hilfsskripts herunterladen, müssen Sie sicherstellen, dass Ihre Daten so organisiert sind, dass sie auf ein Data Box-Gerät passen, und müssen Sie alle nicht benötigten Dateien ausschließen.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Herunterladen von Hilfsskripts und Einrichten Ihres Edgeknotens, um sie auszuführen

1. Führen Sie auf Ihrem Edge- oder Hauptknoten Ihres lokalen Hadoop-Clusters diesen Befehl aus:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Mit diesem Befehl wird das GitHub-Repository geklont, das die Hilfsskripts enthält.

2. Installieren Sie das [jq](https://stedolan.github.io/jq/)-Paket auf Ihrem lokalen Computer.

   ```bash
   
   sudo apt-get install jq
   ```

3. Installieren Sie das Python-Paket [Requests](http://docs.python-requests.org/en/master/).

   ```bash
   
   pip install requests
   ```

4. Legen Sie Ausführungsberechtigungen für die benötigten Skripts fest.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Sicherstellen, dass Ihre Daten so organisiert sind, dass sie auf ein Data Box-Gerät passen

Überschreitet der Umfang Ihrer Daten die Größe eines einzelnen Data Box-Geräts, können Sie Dateien in Gruppen aufteilen, die Sie auf mehreren Data Box-Geräten speichern können.

Wenn Ihre gesamten Daten auf ein einzelnes Data Box-Gerät passen, können Sie zum nächsten Abschnitt springen.

1. Führen Sie mit erhöhten Rechten das Skript `generate-file-list` aus, das Sie entsprechend der Anleitung im vorherigen Abschnitt heruntergeladen haben.

   Nachstehend sind die Befehlsparameter beschrieben:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Kopieren Sie die generierten Dateilisten in das Hadoop Distributed File System, damit sie für den [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)-Auftrag zugänglich sind.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Ausschließen von nicht benötigten Dateien

Sie müssen einige Verzeichnisse aus dem DisCp-Auftrag ausschließen. Schließen Sie beispielsweise Verzeichnisse aus, die Statusinformationen enthalten, die für den Clusterbetrieb benötigt werden.

Erstellen Sie in dem lokalen Hadoop-Cluster, in dem Sie den DistCp-Auftrag ausführen möchten, eine Datei, die die Liste der Verzeichnisse enthält, die Sie ausschließen möchten.

Hier sehen Sie ein Beispiel:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Data Lake Storage Gen2 mit HDInsight-Clustern funktioniert. Siehe [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
