<properties
    pageTitle="Forberedelse og oprydning i Batch | Microsoft Azure"
    description="Brug job niveau Forberedelsesopgaver for at minimere dataoverførsel til Azure batchen beregne noder, og slip opgaver for node oprydning ved job afslutning."
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
    ms.date="09/16/2016"
    ms.author="marsma" />

# <a name="run-job-preparation-and-completion-tasks-on-azure-batch-compute-nodes"></a>Kør jobbet forberedelse og fuldførelse opgaver på Azure batchen beregne noder

 En Azure kørslen kræver ofte nogle form for installation, før dens opgaver er udført, og efter job vedligeholdelse, når dens opgaver er fuldført. Du skal muligvis hente fælles opgave Indtast data til din Beregn noder eller overføre opgavedata output til Azure-lager, når jobbet er fuldført. Du kan bruge **jobbet forberedelse** og **job slip** opgaver til at udføre disse handlinger.

## <a name="what-are-job-preparation-and-release-tasks"></a>Hvad er jobbet forberedelse og slip opgaver?

Før et job opgaver kører, kører forberedelse jobopgave på alle Beregn noder planlagt til at køre mindst én opgave. Når jobbet er fuldført, kører jobbet Frigiv opgave på hver node i puljen, udføres mindst én opgave. Som med normal batchopgaver, kan du angive en kommandolinje skal aktiveres, når en forberedelse eller release jobopgave køres.

Forberedelse og release opgaver tilbyder velkendte batchen opgave funktioner som filoverførsel ([ressourcefiler][net_job_prep_resourcefiles]), udvidede udførelse af, brugerdefineret miljøvariabler, maksimale udførelse af varighed, antal forsøg og klokkeslæt i filen opbevaring.

I de følgende afsnit, du vil lære at bruge [JobPreparationTask] [ net_job_prep] og [JobReleaseTask] [ net_job_release] klasser findes i [Batchen .NET] [ api_net] bibliotek.

> [AZURE.TIP] Forberedelse og release opgaver er især nyttige i "delt puljen" miljøer, i som en samling af Beregn noder fortsætter mellem job kører og bruges af mange job.

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Hvornår skal jeg bruge job forberedelse og slip opgaver

Job forberedelse og job release opgaver er passer til følgende situationer:

**Hente almindelige opgavedataene**

Batchjob kræver ofte en fælles gruppe data som input til det job opgaver. For eksempel i daglige risikoen analyse beregninger er markedsdata job-specifikke, endnu fælles for alle opgaver i jobbet. Disse markedsdata ofte flere gigabyte i størrelse, skal hentes til hver beregningsnode kun én gang så alle opgaver, der kører på noden kan bruge den. Bruge en **jobopgave forberedelse** til at hente disse data til hver node, før udførelse af jobbet er andre opgaver.

**Slette sag og output**

I en "delt puljen" miljø, hvor en samling Beregn noder ikke afvikles mellem job, kan du vil slette jobbet data mellem kører. Du skal muligvis spare diskplads på noderne eller opfylder organisationens sikkerhedspolitikker. Brug en **sag Frigiv opgave** til at slette data, der er hentet ved en opgave til forberedelse af jobbet eller genereres under udførelse af opgave.

**Vedligeholdelse af logfiler**

Du muligvis vil beholde en kopi af logfiler, som dine opgaver generere, eller måske ned lagring af filer, der kan genereres af mislykkedes programmer. Brug et **job Frigiv opgave** i så fald for at komprimere, og overfør disse data til en [Azure-lager] [ azure_storage] konto.

>[AZURE.TIP] En anden måde at fastholdes logfiler og andre sag og Medtag data er at bruge biblioteket [Azure batchen fil konventioner](batch-task-output.md) .

## <a name="job-preparation-task"></a>Forberedelse af jobopgave

Før udførelse af et job opgaver udfører batchen forberedelse jobopgave på hver beregningsnode, der er planlagt til at køre en opgave. Som standard venter tjenesten batchen job forberedelse opgaven skal være fuldført, før du kører de opgaver, der er planlagt til at udføre på noden. Du kan dog konfigurere tjenesten ikke at vente. Hvis noden genstarter, kører jobbet forberedelse opgaven igen, men du kan også deaktivere denne funktion.

Forberedelse jobopgave køres kun på noder, der er planlagt til at køre en opgave. Dette forhindrer unødvendige udførelse af en forberedelsesopgave i tilfælde af en node ikke er tildelt en opgave. Dette kan ske, når antallet af opgaver for en sag er mindre end antallet af knuder i en gruppe. Det gælder også, når [udførelse af samtidige opgave](batch-parallel-node-tasks.md) er aktiveret, der forlader nogle noder inaktiv, hvis antal opgaver er lavere end de samlede mulige samtidige opgaver. Ved at køre ikke forberedelse jobopgave på inaktiv noder, kan du sparer penge på datagebyrer overførsel.

