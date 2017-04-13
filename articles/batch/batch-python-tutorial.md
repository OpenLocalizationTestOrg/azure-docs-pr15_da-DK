<properties
    pageTitle="Selvstudium - Introduktion til Azure batchen Python klienten | Microsoft Azure"
    description="Lær de grundlæggende begreber i Azure batchen, og hvordan du udvikler tjenesten batchen med et enkelt scenarie"
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/27/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-python-client"></a>Introduktion til Azure batchen Python klienten

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Lær grundlæggende [Azure batchen] [ azure_batch] og [Batchen Python] [ py_azure_sdk] klienten som vi diskutere et lille Batch-program, der er skrevet i Python. Vi se på hvordan to eksempel scripts Brug tjenesten batchen til at behandle en parallelle arbejdsbyrde på Linux virtuelle maskiner i skyen, og hvordan de skal interagere med [Azure-lager](./../storage/storage-introduction.md) til midlertidige filer og hentning. Du får få mere at vide et almindelige batchen programmet arbejdsproces og få en grundlæggende forståelse af de vigtigste komponenter i batchen som job, opgaver, grupper og beregne noder.

![Batchen løsning arbejdsproces (basic)][11]<br/>

## <a name="prerequisites"></a>Forudsætninger

Denne artikel forudsætter, at du har et vist kendskab til Python og kendskab til Linux. Det antages, at du er mulighed for at tilfredsstille konto oprettelse af krav, der er angivet under til Azure og tjenesterne batchen og lagerplads.

### <a name="accounts"></a>Konti

