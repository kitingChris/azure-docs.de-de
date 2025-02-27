---
title: Durchführen eines Upgrades für einen Azure Kubernetes Service-Cluster (AKS)
description: Erfahren Sie, wie Sie ein Upgrade für einen AKS-Cluster (Azure Kubernetes Service) vornehmen.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: dd88b5a044fe495da374178be8774f45bdd30f61
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614062"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Durchführen eines Upgrades für einen Azure Kubernetes Service-Cluster (AKS)

Im Verlauf des Lebenszyklus eines AKS-Clusters müssen Sie häufig ein Upgrade auf die neueste Kubernetes-Version vornehmen. Es ist wichtig, jeweils die aktuelle Kubernetes-Sicherheitsversion anzuwenden oder bei einem Upgrade die neuesten Features zu erhalten. In diesem Artikel wird veranschaulicht, wie Sie die Masterkomponenten oder einen einzelnen Standardknotenpool in einem AKS-Cluster aktualisieren.

Informationen zu AKS-Clustern, für die mehrere Knotenpools oder Windows Server-Knoten (in AKS jeweils in der Vorschauphase) verwendet werden, finden Sie unter [Durchführen eines Upgrades für einen Knotenpool][nodepool-upgrade].

## <a name="before-you-begin"></a>Voraussetzungen

Der Artikel setzt voraus, dass Sie mindestens Version 2.0.65 der Azure-Befehlszeilenschnittstelle (Azure CLI) ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Suchen nach verfügbaren AKS-Clusterupgrades

Überprüfen Sie mithilfe des Befehls [az aks get-upgrades][az-aks-get-upgrades], welche Kubernetes-Versionen für Ihren Cluster verfügbar sind. Im folgenden Beispiel werden für den Cluster namens *myAKSCluster* in der Ressourcengruppe namens *myResourceGroup* verfügbare Upgrades gesucht:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Beim Upgrade eines AKS-Clusters können Nebenversionen von Kubernetes nicht übersprungen werden. Beispielsweise sind Upgrades von *1.11.x* -> *1.12.x* oder *1.12.x* -> *1.13.x* zulässig, während ein Upgrade von *1.11.x* -> *1.13.x* nicht möglich ist.
>
> Für ein Upgrade von *1.11.x* -> *1.13.x* müssen Sie zuerst ein Upgrade von *1.11.x* -> *1.12.x* und dann ein Upgrade von *1.12.x* -> *1.13.x* durchführen.

Die Ausgabe im folgenden Beispiel zeigt, dass der Cluster auf Version *1.12.7* oder *1.12.8* aktualisiert werden kann:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>Aktualisieren eines AKS-Clusters

Wenn Sie über eine Liste mit den verfügbaren Versionen für Ihren AKS-Cluster verfügen, verwenden Sie den Befehl [az aks upgrade][az-aks-upgrade], um das Upgrade auszuführen. Während des Upgradeprozesses fügt AKS dem Cluster einen neuen Knoten hinzu, auf dem die angegebene Kubernetes-Version ausgeführt wird, und führt dann einen Absperr- und Ausgleichsvorgang für einen der alten Knoten aus, um die Beeinträchtigung für ausgeführte Anwendungen möglichst gering zu halten. command to upgrade. During the upgrade process, AKS adds a new node to the cluster that runs the specified Kubernetes version, then carefully [cordon and drains][kubernetes-drain] Wenn für den neuen Knoten die Ausführung von Anwendungspods bestätigt wird, wird der alte Knoten gelöscht. Dieser Prozess wird wiederholt, bis alle Knoten im Cluster aktualisiert wurden.

Im folgenden Beispiel wird der Cluster auf Version *1.12.8* aktualisiert:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

Die Dauer des Clusterupgrades hängt von der Anzahl der vorhanden Knoten ab und kann einige Minuten in Anspruch nehmen.

Überprüfen Sie nun mit dem Befehl [az aks show][az-aks-show], ob das Upgrade erfolgreich war:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Die Ausgabe im folgenden Beispiel zeigt, dass im Cluster nun Version *1.12.8* ausgeführt wird:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde gezeigt, wie Sie einen vorhandenen AKS-Cluster aktualisieren. In verschiedenen Tutorials können Sie mehr über die Bereitstellung und Verwaltung von AKS-Clustern erfahren.

> [!div class="nextstepaction"]
> [AKS-Tutorials][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
