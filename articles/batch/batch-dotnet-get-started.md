<properties
    pageTitle="Selvstudium - Introduktion til biblioteket Azure batchen .NET | Microsoft Azure"
    description="Lær de grundlæggende begreber i Azure batchen, og hvordan du udvikler til tjenesten batchen med en eksempel-scenarie."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="08/15/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-library-for-net"></a>Introduktion til biblioteket Azure batchen til .NET

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Lær grundlæggende [Azure batchen] [ azure_batch] og [Batchen .NET] [ net_api] bibliotek i denne artikel som gennemgås en C# Northwind trin for trin. Vi vil se på hvordan dette eksempelprogram bruger tjenesten batchen for at behandle en parallelle arbejdsbyrde i skyen, samt hvordan det fungerer sammen med [Azure-lager](../storage/storage-introduction.md) til midlertidige filer og hentning. Du skal lære almindelige batchen programmet arbejdsproces teknikker. Du kan også få en grundlæggende forståelse af de vigtigste komponenter i batchen, såsom job, opgaver, grupper, og beregne noder.

![Batchen løsning arbejdsproces (basic)][11]<br/>

## <a name="prerequisites"></a>Forudsætninger

Denne artikel forudsætter, at du har et vist kendskab til C# og Visual Studio. Det antages, at du er mulighed for at tilfredsstille konto oprettelse af krav, der er angivet under til Azure og tjenesterne batchen og lagerplads.

### <a name="accounts"></a>Konti

