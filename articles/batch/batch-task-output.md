<properties
    pageTitle="Sag og output brugerdata i Azure Batch | Microsoft Azure"
    description="Lær at bruge Azure-lager, som en pålidelig store til batchopgave og job output, og Aktivér visning af dette permanente output i Azure portal."
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
    ms.date="09/07/2016"
    ms.author="marsma" />

# <a name="persist-azure-batch-job-and-task-output"></a>Fastholdes Azure batchen sag og output

De opgaver, du kører i batchen typisk producere output, der skal være gemt og derefter senere bliver hentet af andre opgaver i sag, klientprogrammet, udføres jobbet eller begge dele. Dette output kan være filer, der oprettes ved at behandle indtastede data eller logfiler, der er knyttet til udførelse af opgave. I denne artikel introducerer en .NET klassebibliotek, der bruger en konventioner-baserede metode til at fastholdes sådanne opgave output til Azure Blob-lager, hvilket gør det tilgængelig, selv efter at du sletter dine grupper, sager, og beregne noder.

Ved hjælp af metode i denne artikel, kan du også se din opgave output i **gemt outputfiler** og **gemt logge** på [Azure portal][portal].

![Gemt outputfiler og gemt logfiler vælgere i portalen][1]

>[AZURE.NOTE] [Azure batchen fil konventioner] [ nuget_package] .NET klassebibliotek beskrevet i denne artikel er i øjeblikket i Vis udskrift. Nogle af de funktioner, der er beskrevet her kan blive ændret, før du generelt tilgængelig.

## <a name="task-output-considerations"></a>Overvejelser i forbindelse med opgave output

Når du designer din batchen løsning, skal du overveje flere faktorer, der er relateret til sag og output.

* **Beregne node levetid**: beregne noder er ofte forbigående, især i Autoskalering aktiverede grupper. Output af de opgaver, der kører på en node er tilgængelige, kun mens noden findes, og kun inden for fil opbevaring tid, du har angivet for opgaven. For at sikre, at opgaven output bevares, overføre dine opgaver derfor deres outputfiler til en robust butik, for eksempel Azure-lager.

* **Output lagerplads**: kan du bevare opgavedata output til robust lager, du kan bruge [Azure lagerplads SDK](../storage/storage-dotnet-how-to-use-blobs.md) i Opgavekode til at overføre opgave output til en objektbeholder til lagring af Blob. Hvis du implementerer en objektbeholder og fil naming convention, kan klientprogrammet eller andre opgaver i jobbet derefter finde og hente denne output, der er baseret på konferencen.

* **Hentning af output**: Du kan hente opgave output direkte fra Beregn knuderne i din gruppe eller fra Azure-lager, hvis dine opgaver fastholdes deres output. Hvis du vil hente en opgaves output direkte fra en Beregn knude, skal du filnavnet og outputplacering på noden. Hvis du fortsætter output til Azure-lager, skal efterfølgende opgaver eller klientprogrammet have den fulde sti til filen i Azure-lager til at hente den ved hjælp af Azure lagerplads SDK.

* **Få vist output**: Når du navigere til en opgave i portalen Azure og vælg **filer på knude**, får du vist alle filer, der er knyttet til opgaven, ikke blot outputfiler, du er interesseret i. Igen, filer på Beregn noder kan kun mens noden findes, og kun inden for fil opbevaring tid, du har angivet for opgaven. Til at få vist opgave output, som du har bevaret til Azure-lager i portalen eller et program som [Azure Storage Explorer][storage_explorer], skal du kender dets placering og gå direkte til filen.

## <a name="help-for-persisted-output"></a>Hjælp til permanente output

For at hjælpe dig mere nemt fastholdes sag og output, batchen teamet har defineret og implementeret en række navngivningskonventioner samt en .NET klassebibliotek, [Azure batchen fil konventioner] [ nuget_package] bibliotek, som du kan bruge i programmerne batchen. Portalen Azure er desuden opmærksom på disse navngivningskonventioner, så du nemt kan finde de filer, du har gemt ved hjælp af biblioteket.

## <a name="using-the-file-conventions-library"></a>Brug af biblioteket fil konventioner

