<properties
    pageTitle="Opgaveafhængigheder i Azure Batch | Microsoft Azure"
    description="Oprette opgaver, der er afhængige af andre opgaver til at behandle MapReduce typografi og ensartede stor data er fuldført arbejdsmængder i Azure Batch."
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
    ms.date="09/28/2016"
    ms.author="marsma" />

# <a name="task-dependencies-in-azure-batch"></a>Opgaveafhængigheder i Azure Batch

Funktionen opgave afhængigheder af Azure batchen er passer, hvis du vil behandle:

- MapReduce-typografi arbejdsmængder i skyen.
- Job Hvis databehandling opgaver kan udtrykkes som en direkte acykliske graf (DAG).
- Ethvert andet job, hvori efterfølgende opgaver, der er afhængige af output fra foregående opgaver.

Batchen opgaveafhængigheder gør det muligt at oprette opgaver, der er planlagt til udførelse af på Beregn noder kun efter en eller flere opgaver er fuldført. For eksempel kan du oprette et job, der gengiver hvert billede af en 3D-film med separate, parallelle opgaver. Endeligt opgave – "Flet opgaven"--fletter gengivne rammer i den komplette film, når alle rammer er gjort korrekt.

Du kan oprette opgaver, der er afhænger af andre opgaver i en en til en eller en-til-mange-relation. Du kan endda oprette afhængigheden område, hvor en opgave afhænger af en gruppe af opgaver i et bestemt område med opgave-id'erne er fuldført. Du kan kombinere disse tre grundlæggende scenarier for at oprette mange til mange-relationer.

## <a name="task-dependencies-with-batch-net"></a>Opgaveafhængigheder med batchen .NET