> [AZURE.NOTE] [JobPreparationTask] [ net_job_prep_cloudjob] er forskellig fra [CloudPool.StartTask] [ pool_starttask] i den valgte JobPreparationTask udfører i starten af hvert job, mens startopgave er udfører kun, når en beregningsnode først sammenkæder en gruppe eller genstarter.

## <a name="job-release-task"></a>Jobbet Frigiv opgave

Når et job er markeret som fuldført, udføres jobbet Frigiv opgave på hver node i puljen, udføres mindst én opgave. Du har markeret en opgave som fuldført ved at udstede en anmodning om Afslut. Tjenesten batchen indstiller job tilstanden til *afslutning*, ophører en hvilken som helst aktiv eller igangværende opgaver, der er knyttet til jobbet og derefter kører jobbet Frigiv opgave. Jobbet går videre til tilstanden, der er *fuldført* .

> [AZURE.NOTE] Jobbet sletningen udfører også jobbet Frigiv opgave. Men hvis en sag er allerede blevet afsluttet, køre Frigiv opgave er ikke endnu en gang, hvis jobbet slettes senere.

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Job forberedelse og slip opgaver med batchen .NET

Hvis du vil bruge en jobopgave forberedelse, tildele en [JobPreparationTask] [ net_job_prep] objekt til dit arbejde [CloudJob.JobPreparationTask] [ net_job_prep_cloudjob] egenskab. På samme måde, initialiseret [JobReleaseTask] [ net_job_release] og tildele den til din sag [CloudJob.JobReleaseTask] [ net_job_prep_cloudjob] egenskab til at angive det job Frigiv opgave.

I denne kodestykke `myBatchClient` er en forekomst af [BatchClient][net_batch_client], og `myPool` er en eksisterende gruppe i batchen konto.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobPreparationTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Som tidligere nævnt udføres Frigiv opgave, når et job er afsluttet eller slettet. Afslutte en sag med [JobOperations.TerminateJobAsync][net_job_terminate]. Slette en sag med [JobOperations.DeleteJobAsync][net_job_delete]. Du typisk afbryde eller slette et job, når dens opgaver er fuldført, eller når en timeout, som du har angivet er nået.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsy("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Eksempel på kode på GitHub

For at se jobbet forberedelse, og slip opgaver i handling skal du se [JobPrepRelease] [ job_prep_release_sample] eksempelprojekt på GitHub. Dette program console gør følgende:

1. Opretter en gruppe med to "lille" noder.
2. Opretter et job med jobbet forberedelse, version og almindelige opgaver.
3. Kører job forberedelse opgaven, der først skriver node ID til en tekstfil i en node "fælles" mappe.
4. Kører en opgave på hver node, der skriver dens opgave-ID til den samme tekstfil.
5. Når alle opgaver bliver færdige (eller timeouten er nået), du udskriver indholdet af hver node tekstfil til konsollen.
6. Når jobbet er fuldført, du kører jobbet Frigiv opgave for at slette filen fra noden.
6. Udskriver Afslut koderne job forberedelse og release opgaver for hver enkelt node, hvor de udført.
7. Pauser udførelse tillade bekræftelse af sletning af job og/eller puljen.

Output fra eksempelprogrammet minder om følgende:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] På grund af variable oprettelse og start tidspunktet for knuderne i en ny gruppe (nogle noder er klar til opgaver, før andre), kan du se forskellige output. Da opgaverne har fuldført hurtigt, kan en af de puljen noder nærmere betegnet kan udføre alle de job opgaver. Hvis dette forekommer, kan du se, at forberede jobbet og release opgaver ikke findes for den node, der udføres nogen opgaver.

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Undersøge job forberedelse og release opgaver i portalen Azure

Når du kører programmet, kan du bruge [Azure portal] [ portal] til at se egenskaberne for jobbet og dets opgaver eller endda hente den delte tekstfil, der er ændret af det job opgaver.

Skærmbilledet nedenfor viser **forberedelse opgaver blade** i portalen Azure efter en kørsel af programmet. Gå til egenskaberne *JobPrepReleaseSampleJob* , når dine opgaver er fuldført (men før du sletter dit arbejde og puljen), og klik på **Forberedelsesopgaver** eller **Release opgaver** for at få vist deres egenskaber.

![Forberedelse jobegenskaber i Azure portal][1]

## <a name="next-steps"></a>Næste trin

### <a name="application-packages"></a>Programpakker

Du kan også bruge funktionen [programpakker](batch-application-packages.md) i batchen til at forberede Beregn noder til udførelse af opgave ud over jobopgave forberedelse. Denne funktion er især nyttig til implementering af programmer, der ikke kræver kører et installationsprogram, -programmer, der indeholder mange (100 +) filer eller programmer, der kræver nøje versionsstyring.

### <a name="installing-applications-and-staging-data"></a>Installation af programmer og arrangere data

Dette MSDN-forumindlæg giver et overblik over flere metoder til at forberede dit noder til kørsel af opgaver:

[Installation af programmer og arrangere data på batchen beregne noder][forum_post]

Skrevet af en af Azure batchen gruppemedlemmerne, behandler den flere metoder, du kan bruge til at udrulle programmer og data for at beregne noder.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
