---
title: Konzepte – Grundlagen zu Kubernetes für Azure Kubernetes Service (AKS)
description: Lernen Sie die grundlegenden Cluster- und Workloadkomponenten von Kubernetes kennen, und erfahren Sie, wie diese mit den Features in Azure Kubernetes Service (AKS) in Zusammenhang stehen.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mlearned
ms.openlocfilehash: 5f387310e737982b824d0ac9662822d9a74f39e9
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616021"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service (AKS)

Da die Anwendungsentwicklung zunehmend auf eine containerbasierte Vorgehensweise setzt, ist es wichtig, die Ressourcen zu orchestrieren und zu verwalten. Kubernetes ist die führende Plattform, die Funktionen bietet, um eine zuverlässige Planung fehlertoleranter Anwendungsworkloads bereitzustellen. Azure Kubernetes Service (AKS) ist ein Managed Kubernetes-Angebot, das die containerbasierte Anwendungsbereitstellung und -verwaltung weiter vereinfacht.

In diesem Artikel werden die grundlegenden Kubernetes-Infrastrukturkomponenten wie *Clustermaster*, *Knoten* und *Knotenpools* vorgestellt. Darüber hinaus werden Workloadressourcen wie *Pods*, *Bereitstellungen* und *Sets* erläutert, und es wird beschrieben, wie Sie Ressourcen in *Namespaces* gruppieren.

## <a name="what-is-kubernetes"></a>Was ist Kubernetes?

Kubernetes ist eine schnell wachsende Plattform, die containerbasierte Anwendungen und die zugehörigen Netzwerk- und Speicherkomponenten verwaltet. Der Fokus liegt auf den Anwendungsworkloads, nicht auf den zugrunde liegenden Infrastrukturkomponenten. Kubernetes bietet einen deklarativen Ansatz für Bereitstellungen und wird durch einen stabilen Satz an APIs für Verwaltungsvorgänge unterstützt.

Sie können moderne, portierbare, auf Microservices basierende Anwendungen erstellen und ausführen, die von Kubernetes-Funktionen für die Orchestrierung und Verwaltung der Verfügbarkeit dieser Anwendungskomponenten profitieren. Kubernetes unterstützt sowohl zustandslose als auch zustandsbehaftete Anwendungen für Teams, die zunehmend auf Microservices basierende Anwendungen entwickeln.

Als offene Plattform ermöglicht Kubernetes Ihnen, Ihre Anwendungen mit Ihren bevorzugten Programmiersprachen, Betriebssystemen, Bibliotheken oder Messagingbussen zu erstellen. Vorhandene CI/CD-Tools (Continuous Integration/Continuous Delivery) können in Kubernetes integriert werden, um Releases zu planen und bereitzustellen.

Azure Kubernetes Service (AKS) ist ein Managed Kubernetes-Dienst, der die Komplexität von Bereitstellungs- und wichtigen Verwaltungsaufgaben reduziert – beispielsweise die Koordination von Upgrades. Die AKS-Clustermaster werden von der Azure-Plattform verwaltet, und Sie zahlen nur für die AKS-Knoten, die Ihre Anwendungen ausführen. AKS baut auf der Open-Source-Engine von Azure Kubernetes Service ([aks-engine][aks-engine]) auf.

## <a name="kubernetes-cluster-architecture"></a>Kubernetes-Cluster – Architektur

Ein Kubernetes-Cluster ist in zwei Komponenten unterteilt:

- *Clustermasterknoten* stellen die grundlegenden Kubernetes-Dienste und Orchestrierungsfunktionen für Anwendungsworkloads bereit.
- *Knoten* führen Ihre Anwendungsworkloads aus.

