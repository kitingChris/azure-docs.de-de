---
title: Ausführen von Linux auf virtuellen Computeknoten – Azure Batch | Microsoft-Dokumentation
description: Erfahren Sie, wie parallele Compute-Workloads auf Pools auf virtuellen Linux-Computern in Azure Batch verarbeitet werden.
services: batch
documentationcenter: python
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 10a3c5a4f1c6eaceecb9dc5262d8694ee4265b48
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340177"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Bereitstellen von Linux-Computeknoten in Batch-Pools

Sie können Azure Batch verwenden, um Computeworkloads auf virtuellen Linux- und Windows-Computern parallel auszuführen. In diesem Artikel erfahren Sie, wie Sie im Batch-Dienst mithilfe der Clientbibliotheken [Batch Python][py_batch_package] und and [Batch .NET][api_net] mit Linux-Computeknoten erstellen.

> [!NOTE]
> Anwendungspakete werden für alle Batch-Pools unterstützt, die nach dem 5. Juli 2017 erstellt wurden. Für Batch-Pools, die zwischen dem 10. März 2016 und dem 5. Juli 2017 erstellt wurden, werden sie nur unterstützt, wenn der Pool mit einer Clouddienstkonfiguration erstellt wurde. Batch-Pools, die vor dem 10. März 2016 erstellt wurden, unterstützen keine Anwendungspakete. Weitere Informationen zum Bereitstellen von Anwendungen für Batch-Knoten mithilfe von Anwendungspaketen finden Sie unter [Anwendungsbereitstellung mit Azure Batch-Anwendungspaketen](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Konfiguration des virtuellen Computers
Wenn Sie einen Pool von Computeknoten in Batch erstellen, stehen Ihnen zwei Optionen zur Auswahl der Knotengröße und des Betriebssystems zur Verfügung: die Clouddienstkonfiguration und die Konfiguration des virtuellen Computers.

**Clouddienstkonfiguration** stellt *nur*. Die verfügbaren Größen für Computeknoten sind unter [Größen für Clouddienste](../cloud-services/cloud-services-sizes-specs.md) aufgelistet. Die verfügbaren Betriebssysteme sind unter [Azure-Gastbetriebssystemversionen und SDK-Kompatibilitätsmatrix](../cloud-services/cloud-services-guestos-update-matrix.md) aufgeführt. Beim Erstellen eines Pools, der Azure Cloud Services-Knoten enthält, geben Sie die Knotengröße und dessen Betriebssystemfamilie an, die in den zuvor angegebenen Artikeln beschrieben werden. Für Pools mit Windows-Computeknoten wird Cloud Services am häufigsten verwendet.

**Virtual Machine Configuration** stellt sowohl Linux- als auch Windows-Images für Computeknoten bereit. Die verfügbaren Größen für Computeknoten sind unter [Größen für virtuelle Computer in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) und [Größen für virtuelle Computer in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows) aufgelistet. Beim Erstellen eines Pools, der Knoten mit der Konfiguration des virtuellen Computers enthält, müssen Sie die Knotengröße, die VM-Imagereferenz und die Knoten-Agent-SKU von Batch für die Installation auf den Knoten angeben.

### <a name="virtual-machine-image-reference"></a>VM-Imagereferenz
Der Batch-Dienst verwendet [VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md), um Computeknoten in der VM-Konfiguration bereitzustellen. Sie können ein Image aus dem [Azure Marketplace][vm_marketplace] angeben oder ein benutzerdefiniertes Image bereitstellen, das Sie vorbereitet haben. Ausführlichere Informationen zu benutzerdefinierten Images finden Sie unter [Erstellen eines Pools mit einem benutzerdefinierten Image](batch-custom-images.md).

Wenn Sie eine VM-Imagereferenz konfigurieren, müssen Sie die Eigenschaften des VM-Images festlegen. Die folgenden Eigenschaften sind erforderlich, wenn Sie eine VM-Imagereferenz erstellen:

| **Imagereferenzeigenschaften** | **Beispiel** |
| --- | --- |
| Herausgeber |Canonical |
| Angebot |UbuntuServer |
| SKU |14.04.4-LTS |
| Version |latest |

> [!TIP]
> Weitere Informationen zu diesen Eigenschaften und zur Auflistung von Marketplace-Images finden Sie unter [Navigieren zu und Auswählen von Images virtueller Linux-Computer in Azure mithilfe der Befehlszeilenschnittstelle oder PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Beachten Sie, dass nicht alle Marketplace-Images derzeit mit Batch kompatibel sind. Weitere Informationen hierzu finden Sie unter [Knoten-Agent-SKU](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Knoten-Agent-SKU
Der Batch-Knoten-Agent ist ein Programm, das auf jedem Knoten im Pool ausgeführt wird. Er stellt die Befehls- und Steuerungsschnittstelle zwischen dem Knoten und dem Batch-Dienst dar. Es gibt verschiedene Implementierungen des Knoten-Agents (SKUs) für verschiedene Betriebssysteme. Insbesondere beim Erstellen einer Konfiguration für einen virtuellen Computer legen Sie zuerst die VM-Imagereferenz und anschließend den Knoten-Agent fest, der auf dem Image installiert werden soll. In der Regel ist jede Knoten-Agent-SKU mit mehreren VM-Images kompatibel. Hier sind einige Beispiele für Knoten-Agent-SKUs:

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Nicht alle im Marketplace verfügbaren VM-Images sind mit den derzeit verfügbaren Batch-Knoten-Agents kompatibel. Verwenden Sie die Batch-SDKs, um die verfügbaren Knoten-Agent-SKUs und die VM-Images, mit denen sie kompatibel sind, aufzulisten. Weitere Informationen und Beispiele, wie eine Liste der gültigen Images zur Runtime abgerufen wird, finden Sie in diesem Artikel unter [Liste der VM-Images](#list-of-virtual-machine-images).
>
>

## <a name="create-a-linux-pool-batch-python"></a>Erstellen eines Linux-Pools: Batch Python
Der folgende Codeausschnitt zeigt ein Beispiel für die Verwendung der [Microsoft Azure Batch-Clientbibliothek für Python][py_batch_package] auf „Read the Docs“.

Dieser Ausschnitt erstellt eine [ImageReference][py_imagereference]-Methode, um das verfügbare Image und die SKU-Kombinationen für den Knoten-Agent zur Laufzeit zu ermitteln und auszuwählen.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="14.04.2-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Wie bereits erwähnt, wird empfohlen, nicht die Methode [ImageReference][py_imagereference] zu erstellen, sondern aus den derzeit unterstützten Kombinationen aus Knoten-Agent und Marketplace-Image eine dynamische Auswahl zu treffen. Der folgende Python-Ausschnitt veranschaulicht die Verwendung dieser Methode.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(
    agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id=ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Erstellen eines Linux-Pools: Batch .NET
Das folgende Codebeispiel zeigt ein Beispiel für die Verwendung von [Batch .NET][nuget_batch_net] auf „docs.microsoft.com“.

Im folgenden Codeausschnitt wird die Methode [PoolOperations][net_pool_ops] verwendet, um aus der Liste mit den derzeit unterstützten Kombinationen aus Marketplace-Image und Knoten-Agent SKUs eine Auswahl zu treffen. Diese Technik ist von Vorteil, da sich die Liste mit den unterstützten Kombinationen von Zeit zu Zeit ändern kann. In den meisten Fällen werden unterstützte Kombinationen hinzugefügt.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Im vorherigen Codeausschnitt wird zwar die Methode [PoolOperations][net_pool_ops] verwendet, um unterstützte Kombinationen aus Image und Knoten-Agent SKU dynamisch aufzulisten und eine Auswahl zu treffen (empfohlene Vorgehensweise). Sie können aber auch explizit einen Imageverweis ([ImageReference][net_imagereference]) konfigurieren:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Liste mit VM-Images
In der folgenden Tabelle ist angegeben, welche Marketplace-VM-Images zum Zeitpunkt der Aktualisierung dieses Artikels mit den verfügbaren Batch-Knoten-Agents kompatibel waren. Dabei ist zu beachten, dass diese Liste nicht final ist, da Images und Knoten-Agents jederzeit hinzugefügt oder entfernt werden können. Es wird empfohlen, für die Batch-Anwendungen und -Dienste immer [list_node_agent_skus][py_list_skus] (Batch .NET) zu verwenden, um die zum jeweiligen Zeitpunkt verfügbaren SKUs zu bestimmen und eine Auswahl zu treffen.

> [!WARNING]
> Die folgende Liste kann sich jederzeit ändern. Verwenden Sie immer die in den Batch-APIs verfügbaren Methoden zum **Auflisten von Knoten-Agent-SKUs**, um die kompatiblen virtuellen Computer und Knoten-Agent-SKUs aufzulisten, wenn Sie Batch-Aufträge ausführen.
>
>

| **Herausgeber** | **Angebot** | **Image-SKU** | **Version** | **Knoten-Agent-SKU-ID** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| Batch | rendering-centos73 | Rendering | latest | batch.node.centos 7 |
| Batch | rendering-windows2016 | Rendering | latest | batch.node.windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | latest | batch.node.ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | latest | batch.node.ubuntu 14.04 |
| Credativ | Debian | 9 | latest | batch.node.debian 9 |
| Credativ | Debian | 8 | latest | batch.node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | latest | batch.node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | latest | batch.node.windows amd64 |
| microsoft-azure-batch | centos-container | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | centos-container-rdma | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | Windows Server | 2016-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | Windows Server | 2016-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | Windows Server | 2016-Datacenter-with-Containers | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | Windows Server | 2012-R2-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | Windows Server | 2012-R2-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | Windows Server | 2012-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | Windows Server | 2012-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | Windows Server | 2008-R2-SP1 | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | Windows Server | 2008-R2-SP1-smalldisk | latest | batch.node.windows amd64 |
| OpenLogic | CentOS | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | latest | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.4 | latest | batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | latest | batch.node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Herstellen einer Verbindung mit Linux-Knoten über SSH
Während der Entwicklung oder bei der Fehlerbehebung ist es unter Umständen erforderlich, sich bei den Knoten im Pool anzumelden. Im Gegensatz zu Windows-Computeknoten können Sie das Remotedesktopprotokoll (RDP) für die Herstellung einer Verbindung mit Linux-Knoten nicht verwenden. Stattdessen ermöglicht der Batch-Dienst den SSH-Zugriff auf jeden Knoten zum Herstellen einer Remoteverbindung.

Mit dem folgenden Python-Codeausschnitt wird ein Benutzer für jeden Knoten eines Pools erstellt, der für eine Remoteverbindung erforderlich ist. Anschließend werden die SSH-Verbindungsinformationen (Secure Shell) für die einzelnen Knoten ausgegeben.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Hier ist eine Beispielausgabe für den vorherigen Code eines Pools mit vier Linux-Knoten angegeben:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Anstelle eines Kennworts können Sie einen öffentlichen SSH-Schlüssel festlegen, wenn Sie einen Benutzer auf einem Knoten erstellen. Verwenden Sie im Python SDK den Parameter **ssh_public_key** für die Eigenschaft [ComputeNodeUser][py_computenodeuser]. In .NET, use the [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key].

## <a name="pricing"></a>Preise
Azure Batch basiert auf der Technologie von Azure Cloud Services und von Azure Virtual Machines. Der Batch-Dienst wird kostenfrei angeboten. Es werden also nur die Computeressourcen berechnet, die von Ihren Batch-Lösungen verbraucht werden. Wenn Sie sich für die **Cloud Services-Konfiguration** entscheiden, erfolgt die Abrechnung auf der Grundlage der Preisstruktur, die Sie unter [Cloud Services-Preise][cloud_services_pricing] finden. Wenn Sie sich für die **VM-Konfiguration** entscheiden, erfolgt die Abrechnung auf der Grundlage der Preisstruktur unter [Virtuelle Computer – Preise][vm_pricing]. 

Wenn Sie Anwendungen mit [Anwendungspaketen](batch-application-packages.md) auf Ihren Batch-Knoten bereitstellen, werden auch die von Ihren Anwendungspaketen genutzten Azure Storage-Ressourcen in Rechnung gestellt. Grundsätzlich sind die Kosten für Azure Storage minimal. 

## <a name="next-steps"></a>Nächste Schritte

Das Repository für [Python-Codebeispiele][github_samples_py] auf GitHub enthält Skripts, die die Durchführung allgemeiner Batch-Vorgänge wie etwa die Erstellung von Pools, Aufträgen und Aufgaben veranschaulichen. Die zu den Python-Beispielen gehörige [INFODATEI][github_py_readme] enthält Details zur Installation der erforderlichen Pakete.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: https://docs.microsoft.com/python/api/azure.batch.models.computenodeuser
[py_imagereference]: https://docs.microsoft.com/python/api/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
