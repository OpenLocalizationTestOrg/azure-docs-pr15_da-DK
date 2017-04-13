<properties
    pageTitle="Linux knuder på Azure batchen grupper | Microsoft Azure"
    description="Lær at behandle dine parallelle Beregn arbejdsbelastningen på grupper af Linux virtuelle maskiner i Azure Batch."
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="marsma" />

# <a name="provision-linux-compute-nodes-in-azure-batch-pools"></a>Klargøre Linux Beregn knuder på Azure batchen grupper

Du kan bruge Azure batchen til at køre parallelle Beregn arbejdsbelastninger på både Linux og Windows virtuelle computere. I denne artikel indeholder oplysninger om hvordan du opretter grupper af Linux Beregn knuder i batchen tjenesten ved hjælp af begge [Batchen Python] [ py_batch_package] og [Batchen .NET] [ api_net] klientbiblioteker.

> [AZURE.NOTE] [Programpakker](batch-application-packages.md) er i øjeblikket ikke understøttes på Linux Beregn noder.

## <a name="virtual-machine-configuration"></a>Virtuelt konfiguration

Når du opretter en samling af Beregn noder i Batch, har du to muligheder, du vil vælge node størrelse og operativsystem fra: konfiguration af Cloud Services og virtuelt konfiguration.

**Konfiguration af Cloud Services** indeholder Windows beregne noder *kun*. Tilgængelige Beregn node størrelser, findes i [størrelser til Cloud Services](../cloud-services/cloud-services-sizes-specs.md), og tilgængelige operativsystemer vises i [Azure gæst OS versioner og SDK kompatibilitet matrix](../cloud-services/cloud-services-guestos-update-matrix.md). Når du opretter en gruppe, som indeholder Azure Cloud Services noder, skal du angive nodestørrelsen og dens "OS familie," der findes i de tidligere nævnte artikler. For grupper af Windows beregne noder, er mest almindeligt brugte Cloud Services.

