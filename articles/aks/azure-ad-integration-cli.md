---
title: Integrieren von Azure Active Directory in Azure Kubernetes Service
description: In diesem Artikel erfahren Sie, wie Sie mit der Azure CLI einen Azure Active Directory-fähigen AKS (Azure Kubernetes Service)-Cluster erstellen.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: mlearned
ms.openlocfilehash: 5b99d76ef20c288d6ae0bd33e1e2b6a75a359d3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616272"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrieren von Azure Active Directory in Azure Kubernetes Service mit der Azure CLI

Azure Kubernetes Service (AKS) kann für die Verwendung von Azure Active Directory (AD) für die Benutzerauthentifizierung konfiguriert werden. In dieser Konfiguration melden Sie sich an einem AKS-Cluster über ein Azure AD-Authentifizierungstoken an. Clusterbetreiber können auch die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Kubernetes auf der Grundlage einer Benutzeridentität oder Verzeichnisgruppenmitgliedschaft konfigurieren.

In diesem Artikel wird erläutert, wie Sie die erforderlichen Azure AD-Komponenten erstellen, anschließend einen Azure AD-fähigen Cluster bereitstellen und dann eine grundlegende RBAC-Rolle im AKS-Cluster erstellen. Sie können [diese Schritte auch über das Azure-Portal durchführen][azure-ad-portal].

Das komplette in diesem Artikel verwendete Beispielskript finden Sie unter [Azure CLI samples – AKS integration with Azure AD][complete-script].

Es gelten die folgenden Einschränkungen:

- Azure AD kann nur aktiviert werden, wenn Sie einen neuen, RBAC-fähigen Cluster erstellen. Es ist nicht möglich, Azure AD für einen vorhandenen AKS-Cluster zu aktivieren.

## <a name="before-you-begin"></a>Voraussetzungen

Azure CLI-Version 2.0.61 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

Aus Konsistenzgründen und um die Ausführung der Befehle in diesem Artikel zu erleichtern, erstellen Sie eine Variable für den gewünschten AKS-Clusternamen. Im folgenden Beispiel wird der Name *myakscluster* verwendet:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Übersicht über die Azure AD-Authentifizierung

Die Azure AD-Authentifizierung wird für AKS-Cluster mit OpenID Connect bereitgestellt. OpenID Connect ist eine Identitätsebene, die auf dem OAuth 2.0-Protokoll aufbaut. Weitere Informationen zu OpenID Connect finden Sie in der [OpenID Connect-Dokumentation][open-id-connect].

Innerhalb des Kubernetes-Clusters werden Authentifizierungstoken mithilfe der Webhooktokenauthentifizierung überprüft. Die Webhooktokenauthentifizierung wird als Teil des AKS-Clusters konfiguriert und verwaltet. Weitere Informationen zur Webhooktoken-Authentifizierung finden Sie in der [Dokumentation zur Webhookauthentifizierung][kubernetes-webhook].

> [!NOTE]
> Bei der Konfiguration von Azure AD für die AKS-Authentifizierung werden zwei Azure AD-Anwendungen konfiguriert. Dieser Vorgang muss von einem Azure-Mandantenadministrator ausgeführt werden.

## <a name="create-azure-ad-server-component"></a>Erstellen der Azure AD-Serverkomponente

Für die Integration mit AKS erstellen und verwenden Sie eine Azure AD-Anwendung, die als Endpunkt für die Identitätsanforderungen fungiert. Die erste benötigte Azure AD-Anwendung erhält die Azure AD-Gruppenmitgliedschaft für einen Benutzer.