![Komponenten der Kubernetes-Clustermaster und -Clusterknoten](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>Clustermaster

Wenn Sie einen AKS-Cluster erstellen, wird automatisch ein Clustermaster erstellt und konfiguriert. Dieser Clustermaster wird als verwaltete Azure-Ressource bereitgestellt, die für den Benutzer abstrahiert wird. Für den Clustermaster fallen keine Kosten an. Nur die Knoten, die Teil des AKS-Clusters sind, werden in Rechnung gestellt.

Der Clustermaster umfasst die folgenden Kubernetes-Kernkomponenten:

- *kube-apiserver*: Die zugrunde liegenden Kubernetes-APIs werden auf dem API-Server verfügbar gemacht. Diese Komponente ermöglicht die Interaktion für Verwaltungstools, z.B. `kubectl` oder das Kubernetes-Dashboard.
- *etcd*: Für die Verwaltung des Zustands Ihres Kubernetes-Clusters und Ihrer Kubernetes-Konfiguration ist *etcd* ein hoch verfügbarer Schlüssel-Wert-Speicher in Kubernetes.
- *kube-scheduler*: Wenn Sie Anwendungen erstellen oder skalieren, ermittelt der Scheduler, welche Knoten die Workload ausführen können, und startet diese.
- *kube-controller-manager*: Der Controller Manager überwacht eine Reihe kleinerer Controller, die Aktionen wie beispielsweise das Replizieren von Pods und das Verarbeiten von Knotenvorgängen ausführen.

AKS stellt einen Clustermaster mit Einzelmandant, einem dedizierten API-Server, einem Scheduler usw. bereit. Sie definieren die Anzahl und Größe der Knoten, und die Azure-Plattform konfiguriert die sichere Kommunikation zwischen Clustermaster und Clusterknoten. Die Interaktion mit dem Clustermaster erfolgt über Kubernetes-APIs, z.B. `kubectl` oder das Kubernetes-Dashboard.

Dieser verwaltete Clustermaster bedeutet, dass Sie Komponenten wie einen hoch verfügbaren *etcd*-Speicher nicht konfigurieren müssen. Er bedeutet aber auch, dass Sie nicht direkt auf den Clustermaster zugreifen können. Upgrades für Kubernetes werden über die Azure CLI oder das Azure-Portal orchestriert. Ein Upgrade erfolgt erst auf dem Clustermaster, dann auf den Knoten. Zum Beheben möglicher Probleme können Sie über Azure Monitor-Protokolle die Clustermasterprotokolle überprüfen.

Wenn Sie den Clustermaster auf eine bestimmte Weise konfigurieren müssen oder direkten Zugriff benötigen, können Sie mit [aks-engine][aks-engine] selbst einen Kubernetes-Cluster bereitstellen.

Entsprechende bewährte Methoden finden Sie unter [Best Practices für Clustersicherheit und Upgrades in Azure Kubernetes Service (AKS)][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Knoten und Knotenpools

Zum Ausführen Ihrer Anwendungen und der unterstützenden Dienste benötigen Sie einen Kubernetes-*Knoten*. Ein AKS-Cluster besteht aus mindestens einem Knoten, bei dem es sich um einen virtuellen Azure-Computer handelt, der die Kubernetes-Knotenkomponenten und die Containerruntime ausführt:

- Das `kubelet` ist der Kubernetes-Agent, der die Orchestrierungsanforderungen des Clustermasters und die Planung der Ausführung der angeforderten Container verarbeitet.
- Die virtuellen Netzwerkfunktionen werden vom *kube-proxy* auf jedem Knoten verarbeitet. Der Proxy leitet den Netzwerkdatenverkehr weiter und verwaltet die IP-Adressen für Dienste und Pods.
- Die *Containerruntime* ist die Komponente, die es Anwendungen in Containern ermöglicht, zusätzliche Ressourcen wie das virtuelle Netzwerk und den virtuellen Speicher auszuführen und damit zu interagieren. In AKS wird Moby als Containerruntime verwendet.

![Virtuelle Azure-Computer und unterstützende Ressourcen für einen Kubernetes-Knoten](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Die Größe der Azure-VMs für Ihre Knoten definiert die Anzahl der CPUs, die Größe des Arbeitsspeichers und die Größe und den Typ des verfügbaren Speichers (z.B. hochleistungsfähige SSDs oder reguläre HDDs). Wenn Sie damit rechnen, dass Sie Anwendungen bereitstellen werden, die große Mengen an CPUs und Arbeitsspeicher oder hochleistungsfähigen Datenspeicher erfordern, planen Sie die Knotengröße entsprechend. Sie können auch die Anzahl von Knoten in Ihrem AKS-Cluster zentral hochskalieren, um Anforderungen zu erfüllen.

In AKS basiert das VM-Image für die Knoten in Ihrem Cluster derzeit auf Ubuntu Linux oder Windows Server 2019. Wenn Sie einen AKS-Cluster erstellen oder die Anzahl von Knoten zentral hochskalieren, erstellt die Azure-Plattform die erforderliche Anzahl von VMs und konfiguriert diese. Sie müssen keine manuellen Konfigurationsaufgaben ausführen. Agent-Knoten werden als Standard-VMs in Rechnung gestellt, sodass alle etwaigen Rabatte, über die Sie auf die von Ihnen verwendete VM-Größe verfügen (einschließlich [Azure-Reservierungen][reservation-discounts]), automatisch angewendet werden.

Wenn Sie ein anderes Hostbetriebssystem oder eine andere Containerruntime benötigen oder benutzerdefinierte Pakete verwenden müssen, können Sie mit [aks-engine][aks-engine] selbst einen Kubernetes-Cluster bereitstellen. Die `aks-engine`-Upstreamreleases stellen Konfigurationsoptionen bereit, bevor diese offiziell in AKS-Clustern unterstützt werden. Wenn Sie z.B. eine andere Containerruntime als Moby verwenden möchten, können Sie mithilfe von `aks-engine` einen Kubernetes-Cluster konfigurieren und bereitstellen, der Ihre aktuellen Anforderungen erfüllt.

### <a name="resource-reservations"></a>Ressourcenreservierungen

Sie müssen nicht die Kubernetes-Kernkomponenten auf jedem Knoten verwalten, z.B. *kubelet*, *kube-proxy* und *kube-dns*, sie belegen jedoch einige der verfügbaren Computeressourcen. Um die Leistung und Funktionalität des Knotens zu gewährleisten, werden auf jedem Knoten die folgenden Computeressourcen reserviert:

- **CPU**: 60 ms
- **Arbeitsspeicher:** 20 % bis zu 4 GiB

Diese Reservierungen führen dazu, dass möglicherweise eine geringere Menge verfügbarer CPU-Leistung und Arbeitsspeicher für Ihre Anwendungen angezeigt wird, als der eigentliche Knoten enthält. Wenn Ressourceneinschränkungen aufgrund der Anzahl von ausgeführten Anwendungen vorliegen, gewährleisten diese Reservierungen, dass für die Kubernetes-Kernkomponenten weiterhin CPU-Leistung und Arbeitsspeicher zur Verfügung stehen. Die Ressourcenreservierungen können nicht geändert werden.

Beispiel:

- Knotengröße **Standard DS2 v2** mit 2 vCPUs und 7 GiB Arbeitsspeicher
    - 20 % von 7 GiB Arbeitsspeicher = 1,4 GiB
    - Insgesamt *7 – 1,4 = 5,6 GiB* Arbeitsspeicher für den Knoten verfügbar
    
- Knotengröße **Standard E4s v3** mit 4 vCPUs und 32 GiB Arbeitsspeicher
    - 20 % von 32 GiB Arbeitsspeicher = 6,4 GiB, AKS reserviert jedoch maximal 4 GiB
    - Insgesamt *32 – 4 = 28 GiB* für den Knoten verfügbar
    
Das zugrunde liegende Betriebssystem des Knotens erfordert auch eine gewisse Menge an CPU- und Speicherressourcen für die eigenen Kernfunktionen.

Entsprechende bewährte Methoden finden Sie unter [Best Practices für grundlegende Schedulerfunktionen in Azure Kubernetes Service (AKS)][operator-best-practices-scheduler].

### <a name="node-pools"></a>Knotenpools

Knoten mit der gleichen Konfiguration werden in *Knotenpools* gruppiert. Ein Kubernetes-Cluster enthält mindestens einen Knotenpool. Die anfängliche Knotenanzahl und -größe wird beim Erstellen eines AKS-Clusters definiert, wobei ein *Standardknotenpool* erstellt wird. Dieser Standardknotenpool in AKS enthält die zugrunde liegenden VMs, die Ihre Agent-Knoten ausführen. Die Unterstützung für mehrere Knotenpools in AKS ist derzeit in der Vorschauversion.

Wenn Sie einen AKS-Cluster skalieren oder ein Upgrade des Clusters durchführen, wird die Aktion im Standardknotenpool ausgeführt. Sie können auch auswählen, einen bestimmten Knotenpool zu skalieren oder ein Upgrade für diesen auszuführen. Bei Upgradevorgängen wird die Ausführung von Containern in anderen Knoten im Knotenpool geplant, bis das Upgrade für alle Knoten erfolgreich durchgeführt wurde.

Weitere Informationen zur Verwendung mehrerer Knotenpools in AKS finden Sie unter [Erstellen und Verwalten mehrerer Knotenpools für einen Cluster in AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Knotenselektoren

In einem AKS-Cluster mit mehreren Knotenpools müssen Sie dem Kubernetes-Scheduler möglicherweise mitteilen, welcher Knotenpool für eine bestimmte Ressource verwendet werden soll. Beispielsweise sollten Eingangscontroller nicht auf Windows Server-Knoten (derzeit in der Vorschau in AKS) ausgeführt werden. Mit Knotenselektoren können Sie verschiedene Parameter festlegen, wie z.B. das Betriebssystem des Knotens, um zu steuern, wo ein Pod geplant werden soll.

Das folgende einfache Beispiel plant eine NGINX-Instanz auf einem Linux-Knoten unter Verwendung des Knotenselektors *"beta.kubernetes.io/os": linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Weitere Informationen zum Steuern, wo Pods geplant werden, finden Sie unter [Best Practices für erweiterte Schedulerfunktionen in Azure Kubernetes Service (AKS)][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Pods

Kubernetes verwendet *Pods*, um eine Instanz Ihrer Anwendung auszuführen. Ein Pod repräsentiert eine einzelne Instanz der Anwendung. Pods weisen in der Regel eine 1:1-Zuordnung mit einem Container auf. Es gibt jedoch auch erweiterte Szenarien, in denen ein Pod mehrere Container enthalten kann. Diese Pods mit mehreren Containern werden zusammen auf dem gleichen Knoten geplant und ermöglichen es Containern, zugehörige Ressourcen gemeinsam zu nutzen.

Wenn Sie einen Pod erstellen, können Sie *Ressourcenlimits* definieren, um eine bestimmte Menge an CPU- oder Arbeitsspeicherressourcen anzufordern. Der Kubernetes Scheduler versucht, die Ausführung der Pods auf einem Knoten mit den verfügbaren Ressourcen zu planen, um die Anforderung zu erfüllen. Sie können auch maximale Ressourcenlimits angeben, um zu verhindern, dass ein bestimmter Pod zu viele Computeressourcen des zugrunde liegenden Knotens verbraucht. Eine bewährte Methode ist es, Ressourcenlimits für alle Pods einzurichten, um den Kubernetes Scheduler darüber zu informieren, welche Ressourcen benötigt werden und zulässig sind.

Weitere Informationen finden Sie unter [Kubernetes-Pods][kubernetes-pods] and [Kubernetes pod lifecycle][kubernetes-pod-lifecycle].

Ein Pod ist eine logische Ressource, aber die Container sind die Ressourcen, in denen die Anwendungsworkloads ausgeführt werden. Pods sind in der Regel kurzlebige Ressourcen, die gelöscht werden können. Einzeln geplante Pods bieten nicht alle Hochverfügbarkeits- und Redundanzfeatures von Kubernetes. Stattdessen werden Pods üblicherweise von Kubernetes-*Controllern* bereitgestellt und verwaltet, beispielsweise dem Bereitstellungscontroller.

## <a name="deployments-and-yaml-manifests"></a>Bereitstellungen und YAML-Manifeste

Eine *Bereitstellung* repräsentiert einen oder mehrere identische Pods, die vom Kubernetes-Bereitstellungscontroller verwaltet werden. Eine Bereitstellung definiert die Anzahl von *Replikaten* (Pods), die erstellt werden sollen. Der Kubernetes Scheduler stellt sicher, dass weitere Pods auf intakten Knoten geplant werden, falls Probleme mit Pods oder Knoten auftreten sollten.

Sie können Bereitstellungen aktualisieren, um die Konfiguration von Pods, das verwendete Containerimage oder den angeschlossenen Speicher zu ändern. Der Bereitstellungscontroller leert und beendet eine bestimmte Anzahl von Replikaten, erstellt Replikate gemäß der neuen Bereitstellungsdefinition und setzt den Prozess so lange fort, bis alle Replikate in der Bereitstellung aktualisiert wurden.

Die meisten zustandslosen Anwendungen in AKS sollten das Bereitstellungsmodell verwenden, anstatt einzelne Pods zu planen. Kubernetes kann die Integrität und den Status von Bereitstellungen überwachen, um sicherzustellen, dass die erforderliche Anzahl von Replikaten im Cluster ausgeführt wird. Wenn Sie nur einzelne Pods planen, werden diese nach dem Auftreten eines Problems nicht neu gestartet. Außerdem werden die Pods nicht auf intakten Knoten erneut geplant, wenn auf dem aktuellen Knoten ein Problem auftritt.

Wenn eine Anwendung erfordert, dass jederzeit ein Quorum aus Instanzen verfügbar ist, damit Verwaltungsentscheidungen getroffen werden können, darf diese Funktionalität nicht durch Aktualisierungsprozesse unterbrochen werden. *Budgets für die Unterbrechung von Pods* können verwendet werden, um zu definieren, wie viele Replikate in einer Bereitstellung während einer Aktualisierung oder eines Knotenupgrades heruntergefahren werden können. Ein Beispiel: Wenn Sie in Ihrer Bereitstellung über *5* Replikate verfügen, können Sie eine Podunterbrechung für *4* Replikate definieren, damit nur ein Replikat gleichzeitig gelöscht bzw. neu geplant werden darf. Ebenso wie bei Podressourcenlimits besteht eine bewährte Methode darin, Budgets für die Unterbrechung von Pods für Anwendungen zu definieren, für die immer eine bestimmte Mindestanzahl von Replikaten vorhanden sein muss.

Bereitstellungen werden in der Regel mit `kubectl create` oder `kubectl apply` erstellt und verwaltet. Um eine Bereitstellung zu erstellen, definieren Sie eine Manifestdatei im YAML-Format (YAML Ain't Markup Language). Das folgende Beispiel erstellt eine grundlegende Bereitstellung eines NGINX-Webservers. Die Bereitstellung gibt an, dass *3* Replikate erstellt werden sollen und dass Port *80* auf dem Container geöffnet werden soll. Es werden auch Ressourcenanforderungen und -limits für CPU und Arbeitsspeicher definiert.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Indem Sie Dienste wie Lastenausgleichsmodule im YAML-Manifest angeben, können Sie auch komplexere Anwendungen erstellen.

Weitere Informationen finden Sie unter [Kubernetes-Bereitstellungen][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Paketverwaltung mit Helm

Ein häufiger Ansatz für die Verwaltung von Anwendungen in Kubernetes ist die Verwendung von [Helm][helm]. Sie können vorhandene Helm-*Charts* erstellen und verwenden, die eine gepackte Version des Anwendungscodes und der Kubernetes-YAML-Manifeste zum Bereitstellen von Ressourcen enthalten. Diese Helm-Charts können lokal oder in einem Remoterepository gespeichert werden, z.B. einem [Helm-Chart-Repository in Azure Container Registry][acr-helm].

Zur Verwendung von Helm wird eine Serverkomponente namens *Tiller* in Ihrem Kubernetes-Cluster installiert. Tiller verwaltet die Installation von Charts im Cluster. Der Helm-Client selbst wird lokal auf Ihrem Computer installiert oder kann in der [Azure Cloud Shell][azure-cloud-shell] verwendet werden. Sie können im Client nach Helms-Charts suchen oder Helm-Charts erstellen und diese dann in Ihrem Kubernetes-Cluster installieren.

![Helm umfasst eine Clientkomponente und eine serverseitige Tiller-Komponente, die Ressourcen im Kubernetes-Cluster erstellt.](media/concepts-clusters-workloads/use-helm.png)

Weitere Informationen finden Sie unter [Installieren von Anwendungen mit Helm in Azure Kubernetes Service (AKS)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets und DaemonSets

Der Bereitstellungscontroller verwendet den Kubernetes Scheduler, um eine bestimmte Anzahl von Replikaten auf einem beliebigen verfügbaren Knoten mit verfügbaren Ressourcen auszuführen. Diese Art der Verwendung von Bereitstellungen mag für zustandslose Anwendungen ausreichend sein, nicht aber für Anwendungen, die eine permanente Namenskonvention oder einen permanenten Speicher benötigen. Bei Anwendungen, für die auf jedem Knoten oder auf ausgewählten Knoten in einem Cluster ein Replikat vorhanden sein muss, überprüft der Bereitstellungscontroller nicht, wie die Replikate auf den Knoten verteilt sind.

Es gibt zwei Kubernetes-Ressourcen, mit denen Sie diese Arten von Anwendungen verwalten können:

- *StatefulSets*: Verwalten den Zustand von Anwendungen über den Lebenszyklus eines einzelnen Pods hinweg, z.B. Speicher.
- *DaemonSets*: Stellen zu einem frühen Zeitpunkt im Kubernetes-Bootstrapprozess eine ausgeführte Ressource auf jedem Knoten sicher.

### <a name="statefulsets"></a>StatefulSets

Die moderne Anwendungsentwicklung strebt häufig nach zustandslosen Anwendungen, aber *StatefulSets* können für zustandsbehaftete Anwendungen verwendet werden, z.B. für Anwendungen, die Datenbankkomponenten enthalten. Ein StatefulSet ähnelt einer Bereitstellung insofern, als ein oder mehrere identische Pods erstellt und verwaltet werden. Replikate in einem StatefulSet folgen einem ordnungsgemäßen, sequenziellen Verfahren für Bereitstellung, Skalierung, Upgrades und Beendigungen. In einem StatefulSet bleiben Namenskonvention, Netzwerknamen und Speicher permanent erhalten, wenn Replikate neu geplant werden.

Sie definieren die Anwendung mit `kind: StatefulSet` im YAML-Format. Der StatefulSet-Controller sorgt dann für die Bereitstellung und Verwaltung der erforderlichen Replikate. Daten werden in permanenten Speicher geschrieben, der von Azure Managed Disks oder Azure Files bereitgestellt wird. Bei StatefulSets bleibt der zugrunde liegende permanente Speicher erhalten, auch wenn das StatefulSet gelöscht wird.

Weitere Informationen finden Sie unter [Kubernetes StatefulSets][kubernetes-statefulsets].

Replikate in einem StatefulSet werden auf einem beliebigen verfügbaren Knoten in einem AKS-Cluster geplant und ausgeführt. Wenn Sie sicherstellen müssen, dass mindestens ein Pod in Ihrem Set auf einem Knoten ausgeführt wird, können Sie stattdessen ein DaemonSet verwenden.

### <a name="daemonsets"></a>DaemonSets

Bei spezifischen Anforderungen an die Protokollsammlung oder Überwachung müssen Sie möglicherweise einen bestimmten Pod auf allen bzw. auf ausgewählten Knoten ausführen. Auch ein *DaemonSet* wird zum Bereitstellen von einem oder mehreren identischen Pods verwendet, aber der DaemonSet-Controller stellt sicher, dass jeder angegebene Knoten eine Instanz des Pods ausführt.

Der DaemonSet-Controller kann Pods auf Knoten zu einem frühen Zeitpunkt im Clusterstartprozess planen, bevor der Kubernetes Scheduler gestartet wird. Dies stellt sicher, dass die Pods in einem DaemonSet gestartet wurden, bevor herkömmliche Pods in einer Bereitstellung oder einem StatefulSet geplant werden.

Ähnlich wie StatefulSets wird auch ein DaemonSet mit `kind: DaemonSet` als Teil einer YAML-Definition definiert.

Weitere Informationen finden Sie unter [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Wenn Sie das [Add-On für virtuelle Knoten](virtual-nodes-cli.md#enable-virtual-nodes-addon) verwenden, erstellen DaemonSets keine Pods auf dem virtuellen Knoten.

## <a name="namespaces"></a>Namespaces

Kubernetes-Ressourcen wie Pods und Bereitstellungen werden logisch in einen *Namespace* gruppiert. Diese Gruppierungen ermöglichen es, einen AKS-Cluster logisch zu unterteilen und den Zugriff zum Erstellen, Anzeigen oder Verwalten von Ressourcen einzuschränken. Sie können Namespaces erstellen, um beispielsweise Unternehmensgruppen voneinander zu trennen. Benutzer können nur mit den Ressourcen innerhalb der ihnen zugewiesenen Namespaces interagieren.

![Kubernetes-Namespaces zur logischen Unterteilung von Ressourcen und Anwendungen](media/concepts-clusters-workloads/namespaces.png)

Wenn Sie einen AKS-Cluster erstellen, stehen Ihnen folgende Namespaces zur Verfügung:

- *default*: In diesem Namespace werden Pods und Bereitstellungen standardmäßig erstellt, wenn kein anderer Namespace angegeben wird. In kleineren Umgebungen können Sie Anwendungen direkt im Standardnamespace bereitstellen, ohne weitere logische Unterteilungen zu erstellen. Wenn Sie mit der Kubernetes-API interagieren, z.B. mit `kubectl get pods`, wird der Standardnamespace verwendet, wenn kein anderer Namespace angegeben wurde.
- *kube-system*: In diesem Namespace befinden sich grundlegende Ressourcen, beispielsweise Netzwerkfeatures wie DNS und Proxy oder das Kubernetes-Dashboard. In diesem Namespace stellen Sie in der Regel keine eigenen Anwendungen bereit.
- *kube-public*: Dieser Namespace wird in der Regel nicht genutzt. Er kann jedoch für Ressourcen verwendet werden, die über den gesamten Cluster hinweg sichtbar sein sollen, und er kann von allen Benutzern angezeigt werden.

Weitere Informationen finden Sie unter [Kubernetes-Namespaces][kubernetes-namespaces].

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden einige der wichtigsten Kubernetes-Komponenten sowie deren Anwendung in AKS-Clustern beschrieben. Weitere Informationen zu den wesentlichen Konzepten von Kubernetes und AKS finden Sie in den folgenden Artikeln:

- [Kubernetes/AKS: Zugriff und Identität][aks-concepts-identity]
- [Kubernetes/AKS: Sicherheit][aks-concepts-security]
- [Kubernetes/AKS: virtuelle Netzwerke][aks-concepts-network]
- [Kubernetes/AKS: Speicher][aks-concepts-storage]
- [Kubernetes/AKS: Skalierung][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md