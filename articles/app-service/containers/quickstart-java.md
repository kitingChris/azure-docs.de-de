---
title: Erstellen von Java-Web-Apps unter Linux – Azure App Service
description: In dieser Schnellstartanleitung stellen Sie in wenigen Minuten Ihre erste Java-App „Hallo Welt“ in Azure App Service unter Linux bereit.
keywords: Azure, App Service, Web-App, Linux, Java, Maven, Schnellstart
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 30689e05a2567646ff541818dc68a90c13da7a56
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297256"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>Schnellstart: Erstellen einer Java-App in App Service unter Linux

[App Service unter Linux](app-service-linux-intro.md) bietet einen hochgradig skalierbaren Webhostingdienst mit Self-Patching unter dem Linux-Betriebssystem. In dieser Schnellstartanleitung wird gezeigt, wie Sie mit der [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) und dem [Maven-Plug-In für Azure App Service](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) eine Java-Webarchivdatei (WAR) bereitstellen.
> [!NOTE]
>
> Dazu können auch gängige IDEs wie IntelliJ und Eclipse verwendet werden. Sehen Sie sich unsere ähnlichen Dokumente in den Schnellstarts für [Azure-Toolkit für IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) oder [Azure-Toolkit für Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) an.
>
![In Azure ausgeführte Beispiel-App](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Erstellen einer Java-App

Führen Sie den folgenden Maven-Befehl über die Eingabeaufforderung der Cloud Shell aus, um eine neue App mit dem Namen `helloworld` zu erstellen:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Konfigurieren des Maven-Plug-Ins

Verwenden Sie zum Bereitstellen über Maven den Code-Editor in der Cloud Shell, um die Projektdatei `pom.xml` im Verzeichnis `helloworld` zu öffnen. 

```bash
code pom.xml
```

Fügen Sie dann die folgende Plug-In-Definition im `<build>`-Element der Datei `pom.xml` hinzu.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
    </plugin>
</plugins>
```

Beim Prozess der Bereitstellung in Azure App Service werden Kontoanmeldeinformation aus der Azure CLI verwendet. [Melden Sie sich über die Azure CLI an](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), ehe Sie fortfahren.

```azurecli
az login
```

Dann können Sie die Bereitstellung konfigurieren, den maven-Befehl `mvn azure-webapp:config` an der Eingabeaufforderung ausführen und die Standardkonfigurationen verwenden, indem Sie die **EINGABETASTE** drücken, bis die Eingabeaufforderung **Confirm (Y/N)** (Bestätigen [ja/nein]) angezeigt wird. Drücken Sie dann **y**, um die Konfiguration abzuschließen.

```cmd
~@Azure:~/helloworld$ mvn azure-webapp:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.7.0:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. jre8 [*]
2. java11
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. jre8
3. TOMCAT 8.5 [*]
4. WILDFLY 14
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> In diesem Artikel arbeiten wir nur mit Java-Apps, die in WAR-Dateien enthalten sind. Das Plug-In unterstützt auch JAR-Webanwendungen. Dies können Sie unter [Bereitstellen einer Spring Boot-App mit JAR-Datei in Azure App Service unter Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) ausprobieren.

Navigieren Sie erneut zu `pom.xml`, um zu prüfen, ob die Plug-In-Konfiguration aktualisiert wurde. Sie können bei Bedarf andere Konfigurationen für den App Service direkt in Ihrer POM-Datei ändern. Einige gängige sind nachstehend aufgeführt:

 Eigenschaft | Erforderlich | BESCHREIBUNG | Version
---|---|---|---
`<schemaVersion>` | false | Gibt die Version des Konfigurationsschemas an. Folgende Werte werden unterstützt: `v1` und `v2`. | 1.5.2
`<resourceGroup>` | true | Azure-Ressourcengruppe für Ihre Web-App. | 0.1.0+
`<appName>` | true | Der Name Ihrer Web-App. | 0.1.0+
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | true | Gibt an, die Region, in der Ihre Web-App gehostet wird. Der Standardwert ist **westus**. Alle gültigen Regionen finden Sie im Abschnitt [Unterstützten Regionen](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region). | 0.1.0+
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | false | Der Tarif für Ihre Web-App. Der Standardwert ist **P1V2**.| 0.1.0+
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | true | Details zur Konfiguration der Laufzeitumgebung finden Sie [hier](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting). | 0.1.0+
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | true | Details zur Konfiguration der Bereitstellung finden Sie [hier](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting). | 0.1.0+

## <a name="deploy-the-app"></a>Bereitstellen der App

Stellen Sie Ihre Java-App mit dem folgenden Befehl in Azure bereit:

```bash
mvn package azure-webapp:deploy
```

Navigieren Sie nach Abschluss der Bereitstellung zur bereitgestellten Anwendung, indem Sie in Ihrem Webbrowser die folgende URL verwenden, z. B. `http://<webapp>.azurewebsites.net`. 

![In Azure ausgeführte Beispiel-App](media/quickstart-java/java-hello-world-in-browser.png)

**Glückwunsch!** Sie haben Ihre erste Java-App für App Service unter Linux bereitgestellt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, löschen Sie die Ressourcengruppe, indem Sie den folgenden Befehl in Cloud Shell ausführen:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Die Ausführung dieses Befehls kann eine Minute in Anspruch nehmen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Java-Unternehmens-App mit PostgreSQL](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurieren der Java-App](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD mit Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Weitere Azure-Ressourcen für Java-Entwickler](/java/azure/)