[Azure batchen fil konventioner] [ nuget_package] er en .NET klassebibliotek, dine batchen .NET-programmer kan bruge til hurtigt og nemt at gemme og finde opgave output til og fra Azure-lager. Det er beregnet til brug i både opgave og klienten kode – i opgavekoden for faste filer og i klienten kode til listen, og hente dem. Opgaver kan også bruge biblioteket til at hente lagrer foregående opgaver, som i et scenarie med [opgaveafhængigheder](batch-task-dependencies.md) .

Biblioteket konventioner sørger for at sikre, at objektbeholdere og opgave outputformater filer navngives efter konferencen, og der overføres til det rigtige sted, når bevaret til Azure-lager. Når du henter output, kan du nemt finde afgang for et givet job eller en opgave ved at vise eller hentning af output ved-ID og formål, i stedet for få at vide filnavne eller hvor den findes i lagerplads.

Du kan for eksempel bruge biblioteket til "Vis alle mellemliggende filer for opgave 7" eller "Hent mig miniaturevisningen for jobbet *mymovie*," uden at skulle kender filnavne og placering i kontoen lagerplads.

### <a name="get-the-library"></a>Få biblioteket

Du kan hente det bibliotek, som indeholder nye klasser og udvider [CloudJob] [ net_cloudjob] og [CloudTask] [ net_cloudtask] klasser med nye metoder, fra [NuGet][nuget_package]. Kan du tilføje den til dit Visual Studio-projekt ved hjælp af [NuGet bibliotek Package Manager][nuget_manager].

>[AZURE.TIP] Du kan finde [kildekode] [ github_file_conventions] til Azure batchen fil konventioner biblioteket på GitHub i Microsoft Azure SDK til .NET lager.

## <a name="requirement-linked-storage-account"></a>Krav: sammenkædede lagerplads konto