Erstellen Sie die Serveranwendungskomponente mit dem command, then update the group membership claims using the [az ad app update][az-ad-app-update]-Befehl [az ad app create][az-ad-app-create]. Im folgenden Beispiel wird die Variable *aksname* (im Abschnitt [Voraussetzungen](#before-you-begin) definiert) verwendet, und es wird eine Variable erstellt.

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Erstellen Sie nun mit dem command. This service principal is used to authenticate itself within the Azure platform. Then, get the service principal secret using the [az ad sp credential reset][az-ad-sp-credential-reset]-Befehl [az ad sp create][az-ad-sp-create] ein Serviceprinzipal für die Serveranwendung, und weisen Sie der Variable *serverApplicationSecret* zur Verwendung in einem der folgenden Schritte zu:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD benötigt die Berechtigungen zum Ausführen der folgenden Aktionen:

* Verzeichnisdaten lesen
* Anmelden und Benutzerprofil lesen

Weisen Sie diese Berechtigungen mit dem Befehl [az ad app permission add][az-ad-app-permission-add] zu:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Erteilen Sie schließlich die im vorherigen Schritt zugewiesenen Berechtigungen für die Serveranwendung mit [az ad app permission grant][az-ad-app-permission-grant] command. This step fails if the current account is not a tenant admin. You also need to add permissions for Azure AD application to request information that may otherwise require administrative consent using the [az ad app permission admin-consent][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Erstellen der Azure AD-Clientkomponente

Die zweite Azure AD-Anwendung wird beim Anmelden eines Benutzers beim AKS-Cluster mit der Kubernetes-CLI (`kubectl`) verwendet. Diese Clientanwendung empfängt die Authentifizierungsanforderung vom Benutzer und überprüft dessen Anmeldeinformationen und Berechtigungen. Erstellen Sie die Azure AD-App für die Client-Komponente mit dem Befehl [az ad app create][az-ad-app-create]:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Erstellen Sie ein Serviceprinzipal für die Client-Anwendung mit dem Befehl [az ad sp create][az-ad-sp-create]:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Holen Sie sich mit dem Befehl [az ad app show][az-ad-app-show] die oAuth2-ID für die Serveranwendung, um den Authentifizierungsfluss zwischen den beiden App-Komponenten zu ermöglichen. Diese oAuth2-ID wird im nächsten Schritt verwendet.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Fügen Sie mit dem command. Then, grant permissions for the client application to communication with the server application using the [az ad app permission grant][az-ad-app-permission-grant]-Befehl [az ad app permission add][az-ad-app-permission-add] die Berechtigungen für die Client-Anwendungs- und Server-Anwendungskomponenten hinzu, um den Kommunikationsfluss oAuth2 zu verwenden:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Bereitstellen des Clusters

Die beiden Azure AD-Anwendungen sind nun erstellt. Jetzt müssen Sie den AKS-Cluster selbst erstellen. Erstellen Sie zunächst eine Ressourcengruppe mit dem Befehl [az group create][az-group-create]. Im folgenden Beispiel wird die Ressourcengruppe in der Region *EastUS* erstellt:

Erstellen einer Ressourcengruppe für den Cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Holen Sie sich die Mandanten-ID Ihres Azure-Abonnements mit dem command. Then, create the AKS cluster using the [az aks create][az-aks-create]-Befehl [az account show][az-account-show]. Im Befehl zum Erstellen des AKS-Clusters werden die IDs der Server- und der Clientanwendung, das Dienstprinzipal-Geheimnis der Serveranwendung und Ihre Mandanten-ID angegeben:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Schließlich erhalten Sie die Cluster-Administrations-Anmeldeinformationen mit dem Befehl [az aks get-credentials][az-aks-get-credentials]. In einem der folgenden Schritte rufen Sie die Clusteranmeldeinformationen für reguläre *Benutzer* ab, um den Azure AD-Authentifizierungsablauf in Aktion sehen zu können.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Erstellen einer RBAC-Bindung

Bevor ein Azure Active Directory-Konto mit dem AKS-Cluster verwendet werden kann, muss eine Rollenbindung oder eine Clusterrollenbindung erstellt werden. *Rollen* definieren die zu erteilenden Berechtigungen, und *Bindungen* wenden diese auf die gewünschten Benutzer an. Diese Zuweisungen können auf einen bestimmten Namespace oder im gesamten Cluster angewendet werden. Weitere Informationen finden Sie unter [Verwendung der RBAC-Berechtigung][rbac-authorization].

Ermitteln Sie den Benutzer-Hauptnamen (UPN) für den aktuell angemeldeten Benutzer mit dem Befehl [az ad signed-in-user show][az-ad-signed-in-user-show]. Dieses Benutzerkonto wird im nächsten Schritt für die Azure AD-Integration aktiviert.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Wenn sich der Benutzer, für den die RBAC-Bindung gewährt wird, sich im selben Azure AD-Mandanten befindet, weisen Sie Berechtigungen entsprechend dem *userPrincipalName* zu. Befindet sich der Benutzer in einem anderen Azure AD-Mandanten, müssen Sie stattdessen die *objectId*-Eigenschaft abfragen und verwenden.

Erstellen Sie ein YAML-Manifest mit dem Namen `basic-azure-ad-binding.yaml`, und fügen Sie die folgenden Inhalte ein. Ersetzen Sie in der letzten Zeile *userPrincipalName_or_objectId* durch den UPN oder die Objekt-ID-Ausgabe des vorherigen Befehls:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Erstellen Sie den ClusterRoleBinding mit dem Befehl [kubectl apply][kubectl-apply] und geben Sie den Dateinamen Ihres YAML-Manifests an:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Zugriff auf Cluster mit Azure AD

Testen wir nun die Integration der Azure AD-Authentifizierung für den AKS-Cluster. Legen Sie den `kubectl`-Konfigurationskontext auf die Verwendung regulärer Benutzeranmeldeinformationen fest. Dieser Kontext übergibt alle Authentifizierungsanforderungen über Azure AD zurück.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Verwenden Sie nun den Befehl[kubectl get pods][kubectl-get], um Pods über alle Namensräume hinweg anzuzeigen:

```console
kubectl get pods --all-namespaces
```

Sie erhalten eine Anmeldeaufforderung, sich in einem Webbrowser mit Azure AD-Anmeldeinformationen zu authentifizieren. Nach erfolgreicher Authentifizierung zeigt der Befehl `kubectl` die Pods im AKS-Cluster an, wie in der folgenden Beispielausgabe veranschaulicht:

```console
$ kubectl get pods --all-namespaces

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Das für `kubectl` empfangene Authentifizierungstoken wird zwischengespeichert. Sie werden nur erneut aufgefordert, sich anzumelden, wenn das Token abgelaufen ist oder die Kubernetes-Konfigurationsdatei neu erstellt wird.

Wenn wie in der folgenden Beispielausgabe nach erfolgreicher Anmeldung im Webbrowser eine Meldung zu einem Authentifizierungsfehler angezeigt wird, untersuchen Sie die folgenden möglichen Probleme:

```console
error: You must be logged in to the server (Unauthorized)
```

* Sie haben die entsprechende Objekt-ID oder den UPN definiert, je nachdem, ob das Benutzerkonto im gleichen Azure AD-Mandanten vorhanden ist oder nicht.
* Der Benutzer ist kein Mitglied der über 200 Gruppen.
* Das Geheimnis, das in der Anwendungsregistrierung für den Server definiert ist, entspricht dem mit `--aad-server-app-secret` konfigurierten Wert.

## <a name="next-steps"></a>Nächste Schritte

Das vollständige Skript, das die in diesem Artikel aufgeführten Befehle enthält, finden Sie im [Azure AD Integrationsskript im AKS Samples-Repo][complete-script].

Informationen zur Verwendung von Azure AD-Benutzern und -Gruppen für die Zugriffskontrolle auf Cluster-Ressourcen finden Sie unter [Zugriffskontrolle auf Cluster-Ressourcen mit rollenbasierter Zugriffskontrolle und Azure AD-Identitäten in AKS][azure-ad-rbac].

Weitere Informationen zum Schutz von Kubernetes-Clustern finden Sie unter [Zugriffs- und Identitätsoptionen für AKS)][rbac-authorization].

Best Practices zur Identitäts- und Ressourcenkontrolle finden Sie unter [Best Practices für Authentifizierung und Autorisierung in AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