- **Azure-konto**: Hvis du ikke allerede har et Azure-abonnement, [oprette en gratis Azure konto][azure_free_account].
- **Batchen konto**: Når du har et Azure-abonnement, [opretter en Azure batchen konto](batch-account-create-portal.md).
- **Lagerplads konto**: under [Opret en lagerplads konto](../storage/storage-create-storage-account.md#create-a-storage-account) i [om Azure lagerplads konti](../storage/storage-create-storage-account.md).

> [AZURE.IMPORTANT] Batchen understøtter i øjeblikket *kun* kontotypen **generelle formål** lagerplads som beskrevet i trin 5 # [Opret en lagerplads konto](../storage/storage-create-storage-account.md#create-a-storage-account) i [om Azure lagerplads konti](../storage/storage-create-storage-account.md).

### <a name="visual-studio"></a>Visual Studio

Du skal have **Visual Studio 2015** at opbygge eksempelprojektet. Du kan finde gratis versioner og prøveversioner af Visual Studio i [Oversigt over Visual Studio 2015 produkter][visual_studio].

### <a name="dotnettutorial-code-sample"></a>Eksempel på *DotNetTutorial* -kode

[DotNetTutorial] [ github_dotnettutorial] prøven er et af de mange kodeeksempler, der findes i [azure-batchen-eksempler] [ github_samples] lager på GitHub. Du kan hente stikprøvernes ved at klikke på knappen **Hent ZIP** lager hjemmeside eller ved at klikke på [azure-batchen-eksempler-master.zip] [ github_samples_zip] direkte Hent link. Når du har udpakket indholdet af ZIP-filen, kan du finde løsningen i følgende mappe:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="azure-batch-explorer-optional"></a>Azure batchen Explorer (valgfrit)

[Azure batchen Explorer] [ github_batchexplorer] er et gratis program, der er inkluderet i [azure-batchen-eksempler] [ github_samples] lager på GitHub. Mens ikke kræves for at fuldføre dette selvstudium, kan det være nyttigt under udvikling og fejlfinding af dine batchen løsninger.

## <a name="dotnettutorial-sample-project-overview"></a>DotNetTutorial eksempel Projektoversigt

*DotNetTutorial* kode prøven er en løsning i Visual Studio 2015, der består af to projekter: **DotNetTutorial** og **TaskApplication**.

- **DotNetTutorial** er klientprogrammet, der skal arbejde sammen med tjenesterne batchen og lagerplads til at udføre en parallelle arbejdsbyrde på beregne noder (virtuelle maskiner). DotNetTutorial kører på computeren lokale.

- **TaskApplication** er det program, der kører på Beregn noder i Azure til at udføre det faktiske arbejde. I eksemplet, `TaskApplication.exe` fortolker teksten i en fil, der er hentet fra Azure-lager (input-fil). Derefter returnerer den en tekstfil (outputfilen), der indeholder en liste over de øverste tre ord, der vises i filen. Når det opretter outputfilen, uploader TaskApplication filen til Azure-lager. Det gør det tilgængeligt til klientprogrammet til hentning. TaskApplication kører parallelt på flere Beregn noder i tjenesten batchen.

I følgende diagram vises de primære handlinger, der er udført af klientprogrammet, *DotNetTutorial*og det program, der er udført af opgaverne, *TaskApplication*. Grundlæggende arbejdsprocessen er typisk mange Beregn løsninger, der er oprettet med batchen. Mens den ikke viser alle funktioner, der er tilgængelige i tjenesten batchen, omfatter næsten alle batchen scenarie lignende processer.

![Eksempel på batchen en arbejdsgang][8]<br/>

[**Trin 1.**](#step-1-create-storage-containers) Oprette **beholdere** i Azure Blob-lager.<br/>
[**Trin 2.**](#step-2-upload-task-application-and-data-files) Overføre opgave programfiler og input filer til beholdere.<br/>
[**Trin 3.**](#step-3-create-batch-pool) Oprette en batchen **puljen**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Puljen **startopgave er** henter opgave binære filer (TaskApplication) til noder, som de deltager puljen.<br/>
[**Trin 4.**](#step-4-create-batch-job) Oprette et **job**.<br/>
[**Trin 5.**](#step-5-add-tasks-to-job) Føje **opgaver** til jobbet.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Opgaverne, der er planlagt til at udføre på noder.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Hver enkelt opgave henter den indtastede data fra Azure-lager og derefter begynder at blive udført.<br/>
[**Trin 6.**](#step-6-monitor-tasks) Overvåge opgaver.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Opgaver er fuldført, overføre de deres outputdata til Azure-lager.<br/>
[**Trin 7.**](#step-7-download-task-output) Du kan hente opgave output fra lagerplads.

Som nævnt, ikke alle batchen løsning udfører disse nøjagtige trin og kan omfatte mange flere, men programmet *DotNetTutorial* eksempel viser almindelige processer, der findes i en batchen løsning.

## <a name="build-the-dotnettutorial-sample-project"></a>Opbygge *DotNetTutorial* eksempelprojektet

Før du kan køre eksemplet, skal du angive både batchen og lager kontolegitimationsoplysninger i *DotNetTutorial* projektets `Program.cs` fil. Hvis du allerede ikke har gjort det, Åbn løsningen i Visual Studio ved at dobbeltklikke på den `DotNetTutorial.sln` løsningsfil. Eller Åbn den fra i Visual Studio ved hjælp af den **fil > Åbn > Project/løsning** menu.

Åbn `Program.cs` i *DotNetTutorial* projektet. Tilføj derefter dine legitimationsoplysninger som angivet i øverst del af filen:

```
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [AZURE.IMPORTANT] Som nævnt ovenfor, skal du i øjeblikket angive legitimationsoplysninger for et **almindelige formål** lagerplads konto i Azure-lager. Programmerne batchen Brug blob-lager i den **almindelige formål** lagerplads konto. Undlad at angive legitimationsoplysninger for et lager-konto, der blev oprettet ved at vælge kontotypen *Blob-lager* .

Du kan finde dine batchen og lager kontolegitimationsoplysninger i bladet konto for hver tjeneste i [Azure portal][azure_portal]:

![Batch legitimationsoplysninger i portalen][9]
![lagerplads legitimationsoplysninger i portalen][10]<br/>

Nu, hvor du har opdateret projektet med dine legitimationsoplysninger, højreklik på løsningen i Solution Explorer, og klik på **Opret løsning**. Bekræfte gendannelsen af enhver NuGet-pakker, hvis du bliver bedt om.

> [AZURE.TIP] Hvis NuGet pakker ikke gendannes automatisk, eller hvis du ser fejl om en mislykkede forsøg på at gendanne pakkerne, kan du sikre dig, at du har [NuGet Package Manager] [ nuget_packagemgr] installeret. Derefter aktivere overførsel af manglende pakker. Se [Aktivere pakke gendanne under opbygge] [ nuget_restore] til at aktivere pakke download.

I de følgende afsnit VI Opdel eksempelprogrammet i de trin, som udfører den for at behandle en arbejdsbyrde i tjenesten batchen og diskutere disse trin i detaljer. Opfordrer vi dig til at referere til Åbn løsningen i Visual Studio, mens du arbejder rundt gennem resten af denne artikel, da ikke hver linje af kode i stikprøven gennemgås.

Gå til toppen af den `MainAsync` metode i *DotNetTutorial* projektets `Program.cs` fil for at starte med trin 1. Hvert trin nedenfor derefter omkring følger forløb af metode opkald i `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Trin 1: Oprette objektbeholdere

![Oprette beholdere i Azure-lager][1]
<br/>

Batchen indeholder indbygget understøttelse af interaktion med Azure-lager. Beholdere i kontoen lagerplads give de filer, der bruges til de opgaver, der kører på kontoen batchen. Beholdere giver også et sted at gemme outputdata, som opgaverne landbrugsprodukter. Det første klientprogrammet *DotNetTutorial* betyder, er at oprette tre beholdere i [Azure Blob-lager](../storage/storage-introduction.md):

- **program**: denne objektbeholder lagrer det program, der køres af opgaverne samt et af dens afhængigheder, såsom DLL-filer.
- **input**: opgaver henter datafiler til at behandle fra objektbeholderen *input* .
- **output**: Når opgaver fuldført behandling af input, de overfører resultaterne til objektbeholderen *output* .

Før du kan interagere med en lagerplads konto og oprette beholdere, vi bruge [Azure lagerplads klientbibliotek til .NET][net_api_storage]. Vi oprette en reference til kontoen med [CloudStorageAccount][net_cloudstorageaccount], og fra, oprette en [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Vi bruger den `blobClient` reference i hele programmet og sende dem som en parameter til flere forskellige metoder. Et eksempel på dette er i den kodeblok, umiddelbart efter ovenstående, hvor vi kalder `CreateContainerIfNotExistAsync` til at oprette faktisk beholdere.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Når objektbeholdere er blevet oprettet, kan programmet nu overføre de filer, der anvendes af opgaverne.

> [AZURE.TIP] [Sådan bruger du Blob-lager fra .NET](../storage/storage-dotnet-how-to-use-blobs.md) er en god oversigt over arbejde med Azure objektbeholdere og BLOB. Det skal være øverst på listen over læsning, når du begynder at arbejde med batchen.

## <a name="step-2-upload-task-application-and-data-files"></a>Trin 2: Overføre opgave-program og datafiler

![Overføre opgave programmet og input (data) filer til objektbeholdere][2]
<br/>

I filhandling Overfør definerer *DotNetTutorial* først samlinger af **programmet** og **input** filstier, som de findes på den lokale computer. Den overfører derefter disse filer til beholdere, du har oprettet i ovenstående trin.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Der er to metoder i `Program.cs` , der er involveret i overførslen:

- `UploadFilesToContainerAsync`: Denne metode returnerer en samling af [ResourceFile] [ net_resourcefile] objekter (beskrevet nedenfor) og internt opkald `UploadFileToContainerAsync` til at overføre hver fil, der sendes i parameteren *filePaths* .
- `UploadFileToContainerAsync`: Dette er den metode, der faktisk udfører filoverførsel og opretter [ResourceFile] [ net_resourcefile] objekter. Når du har overført filen, den henter en delt adgang signatur (SAS) til filen og returnerer et ResourceFile objekt, der repræsenterer det. Delt access signaturer gennemgås også nedenfor.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath, FileMode.Open);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles

En [ResourceFile] [ net_resourcefile] indeholder opgaver i Batch med URL-adressen til en fil i Azure-lager, der er hentet til en beregningsnode, før der køres denne opgave. [ResourceFile.BlobSource] [ net_resourcefile_blobsource] egenskab specificerer den fulde URL-adresse på fil, som de forefindes i Azure-lager. URL-adressen kan også indeholde en delt adgang signatur (SAS), som giver sikker adgang til filen. De fleste opgaver typer i batchen .NET omfatter en *ResourceFiles* egenskab, herunder:

- [CloudTask][net_task]
- [Startopgave er][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

Programmet DotNetTutorial eksempel bruger ikke opgavetyper JobPreparationTask eller JobReleaseTask, men du kan læse mere om dem i [Kør job forberedelse og fuldførelse opgaver på Azure batchen beregne noder](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Delt adgang signatur (SAS)

Delt adgang signaturer er strenge, – når medtages som en del af en URL-adresse – Giv sikker adgang til beholdere og BLOB i Azure-lager. DotNetTutorial program bruger både blob og objektbeholder delt adgang til signatur URL-adresser og viser, hvordan du får fat i strengene delt adgang signatur fra tjenesten Storage.

- **BLOB delt access signaturer**: den puljen startopgave er i DotNetTutorial bruger blob delt access signaturer, når den henter programmet binære filer og inputdataene filer fra lagerplads (se trin #3 nedenfor). Den `UploadFileToContainerAsync` metode i Dotnettutorial's `Program.cs` indeholder den kode, der henter hver blob delt adgang signatur. Sker det ved at ringe til [CloudBlob.GetSharedAccessSignature][net_sas_blob].

- **Objektbeholder delt access signaturer**: som hver enkelt opgave er afsluttet på noden Beregn, den uploader dens outputfilen til objektbeholderen *output* i Azure-lager. Hvis du vil gøre det, bruger TaskApplication en objektbeholder delt adgang signatur, der indeholder skriveadgang til objektbeholderen som en del af sti, når den overfører filen. Bestil objektbeholder delt adgang signatur gøres på samme måde som når bestille blob delt access signatur. I DotNetTutorial, kan du finde, som den `GetContainerSasUrl` hjælper metode kalder [CloudBlobContainer.GetSharedAccessSignature] [ net_sas_container] at gøre dette. Du kan læse mere om, hvordan TaskApplication anvender objektbeholderen delt access signatur i "trin 6: overvåge opgaver."

> [AZURE.TIP] Se to dele serien på delt adgang signaturer, [del 1: om delt adgang signatur (SAS) datamodellen](../storage/storage-dotnet-shared-access-signature-part-1.md) og [del 2: oprette og bruge en delt adgang signatur (SAS) med Blob-lager](../storage/storage-dotnet-shared-access-signature-part-2.md), mere at vide om at give sikker adgang til data i din lagerplads konto.

## <a name="step-3-create-batch-pool"></a>Trin 3: Opret batchen puljen

![Oprette en batchen gruppe][3]
<br/>

En batchen **puljen** er en samling af Beregn noder (virtuelle maskiner), som udfører batchen et job opgaver.

Når den overfører filerne, programmer og data til kontoen lagerplads, starter *DotNetTutorial* dens interaktion med batchen-tjenesten ved hjælp af biblioteket batchen .NET. Gøre det, en [BatchClient] [ net_batchclient] oprettes:

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Næste, en samling af Beregn noder oprettes i batchen konto med et opkald til `CreatePoolAsync`. `CreatePoolAsync`bruger [BatchClient.PoolOperations.CreatePool] [ net_pool_create] metode til at oprette en gruppe i tjenesten batchen.

```csharp
private static async Task CreatePoolAsync(
    BatchClient batchClient,
    string poolId,
    IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(),
    // no pool is actually created in the Batch service. This CloudPool instance is
    // therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicated: 3,           // 3 compute nodes
            virtualMachineSize: "small",  // single-core, 1.75 GB memory, 224 GB disk
            cloudServiceConfiguration:
                new CloudServiceConfiguration(osFamily: "4")); // Win Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join
    // the pool. In this case, we copy the StartTask's resource files (that will be
    // automatically downloaded to the node by the StartTask) into the shared
    // directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application
        // files to the node's shared directory. Every compute node in a Batch pool
        // is configured with several pre-defined environment variables that you can
        // reference by using commands or applications run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code
        // (e.g. 1 when one or more files were successfully copied) we need to
        // manually exit with a 0 for Batch to recognize StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Når du opretter en gruppe med [CreatePool][net_pool_create], du angiver flere parametre som antallet af Beregn noder, [størrelse for knuderne](../cloud-services/cloud-services-sizes-specs.md)og de noder operativsystem. I *DotNetTutorial*, vi bruge [CloudServiceConfiguration] [ net_cloudserviceconfiguration] til at angive Windows Server 2012 R2 fra [Cloud Services](../cloud-services/cloud-services-guestos-update-matrix.md). Men ved at angive en [VirtualMachineConfiguration] [ net_virtualmachineconfiguration] i stedet kan du oprette grupper af knuder, der er oprettet ud fra Marketplace billeder, som indeholder både Windows og Linux billeder – se [klargøring Linux beregne knuder på Azure batchen grupper](batch-linux-nodes.md) for flere oplysninger.

> [AZURE.IMPORTANT] Du betaler for Beregn ressourcer i Batch. For at minimere omkostninger, du kan reducere `targetDedicated` til 1, før du kører stikprøvernes.

Sammen med disse fysiske node egenskaber, du kan også angive en [startopgave er] [ net_pool_starttask] til puljen. Den startopgave er udføres på hver node, som denne node deltagere puljen, hver gang en node genstartes. Den startopgave er er især nyttig til installation af programmer på Beregn noder før udførelse af opgaver. Dine opgaver behandle data ved hjælp af Python scripts, kan du bruge en startopgave er for at installere Python på noderne Beregn.

I dette eksempelprogram, den startopgave er kopierer de filer, som det er hentet fra Storage (som er angivet ved hjælp af [startopgave er][net_starttask].[ ResourceFiles] [ net_starttask_resourcefiles] egenskaben) fra arbejdsmappe startopgave er den delte mappe, der kan få adgang til *alle* opgaver, der kører på noden. Grundlæggende, Derved kopieres `TaskApplication.exe` og dens afhængigheder til den delte mappe på hver node som noden sammenkæder puljen, så opgaverne, der kører på noden kan åbne den.

> [AZURE.TIP] Funktionen **programpakker** i Azure batchen giver en anden måde at få dit program på Beregn knuderne i en gruppe. Du kan finde oplysninger i [programmet-installation med Azure batchen programpakker](batch-application-packages.md) .

Bemærkelsesværdige i ovenstående kodestykket er også brug af to miljøvariabler i egenskaben *kommandolinjen* for den startopgave er: `%AZ_BATCH_TASK_WORKING_DIR%` og `%AZ_BATCH_NODE_SHARED_DIR%`. Hver beregningsnode i en gruppe i batchen er automatisk konfigureret med flere miljøvariabler, der er specifikke for batchen. En proces, der skal udføres ved en opgave har adgang til disse miljøvariabler.

> [AZURE.TIP] Hvis du vil have mere at vide om miljøet afsnittene variabler, der er tilgængelige på Beregn noder i en batchen puljen, samt oplysninger om opgaven arbejde kataloger, [miljøindstillinger for opgaver](batch-api-basics.md#environment-settings-for-tasks) og [filer og mapper](batch-api-basics.md#files-and-directories) i [batchen funktionsoversigt for udviklere](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Trin 4: Oprette kørslen

![Oprette kørslen][4]<br/>

Et **job** er en samling af opgaver, og der er knyttet til en samling af Beregn noder. Opgaverne i et job udføre på den tilknyttede puljen Beregn noder.

Du kan bruge et job ikke kun til at organisere og sporing af opgaver i relaterede arbejdsbelastninger, men også for stille bestemte begrænsninger – som den maksimale runtime til jobbet (og dermed dens opgaver) samt jobprioritet i forhold til andre opgaver i batchen kontoen. I dette eksempel, men er jobbet tilknyttet kun puljen, du oprettede i trin #3. Ingen yderligere egenskaber, der er konfigureret.

Alle Batch jobs er knyttet til en bestemt samling. Denne tilknytning angiver, hvilke knuder på arbejdsopgaver udføres på. Du kan angive dette ved hjælp af [CloudJob.PoolInformation] [ net_job_poolinfo] egenskab, som vist i nedenstående kodestykket.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Nu, hvor et job er blevet oprettet, føjes opgaver til at udføre arbejdet.

## <a name="step-5-add-tasks-to-job"></a>Trin 5: Føj opgaver til job

![Føj opgaver til job][5]<br/>
*(1) opgaver føjes til jobbet, (2) opgaverne, der er planlagt til at køre på noder og (3) opgaver downloade datafiler til at behandle*

Batchen **opgaver** er de individuelle enheder af arbejde, der udføres på noderne Beregn. En opgave har en kommandolinje og kører scripts eller eksekverbare filer, du angiver i pågældende kommandolinjen.

Hvis du vil udføre faktisk arbejde, skal opgaver føjes til et job. Hver [CloudTask] [ net_task] er konfigureret ved hjælp af en kommandolinjen egenskab og [ResourceFiles] [ net_task_resourcefiles] (som med den puljen startopgave er), som opgaven er hentet til noden før dens kommandolinjen udføres automatisk. I *DotNetTutorial* eksempelprojektet behandler hver enkelt opgave kun én fil. Altså indeholder dens ResourceFiles samling et enkelt element.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT] Når de åbner miljøvariabler såsom `%AZ_BATCH_NODE_SHARED_DIR%` eller udføre et program, der ikke findes i noden `PATH`, skal have præfikset opgave kommandolinjer `cmd /c`. Derved eksplicit udføre kommandofortolker, og Fortæl det til at afslutte efter at have foretaget din kommando. Dette krav gælder ikke nødvendig, hvis dine opgaver udføre et program i noden `PATH` (såsom *robocopy.exe* eller *powershell.exe*) og ingen miljøvariabler bruges.

I den `foreach` løkke i kodestykket ovenfor, kan du se, at kommandolinjen for opgaven er opbygget, så der overføres tre kommandolinjeargumenter til *TaskApplication.exe*:

1. Det **første argument** er stien til filen til at behandle. Dette er den lokale sti til filen, som det findes på noden. Når ResourceFile objekt i `UploadFileToContainerAsync` oprindeligt blev oprettet oven filnavnet blev brugt til denne egenskab (som en parameter til ResourceFile parametre). Dette angiver, at filen kan findes i den samme mappe som *TaskApplication.exe*.

2. Det **andet argument** angiver, at de øverste *N* ord skal skrives til outputfilen. I eksemplet, er dette faste så de øverste tre ord, der skrives til outputfilen.

3. Det **tredje argument** er delt adgang signaturen (SAS), hvor du kan skrive-adgang til objektbeholderen **output** i Azure-lager. *TaskApplication.exe* bruger denne URL-adresse til delt access-signatur, når den uploader outputfilen til Azure-lager. Du kan finde koden for denne i den `UploadFileToContainer` metode i TaskApplication projektets `Program.cs` fil:

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath, FileMode.Open);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Trin 6: Overvåge opgaver

![Overvåge opgaver][6]<br/>
*Klientprogrammet (1) overvåger opgaverne for fuldførelse og succes status og (2) opgaver uploade resultatdata til Azure-lager*

Når opgaver er føjet til et job, er de automatisk i kø og planlagt til udførelse af på Beregn noder inden for den gruppe, der er knyttet til jobbet. Batchen, der er baseret på de indstillinger, du angiver, håndterer alle opgave queuing, planlægning, prøver igen og andre opgaver til administration af opgave for dig. Der findes mange måder til at overvåge udførelse af opgave. DotNetTutorial viser et simpelt eksempel, som oplyser kun på fuldførelse og opgave fejl eller succes tilstand.

I den `MonitorTasks` metode i Dotnettutorial's `Program.cs`, der er tre batchen .NET begreber, som berettiger diskussion. De er angivet under i deres visningsrækkefølge:

1. **ODATADetailLevel**: angive [ODATADetailLevel] [ net_odatadetaillevel] listen handlinger (såsom at hente en liste over et job opgaver) er vigtigt at sikre batchen programmets ydeevne. Tilføje [forespørgsel tjenesten Azure batchen effektivt](batch-efficient-list-queries.md) på listen læse, hvis du har planer om at gøre en hvilken som helst type status overvågning i programmerne batchen.

2. **TaskStateMonitor**: [TaskStateMonitor] [ net_taskstatemonitor] indeholder batchen .NET-programmer med hjælpeværktøjer til at overvåge opgave tilstand. I `MonitorTasks`, *DotNetTutorial* venter på, at alle opgaver når [TaskState.Completed] [ net_taskstate] inden for en tidsfrist. Det afsluttes derefter jobbet.

3. **TerminateJobAsync**: afslutning af en sag med [JobOperations.TerminateJobAsync] [ net_joboperations_terminatejob] (eller blokering JobOperations.TerminateJob) markerer jobbet som fuldført. Det er vigtigt at gøre, hvis din løsning batchen bruger en [JobReleaseTask][net_jobreltask]. Dette er en speciel type opgave, og som er beskrevet i [jobbet forberedelse og fuldførelse opgaver](batch-job-prep-release.md).

Den `MonitorTasks` metode fra *DotNetTutorial* `Program.cs` vises under:

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a scheduling error
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the
        // Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.SchedulingError != null)
        {
            // A scheduling error indicates a problem starting the task on the node.
            // It is important to note that the task's state can be "Completed," yet
            // still have encountered a scheduling error.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}",
                task.Id,
                task.ExecutionInformation.SchedulingError.Message);
        }
        else if (task.ExecutionInformation.ExitCode != 0)
        {
            // A non-zero exit code may indicate that the application executed by
            // the task encountered an error during execution. As not every
            // application returns non-zero on failure by default (e.g. robocopy),
            // your implementation of error checking may differ from this example.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Trin 7: Hente opgave output

![Hente opgave output fra lager][7]<br/>

Nu, hvor jobbet er afsluttet, kan output fra opgaverne, der hentes fra Azure-lager. Dette er færdig med et opkald til `DownloadBlobsFromContainerAsync` i *DotNetTutorial* `Program.cs`:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE] Opkaldet til `DownloadBlobsFromContainerAsync` i *DotNetTutorial* programmet angiver, at filerne, der skal hentes til din `%TEMP%` mappe. Velkommen til at ændre denne outputplacering.

## <a name="step-8-delete-containers"></a>Trin 8: Slet objektbeholdere

Fordi du betaler for data, der er placeret i Azure-lager, er det altid er en god ide at fjerne en hvilken som helst blob, der ikke længere skal bruges til dine batchen sager. I Dotnettutorial's `Program.cs`, dette gøres med tre opkald til metoden hjælper `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

Metoden selve blot henter en reference til objektbeholderen, og klik derefter kalder [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Trin 9: Slette jobbet og puljen

I det sidste trin, er brugeren bedt om at slette jobbet og puljen, der er oprettet af DotNetTutorial-program. Selvom du ikke betaler for job og opgaver, du *er* betale for Beregn noder. Vi anbefaler derfor, at du allokerer noder kun efter behov. Slette ubrugte grupper kan være en del af processen vedligeholdelse.

Den BatchClient [JobOperations] [ net_joboperations] og [PoolOperations] [ net_pooloperations] begge har tilsvarende sletningen metoder, som kaldes, hvis brugeren bekræfter sletningen:

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT] Husk, at du betaler for Beregn ressourcer – slette ubrugte grupper minimeres omkostninger. Desuden være opmærksom på, når du sletter en samling sletter alle Beregn noder i denne gruppe, og at alle data på noderne bliver uoprettelig efter puljen er blevet slettet.

## <a name="run-the-dotnettutorial-sample"></a>Køre eksemplet *DotNetTutorial*

Når du kører eksempelprogrammet, vil der være output fra konsollen stil med følgende. Under kørslen, vil du opleve en pause på `Awaiting task completion, timeout in 00:30:00...` når den puljen Beregn noder er i gang. Bruge [Azure portal] [ azure_portal] for at overvåge dine puljen, beregne noder, job og opgaver under og efter udførelse af. Bruge [Azure portal] [ azure_portal] eller [Azure Storage Explorer] [ storage_explorers] til at få vist lagerplads ressourcer (beholdere og BLOB), der er oprettet af programmet.

Typisk kørselstid er **ca. 5 minutter** , når du kører programmet i dens standardkonfiguration.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Næste trin

Velkommen til at foretage ændringer i *DotNetTutorial* og *TaskApplication* for at eksperimentere med forskellige Beregn scenarier. For eksempel forsøge at tilføje en udførelse af forsinkelse *TaskApplication*, f.eks med [Thread.Sleep][net_thread_sleep], til at efterligne længerevarende opgaver og overvåge dem på portalen. Prøv at tilføje flere opgaver eller justere antallet af knuder, Beregn. Føje logik for at søge efter og tillade brug af en eksisterende gruppe til hastigheden af tid (*Tip*: se `ArticleHelpers.cs` i [Microsoft.Azure.Batch.Samples.Common] [ github_samples_common] projekt i [azure-batchen-eksempler][github_samples]).

Nu hvor du er vant til grundlæggende arbejdsprocessen af en batchen løsning, er det tid at grave de ekstra funktioner til batchen-tjenesten.

- Læs [batchen funktionsoversigt for udviklere](batch-api-basics.md), som vi anbefaler for alle nye batchen brugere.
- Start på de andre Batch udvikling artikler under **udvikling i dybden** i [batchen læringssti][batch_learning_path].
- Se en anden installation af behandling "øverste N ord" arbejdsbelastningen ved hjælp af batchen i [TopNWords] [ github_topnwords] eksempel.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Oprette beholdere i Azure-lager"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Overføre opgave programmet og input (data) filer til objektbeholdere"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Oprette batchen puljen"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Oprette kørslen"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Føj opgaver til job"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Overvåge opgaver"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Hente opgave output fra lager"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Batchen løsning arbejdsproces (fuld diagram)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Batchen legitimationsoplysninger i portalen"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Lagerplads legitimationsoplysninger i portalen"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Batchen løsning arbejdsproces (minimale diagram)"