Hvis du vil gemme output til robust-lager, med filen konventioner biblioteket, og få dem vist i portalen Azure, skal du [kæde en Azure-lager-konto](batch-application-packages.md#link-a-storage-account) til kontoen batchen. Hvis du ikke allerede har gjort det, kan du sammenkæde en lagerplads konto til kontoen batchen ved hjælp af portalen Azure:

**Batchen konto** blade > **Indstillinger for** > **Lagerplads konto** > **Lagerplads konto** (ingen) > Vælg en konto, lagerplads i dit abonnement

Du kan finde en mere detaljeret gennemgang i sammenkædning af en lagerplads konto [program-installation med Azure batchen programpakker](batch-application-packages.md).

## <a name="persist-output"></a>Fastholdes output

Der er to primære handlinger til at udføre, når du gemmer sag og output med biblioteket fil konventioner: oprette objektbeholderen til lagring af og gemme output til objektbeholderen.

>[AZURE.WARNING] Da alle sag og output er gemt i samme beholder, kan [lagerplads (throttling) begrænsninger](../storage/storage-performance-checklist.md#blobs) er tvungne Hvis et stort antal opgaver forsøger at bevare filer på samme tid.

### <a name="create-storage-container"></a>Oprette objektbeholder til lagring

Før dine opgaver begynder vedvarende output til lager, skal du oprette en objektbeholder til lagring af blob, som de overføre deres output. Gør dette ved at ringe til [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync] [net_prepareoutputasync]. Denne metode lokalnummer tager en [CloudStorageAccount] [ net_cloudstorageaccount] objekt som en parameter og opretter en objektbeholder med navnet på en sådan måde, indholdet er synlig ved portalen Azure og de hentning metoder, som beskrives senere i denne artikel.

Du typisk placere denne kode i klientprogrammet – det program, der opretter dine grupper, job og opgaver.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Store opgave output

Nu hvor du har forberedt en objektbeholder i blob-lager, opgaver, kan gemme output til objektbeholderen ved hjælp af [TaskOutputStorage] [ net_taskoutputstorage] klasse findes i biblioteket fil konventioner.

I din opgave kode, skal du først oprette en [TaskOutputStorage] [ net_taskoutputstorage] objekt og derefter når opgaven er afsluttet sit arbejde kalde [TaskOutputStorage][net_taskoutputstorage]. [SaveAsync] [net_saveasync] metode til at gemme dens output til Azure-lager.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

Parameteren "output type" kategoriserer permanente filerne. Der er fire foruddefinerede [TaskOutputKind] [ net_taskoutputkind] typer: "TaskOutput", "TaskPreview", "TaskLog" og "TaskIntermediate." Du kan også angive brugerdefinerede typer, hvis de kan være nyttig i arbejdsprocessen.

Disse output typer giver dig mulighed at angive, hvilke typer output til listen, når du senere forespørge batchen efter de permanente output for en bestemt opgave. Det vil sige, når du listen afgang for en opgave, kan du filtrere listen på en af typerne output. For eksempel "Giv mig *preview* output til opgave *109*." Mere vises på listen over og hentning af output i [hentning af output](#retrieve-output) senere i denne artikel.

>[AZURE.TIP] Output type angiver også hvor i portalen Azure en bestemt fil vises: *TaskOutput*-kategoriserede filer vises i "Opgave outputfiler", og *TaskLog* filer vises i "Opgave logfiler."

### <a name="store-job-outputs"></a>Store job output

Ud over at gemme opgave output, kan du gemme de output, der er knyttet til en hel sag. I opgaven fletningen af en film gengivelse sag kan du f.eks fastholdes fuldt gengivne filmen som et job output. Når din sag er afsluttet, klientprogrammet kan blot liste og hente output til opgaven, og du behøver ikke at forespørge på de enkelte opgaver.

Gemme jobbet output ved at ringe til [JobOutputStorage][net_joboutputstorage]. [SaveAsync] [net_joboutputstorage_saveasync] metode, og angiv [JobOutputKind] [ net_joboutputkind] og filnavn:

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Når med TaskOutputKind for opgave output, du bruger [JobOutputKind] [ net_joboutputkind] parameter for at kategorisere et job er bevaret filer. Denne parameter kan du senere forespørgsel (liste) en bestemt type output. JobOutputKind indeholder både output og preview og understøtter oprettelse af brugerdefinerede typer.

### <a name="store-task-logs"></a>Store opgave logfiler

Ud over vedvarer en fil til robust lager, når en opgave eller et job er fuldført, kan det være nødvendigt at bevare filer, der opdateres under kørsel af en opgave – logfiler eller `stdout.txt` og `stderr.txt`, f.eks. Hertil, indeholder biblioteket Azure batchen fil konventioner [TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync] [net_savetrackedasync] metode. Med [SaveTrackedAsync][net_savetrackedasync], du kan registrere opdateringer til en fil på noden (i et interval, du angiver) og fastholdes disse opdateringer til Azure-lager.

I det følgende kodestykke vi bruge [SaveTrackedAsync] [ net_savetrackedasync] opdatere `stdout.txt` i Azure-lager hver 15 sekunder under udførelse af opgaven:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
    await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)`er blot en pladsholder for den kode, som opgaven skal udføres normalt. Du muligvis eksempelvis kode, der henter data fra Azure-lager og udfører transformation eller beregning på den. Den vigtige del af denne kodestykke er viser, hvordan du kan ombryde anført kode i en `using` blok med jævne mellemrum opdatere en fil med [SaveTrackedAsync][net_savetrackedasync].

Den `Task.Delay` er påkrævet i slutningen af dette `using` for at sikre, at node agent har tid til at rydde indholdet af standard op til filen stdout.txt på noden (node agent er et program, der kører på hver node i puljen og indeholder kommandoen kontrolelement grænsefladen mellem noden og tjenesten batchen). Det er muligt at gå glip af de sidste par sekunder af output denne straks. Denne forsinkelse muligvis ikke kræves til alle filer.

>[AZURE.NOTE] Når du aktiverer registrering med SaveTrackedAsync fil, er kun *tilføjer* til registrerede fil bevaret til Azure-lager. Brug denne metode til sporing ikke rotere logfiler eller andre filer, der er føjet til, det vil sige, data føjes kun til slutningen af filen, når det er opdateret.

## <a name="retrieve-output"></a>Hente output

Når du henter dine permanente outputtet ved hjælp af biblioteket Azure batchen fil konventioner, kan du gøre det på en opgave - og jobbet-centreret måde. Du kan anmode om output, for den pågældende opgave eller et job uden at skulle vide stien i blob Storage eller endda filens navn. Du kan blot sige "Giv mig outputfilerne til opgave *109*."

Følgende kodestykke gentager listen over alle et job opgaver, udskrives nogle oplysninger om outputfilerne for opgaven og derefter henter filerne fra lagerplads.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>Opgave lagrer og Azure portal

Portalen Azure viser logfiler, som er bevaret og opgave output til en sammenkædet Azure-lager-konto ved hjælp af de navngivningskonventioner, findes i [Azure batchen fil konventioner vigtige oplysninger om][github_file_conventions_readme]. Du kan også selv implementere disse konventioner i et sprog efter eget valg, eller du kan bruge biblioteket fil konventioner i dine .NET-programmer.

### <a name="enable-portal-display"></a>Aktivere portal visning

Hvis du vil aktivere visningen af din output i portalen, skal du opfylder følgende krav:

 1. [Kæde en Azure-lager-konto](#requirement-linked-storage-account) til kontoen batchen.
 2. Overholde de foruddefinerede navngivningskonventioner for objektbeholdere og filer når vedvarer output. Du kan finde definitionen af disse konventioner i biblioteket fil konventioner [vigtige oplysninger om][github_file_conventions_readme]. Hvis du bruger [Azure batchen fil konventioner] [ nuget_package] bibliotek, der skal bevares din output, dette krav er opfyldt.

### <a name="view-outputs-in-the-portal"></a>Vis output i portalen

For at få vist opgave output og logge på portalen Azure, gå til opgaven, output, du er interesseret i, og klik derefter enten **gemt outputfiler** eller **Gemte logfiler**. Dette billede viser **gemt outputfiler** for opgaven med ID "007":

![Opgave lagrer blade i portalen Azure][2]

## <a name="code-sample"></a>Eksempel på kode

[PersistOutputs] [ github_persistoutputs] eksempel-projekt er et af [Azure batchen kodeeksempler] [ github_samples] på GitHub. Denne løsning i Visual Studio 2015 viser, hvordan du bruger Azure batchen fil konventioner biblioteket til fastholdes opgave output til robust lager. Hvis du vil køre eksemplet, skal du følge disse trin:

1. Åbn projektet i **Visual Studio-2015**.
2. Føj dine batchen og lager **kontolegitimationsoplysninger** til **AccountSettings.settings** i Microsoft.Azure.Batch.Samples.Common projektet.
3. **Opbygge** (men kører ikke) løsningen. Gendanne en hvilken som helst NuGet-pakker, hvis du bliver bedt om.
4. Brug portalen Azure til at overføre en [programpakke](batch-application-packages.md) på **PersistOutputsTask**. Medtage den `PersistOutputsTask.exe` og dens afhængige assemblies i pakken .zip angive program-ID til "PersistOutputsTask", og den programmet Pakkeversion til "1,0".
5. **Starte** (Kør...) **PersistOutputs** projekt.

## <a name="next-steps"></a>Næste trin

### <a name="application-deployment"></a>Programmet installation

Funktionen [programpakker](batch-application-packages.md) i batchen er en nem måde at begge installere og de programmer, som opgaverne udføres på beregne noder-version.

### <a name="installing-applications-and-staging-data"></a>Installation af programmer og arrangere data

Se den [installation af programmer og midlertidige data på batchen beregne noder] [ forum_post] indlæg i Azure batchen forummet for en oversigt over de forskellige metoder til at forberede dit noder til kørsel af opgaver. Dette indlæg er skrevet af en af Azure batchen gruppemedlemmerne, en god grundlæggende på de forskellige måder at få filer (herunder både programmer og input opgavedata) på din Beregn noder og nogle særlige overvejelser for hver enkelt metode.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Gemt outputfiler og gemt logfiler vælgere i portalen"
[2]: ./media/batch-task-output/task-output-02.png "Opgave output blade i portalen Azure"