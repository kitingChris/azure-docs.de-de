---
title: Verwenden der Azure CLI zum Verwalten von Azure AD-Zugriffsrechten für Blob- und Warteschlangendaten mit RBAC – Azure Storage
description: Verwenden Sie die Azure CLI, um den Zugriff auf Container und Warteschlangen mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) zuzuweisen. Azure Storage unterstützt für die Authentifizierung über Azure AD integrierte und benutzerdefinierte RBAC-Rollen.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1e29b8e23927ef2ff70416d1adc76e2b2b3f2d8a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443710"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-azure-cli"></a>Gewähren von Zugriff auf Azure-Blob- und -Warteschlangendaten mit RBAC über die Azure CLI

Azure Active Directory (Azure AD) autorisiert Rechte für den Zugriff auf abgesicherte Ressourcen über die [rollenbasierte Zugriffssteuerung (RBAC)](../../role-based-access-control/overview.md). Azure Storage bietet eine Reihe integrierter RBAC-Rollen mit allgemeinen Berechtigungssätzen für den Zugriff auf Blob- und Warteschlangendaten.

Wenn einem Azure AD-Sicherheitsprinzipal eine RBAC-Rolle zugewiesen wird, gewährt Azure diesem Sicherheitsprinzipal Zugriff auf diese Ressourcen. Der Zugriff kann auf die Ebene des Abonnements, der Ressourcengruppe, des Speicherkontos oder eines einzelnen Containers oder einer Warteschlange begrenzt werden. Eine Azure AD-Sicherheitsprinzipal kann ein Benutzer, eine Gruppe, ein Anwendungsdienstprinzipal oder eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) sein.

Dieser Artikel beschreibt, wie Sie mithilfe der Azure CLI integrierte RBAC-Rollen auflisten und diese Benutzern zuweisen. Weitere Informationen zur Verwendung der Azure CLI finden Sie unter [Azure-Befehlszeilenschnittstelle (CLI)](https://docs.microsoft.com/cli/azure).

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-Rollen für Blobs und Warteschlangen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Bestimmen des Ressourcenumfangs 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Auflisten verfügbarer RBAC-Rollen

Verwenden Sie zum Auflisten verfügbarer integrierter RBAC-Rollen mit der Azure CLI den Befehl [az role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli-interactive
az role definition list --out table
```

Die integrierten Azure Storage-Datenrollen werden zusammen mit anderen integrierten Rollen für Azure aufgelistet:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Zuweisen einer RBAC-Rolle zu einem Sicherheitsprinzipal

Verwenden Sie den Befehl [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create), um einem Sicherheitsprinzipal eine RBAC-Rolle zuzuweisen. Das Format des Befehls kann je nach Bereich der Zuweisung unterschiedlich sein. Die folgenden Beispiele zeigen, wie Sie einem Benutzer eine Rolle in verschiedenen Bereichen zuweisen können. Sie können jedoch den gleichen Befehl verwenden, um eine Rolle einem beliebigen Sicherheitsprinzipal zuzuordnen.

### <a name="container-scope"></a>Containerbereich

Geben Sie zum Zuweisen einer auf einen Container begrenzten Rolle für den Parameter `--scope` eine Zeichenfolge an, die den Containerbereich enthält. Der Bereich für einen Container weist folgendes Format auf:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Im folgenden Beispiel wird die Rolle **Mitwirkender an Storage-Blobdaten** einem Benutzer zugewiesen und auf einen Container mit dem Namen *sample-container* begrenzt. Ersetzen Sie die Beispielwerte und die Platzhalterwerte in Klammern durch Ihre eigenen Werte: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Warteschlangenbereich

Geben Sie zum Zuweisen einer auf eine Warteschlange begrenzten Rolle für den Parameter `--scope` eine Zeichenfolge an, die den Warteschlangenbereich enthält. Der Bereich für eine Warteschlange weist folgendes Format auf:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Im folgenden Beispiel wird die Rolle **Mitwirkender an Storage-Warteschlangendaten** einem Benutzer zugewiesen und auf eine Warteschlange mit dem Namen *sample-queue* begrenzt. Ersetzen Sie die Beispielwerte und die Platzhalterwerte in Klammern durch Ihre eigenen Werte: 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Speicherkontobereich

Geben Sie zum Zuweisen einer auf eine Warteschlange begrenzten Rolle für den Parameter `--scope` den Bereich der Speicherkontoressource an. Der Bereich für ein Speicherkonto weist folgendes Format auf:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Im folgenden Beispiel wird veranschaulicht, wie die Rolle **Storage-Blobdatenleser** einem Benutzer auf Speicherkontoebene zugewiesen wird. Ersetzen Sie die Beispielwerte durch Ihre eigenen Werte: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/storagesamples"
```

### <a name="resource-group-scope"></a>Ressourcengruppenbereich

Geben Sie zum Zuweisen einer auf eine Ressourcengruppe begrenzten Rolle für den Parameter `--resource-group` den Namen oder die ID der Ressourcengruppe an. Im folgenden Beispiel wird die Rolle **Storage-Warteschlangendatenleser** einem Benutzer auf Ressourcengruppenebene zugewiesen. Ersetzen Sie die Beispielwerte und Platzhalterwerte in Klammern durch Ihre eigenen Werte: 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group sample-resource-group
```

### <a name="subscription-scope"></a>Abonnementbereich

Geben Sie zum Zuweisen einer auf das Abonnement begrenzten Rolle für den Parameter `--scope` den Bereich für das Abonnement an. Der Bereich für ein Abonnement weist folgendes Format auf:

```
/subscriptions/<subscription>
```

Im folgenden Beispiel wird veranschaulicht, wie die Rolle **Storage-Blobdatenleser** einem Benutzer auf Speicherkontoebene zugewiesen wird. Ersetzen Sie die Beispielwerte durch Ihre eigenen Werte: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>"
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Grant access to Azure blob and queue data with RBAC using Azure PowerShell (Gewähren von Zugriff auf Azure-Blob- und -Warteschlangendaten mit RBAC über Azure PowerShell)](storage-auth-aad-rbac-powershell.md)
- [Grant access to Azure blob and queue data with RBAC in the Azure portal (Gewähren von Zugriff auf Azure-Blob- und -Warteschlangendaten mit RBAC über das Azure-Portal)](storage-auth-aad-rbac-portal.md)
