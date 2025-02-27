---
title: Aktivieren von Azure Monitor für VMs (Vorschauversion) mit Azure PowerShell oder Resource Manager-Vorlagen | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Azure Monitor für VMs mithilfe von Azure PowerShell oder Azure Resource Manager-Vorlagen für mindestens einen virtuellen Azure-Computer oder eine VM-Skalierungsgruppe aktivieren.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ff284ea0adf6021ace84cd6a41f0a0e4e987a9c8
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144243"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Aktivieren von Azure Monitor für VMs (Vorschauversion) mit Azure PowerShell oder Resource Manager-Vorlagen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In diesem Artikel wird erläutert, wie Sie Azure Monitor für VMs (Vorschauversion) mithilfe von Azure PowerShell oder Azure Resource Manager-Vorlagen für virtuelle Azure-Computer oder VM-Skalierungsgruppen aktivieren. Nachdem Sie diesen Prozess durchgeführt haben, haben Sie erfolgreich damit begonnen, alle Ihre VMs (virtuelle Computer) zu überwachen, und werden erfahren, ob Leistungs- und Verfügbarkeitsprobleme auftreten.

## <a name="set-up-a-log-analytics-workspace"></a>Einrichten eines Log Analytics-Arbeitsbereichs 

Wenn Sie noch nicht über einen Log Analytics-Arbeitsbereich verfügen, müssen Sie einen erstellen. Sehen Sie sich die Methoden an, die im Abschnitt [Voraussetzungen](vminsights-enable-overview.md#log-analytics) vorgeschlagen werden, bevor Sie mit den Schritten zur Konfiguration fortfahren. Anschließend können Sie die Bereitstellung von Azure Monitor für VMs mithilfe der Methode für Azure Resource Manager-Vorlagen abschließen.

### <a name="enable-performance-counters"></a>Aktivieren von Leistungsindikatoren

Wenn der Log Analytics-Arbeitsbereich, auf den die Lösung verweist, noch nicht für die Erfassung der von der Lösung benötigten Leistungsindikatoren konfiguriert ist, müssen Sie diese aktivieren. Hierzu stehen zwei Möglichkeiten zur Verfügung:
* Manuell, wie in [Windows- und Linux-Leistungsindikatoren in Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md) beschrieben.
* Durch Herunterladen und Ausführen eines PowerShell-Skripts, das über den [Azure PowerShell-Katalog](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1) verfügbar ist.

### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Installieren der Projektmappen „ServiceMap“ und „InfrastructureInsights“
Diese Methode umfasst eine JSON-Vorlage, die die Konfiguration zum Aktivieren der Lösungskomponenten für Ihren Log Analytics-Arbeitsbereich angibt.

Wenn Sie nicht wissen, wie Ressourcen mithilfe einer Vorlage bereitgestellt werden, lesen Sie die folgenden Artikel:
* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Wenn Sie die Azure CLI verwenden möchten, müssen Sie sie zuerst installieren und lokal verwenden. Sie benötigen Azure CLI 2.0.27 oder höher. Um Ihre Version zu ermitteln, führen Sie `az --version` aus. Informationen zur Installation und zum Upgrade der Azure CLI finden Sie unter [Installieren der Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Kopieren Sie die folgende JSON-Syntax, und fügen Sie sie in Ihre Datei ein:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Speichern Sie diese Datei als *installsolutionsforvminsights.json* in einem lokalen Ordner.

1. Erfassen Sie die Werte für *WorkspaceName*, *ResourceGroupName* und *WorkspaceLocation*. Der Wert für *WorkspaceName* ist der Name des Log Analytics-Arbeitsbereichs. Der Wert für *WorkspaceLocation* ist die Region, in der der Arbeitsbereich definiert ist.

1. Die Vorlage kann nun bereitgestellt werden.
 
    * Verwenden Sie die folgenden PowerShell-Befehle in dem Ordner mit der Vorlage:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Die Änderung der Konfiguration kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das Ergebnis anzeigt:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Führen Sie den folgenden Befehl mithilfe der Azure CLI aus:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Die Änderung der Konfiguration kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das Ergebnis anzeigt:

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Aktivieren mithilfe von Azure Resource Manager-Vorlagen
Für das Onboarding Ihrer VMs oder VM-Skalierungsgruppen stehen Azure Resource Manager-Beispielvorlagen zur Verfügung. Diese Vorlagen umfassen Szenarios, mit denen Sie die Überwachung für eine vorhandene Ressource aktivieren und eine neue Ressource mit aktivierter Überwachung erstellen können.

>[!NOTE]
>Die Vorlage muss in derselben Ressourcengruppe wie die zu integrierende Ressource bereitgestellt werden.

Wenn Sie nicht wissen, wie Ressourcen mithilfe einer Vorlage bereitgestellt werden, lesen Sie die folgenden Artikel:
* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Wenn Sie die Azure CLI verwenden möchten, müssen Sie sie zuerst installieren und lokal verwenden. Sie benötigen Azure CLI 2.0.27 oder höher. Um Ihre Version zu ermitteln, führen Sie `az --version` aus. Informationen zur Installation und zum Upgrade der Azure CLI finden Sie unter [Installieren der Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Herunterladen der Vorlagen

Die Azure Resource Manager-Vorlagen werden in einer Archivdatei (.zip) bereitgestellt, die Sie aus dem GitHub-Repository [herunterladen](https://aka.ms/VmInsightsARMTemplates) können. Zu den Inhalten der Datei gehören Ordner, die einzelne Bereitstellungsszenarios darstellen und eine Vorlage sowie eine Parameterdatei enthalten. Vor der Ausführung passen Sie die Parameterdatei an, und legen Sie die erforderlichen Werte fest. Ändern Sie die Vorlagendatei nicht, es sei denn, Sie müssen sie anpassen, um Ihre speziellen Anforderungen zu erfüllen. Nachdem Sie die Parameterdatei bearbeitet haben, können Sie sie mithilfe der Methoden bereitstellen, die später in diesem Artikel beschrieben werden. 

Die herunterladbare Datei enthält die folgenden Vorlagen für verschiedene Szenarios:

- Mit der Vorlage **ExistingVmOnboarding** wird Azure Monitor für VMs aktiviert, wenn die VM bereits vorhanden ist.
- Mit der Vorlage **NewVmOnboarding** wird eine VM erstellt und Azure Monitor für VMs wird aktiviert, um diese zu überwachen.
- Mit der Vorlage **ExistingVmssOnboarding** wird Azure Monitor für VMs aktiviert, wenn die VM-Skalierungsgruppe bereits vorhanden ist.
- Mit der Vorlage **NewVmssOnboarding** werden VM-Skalierungsgruppen erstellt und Azure Monitor für VMs wird aktiviert, um diese zu überwachen.
- Mit der Vorlage **ConfigureWorkspace** wird Ihr Log Analytics-Arbeitsbereich für die Unterstützung von Azure Monitor für VMs konfiguriert, indem die Lösungen und die Erfassung von Leistungsindikatoren der Betriebssysteme Linux und Windows aktiviert werden.

>[!NOTE]
>Wenn bereits VM-Skalierungsgruppen vorhanden sind und die Upgraderichtlinie auf **Manuell** festgelegt ist, wird Azure Monitor für VMs für diese Instanzen nicht standardmäßig aktiviert, wenn die Azure Resource Manager-Vorlage **ExistingVmssOnboarding** ausgeführt wird. Sie müssen ein manuelles Upgrade für diese Instanzen durchführen.

### <a name="deploy-by-using-azure-powershell"></a>Bereitstellen mit Azure PowerShell

Mit dem folgenden Schritt wird die Überwachung mithilfe von Azure PowerShell aktiviert.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Die Änderung der Konfiguration kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das Ergebnis anzeigt:

```powershell
provisioningState       : Succeeded
```
### <a name="deploy-by-using-the-azure-cli"></a>Bereitstellen über die Azure CLI

Mit dem folgenden Schritt wird die Überwachung mithilfe der Azure CLI aktiviert.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Die Ausgabe sieht ungefähr so aus:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Aktivieren mithilfe von PowerShell

Um Azure Monitor für VMs für mehrere VMs oder VM-Skalierungsgruppen zu aktivieren, verwenden Sie das PowerShell-Skript [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Dieses Skript ist im Azure PowerShell-Katalog verfügbar. Das Skript durchläuft Folgendes:

- Alle virtuellen Computer und VM-Skalierungsgruppen in Ihrem Abonnement.
- Die bereichsbezogene Ressourcengruppe gemäß der Angabe in *ResourceGroup*. 
- Eine einzelne VM oder VM-Skalierungsgruppe, die in *Name* angegeben ist.

Für jede VM oder VM-Skalierungsgruppe überprüft das Skript, ob die VM-Erweiterung bereits installiert ist. Wenn die VM-Erweiterung nicht installiert ist, versucht das Skript, sie neu zu installieren. Wenn die VM-Erweiterung installiert ist, installiert das Skript die Log Analytics- und Dependency-Agent-VM-Erweiterungen.

Für dieses Skript ist Version 1.0.0 oder höher des Azure PowerShell-Moduls „Az“ erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](https://docs.microsoft.com/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

Führen Sie `Get-Help` aus, um eine Liste der Argumentendetails des Skripts und der Beispielverwendung zu erhalten.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

Das folgende Beispiel veranschaulicht die Verwendung der PowerShell-Befehle im Ordner, um Azure Monitor for VMs zu aktivieren und die erwartete Ausgabe zu verstehen:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem die Überwachung für Ihre virtuellen Computer aktiviert wurde, stehen diese Informationen für die Analyse mit Azure Monitor für VMs zur Verfügung.
 
- Informationen zum Verwenden des Integritätsfeatures finden Sie unter [Azure Monitor für VMs – Integrität anzeigen](vminsights-health.md). 
- Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Azure Monitor für VMs – Zuordnung anzeigen](vminsights-maps.md). 
- Informationen zum Erkennen von Engpässen und der Gesamtauslastung im Hinblick auf die Leistung Ihrer VM finden Sie unter [Anzeigen der Leistung von Azure-VMs](vminsights-performance.md). 
- Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Azure Monitor für VMs – Zuordnung anzeigen](vminsights-maps.md).