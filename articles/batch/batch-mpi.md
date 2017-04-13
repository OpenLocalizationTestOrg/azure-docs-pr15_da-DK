<properties
    pageTitle="Køre MPI programmer i Azure batchen med flere forekomster opgaver | Microsoft Azure"
    description="Lær at udføre der passerer MPI (Message Interface) programmer, der bruger flere forekomster opgavetypen i Azure Batch."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-azure-batch"></a>Brug flere forekomster opgaver til at køre der passerer MPI (Message Interface) programmer i Azure batchen

Flere forekomster opgaver kan bruges til at køre en opgave i Azure batchen samtidigt på flere Beregn noder. Disse opgaver aktivere høj ydeevne computing scenarier som der passerer MPI (Message Interface)-programmer i Batch. I denne artikel lærer du at udføre flere forekomster opgaver ved hjælp af [Batchen .NET] [ api_net] bibliotek.

>[AZURE.NOTE] Mens eksemplerne i denne artikel fokuserer på batchen .NET, MS-MPI og Windows beregne noder, er de med flere forekomster opgave begreber, der er beskrevet her, gælder for andre platforme og -teknologier (Python og Intel MPI på Linux noder, for eksempel).

## <a name="multi-instance-task-overview"></a>Oversigt over flere forekomster

I Batch, er hver enkelt opgave normalt udføres på en enkelt beregningsnode – du sende flere opgaver til en sag og tjenesten batchen planlægges de enkelte opgaver til udførelse af på en node. Ved at konfigurere en opgave **med flere forekomster indstillinger**, se dog batchen til at oprette en primære opgave og flere underopgaver, der udføres derefter på flere noder i stedet.

![Oversigt over flere forekomster][1]

Når du sender en opgave med flere forekomster indstillinger til en sag, udfører batchen flere trin, der er entydige med flere forekomster opgaver:

1. Tjenesten batchen opretter en **primær** og flere **underopgaver** baseret på indstillingerne for flere forekomster. Det samlede antal opgaver (primær plus alle underopgaver) svarer til antallet af **forekomster** (Beregn noder) du angiver i indstillingerne for flere forekomster.
1. Batchen angiver en af noderne Beregn som **master**og planlægges den primære opgave skal udføre på masteren. Det planlægger underopgaver kan udføre for resten af noderne Beregn tildeles opgaven med flere forekomster, én underopgaven per node.
1. Hent alle **almindelige ressourcefiler** , du angiver i indstillingerne for flere forekomster, primært, og alle underopgaver.
1. Efter almindelige ressourcen filer er hentet, og primært og underopgaver udføre den **kører koordineret kommando** , du angiver i indstillingerne for flere forekomster. Kommandoen kører koordineret bruges typisk til at forberede noder til udførelse af opgaven. Dette kan omfatte Start Baggrundstjenester (som [Microsoft MPI][msmpi_msdn]'s `smpd.exe`) og ved at bekræfte, at noderne er klar til at behandle mellem noder meddelelser.
1. Den primære opgave udfører **kommandoen i programmet på computeren** på den overordnede node *Når* kommandoen kører koordineret er blevet fuldført ved primært og alle underopgaver. Kommandoen program er kommandolinjen på opgaven, med flere forekomster selve og skal udføres, kun ved den primære opgave. I en [MS-MPI][msmpi_msdn]-baseret løsning, dette er, hvor du udfører MPI aktiverede program ved hjælp af `mpiexec.exe`.

> [AZURE.NOTE] Selvom det er funktionelt entydige, "med flere forekomster opgaven" ikke er en entydig opgavetype som [startopgave er] [ net_starttask] eller [JobPreparationTask][net_jobprep]. Flere forekomster opgaven er blot en standard batchopgave ([CloudTask] [ net_task] i batchen .NET) Hvis indstillingerne for flere forekomster er konfigureret. I denne artikel kalder vi dette **med flere forekomster opgave**.

## <a name="requirements-for-multi-instance-tasks"></a>Krav til flere forekomster opgaver

Flere forekomster opgaver kræver en gruppe med **mellem noder kommunikation er aktiveret**og med **samtidige opgave udførelse af deaktiveret**. Hvis du forsøger at køre en opgave med flere forekomster i en gruppe med internode kommunikation deaktiveret eller med en *maxTasksPerNode* værdi større end 1, aldrig opgaven – forbliver den på ubestemt tid i tilstanden "aktive". Denne kodestykke viser oprettelse af en sådan gruppe ved hjælp af biblioteket batchen .NET.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicated: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

Desuden kan med flere forekomster opgaver udføre *kun* for knuderne i **grupper, der er oprettet efter 14 December 2015**.

### <a name="use-a-starttask-to-install-mpi"></a>Bruge en startopgave er for at installere MPI

Hvis du vil køre MPI programmer med en opgave med flere forekomster, skal du først installere en MPI implementering (MS-MPI eller Intel MPI, for eksempel) på Beregn knuderne i puljen. Dette er et godt tidspunkt at bruge en [startopgave er][net_starttask], som udfører, når en node sammenkæder en gruppe eller genstartes. Denne kodestykke opretter en startopgave er, der angiver installationspakke MS-MPI som en [ressourcefil][net_resourcefile]. Start opgavens kommandolinjen udføres, når ressourcefilen er downloadet til noden. I dette tilfælde udfører kommandolinjen automatiseret installation af MS-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Remote direkte hukommelse access (RDMA)

Når du vælger en [RDMA-kompatible størrelse](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) som A9 for noderne Beregn i din batchen puljen, kan dit MPI program drage fordel af Azures høj ydeevne og lav ventetid remote direkte hukommelse access (RDMA) netværk.

Se efter de størrelser, der er angivet som "RDMA kan" i følgende artikler:

* **CloudServiceConfiguration** grupper

  * [Størrelser for Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (Kun Windows)

* **VirtualMachineConfiguration** grupper

  * [Størrelser for virtuelle maskiner i Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux)

  * [Størrelser for virtuelle maskiner i Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows)

>[AZURE.NOTE] For at kunne udnytte RDMA på [Linux beregne noder](batch-linux-nodes.md), skal du bruge **Intel MPI** på noderne. Flere oplysninger om CloudServiceConfiguration og VirtualMachineConfiguration grupper, i afsnittet [puljen](batch-api-basics.md#Pool) af funktionsoversigt batchen.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Oprette en opgave med flere forekomster med batchen .NET

Nu hvor vi har dækket puljen krav og MPI pakkeinstallation, Lad os oprette opgaven med flere forekomster. I denne kodestykke vi oprette en standard [CloudTask][net_task], skal du konfigurere dens [MultiInstanceSettings] [ net_multiinstance_prop] egenskab. Som tidligere nævnt er opgaven med flere forekomster ikke en særskilt opgavetype, men en standard batchopgave, der er konfigureret med flere forekomster indstillinger.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Primære opgave og underopgaver

Når du opretter flere forekomster indstillingerne for en opgave, kan du angive antallet Beregn noder, der skal udføre opgaven. Når du sender opgaven til en sag, opretter tjenesten batchen én **primære** opgave og nok **underopgaver** , der svarer til antallet af knuder, du har angivet sammen.

Disse opgaver er tildelt et heltal id i intervallet fra 0 til *numberOfInstances* - 1. Opgave med id'et 0 er den primære opgave, og alle andre id'er er underopgaver. Eksempelvis hvis du opretter flere forekomster følgende indstillinger for en opgave, den primære opgave ville have et id på 0, og underopgaverne ville have id'er 1 til 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Master node

Når du sender en opgave med flere forekomster, tjenesten batchen angiver en af noderne Beregn som "master" node og planlægges primære opgave kan udføre for den overordnede node. Underopgaverne er planlagt til at udføre på resten af noderne tildeles opgaven med flere forekomster.

## <a name="coordination-command"></a>Køre koordineret kommandoen

**Køre koordineret kommandoen** udføres ved både primært og underopgaver.

Aktiveringen af kommandoen kører koordineret blokerer – batchen ikke udføre kommandoen programmet, indtil kommandoen kører koordineret returnerede korrekt for alle underopgaver. Kommandoen kører koordineret skal derfor starte de Baggrundstjenester, der kræves Kontroller, at de er klar til brug og Afslut. For eksempel afslutter denne kører koordineret kommando efter en løsning, der bruger MS-MPI version 7 starter tjenesten SMPD på noden, derefter:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Bemærk brugen af `start` i denne kører koordineret kommando. Dette er påkrævet, fordi den `smpd.exe` program returnerer ikke umiddelbart efter udførelse af. Uden brug af [start] [ cmd_start] kommando, kører koordineret kommandoen returneres ikke, og derfor blokerer kommandoen programmet i at køre.

## <a name="application-command"></a>Kommandoen i programmet på computeren

Når den primære opgave og alle underopgaver er færdig med at udføre kommandoen kører koordineret, udføres med flere forekomster opgavens kommandolinjen ved den primære opgave *kun*. Vi kalder dette **program kommandoen** til at skelne det fra kører koordineret kommandoen.

Brug kommandoen programmet for MS-MPI programmer til at køre din MPI aktiverede programmet med `mpiexec.exe`. Her er for eksempel en programkommando til en løsning med MS-MPI version 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

>[AZURE.NOTE] Fordi MS-MPI `mpiexec.exe` bruger den `CCP_NODES` variabel ved standard (se [miljøvariabler](#environment-variables)) eksemplet programmet kommandolinjen ovenfor udelader den.

## <a name="environment-variables"></a>Miljøvariabler

Batchen opretter adskillige [miljøvariabler] [ msdn_env_var] specifikke for flere forekomster opgaver på Beregn knuderne allokeret til en opgave med flere forekomster. Din kører koordineret og programmet kommandolinjer kan henvise variablerne miljø, som kan scripts og de udføre-programmer.

Følgende miljøvariabler er oprettet af tjenesten batchen til brug af flere forekomster opgaver:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Detaljerede oplysninger om disse og andre batchen Beregn node miljøvariablerne, herunder deres indhold og synlighed finde [beregne node miljøvariabler][msdn_env_var].

>[AZURE.TIP] Eksemplet batchen Linux MPI kode indeholder et eksempel på hvordan flere af disse miljøvariabler kan bruges. [Køre koordineret cmd] [ coord_cmd_example] fest script overførsler almindelige programmer og -input filer fra Azure-lager, gør det muligt for en fil System NFS (Network) del på noden mastersider og konfigurerer de andre noder, der er tildelt opgaven med flere forekomster som NFS-klienter.

## <a name="resource-files"></a>Ressourcefiler

Der er to sæt ressourcefiler overveje for flere forekomster opgaver: **almindelige ressourcefiler** , hente *alle* opgaver (begge primær og underopgaver), og **ressourcefiler** angivet for forekomsten med flere opgave selve hvilken *kun primært* opgave overførsler.

Du kan angive en eller flere **almindelige ressourcefiler** på listen med flere forekomster indstillingerne for en opgave. Disse almindelige ressourcefiler hentes fra [Azure-lager](./../storage/storage-introduction.md) i hver node **opgave delt mappe** ved primært og alle underopgaver. Du kan få adgang til den delte mappe opgave fra programmet og køre koordineret kommandoen linjer ved hjælp af den `AZ_BATCH_TASK_SHARED_DIR` miljøvariablen. Den `AZ_BATCH_TASK_SHARED_DIR` sti er identiske på hver node, der er tildelt opgaven med flere forekomster, og som derfor kan du dele en enkelt kører koordineret kommando mellem primært, og alle underopgaver. Batchen "deler ikke" mappen fjernadgang set, men du kan bruge den som en tilslutning eller dele punkt som nævnt tidligere i spidsen på miljøvariabler.

Ressourcefiler, du angiver for opgaven med flere forekomster selve bliver hentet til opgavens arbejdsmappe `AZ_BATCH_TASK_WORKING_DIR`, som standard. Som nævnt i modsætning til almindelige ressourcefiler henter kun den primære opgave ressourcefiler, der er angivet for opgaven med flere forekomster sig selv.

> [AZURE.IMPORTANT] Brug altid miljøvariabler `AZ_BATCH_TASK_SHARED_DIR` og `AZ_BATCH_TASK_WORKING_DIR` til at referere til disse mapper i din kommandoen linjer. Undlad at opbygge stier manuelt.

## <a name="task-lifetime"></a>Opgave levetid

Levetiden for de primære opgave styrer levetiden for hele med flere forekomster opgaven. Når primært afsluttet, kan alle underopgaverne er afsluttet. Afslutningskoden for primært er afslutningskoden for opgaven, og er derfor bruges til at bestemme, om lykkes eller mislykkes på opgaven, så prøv igen.

Hvis en af underopgaverne mislykkes, mislykkes afslutte med en returnerede kode forskellig fra nul, for eksempel det hele med flere forekomster. Opgaven med flere forekomster er derefter afsluttes og forsøgt igen, op til grænsen prøv igen.

Når du sletter en opgave med flere forekomster, slettes primært, og alle underopgaver også af tjenesten batchen. Alle underopgave kataloger og deres filer, slettes fra noderne Beregn lige som for en standard opgave.

[TaskConstraints] [ net_taskconstraints] for en opgave med flere forekomster, som [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock], og [RetentionTime] [ net_taskconstraint_retention] egenskaber, der er accepteret, som de er til en almindelig opgave, og gælder for primært og alle underopgaver. Men hvis du ændrer [RetentionTime] [ net_taskconstraint_retention] egenskaben når du har tilføjet opgaven med flere forekomster til jobbet, denne ændring anvendes kun på den primære opgave. Alle underopgaverne fortsætte med at bruge den oprindelige [RetentionTime][net_taskconstraint_retention].

En beregningsnode seneste opgaveliste afspejler en underopgaven id, hvis den seneste opgave er en del af en opgave med flere forekomster.

## <a name="obtain-information-about-subtasks"></a>Få oplysninger om underopgaver

For at få oplysninger om underopgaver ved hjælp af biblioteket batchen .NET skal ringe [CloudTask.ListSubtasks] [ net_task_listsubtasks] metode. Denne metode returnerer oplysninger om alle underopgaver samt oplysninger om noden Beregn, udføres opgaverne. I disse oplysninger, kan du se hver underopgaven rodmappen, pulje-id, den aktuelle tilstand, afslutningskode og mere. Du kan bruge disse oplysninger i kombination med [PoolOperations.GetNodeFile] [ poolops_getnodefile] metode til at hente den underopgaven filer. Bemærk, at denne metode ikke returnere oplysninger om de primære opgave (id 0).

> [AZURE.NOTE] Medmindre andet er angivet, batchen .NET metoder, der fungerer i på flere forekomster [CloudTask] [ net_task] selve gælder *kun* for den primære opgave. For eksempel, hvor du ringer [CloudTask.ListNodeFiles] [ net_task_listnodefiles] metode på en opgave med flere forekomster, der skal returneres kun de primære opgave filer.

Følgende kodestykke viser, hvordan du få oplysninger om underopgaver samt anmode noderne som udført de filindholdet.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Eksempel på kode

[MultiInstanceTasks] [ github_mpi] kodeeksempel på GitHub demonstrerer, hvordan du bruger en opgave med flere forekomster til at køre en [MS-MPI] [ msmpi_msdn] program på batchen beregne noder. Følg trinnene i [forberedelse](#preparation) og [udførelse af](#execution) til at køre eksemplet.

### <a name="preparation"></a>Forberedelse

1. Følg de første to trin i [Sådan samle og køre et simpelt MS-MPI program][msmpi_howto]. Dette opfylder prerequesites til det følgende trin.
1. Opbygge *en version af [MPIHelloWorld] * [ helloworld_proj] eksempel MPI program. Dette er det program, der skal køre på Beregn noder af opgaven med flere forekomster.
1. Oprette en zip fil med `MPIHelloWorld.exe` (som du kan indbygget trin 2) og `MSMpiSetup.exe` (som du har hentet trin 1). Du skal overføre denne zip-fil som en programpakke i næste trin.
1. Bruge [Azure portal] [ portal] til at oprette et batchen [program](batch-application-packages.md) kaldet "MPIHelloWorld", og Angiv zip-filen du har oprettet i ovenstående trin som version "1.0" af programpakken. Se [overføre og administrere programmer](batch-application-packages.md#upload-and-manage-applications) kan finde flere oplysninger.

>[AZURE.TIP] Opbygge *en version af* `MPIHelloWorld.exe` så du ikke behøver at medtage yderligere afhængigheder (for eksempel `msvcp140d.dll` eller `vcruntime140d.dll`) i din programpakke.

### <a name="execution"></a>Udførelse af

1. Hente [azure-batchen-eksempler] [ github_samples_zip] fra GitHub.
1. Åbn MultiInstanceTasks **løsning** i Visual Studio-2015. Den `MultiInstanceTasks.sln` løsningsfilen er placeret i:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`

1. Angiv dine batchen og lager-kontolegitimationsoplysninger i `AccountSettings.settings` i **Microsoft.Azure.Batch.Samples.Common** projektet.
1. **Oprette og køre** MultiInstanceTasks løsning til at udføre MPI Lyt program på beregne knuderne i et batchen puljen.
1. *Valgfrit*: bruge [Azure portal] [ portal] eller [Batchen Explorer] [ batch_explorer] undersøge eksempel puljen, job og opgave ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask"), før du slette ressourcerne.

>[AZURE.TIP] Du kan hente [Visual Studio Community] [ visual_studio] gratis, hvis du ikke har Visual Studio.

Output fra `MultiInstanceTasks.exe` ligner følgende:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Næste trin

- Microsoft HPC og Azure batchen Team blog diskuterer [MPI support til Linux på Azure batchen][blog_mpi_linux], og indeholder oplysninger om brug af [OpenFOAM] [ openfoam] med batchen. Du kan finde Python kodeeksempler til [OpenFOAM eksempel på GitHub][github_mpi].

- Lær, hvordan du kan [oprette grupper af Linux Beregn noder](batch-linux-nodes.md) til brug i dine Azure batchen MPI løsninger.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Oversigt over flere forekomster"