**Virtuelt konfiguration** indeholder både Linux og Windows-billeder til Beregn noder. Tilgængelige Beregn node størrelser vises [størrelser for virtuelle maskiner i Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) og [størrelser for virtuelle maskiner i Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Når du opretter en gruppe, som indeholder virtuelt konfiguration noder, skal du angive størrelsen på noderne, virtuelt billede referencen og batchen node agent SKU skal være installeret på noderne.

### <a name="virtual-machine-image-reference"></a>Virtuelt billede reference

Tjenesten batchen bruges [virtuelt skala angiver](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) til at levere Linux Beregn noder. Operativsystembilleder til disse virtuelle maskiner, der leveres med [Azure Marketplace][vm_marketplace]. Når du konfigurerer et virtuelt billede reference, kan du angive egenskaberne for et Marketplace virtuelt billede. Følgende egenskaber er påkrævet, når du opretter en virtuel maskine billede reference:

| **Billede af reference egenskaber** | **Eksempel** |
| ----------------- | ------------------------ |
| Publisher         | Vedtaget                |
| Tilbud             | UbuntuServer             |
| SKU               | 14.04.4-LTS              |
| Version           | seneste                   |

> [AZURE.TIP] Du kan få mere at vide om disse egenskaber, og hvordan med listen over Marketplace billeder i [Naviger og vælge Linux virtuelt billeder i Azure med CLI eller PowerShell](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Bemærk, at ikke alle Marketplace billeder i øjeblikket er kompatibel med batchen. Du kan finde yderligere oplysninger finder [Node agent SKU](#node-agent-sku).

### <a name="node-agent-sku"></a>Node agent SKU

Batchen node agent er et program, der kører på hver node i puljen og giver kommandoen kontrolelement grænsefladen mellem noden og tjenesten batchen. Der findes forskellige implementering af node agent, kaldet lagerenheder, til forskellige operativsystemer. Grundlæggende, når du opretter en virtuel maskine konfiguration, du først angive referencen virtuelt billede, og angiv derefter node agenten for at installere på billedet. Typisk hver node agent SKU er kompatibelt med flere virtuelt billeder. Her er nogle få eksempler på node agent lagerenheder:

* batch.node.Ubuntu 14.04
* batch.node.centos 7
* batch.node.Windows amd64

> [AZURE.IMPORTANT] Ikke alle virtuelt billeder, der er tilgængelige på markedet er kompatible med tilgængelige batchen node supportmedarbejdere. Du skal bruge batchen SDK'er med listen over tilgængelige node agent lagerenheder og virtuelt billederne, som de er kompatible. Se [listen virtuelt billeder](#list-of-virtual-machine-images) senere i denne artikel kan finde flere oplysninger.

## <a name="create-a-linux-pool-batch-python"></a>Oprette en Linux-gruppe: batchen Python

Følgende kodestykke viser et eksempel på, hvordan du bruger [Microsoft Azure batchen klientbibliotek til Python] [ py_batch_package] til at oprette en samling af Ubuntu Server Beregn noder. Referencedokumentation for modulet batchen Python kan findes på [azure.batch pakke] [py_batch_docs] på læst siden dokumenter.

Denne kodestykke opretter en [ImageReference] [ py_imagereference] eksplicit og angiver hver af dens egenskaber (udgiver, tilbud, SKU, version). I fremstilling kode, men vi anbefaler, at du bruger [list_node_agent_skus] [ py_list_skus] metode til at finde ud af, og vælg de tilgængelige billede node agent SKU kombinationer af og på kørselstidspunktet.

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
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Som tidligere nævnt, anbefaler vi, at i stedet for at oprette [ImageReference] [ py_imagereference] eksplicit, du bruger [list_node_agent_skus] [ py_list_skus] metode til at vælge dynamisk fra aktuelt understøttede node agent/Marketplace billede kombinationer. Den følgende Python kodestykke viser brugen af denne metode.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Oprette en Linux-gruppe: batchen .NET

Følgende kodestykke viser et eksempel på, hvordan du bruger [Batchen .NET] [ nuget_batch_net] klientbibliotek til at oprette en samling af Ubuntu Server Beregn noder. Du kan finde [batchen .NET referencedokumentation] [ api_net] på MSDN.

Følgende kodestykke bruger [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] metode til at vælge fra listen over aktuelt understøttes Marketplace billede og node agent SKU kombinationer. Denne metode er hensigtsmæssigt, fordi på listen over understøttede kombinationer kan blive ændret fra tid til anden. Hyppigst, føjes understøttede kombinationer.

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
    imageRef.SkuId.Contains("14.04");

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
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

Selvom den forrige kodestykke bruger [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] metoden til dynamisk listen, og vælg understøttes billede og node agent SKU kombinationer (anbefales), kan du også konfigurere en [ImageReference] [ net_imagereference] eksplicit:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Liste over virtuelt billeder

I følgende tabel vises de Marketplace virtuelt billeder, der er kompatible med tilgængelige batchen node supportmedarbejdere, når i denne artikel sidst blev opdateret. Det er vigtigt at være opmærksom på, at denne liste ikke er endelige, fordi billeder og node supportmedarbejdere kan tilføjes eller fjernes når som helst. Vi anbefaler, at dine batchen programmer og tjenester altid bruger [list_node_agent_skus] [ py_list_skus] (Python) og [ListNodeAgentSkus] [ net_list_skus] (batchen .NET) til at finde ud af, og vælg de tilgængelige lagerenheder.

> [AZURE.WARNING] Følgende liste kan blive ændret når som helst. Brug altid de tilgængelige i batchen API'er **listen node agent SKU** metoder til listen, og vælg derefter fra kompatible virtuelt og node agent lagerenheder, når du kører din Batch jobs.

| **Publisher** | **Tilbud** | **Billede af SKU** | **Version** | **Node agent SKU-ID** |
| ------- | ------- | ------- | ------- | ------- |
| Vedtaget | UbuntuServer | 14.04.0-LTS | seneste | batch.node.Ubuntu 14.04 |
| Vedtaget | UbuntuServer | 14.04.1-LTS | seneste | batch.node.Ubuntu 14.04 |
| Vedtaget | UbuntuServer | 14.04.2-LTS | seneste | batch.node.Ubuntu 14.04 |
| Vedtaget | UbuntuServer | 14.04.3-LTS | seneste | batch.node.Ubuntu 14.04 |
| Vedtaget | UbuntuServer | 14.04.4-LTS | seneste | batch.node.Ubuntu 14.04 |
| Vedtaget | UbuntuServer | 14.04.5-LTS | seneste | batch.node.Ubuntu 14.04 |
| Vedtaget | UbuntuServer | 16.04.0-LTS | seneste | batch.node.Ubuntu 16.04 |
| Credativ | Debian | 8 | seneste | batch.node.debian 8 |
| OpenLogic | CentOS | 7.0 | seneste | batch.node.centos 7 |
| OpenLogic | CentOS | 7.1 | seneste | batch.node.centos 7 |
| OpenLogic | CentOS HPC | 7.1 | seneste | batch.node.centos 7 |
| OpenLogic | CentOS | 7.2 | seneste | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.0 | seneste | batch.node.centos 7 |
| SUSE | openSUSE | 13,2 | seneste | batch.node.OpenSuSE 13,2 |
| SUSE | openSUSE fremskridt | 42.1 | seneste | batch.node.OpenSuSE 42.1 |
| SUSE | SLES HPC | 12 | seneste | batch.node.OpenSuSE 42.1 |
| SUSE | SLES | 12-SP1 | seneste | batch.node.OpenSuSE 42.1 |
| Microsoft-reklamer | standard-data-videnskabelige-vm | standard-data-videnskabelige-vm | seneste | batch.node.Windows amd64 |
| Microsoft-reklamer | Linux-data-videnskabelige-vm | linuxdsvm | seneste | batch.node.centos 7 |
| MicrosoftWindowsServer | WindowsServer | 2008 R2 SP1 | seneste | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | seneste | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | R2-2012-Datacenter | seneste | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-tekniske-Preview | seneste | batch.node.Windows amd64 |

## <a name="connect-to-linux-nodes"></a>Oprette forbindelse til Linux noder

Under udvikling, eller når du foretager fejlfinding, kan det være nødvendigt at logge på noderne i din gruppe. Du kan ikke bruge RDP Remote Desktop Protocol () til at oprette forbindelse til Linux noder til forskel fra Windows Beregn noder. I stedet giver batchen tjenesten SSH adgang på hver node for remote forbindelse.

Følgende Python kodestykke opretter en bruger på hver node i en gruppe, som kræves til remote forbindelse. Derefter udskrives den forbindelsesoplysningerne sikker shell (SSH) for hver node.

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

Her kan du se eksempel på output til den forrige kode for en gruppe, som indeholder fire Linux noder:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Bemærk, at i stedet for en adgangskode, du kan angive en offentlig nøgle SSH når du opretter en bruger på en node. I SDK Python dette gøres ved hjælp af parameteren **ssh_public_key** på [ComputeNodeUser][py_computenodeuser]. I .NET, dette gøres ved hjælp af [ComputeNodeUser][net_computenodeuser]. [SshPublicKey] [net_ssh_key] egenskab.

## <a name="pricing"></a>Priser

Azure batchen er bygget på Azure Cloud Services og virtuelle Azure-computere. Tjenesten batchen selve tilbydes uden omkostninger, hvilket betyder, at du betaler kun for ressourcerne, Beregn, bruge dine batchen løsninger. Når du vælger **Konfiguration af Cloud Services**, du vil betale baseret på den [Cloud Services priser] [ cloud_services_pricing] struktur. Når du vælger **Virtuelt konfiguration**, du vil betale baseret på den [virtuelle maskiner priser] [ vm_pricing] struktur.

## <a name="next-steps"></a>Næste trin

### <a name="batch-python-tutorial"></a>Batchen Python selvstudium

Se [Introduktion til Azure batchen Python klienten](batch-python-tutorial.md)til en mere i dybden selvstudium om at arbejde med kørsel ved hjælp af Python. Dens companion [kodeeksempel] [ github_samples_pyclient] indeholder en funktion, hjælper, `get_vm_config_for_distro`, der viser en anden metode til at hente et virtuelt konfigurationen.

### <a name="batch-python-code-samples"></a>Batchen Python kodeeksempler

Se de andre [Python kode eksempler] [ github_samples_py] i [azure-batchen-eksempler] [ github_samples] lager på GitHub for flere scripts, som viser, hvordan du udfører almindelige batchen handlinger som puljen, job og oprettelse af opgaver. [Vigtige oplysninger om] [ github_py_readme] , der følger med Python eksempler har mere at vide om at installere de nødvendige pakker.

### <a name="batch-forum"></a>Batchen-forum

[Azure batchen Forum] [ forum] på MSDN er et godt sted at diskutere batchen og Stil spørgsmål om tjenesten. Læs nyttige "stickied" indlæg, og stil dit spørgsmål, efterhånden som de opstår, mens du opretter dine batchen løsninger.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
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
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