I denne artikel, hvordan du konfigurerer opgaveafhængigheder ved hjælp af [Batchen .NET] diskuteres[ net_msdn] bibliotek. Vi viser dig hvordan du [aktiverer opgaveafhængighed](#enable-task-dependencies) på dine sager og derefter viser, hvordan du kan [konfigurere en opgave med afhængigheder](#create-dependent-tasks). Til sidst skal gennemgås [afhængighed scenarier](#dependency-scenarios) , der understøtter batchen.

## <a name="enable-task-dependencies"></a>Aktivere opgaveafhængigheder

Hvis du vil bruge opgaveafhængigheder i batchen programmet, skal du først se tjenesten batchen, jobbet bruger opgaveafhængigheder. Aktivere den i batchen .NET i din [CloudJob] [ net_cloudjob] ved at angive dens [UsesTaskDependencies] [ net_usestaskdependencies] egenskab, der skal `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

I det foregående kodestykke "batchClient" er en forekomst af [BatchClient] [ net_batchclient] klasse.

## <a name="create-dependent-tasks"></a>Oprette afhængige opgaver

Hvis du vil oprette en opgave, der er afhængig af en eller flere opgaver er fuldført, skal se du batchen, at opgaven "afhænger af" de andre opgaver. Konfigurere [CloudTask]i batchen .NET[net_cloudtask]. [DependsOn] [net_dependson] egenskaben med en forekomst af [TaskDependencies] [ net_taskdependencies] klasse:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Denne kodestykke opretter en opgave med ID "Blomster", der kan være planlagt til at køre på en beregningsnode, når opgaver med id'erne for "Regn" og "Søn" er fuldført.

 > [AZURE.NOTE] En opgave betragtes som udføres, når det er i tilstanden **fuldført** og dens **afslutte kode** er `0`. I batchen .NET det betyder, at en [CloudTask][net_cloudtask]. [Stat] [net_taskstate] egenskabsværdi for `Completed` og den CloudTask [TaskExecutionInformation][net_taskexecutioninformation]. [ExitCode] [net_exitcode] egenskabsværdi er `0`.

## <a name="dependency-scenarios"></a>Afhængighed scenarier

Der er tre grundlæggende opgave afhængighed scenarier, som du kan bruge i Azure Batch: en, en-til-mange og opgave-ID dataområde afhængighed. Disse kan kombineres for at give et fjerde scenarie, mange-til-mange.

 Scenarie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Eksempel | |
 :-------------------: | ------------------- | -------------------
 [En](#one-to-one) | *taskB* afhænger af *taskA* <p/> *taskB* planlægges ikke til kørsel, indtil *taskA* er fuldført | ![Diagram: en opgaveafhængighed][1]
 [En-til-mange](#one-to-many) | *taskC* afhænger af både *taskA* og *taskB* <p/> *taskC* planlægges ikke til kørsel, indtil både *taskA* og *taskB* har blev fuldført | ![Diagram: en-til-mange-opgaveafhængighed][2]
 [Opgave-ID område](#task-id-range) | *taskD* afhænger af en række opgaver <p/> *taskD* planlægges ikke til kørsel, indtil opgaver med id'er *1* - *10* blev fuldført | ![Diagram: Opgave-id område afhængighed][3]

>[AZURE.TIP] Du kan oprette **mange-til-mange** -relationer, som hvor opgaver C, D, E og F hver afhænger af opgaver A og B. Dette er nyttigt, for eksempel i parallelized forbehandling scenarier, hvor dine efterfølgende opgaver er afhængige af output fra flere foregående opgaver.

### <a name="one-to-one"></a>En

Hvis du vil oprette en opgave, der har en afhængighed på en anden opgave er fuldført, skal du angive en enkelt opgave-ID til [TaskDependencies][net_taskdependencies]. [OnId] [net_onid] statisk metode, når du udfylder [DependsOn] [ net_dependson] egenskab for [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>En-til-mange

Hvis du vil oprette en opgave, der har en afhængighed på flere opgaver er fuldført, skal du angive en samling af opgave-id'erne til [TaskDependencies][net_taskdependencies]. [OnIds] [net_onids] statisk metode, når du udfylder [DependsOn] [ net_dependson] egenskab for [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Opgave-ID område

For at oprette en opgave, der har en afhængighed af en gruppe af opgaver er afsluttet, id'er ligger i et område, skal du angive først og sidste opgave-id'er i området til [TaskDependencies][net_taskdependencies]. [OnIdRange] [net_onidrange] statisk metode, når du udfylder [DependsOn] [ net_dependson] egenskab for [CloudTask][net_cloudtask].

>[AZURE.IMPORTANT] Når du bruger opgave-ID områder for dine afhængigheder, være opgave-id'erne i det område, der *skal* streng repræsentationer af heltalsværdier. Desuden skal hver opgave i området fuldført for den afhængige opgave planlægges til udførelse af.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="code-sample"></a>Eksempel på kode

[TaskDependencies] [ github_taskdependencies] eksempelprojekt er en af [Azure batchen kodeeksempler] [ github_samples] på GitHub. Denne løsning i Visual Studio 2015 viser, hvordan du aktiverer opgaveafhængighed på en sag, oprette opgaver, der er afhænger af andre opgaver og udføre disse opgaver på en samling af Beregn noder.

## <a name="next-steps"></a>Næste trin

### <a name="application-deployment"></a>Programmet installation

Funktionen [programpakker](batch-application-packages.md) i batchen er en nem måde at begge installere og de programmer, som opgaverne udføres på beregne noder-version.

### <a name="installing-applications-and-staging-data"></a>Installation af programmer og arrangere data

Se den [installation af programmer og midlertidige data på batchen beregne noder] [ forum_post] indlæg i Azure batchen forummet for en oversigt over de forskellige metoder til at forberede dit noder til at køre opgaver. Dette indlæg er skrevet af en af Azure batchen gruppemedlemmerne, en god grundlæggende på de forskellige måder at få filer (herunder både programmer og input opgavedata) til din Beregn noder.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: en afhængighed"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: en-til-mange-afhængighed"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: opgave-id område afhængighed"