- **Azure-konto**: Hvis du ikke allerede har et Azure-abonnement, [oprette en gratis Azure konto][azure_free_account].
- **Batchen konto**: Når du har et Azure-abonnement, [opretter en Azure batchen konto](batch-account-create-portal.md).
- **Lagerplads konto**: under [Opret en lagerplads konto](../storage/storage-create-storage-account.md#create-a-storage-account) i [om Azure lagerplads konti](../storage/storage-create-storage-account.md).

### <a name="code-sample"></a>Eksempel på kode

Python selvstudium [kodeeksempel] [ github_article_samples] er en af de mange batchen kodeeksempler findes i [azure-batchen-eksempler] [ github_samples] lager på GitHub. Du kan hente alle eksemplerne ved at klikke på **Klon eller download > Hent ZIP** lager hjemmeside eller ved at klikke på [azure-batchen-eksempler-master.zip] [ github_samples_zip] direkte Hent link. Når du har udpakket indholdet af ZIP-fil, der findes to scripts til dette selvstudium i den `article_samples` directory:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Python miljø

For at køre scriptet *python_tutorial_client.py* eksempel på computeren lokale, du har brug for en **Python fortolker** kompatibelt med version **2.7** eller **3.3 +**. Scriptet er testet i både Linux og Windows.

### <a name="cryptography-dependencies"></a>kryptering afhængigheder

Du skal installere afhængigheder for [kryptering] [ crypto] bibliotek, der kræves, før den `azure-batch` og `azure-storage` Python pakker. Udføre én af følgende handlinger, der er relevante for din platform eller referere til [kryptering installation] [ crypto_install] detaljer kan finde flere oplysninger:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`

* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`

* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`

* Windows

    `pip install cryptography`

>[AZURE.NOTE] Hvis du installerer for Python 3.3 + på Linux, skal du bruge python3 tilsvarende til Python afhængigheder. For eksempel på Ubuntu:`apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`

### <a name="azure-packages"></a>Azure-pakker

Dernæst skal installere **Azure batchen** og **Azure-lager** Python pakkerne. Du kan gøre med **pip** og *requirements.txt* findes her:

`/azure-batch-samples/Python/Batch/requirements.txt`

Problem følge **pip** kommando for at installere batchen og lager pakkerne:

`pip install -r requirements.txt`

Eller du kan installere [azure-batchen] [ pypi_batch] og [azure-lager] [ pypi_storage] Python pakker manuelt:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [AZURE.TIP] Du kan være nødvendigt at præfiks dine kommandoer med `sudo` Hvis du bruger en konto uden privilegier. For eksempel `sudo pip install -r requirements.txt`. Du kan finde flere oplysninger om installation af Python pakker, se [Installation af pakker] [ pypi_install] på readthedocs.io.

## <a name="batch-python-tutorial-code-sample"></a>Eksempel på batchen Python selvstudium kode

Eksemplet batchen Python selvstudium kode består af to Python scripts og nogle datafiler.

- **python_tutorial_client.py**: skal arbejde sammen med tjenesterne batchen og lagerplads til at udføre en parallelle arbejdsbyrde på Beregn noder (virtuelle maskiner). *Python_tutorial_client.py* scriptet kører på computeren lokale.

- **python_tutorial_task.py**: beregne script, der kører på noder i Azure til at udføre det faktiske arbejde. I eksemplet fortolker *python_tutorial_task.py* teksten i en fil, der er hentet fra Azure-lager (input-fil). Derefter returnerer den en tekstfil (outputfilen), der indeholder en liste over de øverste tre ord, der vises i filen. Når det opretter outputfilen, uploader *python_tutorial_task.py* filen til Azure-lager. Det gør det downloades til klientscriptet, der kører på computeren. *Python_tutorial_task.py* scriptet kører parallelt på flere Beregn noder i tjenesten batchen.

- **./data/taskdata\*.txt**: disse tre tekstfiler give input til de opgaver, der kører på noderne Beregn.

I følgende diagram vises de primære handlinger, der er udført af klienten og opgave scripts. Grundlæggende arbejdsprocessen er typisk mange Beregn løsninger, der er oprettet med batchen. Mens den ikke viser alle funktioner, der er tilgængelige i tjenesten batchen, omfatter næsten alle batchen scenarie dele af arbejdsprocessen.

![Eksempel på batchen en arbejdsgang][8]<br/>

[**Trin 1.**](#step-1-create-storage-containers) Oprette **beholdere** i Azure Blob-lager.<br/>
[**Trin 2.**](#step-2-upload-task-script-and-data-files) Upload opgave script og input filer til beholdere.<br/>
[**Trin 3.**](#step-3-create-batch-pool) Oprette en batchen **puljen**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Puljen **startopgave er** henter opgave script (python_tutorial_task.py) til noder, som de deltager puljen.<br/>
[**Trin 4.**](#step-4-create-batch-job) Oprette et **job**.<br/>
[**Trin 5.**](#step-5-add-tasks-to-job) Føje **opgaver** til jobbet.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Opgaverne, der er planlagt til at udføre på noder.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Hver enkelt opgave henter den indtastede data fra Azure-lager og derefter begynder at blive udført.<br/>
[**Trin 6.**](#step-6-monitor-tasks) Overvåge opgaver.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Opgaver er fuldført, overføre de deres outputdata til Azure-lager.<br/>
[**Trin 7.**](#step-7-download-task-output) Du kan hente opgave output fra lagerplads.

Som nævnt, ikke alle batchen løsning udfører disse nøjagtige trin og kan indeholde demonstrerer mange flere, men dette eksempel almindelige processer, der findes i en batchen løsning.

## <a name="prepare-client-script"></a>Forberede klientscript

Før du kører eksemplet, kan du føje dine batchen og lager-kontolegitimationsoplysninger til *python_tutorial_client.py*. Hvis du allerede ikke har gjort det, kan du åbne filen i din foretrukne editor og opdatere følgende linjer med dine legitimationsoplysninger.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

Du kan finde dine batchen og lager kontolegitimationsoplysninger i bladet konto for hver tjeneste i [Azure portal][azure_portal]:

![Batch legitimationsoplysninger i portalen][9]
![lagerplads legitimationsoplysninger i portalen][10]<br/>

I de følgende afsnit analysere vi de trin, der bruges af scriptene til at behandle en arbejdsbyrde i tjenesten batchen. Opfordrer vi dig jævnligt henvise til scripts i editor, mens du arbejder rundt gennem resten af artiklen.

Gå til følgende linje i **python_tutorial_client.py** til at starte med trin 1:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Trin 1: Oprette objektbeholdere

![Oprette beholdere i Azure-lager][1]
<br/>

Batchen indeholder indbygget understøttelse af interaktion med Azure-lager. Beholdere i kontoen lagerplads give de filer, der bruges til de opgaver, der kører på kontoen batchen. Beholdere giver også et sted at gemme outputdata, som opgaverne landbrugsprodukter. Det første *python_tutorial_client.py* scriptet gør, er at oprette tre beholdere i [Azure Blob-lager](../storage/storage-introduction.md#blob-storage):

- **program**: denne objektbeholder skal gemmes Python scriptet køres af opgaverne, *python_tutorial_task.py*.
- **input**: opgaver henter datafiler til at behandle fra objektbeholderen *input* .
- **output**: Når opgaver fuldført behandling af input, de overfører resultaterne til objektbeholderen *output* .

Før du kan interagere med en lagerplads konto og oprette beholdere, vi bruge [azure-lager] [ pypi_storage] pakke til at oprette en [BlockBlobService] [ py_blockblobservice] objekt – "blob-klient." Vi derefter oprette tre beholdere i kontoen lagerplads ved hjælp af blob-klienten.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

Når objektbeholdere er blevet oprettet, kan programmet nu overføre de filer, der anvendes af opgaverne.

> [AZURE.TIP] [Sådan bruger du Azure Blob-lager fra Python](../storage/storage-python-how-to-use-blob-storage.md) er en god oversigt over arbejde med Azure objektbeholdere og BLOB. Det skal være øverst på listen over læsning, når du begynder at arbejde med batchen.

## <a name="step-2-upload-task-script-and-data-files"></a>Trin 2: Overføre opgave script og datafiler

![Overføre opgave programmet og input (data) filer til objektbeholdere][2]
<br/>

I filhandling Overfør definerer *python_tutorial_client.py* først samlinger af **programmet** og **input** filstier, som de findes på den lokale computer. Den overfører derefter disse filer til beholdere, du har oprettet i ovenstående trin.

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

Ved hjælp af listen forståelse af `upload_file_to_container` funktionen kaldes for hver fil i samlingerne og to [ResourceFile] [ py_resource_file] af websteder er udfyldt. Den `upload_file_to_container` funktionen vises under:

```
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles

En [ResourceFile] [ py_resource_file] indeholder opgaver i Batch med URL-adressen til en fil i Azure-lager, der er hentet til en beregningsnode, før der køres denne opgave. [ResourceFile][py_resource_file]. **blob_source** egenskab specificerer den fulde URL-adresse på fil, som de forefindes i Azure-lager. URL-adressen kan også indeholde en delt adgang signatur (SAS), som giver sikker adgang til filen. De fleste opgavetyper i Batch omfatter en *ResourceFiles* egenskab, herunder:

- [CloudTask][py_task]
- [Startopgave er][py_starttask]
- [JobPreparationTask][py_jobpreptask]
- [JobReleaseTask][py_jobreltask]

Dette eksempel bruger ikke opgavetyper JobPreparationTask eller JobReleaseTask, men du kan læse mere om dem i [Kør job forberedelse og fuldførelse opgaver på Azure batchen beregne noder](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Delt adgang signatur (SAS)

Delt adgang signaturer er strenge, som giver sikker adgang til beholdere og BLOB i Azure-lager. *Python_tutorial_client.py* scriptet bruger begge blob og objektbeholder delt access signaturer, og viser, hvordan du får fat i strengene delt adgang signatur fra tjenesten Storage.

- **BLOB delt access signaturer**: den puljen startopgave er bruger blob delt adgang signaturer, når den henter opgave script og input datafiler fra lagerplads (se [trin #3](#step-3-create-batch-pool) nedenfor). Den `upload_file_to_container` -funktionen i *python_tutorial_client.py* indeholder den kode, der henter hver blob delt adgang signatur. Sker det ved at ringe til [BlockBlobService.make_blob_url] [ py_make_blob_url] i modulet lagerplads.

- **Objektbeholder delt adgang signatur**: som hver enkelt opgave er afsluttet på noden Beregn, den uploader dens outputfilen til objektbeholderen *output* i Azure-lager. Hvis du vil gøre det, bruger *python_tutorial_task.py* en objektbeholder delt adgang signatur, der indeholder skriveadgang til objektbeholderen. Den `get_container_sas_token` -funktionen i *python_tutorial_client.py* henter den beholder delt adgang signatur, som sendes derefter som en kommandolinjen argument til opgaver. Trin 5 #, [føje opgaver til et job](#step-5-add-tasks-to-job), i denne artikel beskrives brugen af objektbeholderen SAS.

> [AZURE.TIP] Se to dele serien på delt adgang signaturer, [del 1: om SAS datamodellen](../storage/storage-dotnet-shared-access-signature-part-1.md) og [del 2: oprette og bruge en SAS med tjenesten Blob](../storage/storage-dotnet-shared-access-signature-part-2.md), mere at vide om at give sikker adgang til data i din lagerplads konto.

## <a name="step-3-create-batch-pool"></a>Trin 3: Opret batchen puljen

![Oprette en batchen gruppe][3]
<br/>

En batchen **puljen** er en samling af Beregn noder (virtuelle maskiner), som udfører batchen et job opgaver.

Når det uploader opgave script og datafiler til kontoen lagerplads, starter *python_tutorial_client.py* dens interaktion med batchen-tjenesten ved hjælp af modulet batchen Python. Gøre det, en [BatchServiceClient] [ py_batchserviceclient] oprettes:

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

Næste, en samling af Beregn noder oprettes i batchen konto med et opkald til `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Når du opretter en gruppe, du definerer en [PoolAddParameter] [ py_pooladdparam] , der angiver flere egenskaber for puljen:

- **ID** for puljen (*id* - påkrævet)<p/>Din nye gruppe skal have et entydigt ID i kontoen batchen som i de fleste objekter i Batch. Din kode, der refererer til denne gruppe ved hjælp af dens ID, og det er, hvordan du identificerer puljen i Azure [portal][azure_portal].

- **Antallet af knuder Beregn** (*target_dedicated* - påkrævet)<p/>Denne egenskab angiver, hvor mange FOS skal installeres i puljen. Det er vigtigt at være opmærksom på, at alle batchen konti har en standard- **kvote** , som begrænser antallet af **kerner** (og dermed, Beregn noder) på en batchen konto. Du kan finde standardkvotaer og oplysninger om, hvordan at [øge en kvote](batch-quota-limit.md#increase-a-quota) (som det maksimale antal kerner i kontoen batchen) i [kvotaer og begrænsninger for tjenesten Azure batchen](batch-quota-limit.md). Hvis du kan finde dig selv stille "Hvorfor ikke min puljen når mere end X noder?" Denne core kvote kan være årsagen.

- **Operativsystem** for noder (*virtual_machine_configuration* **eller** *cloud_service_configuration* - påkrævet)<p/>I *python_tutorial_client.py*, vi oprette en samling af Linux noder ved hjælp af en [VirtualMachineConfiguration][py_vm_config]. Den `select_latest_verified_vm_image_with_node_agent_sku` fungerer i `common.helpers` forenkler arbejde med [Azure virtuelle maskiner Marketplace] [ vm_marketplace] billeder. Du kan få flere oplysninger om brugen af Marketplace billeder i [Klargøring Linux beregne knuder på Azure batchen grupper](batch-linux-nodes.md) .

- **Størrelsen på Beregn noder** (*vm_size* - påkrævet)<p/>Da angiver vi Linux noder til vores [VirtualMachineConfiguration][py_vm_config], vi angiver en VM størrelse (`STANDARD_A1` i dette eksempel) fra [størrelser for virtuelle maskiner i Azure](../virtual-machines/virtual-machines-linux-sizes.md). Se igen, [klargøring Linux beregne knuder på Azure batchen grupper](batch-linux-nodes.md) kan finde flere oplysninger.

- **Starte opgave** (*start_task* - ikke påkrævet)<p/>Sammen med det ovenfor fysiske node egenskaber, du kan også angive en [startopgave er] [ py_starttask] til puljen (det ikke er nødvendigt). Den startopgave er udføres på hver node, som denne node deltagere puljen, hver gang en node genstartes. Den startopgave er er især nyttig til forberedelse af Beregn noder til udførelse af opgaver såsom at installere de programmer, der kører dine opgaver.<p/>I dette eksempelprogram kopierer den startopgave er de filer, som den henter fra Storage (som er angivet ved hjælp af den startopgave er **resource_files** egenskaben) fra startopgave er *arbejdsmappe* til den *delte* mappe, der kan få adgang til alle opgaver, der kører på noden. Grundlæggende, Derved kopieres `python_tutorial_task.py` til den delte mappe på hver node som noden sammenkæder puljen, så opgaverne, der kører på noden har adgang til den.

Der kan være opkaldet til den `wrap_commands_in_shell` hjælper funktionen. Denne funktion kræver en samling af separat kommandoer og opretter en enkelt kommandolinje relevante for en opgave kommandolinjen egenskab.

Bemærkelsesværdige i ovenstående kodestykket er også brug af to miljøvariabler i egenskaben **kommandolinje beder** for den startopgave er: `AZ_BATCH_TASK_WORKING_DIR` og `AZ_BATCH_NODE_SHARED_DIR`. Hver beregningsnode i en gruppe i batchen er automatisk konfigureret med flere miljøvariabler, der er specifikke for batchen. En proces, der skal udføres ved en opgave har adgang til disse miljøvariabler.

> [AZURE.TIP] Hvis du vil have mere at vide om miljøet se variabler, der er tilgængelige på Beregn noder i en batchen puljen samt oplysninger om opgaven arbejde kataloger, **miljøindstillinger for opgaver** og **filer og mapper** i [Oversigt over Azure batchen funktioner](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Trin 4: Oprette kørslen

![Oprette kørslen][4]<br/>

Et **job** er en samling af opgaver, og der er knyttet til en samling af Beregn noder. Opgaverne i et job udføre på den tilknyttede puljen Beregn noder.

Du kan bruge et job ikke kun til at organisere og sporing af opgaver i relaterede arbejdsbelastninger, men også for stille bestemte begrænsninger – som den maksimale runtime til jobbet (og dermed dens opgaver) og jobbet prioritet i forhold til andre opgaver i batchen kontoen. I dette eksempel, men er jobbet tilknyttet kun puljen, du oprettede i trin #3. Ingen yderligere egenskaber, der er konfigureret.

Alle Batch jobs er knyttet til en bestemt samling. Denne tilknytning angiver, hvilke knuder på arbejdsopgaver udføre på. Du angiver puljen ved hjælp af [PoolInformation] [ py_poolinfo] egenskab, som vist i nedenstående kodestykket.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Nu, hvor et job er blevet oprettet, føjes opgaver til at udføre arbejdet.

## <a name="step-5-add-tasks-to-job"></a>Trin 5: Føj opgaver til job

![Føj opgaver til job][5]<br/>
*(1) opgaver føjes til jobbet, (2) opgaverne, der er planlagt til at køre på noder og (3) opgaver downloade datafiler til at behandle*

Batchen **opgaver** er de individuelle enheder af arbejde, der udføres på noderne Beregn. En opgave har en kommandolinje og kører scripts eller eksekverbare filer, du angiver i pågældende kommandolinjen.

Hvis du vil udføre faktisk arbejde, skal opgaver føjes til et job. Hver [CloudTask] [ py_task] er konfigureret med en kommandolinjen egenskab og [ResourceFiles] [ py_resource_file] (som med den puljen startopgave er), som opgaven er hentet til noden før dens kommandolinjen udføres automatisk. I eksemplet behandler hver enkelt opgave kun én fil. Altså indeholder dens ResourceFiles samling et enkelt element.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [AZURE.IMPORTANT] Når de åbner miljøvariabler såsom `$AZ_BATCH_NODE_SHARED_DIR` eller udføre et program, der ikke findes i noden `PATH`, opgave kommandolinjer skal kalde shell eksplicit, f.eks med `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Dette krav gælder ikke nødvendig, hvis dine opgaver udføre et program i noden `PATH` og der ikke refererer til en hvilken som helst miljøvariabler.

I den `for` løkke i kodestykket ovenfor, kan du se, at kommandolinjen for opgaven er opbygget med fem kommandolinjeargumenter, der sendes til *python_tutorial_task.py*:

1. **FilePath**: Dette er den lokale sti til filen, som det findes på noden. Når ResourceFile objekt i `upload_file_to_container` blev oprettet i trin 2 ovenfor, der blev brugt filnavnet for denne egenskab (den `file_path` parameter i ResourceFile parametre). Dette angiver, at filen kan findes i den samme mappe på noden som *python_tutorial_task.py*.

2. **NUMWORDS**: de øverste *N* ord, der skal skrives til outputfilen.

3. **storageaccount**: navnet på den konto, lagerplads, der ejer objektbeholderen, opgave output skal overføres.

4. **storagecontainer**: navnet på objektbeholderen til lagring af som outputfilerne skal overføres.

5. **sastoken**: delt adgang signaturen (SAS), hvor du kan skrive-adgang til objektbeholderen **output** i Azure-lager. *Python_tutorial_task.py* scriptet bruger denne delt adgang signatur når oprettes dets BlockBlobService reference. Dette giver skriveadgang til objektbeholderen uden en hurtigtast for kontoen lagerplads.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Trin 6: Overvåge opgaver

![Overvåge opgaver][6]<br/>
*Scriptet (1) overvåger opgaverne for status for fuldførelse og (2) opgaver uploade resultatdata til Azure-lager*

Når opgaver er føjet til et job, er de automatisk i kø og planlagt til udførelse af på Beregn noder inden for den gruppe, der er knyttet til jobbet. Baseret på de indstillinger, du angiver, håndterer batchen alle opgave queuing, planlægning, prøver igen, og andre opgaver til administration af opgave for dig.

Der findes mange måder til at overvåge udførelse af opgave. Den `wait_for_tasks_to_complete` -funktionen i *python_tutorial_client.py* indeholder et simpelt eksempel af overvågning opgaver til en bestemt fase, i dette tilfælde [fuldført] [ py_taskstate] tilstand.

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Trin 7: Hente opgave output

![Hente opgave output fra lager][7]<br/>

Nu, hvor jobbet er afsluttet, kan output fra opgaverne, der hentes fra Azure-lager. Dette er færdig med et opkald til `download_blobs_from_container` i *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [AZURE.NOTE] Opkaldet til `download_blobs_from_container` i *python_tutorial_client.py* angiver, at filerne, der skal hentes til dit eget bibliotek. Velkommen til at ændre denne outputplacering.

## <a name="step-8-delete-containers"></a>Trin 8: Slet objektbeholdere

Fordi du betaler for data, der er placeret i Azure-lager, er det altid er en god ide at fjerne en hvilken som helst blob, der ikke længere skal bruges til dine batchen sager. I *python_tutorial_client.py*dette gøres med tre opkald til [BlockBlobService.delete_container][py_delete_container]:

```
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Trin 9: Slette jobbet og puljen

I det sidste trin, bliver du bedt om at slette jobbet og puljen, der er oprettet af *python_tutorial_client.py* scriptet. Selvom du ikke betaler for job og opgaver, du *er* betale for Beregn noder. Vi anbefaler derfor, at du allokerer noder kun efter behov. Slette ubrugte grupper kan være en del af processen vedligeholdelse.

Den BatchServiceClient [JobOperations] [ py_job] og [PoolOperations] [ py_pool] begge har tilsvarende sletningen metoder, som kaldes, hvis du Bekræft sletning af:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [AZURE.IMPORTANT] Husk på, du betaler for Beregn ressourcer – slette ubrugte grupper minimeres omkostninger. Desuden være opmærksom på, når du sletter en samling sletter alle Beregn noder i denne gruppe, og at alle data på noderne bliver uoprettelig efter puljen er blevet slettet.

## <a name="run-the-sample-script"></a>Kør eksempelscriptet

Når du kører scriptet *python_tutorial_client.py* fra selvstudium [kodeeksempel][github_article_samples], output fra konsollen ligner følgende. Der er en pause på `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` mens den puljen Beregn noder er oprettet, er startet, og udføres kommandoerne i den gruppe start opgave. Bruge [Azure portal] [ azure_portal] for at overvåge dine puljen, beregne noder, job og opgaver under og efter udførelse af. Bruge [Azure portal] [ azure_portal] eller [Microsoft Azure lagerplads Explorer] [ storage_explorer] til at få vist lagerplads ressourcer (beholdere og BLOB), der er oprettet af programmet.

>[AZURE.TIP] Kør scriptet *python_tutorial_client.py* inde fra den `azure-batch-samples/Python/Batch/article_samples` directory. Den anvender en relativ sti til den `common.helpers` modul importen, så du kan se `ImportError: No module named 'common'` Hvis du ikke løber den scriptet fra denne mappe.

Typisk kørselstid er **cirka 5-7 minutter** , når du kører stikprøvernes i dens standardkonfiguration.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Næste trin

Velkommen til at foretage ændringer i *python_tutorial_client.py* og *python_tutorial_task.py* til at eksperimentere med forskellige Beregn scenarier. For eksempel kan du prøve at føje en udførelse af forsinkelse til *python_tutorial_task.py* at simulere længerevarende opgaver og overvåge dem i portalen. Prøv at tilføje flere opgaver eller justere antallet af knuder, Beregn. Føje logik for at søge efter og tillade brug af en eksisterende gruppe til hastighed udførelse af tid.

Nu hvor du er vant til grundlæggende arbejdsprocessen af en batchen løsning, er det tid at grave de ekstra funktioner til batchen-tjenesten.

- Gennemgå artiklen om [Oversigt over Azure batchen funktioner](batch-api-basics.md) , som vi anbefaler, at hvis du er ny bruger tjenesten.
- Start på de andre Batch udvikling artikler under **udvikling i dybden** i [batchen læringssti][batch_learning_path].
- Se en anden installation af behandling "øverste N ord" arbejdsbelastningen med kørsel i [TopNWords] [ github_topnwords] eksempel.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage

[pypi_install]: http://python-packaging-user-guide.readthedocs.io/en/latest/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Oprette beholdere i Azure-lager"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Overføre opgave programmet og input (data) filer til objektbeholdere"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Oprette batchen puljen"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Oprette kørslen"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Føj opgaver til job"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Overvåge opgaver"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Hente opgave output fra lager"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Batchen løsning arbejdsproces (fuld diagram)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Batchen legitimationsoplysninger i portalen"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Lagerplads legitimationsoplysninger i portalen"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Batchen løsning arbejdsproces (minimale diagram)"
