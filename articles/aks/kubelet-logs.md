---
title: Anzeigen von Kubelet-Protokollen in Azure Kubernetes Service (AKS)
description: Informationen zur Problembehandlung in den Kubelet-Protokollen aus AKS-Knoten (Azure Kubernetes Service)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/05/2019
ms.author: mlearned
ms.openlocfilehash: 65b16b3ddc209ef5d2f6287a04cfe402c3b205c6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615179"
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Abrufen von Kubelet-Protokollen aus Azure Kubernetes Service-Clusterknoten (AKS)

Im Rahmen der Ausführung eines AKS-Clusters müssen Sie vielleicht Protokolle überprüfen, um ein Problem zu lösen. Im Azure-Portal können Protokolle für die [AKS-Masterkomponenten][aks-master-logs] oder für Container in einem AKS-Cluster angezeigt werden. or [containers in an AKS cluster][azure-container-logs] Es kann vorkommen, dass Sie *kubelet*-Protokolle zur Problembehandlung aus einem AKS-Knoten abrufen müssen.

In diesem Artikel erfahren Sie, wie Sie `journalctl` zum Anzeigen der *kubelet*-Protokolle auf einem AKS-Knoten verwenden können.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Sollten Sie noch einen AKS-Cluster benötigen, lesen Sie die AKS-Schnellstartanleitung [für die Azure-Befehlszeilenschnittstelle][aks-quickstart-cli] oder für das Azure-Portal. or [using the Azure portal][aks-quickstart-portal]

## <a name="create-an-ssh-connection"></a>Erstellen einer SSH-Verbindung

Erstellen Sie zunächst eine SSH-Verbindung mit dem Knoten, für den Sie *Kubelet*-Protokolle anzeigen müssen. Einzelheiten zu diesem Vorgang finden Sie im Dokument [Zugreifen per SSH auf Azure Kubernetes Service-Clusterknoten (AKS-Clusterknoten)][aks-ssh].

## <a name="get-kubelet-logs"></a>Abrufen von Kubelet-Protokollen

Sobald Sie mit dem Knoten verbunden sind, können Sie den folgenden Befehl ausführen, um die *Kubelet*-Protokolle abzurufen.

```console
sudo journalctl -u kubelet -o cat
```

Die folgende Beispielausgabe zeigt die *Kubelet*-Protokolldaten:

```
I0508 12:26:17.905042    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:27.943494    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:28.920125    8672 server.go:796] GET /stats/summary: (10.370874ms) 200 [[Ruby] 10.244.0.2:52292]
I0508 12:26:37.964650    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:47.996449    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:58.019746    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:05.107680    8672 server.go:796] GET /stats/summary/: (24.853838ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:27:08.041736    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:18.068505    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:28.094889    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:38.121346    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:44.015205    8672 server.go:796] GET /stats/summary: (30.236824ms) 200 [[Ruby] 10.244.0.2:52588]
I0508 12:27:48.145640    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:58.178534    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:05.040375    8672 server.go:796] GET /stats/summary/: (27.78503ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:28:08.214158    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:18.242160    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:28.274408    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:38.296074    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:48.321952    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:58.344656    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
```

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie zur Problembehandlung weitere Informationen des Kubernetes-Masters benötigen, lesen Sie [Anzeigen von Kubernetes-Masterknotenprotokollen in AKS][aks-master-logs].

<!-- LINKS - internal -->
[aks-ssh]: ssh.md
[aks-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-master-logs]: view-master-logs.md
[azure-container-logs]: ../azure-monitor/insights/container-insights-overview.md
