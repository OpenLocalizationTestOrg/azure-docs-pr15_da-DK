<properties
    pageTitle="Visual Studio-skabeloner til Azure batchen | Microsoft Azure"
    description="Lær, hvordan disse Visual Studio projektskabeloner kan hjælpe dig med at implementere og køre din Beregn-intensivt arbejdsbelastninger på Azure batchen"
    services="batch"
    documentationCenter=".net"
    authors="fayora"
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

# <a name="visual-studio-project-templates-for-azure-batch"></a>Visual Studio project-skabeloner til Azure batchen

**Job Manager** og **opgave Processor Visual Studio skabeloner** til batchen indeholder kode for at hjælpe dig med at implementere og køre din Beregn-intensivt arbejdsbelastninger på Batch med den mindste mængde indsats. Dette dokument beskrives disse skabeloner og indeholder en vejledning til, hvordan du bruger dem.

>[AZURE.IMPORTANT] Denne artikel beskrives kun de oplysninger, der er gældende for disse to skabeloner og antages det, at du allerede kender, tjenesten batchen og vigtige begreber, der er relateret til den: grupper, beregne noder, job og opgaver, manager arbejdsopgaver, miljøvariabler og andre relevante oplysninger. Du kan finde flere oplysninger i [Grundlæggende om af Azure batchen](batch-technical-overview.md), [batchen funktionsoversigt for udviklere](batch-api-basics.md)og [komme i gang med biblioteket Azure batchen til .NET](batch-dotnet-get-started.md).

## <a name="high-level-overview"></a>Overordnet overblik

Job Manager og opgave Processor skabelonerne kan bruges til at oprette to nyttige komponenter:

* En sag manager opgave, der implementerer et job delelinje, der kan opdele en sag i flere opgaver, som kan køre uafhængigt af hinanden, parallelt.

* En opgave processor, der kan bruges til at udføre forbehandling af og efter behandling rundt om et program kommandolinjen.

For eksempel i et scenarie med film gengivelse, vil jobbet delelinjen omdanne et enkelt movie job til hundredvis eller tusindvis af separate opgaver, der ville behandler individuelle rammer separat. Tilsvarende, opgave processor vil kalde programmet gengivelse og alle efterfølgende processer, der skal bruges til at gengive hver ramme samt udføre andre handlinger (for eksempel, kopiere gengivne rammen til en lagerplacering).

>[AZURE.NOTE] Job Manager og opgave Processor skabelonerne er uafhængigt af hinanden, så du kan vælge at bruge begge eller kun én af dem, afhængigt af kravene i Beregn tingene og på dine præferencer.

Som vist i diagrammet nedenfor, går et Beregn job, der bruger disse skabeloner gennem tre faser:

1. Klient-kode (f.eks., programmet, webtjeneste, osv.) sender et job til tjenesten batchen på Azure, når du angiver som job manager opgave programmet job manager.

2. Tjenesten batchen køres manager jobopgave på en beregningsnode og det angivne antal opgave processor opgaver starter jobbet delelinjen på, som mange beregne noder efter behov, baseret på de parametre og specifikationer job fordeler koden.

3. Opgave processor opgaverne køres uafhængigt af hinanden, parallelt til at behandle inputdataene og oprette outputdataene.

![Diagram, der viser, hvordan klienten kode arbejde sammen med tjenesten batchen][diagram01]

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil bruge skabelonen til batchen, skal du følgende:

* En computer med Visual Studio-2015, eller nyere, allerede installeret på den.

* Batchen skabeloner, som er tilgængelige i [Visual Studio galleriet] [ vs_gallery] som Visual Studio filtypenavne. Der er to måder at få skabelonerne:

  * Installere de skabeloner, ved hjælp af dialogboksen **Extensions og opdateringer** i Visual Studio (få mere at vide under [finde og ved hjælp af Visual Studio filtypenavne][vs_find_use_ext]). Søge i dialogboksen **Extensions og opdateringer** og hente følgende to filtypenavne:

    * Azure batchen Job Manager med Job delelinje
    * Azure batchen opgave Processor

  * Hente skabeloner i fra galleriet online til Visual Studio: [Microsoft Azure batchen Project-skabeloner][vs_gallery_templates]

* Hvis du planlægger at bruge funktionen [Programpakker](batch-application-packages.md) til at implementere job manager, og opgaven processor til batchen beregne noder, skal du sammenkæde et firma lagerplads til kontoen batchen.

## <a name="preparation"></a>Forberedelse

Vi anbefaler, at oprette en løsning, som kan indeholde chefen job samt din opgave processor, da det kan gøre det nemmere at dele kode mellem dine job manager og opgave processor programmer. Hvis du vil oprette denne løsning, skal du følge disse trin:

1. Åbn Visual Studio-2015 og vælg **fil** > **Ny** > **Project**.

2. Udvid **Andre projekttyper**under **skabeloner**, klikke på **Visual Studio-løsninger**, og vælg derefter **Tom løsning**.

3. Skriv et navn, der beskriver dit program og formålet med denne løsning (f.eks., "LitwareBatchTaskPrograms").

4. Klik på **OK**for at oprette den nye løsning.

## <a name="job-manager-template"></a>Job Manager skabelon

Skabelonen Job Manager hjælper dig med at implementere en jobopgave manager, som kan udføre følgende handlinger:

* Opdele en sag i flere opgaver.
* Sende disse opgaver til at køre på batchen.

>[AZURE.NOTE] Du kan finde flere oplysninger om job manager opgaver, [batchen funktionsoversigt for udviklere](batch-api-basics.md#job-manager-task).

### <a name="create-a-job-manager-using-the-template"></a>Oprette en sag Manager ved hjælp af skabelonen

Hvis du vil tilføje et job manager på den løsning, du oprettede tidligere, skal du følge disse trin:

1. Åbn din eksisterende løsning i Visual Studio-2015.

2. I Solution Explorer skal du højreklikke på løsningen, klikke på **Tilføj** > **Nyt projekt**.

3. Under **Visual C#**, skal du klikke på **skyen**, og klik derefter på **Azure batchen Job Manager med Job fordeler**.

4. Skriv et navn, der beskriver dit program og identificerer dette projekt som job overordnet (f.eks. "LitwareJobManager").

5. Hvis du vil oprette projektet, skal du klikke på **OK**.

6. Til sidst skal oprette projektet for at tvinge Visual Studio at indlæse alle der refereres til NuGet-pakker og for at bekræfte, at projektet er gyldig, før du går i gang med at redigere den.

### <a name="job-manager-template-files-and-their-purpose"></a>Job Manager skabelonfiler og deres formål.

Når du opretter et projekt ved hjælp af skabelonen Job Manager, genererer tre grupper af kodefiler:

* Den primære programfil (Program.cs). Dette indeholder program indgangspunkt og håndtering af på øverste niveau undtagelse. Du må normalt være nødvendigt at ændre dette.

* Mappen Framework. Dette indeholder de filer, der er ansvarlig for 'dele af et' arbejde job manager-program – pakke parametre, føje opgaver til kørslen osv. Du må normalt være nødvendigt at ændre disse filer.

* Fordeler job filen (JobSplitter.cs). Dette er, hvor du kan anbringe dit program-specifikke logik til opdeling af et job i opgaver.

Du kan naturligvis tilføje yderligere filer efter behov til at understøtte din sag fordeler kode, afhængigt af kompleksiteten af jobbet opdele logik.

Skabelonen opretter også standard .NET project-filer som en fil som indeholder .csproj, app.config, packages.config osv.

Resten af dette afsnit beskrives de forskellige filer og deres kodestruktur og forklarer, hvad hver enkelt klasse betyder.

![Visual Studio Solution Explorer viser Job Manager skabelon løsning][solution_explorer01]

**Framework filer**

* `Configuration.cs`: Indkapsler indlæsning af job konfigurationsdata som batchen kontooplysninger, sammenkædede lagerplads kontolegitimationsoplysninger, job og opgaveoplysninger og parametre for jobbet. Det giver også adgang til batchen brugerdefineret miljøvariabler (se miljøindstillinger for opgaver, i dokumentationen til batchen) via klassen Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Abstracts implementeringen af klassen konfiguration, så du kan enhedstest din sag fordeler ved hjælp af et objekt falske eller mock konfiguration.

* `JobManager.cs`: Orchestrates komponenterne i programmet job manager. Det er ansvarlig for initialisering job delelinjen, aktivering job delelinjen og afsendelse de opgaver, der returneres af jobbet delelinjen til afsenderen opgave.

* `JobManagerException.cs`: Repræsenterer en fejl, der kræver job manager til at afslutte. JobManagerException bruges til at ombryde 'forventede' fejl, hvor bestemte diagnostiske oplysninger kan leveres som en del af opsigelse før.

* `TaskSubmitter.cs`: Denne klasse er ansvarlig for at tilføje opgaver, der returneres af jobbet delelinjen til kørslen. JobManager klasse samlinger rækkefølgen af opgaver i batches for effektiv, men tiden tilføjelse til jobbet, kalder derefter TaskSubmitter.SubmitTasks på en baggrundstråd til hvert batch.

**Jobbet delelinje**

`JobSplitter.cs`: Denne klasse indeholder program-specifikke logik til opdeling af jobbet i opgaver. Rammerne aktiverer metoden JobSplitter.Split for at få en sekvens af opgaver, der føjes til jobbet som metoden returnerer dem. Dette er klassen, hvor du vil indsætte logik om tingene. Implementere metoden Opdel for at returnere en sekvens af CloudTask forekomster, der repræsenterer de opgaver, hvor du vil partition jobbet.

**Standard .NET kommandolinjen project-filer**

* `App.config`: Standard .NET program-konfigurationsfil.

* `Packages.config`: Standard NuGet afhængighed pakkefilen.

* `Program.cs`: Indeholder program indgangspunkt og håndtering af på øverste niveau undtagelse.

### <a name="implementing-the-job-splitter"></a>Implementere delelinjen og job

Når du åbner Job Manager skabelonprojektet, have projektet åbnet JobSplitter.cs filen som standard. Du kan implementere opdelt logik for opgaverne, i arbejdsbelastningen ved hjælp af Split() metode vist nedenfor:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

>[AZURE.NOTE] Sektionen kommenterede i den `Split()` metode er den eneste del af den Job Manager skabelonkode, der er beregnet til at redigere ved at føje logik til at opdele dine sager i forskellige opgaver. Hvis du vil ændre en anden sektion på skabelonen, skal du sikre, at du familiarized med hvordan batchen virker, og prøv at bruge nogle af de [batchen kodeeksempler][github_samples].

Implementeringen Split() har adgang til:

* Parametre for jobbet via den `_parameters` felt.
* Objektet CloudJob, der repræsenterer sag, via den `_job` felt.
* Objektet CloudTask, der repræsenterer jobopgave manager via den `_jobManagerTask` felt.

Din `Split()` implementering behøver ikke at føje opgaver til jobbet direkte. I stedet din kode skal returnere en sekvens af CloudTask objekter, og disse, føjes til jobbet automatisk efter de framework klasser, aktivere delelinjen og job. Det er fælles for brug C# 's gentagelsesstreng (`yield return`) funktion til at implementere job opdelingerne, som dette giver mulighed for opgaver for at starte kører så tidligt som muligt i stedet for venter for alle opgaver skal beregnes.

**Jobbet fordeler fejl**

Hvis din sag fordeler registrerer en fejl, skal den enten:

* Afslut den rækkefølge, ved hjælp af C# `yield break` -sætning, hvori sag job manager bliver behandlet som blev gennemført. eller

* Udløs en undtagelse, hvori sag job manager bliver behandlet som mislykkedes og kan blive forsøgt igen afhængigt af hvordan klienten har konfigureret det).

I begge tilfælde vil nogen opgaver allerede returneres af jobbet delelinjen og føjet til kørslen være berettiget til at køre. Hvis du ikke vil dette sker, derefter kan du:

* Afbryde jobbet før der returneres fra delelinjen og job

* Udforme samlingen hele opgaven før der returneres det (det vil sige, at returnere en `ICollection<CloudTask>` eller `IList<CloudTask>` i stedet for at implementere din sag fordeler ved hjælp af en C#-gentagelsesstreng)

* Brug opgaveafhængigheder til at gøre alle opgaver, der er afhængige af den vellykket gennemførelse af job manager

**Nye forsøg job manager**

Hvis sagen manager mislykkes, kan det blive forsøgt igen af tjenesten batchen afhængigt af indstillingerne for klient prøv igen. Generelt er det sikkert, fordi når rammerne føjer opgaver til jobbet, ignoreres de opgaver, der allerede findes. Men hvis det er dyrt beregning af opgaver, du ikke ønsker at afholde omkostningerne for genberegning af opgaver, der allerede er blevet føjet til jobbet. omvendt, hvis igen Kør ingen er garanti for at oprette den samme opgave-id'erne derefter funktionsmåden 'Ignorer dubletter' kan ikke starte. I disse tilfælde skal du designe dit job opdeling til at registrere det arbejde, der allerede er udført, og ikke gentages, f.eks ved at udføre en CloudJob.ListTasks før du begynder at afkast opgaver.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Afslutningskoder og undtagelser i skabelonen Job Manager

Med en funktion for at afgøre resultatet ved at køre et program afslutningskoder og undtagelser, og de kan hjælpe med at identificere problemer med udførelse af programmet. Skabelonen Job Manager implementerer afslutningskoder og undtagelser, der er beskrevet i dette afsnit.

En jobopgave manager, der er implementeret med skabelonen Job Manager kan returnere tre mulige afslutningskoder:

| Kode | Beskrivelse |
|------|-------------|
| 0    | Jobbet leder blev fuldført. Din sag fordeler kode kørte til afslutning, og alle opgaver er blevet føjet til jobbet. |
| 1    | Manager jobopgave mislykkedes med en undtagelse i en 'forventede' del af programmet. Undtagelsen blev oversat til et JobManagerException med diagnostiske oplysninger og, hvor det er muligt, forslag til at løse fejlen. |
| 2    | Manager jobopgave mislykkedes med en 'uventet' undtagelse. Undtagelsen blev logget standard output, men kunne ikke tilføje yderligere oplysninger diagnostic eller afhjælpning job manager. |

I tilfælde af job manager opgave fejl kan nogle opgaver stadig er blevet føjet til tjenesten før der opstod en fejl. Disse opgaver kører som normalt. Se "Job fordeler mislykkedes" herover beskrivelse af denne kodesti.

Alle de oplysninger, der returneres af undtagelser er skrevet i stdout.txt og stderr.txt-filer. Du kan finde flere oplysninger, se [Fejlhåndtering](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Klientovervejelser

I dette afsnit beskrives nogle krav til implementering af klienten under aktivering af et job manager, der er baseret på skabelonen. Se, [hvordan du overfører parametre og miljøvariabler fra klientkoden](#pass-environment-settings) for oplysninger om går parametre og miljøindstillinger.

**Obligatorisk legitimationsoplysninger**

For at føje opgaver til Azure kørslen, kræver manager jobopgave dit Azure batchen konto URL-adresse og din nøgle. Du skal sende disse oplysninger i miljøvariabler med navnet YOUR_BATCH_URL og YOUR_BATCH_KEY. Du kan angive disse i Job Manager opgave miljøindstillinger. For eksempel i en C#-klient:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Lagerplads legitimationsoplysninger**

Typisk behøver klienten ikke at levere sammenkædede lagerplads kontolegitimationsoplysninger til manager jobopgave, fordi (a) mest job ledere behøver ikke eksplicit adgang til kontoen sammenkædede lager og (b) kontoen sammenkædede lagerplads leveres ofte på alle opgaver, som et almindelige miljø indstilling for jobbet. Hvis du ikke har indsat den sammenkædede lagerplads konto ved hjælp af de almindelige miljøindstillinger, og jobbet manager kræver adgang til sammenkædede lagerplads, skal derefter du angive de sammenkædede lagerplads legitimationsoplysninger på følgende måde:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Job manager Opgaveindstillinger**

Klienten skal angive flaget job manager *killJobOnCompletion* til **Falsk**.

Det er normalt sikkert for klienten til at angive *runExclusive* til **Falsk**.

Kunden skal bruge samlingen *resourceFiles* eller *applicationPackageReferences* jobbet manager eksekverbare (og dens nødvendige DLL-filer) Brug af noden Beregn.

Som standard forsøges job manager ikke hvis det ikke lykkes. Afhængigt af din sag manager logik klienten skal aktivere forsøg via *begrænsninger*/*maxTaskRetryCount*.

**Indstillinger for job**

Hvis sagen delelinjen udsender opgaver med afhængigheder, er det job usesTaskDependencies klienten skal indstillet til sand.

I jobbet fordeler modellen er det usædvanlige for klienter mulighed for at vil du føje opgaver til job ud over hvad job delelinjen opretter. Klienten skal derfor normalt indstille det job *onAllTasksComplete* til **terminatejob**.

## <a name="task-processor-template"></a>Skabelon til Processor

En opgave Processor skabelon hjælper dig med at implementere en opgave-processor, der kan udføre følgende handlinger:

* Konfigurere de oplysninger, der kræves af hver enkelt opgave at køre.
* Kør alle handlinger, der kræves, før hver opgave.
* Gem opgave output til fast lager.

Selvom en opgave processor ikke er behøver at køre opgaver på batchen, er vigtige fordelen ved at bruge en opgave processor, at det giver en slikpapir for at implementere alle opgave udførelse af handlinger på ét sted. Eksempelvis hvis du har brug at køre flere programmer i forbindelse med hver enkelt opgave, eller hvis du vil kopiere data til fast lager efter du har udført de enkelte opgaver.

De handlinger, der er udført af opgave processor kan være som enkel eller kompleks, og så mange eller få, som kræves af din arbejdsbyrde. Desuden ved at implementere alle opgavehandlinger til én opgave processor, kan du let opdatere eller tilføje handlinger, der er baseret på ændringer til programmer eller arbejdsbelastningen krav. Men i nogle tilfælde en opgave processor muligvis ikke den optimale løsning til implementeringen som det kan tage ekstra unødvendige kompleksitet, f.eks når du kører job, der hurtigt kan startes fra en simpel kommandolinje.

### <a name="create-a-task-processor-using-the-template"></a>Oprette en opgave Processor ved hjælp af skabelonen

Hvis du vil tilføje en opgave processor på den løsning, du oprettede tidligere, skal du følge disse trin:

1. Åbn din eksisterende løsning i Visual Studio-2015.

2. I Solution Explorer, højreklik på løsningen, klik på **Tilføj**og derefter klikke på **Nyt projekt**.

3. Under **Visual C#**, skal du klikke på **skyen**, og klik derefter på **Azure batchen opgave Processor**.

4. Skriv et navn, der beskriver dit program og identificerer dette projekt som opgave processor (f.eks. "LitwareTaskProcessor").

5. Hvis du vil oprette projektet, skal du klikke på **OK**.

6. Til sidst skal oprette projektet for at tvinge Visual Studio at indlæse alle der refereres til NuGet-pakker og for at bekræfte, at projektet er gyldig, før du går i gang med at redigere den.

### <a name="task-processor-template-files-and-their-purpose"></a>Opgave Processor skabelonfiler og deres formål.

Når du opretter et projekt ved hjælp af skabelonen opgave processor, genererer tre grupper af kodefiler:

* Den primære programfil (Program.cs). Dette indeholder program indgangspunkt og håndtering af på øverste niveau undtagelse. Du må normalt være nødvendigt at ændre dette.

* Mappen Framework. Dette indeholder de filer, der er ansvarlig for 'dele af et' arbejde job manager-program – pakke parametre, føje opgaver til kørslen osv. Du må normalt være nødvendigt at ændre disse filer.

* Opgave processor filen (TaskProcessor.cs). Dette er, hvor du kan anbringe dit program-specifikke logik for at udføre en opgave (typisk ved opkald til en eksisterende eksekverbar fil). Foreløbig og efterbehandling kode, som henter yderligere data eller overførsel af filer er resultatet, vises også her.

Du kan naturligvis tilføje yderligere filer efter behov til at understøtte din opgave processor kode, afhængigt af kompleksiteten af jobbet opdele logik.

Skabelonen opretter også standard .NET project-filer som en fil som indeholder .csproj, app.config, packages.config osv.

Resten af dette afsnit beskrives de forskellige filer og deres kodestruktur og forklarer, hvad hver enkelt klasse betyder.

![Visual Studio Solution Explorer med løsningen opgave Processor skabelon][solution_explorer02]

**Framework filer**

* `Configuration.cs`: Indkapsler indlæsning af job konfigurationsdata som batchen kontooplysninger, sammenkædede lagerplads kontolegitimationsoplysninger, job og opgaveoplysninger og parametre for jobbet. Det giver også adgang til batchen brugerdefineret miljøvariabler (se miljøindstillinger for opgaver, i dokumentationen til batchen) via klassen Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Abstracts implementeringen af klassen konfiguration, så du kan enhedstest din sag fordeler ved hjælp af et objekt falske eller mock konfiguration.

* `TaskProcessorException.cs`: Repræsenterer en fejl, der kræver job manager til at afslutte. TaskProcessorException bruges til at ombryde 'forventede' fejl, hvor bestemte diagnostiske oplysninger kan leveres som en del af opsigelse før.

**Opgave Processor**

* `TaskProcessor.cs`: Kører denne opgave. Rammerne aktiverer metoden TaskProcessor.Run. Dette er klassen, hvor du vil indsætte program-specifikke logik om opgaven. Implementere Run-metoden til at:
  * Fortolke og validere en hvilken som helst opgave-parametre
  * Skriv kommandolinjen til alle eksterne program, du vil aktivere
  * Log over en hvilken som helst diagnostiske oplysninger, kan du få brug for i forbindelse med fejlfinding
  * Starte en proces, der kommandolinje
  * Vente på at afslutte processen
  * Registrere afslutningskoden for at bestemme, om den vellykkede eller mislykkede processen
  * Gemme en hvilken som helst outputfiler, du vil beholde til fast lager

**Standard .NET kommandolinjen project-filer**

* `App.config`: Standard .NET program-konfigurationsfil.
* `Packages.config`: Standard NuGet afhængighed pakkefilen.
* `Program.cs`: Indeholder program indgangspunkt og håndtering af på øverste niveau undtagelse.

## <a name="implementing-the-task-processor"></a>Implementere opgave-processor

Når du åbner opgave Processor skabelonprojektet, have projektet åbnet TaskProcessor.cs filen som standard. Du kan implementere køre logik for opgaverne, i arbejdsbelastningen ved hjælp af metoden kører, som vist nedenfor:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
>[AZURE.NOTE] Sektionen kommenterede i metoden kører er den eneste sektion af koden opgave Processor skabelon, der er beregnet til at redigere ved at tilføje køre logik for opgaverne, i din arbejdsbyrde. Hvis du vil ændre en anden sektion på skabelonen, skal du først blive fortrolig med hvordan batchen fungerer ved at gennemse batchen dokumentation og prøve nogle af kodeeksempler batchen.

Metoden kører er ansvarlig for at åbne kommandolinjen skal starte en eller flere processer, venter på alle proces til at fuldføre, gemme resultaterne, og at vende tilbage til sidst med en afslutningskode. Metoden kører er, hvor du implementerer behandlingslogik for dine opgaver. Opgave processor framework aktiverer metoden kører for dig; Du behøver ikke at ringe det til dig selv.

Implementeringen kører har adgang til:

* Parametrene opgave via den `_parameters` felt.
* -Id'er sag og via de `_jobId` og `_taskId` felter.
* Konfiguration opgave via den `_configuration` felt.

**Opgave-fejl**

Du kan lukke metoden kører ved opstod en undtagelse i tilfælde af mislykket, men dette bevarer den øverste niveau undtagelsesbehandler kontrol over opgave afslutningskoden. Hvis du har brug for til at styre afslutningskoden, så du kan skelne mellem forskellige typer fejl, for eksempel til diagnosticering, eller fordi nogle fejl funktionsmåder skal afbryde jobbet og andre ikke skal, skal du afslutte metoden kører ved at returnere en forskellig fra nul afslutningskode. Det bliver opgave afslutningskoden.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Afslutningskoder og undtagelser i skabelonen opgave Processor

Med en funktion for at afgøre resultatet ved at køre et program afslutningskoder og undtagelser, og de kan hjælpe med at identificere problemer med udførelse af programmet. Skabelonen opgave Processor implementerer afslutningskoder og undtagelser, der er beskrevet i dette afsnit.

En opgave processor opgave, der er implementeret med skabelonen opgave Processor kan returnere tre mulige afslutningskoder:

| Kode | Beskrivelse |
|------|-------------|
|  [Process.ExitCode][process_exitcode] | Opgave-processor kørte til afslutning. Bemærk, at dette ikke indebærer, at det program, du har aktiveret var en succes – kun, en opgave processor startede den korrekt og udført en hvilken som helst efterbehandling uden undtagelser. Betydningen af afslutningskoden afhænger af det aktiverede program – typisk afslutningskode 0 betyder, at programmet er lykkedes og en hvilken som helst anden afslutningskode betyder programmet mislykkedes. |
| 1    | Opgave-processor mislykkedes med en undtagelse i en 'forventede' del af programmet. Undtagelsen blev oversat til et `TaskProcessorException` med diagnostiske oplysninger og, hvor det er muligt, forslag til at løse fejlen. |
| 2    | Opgave-processor mislykkedes med en 'uventet' undtagelse. Undtagelsen blev logget standard output, men opgave processor kunne ikke føje eventuelle yderligere diagnostic eller afhjælpning oplysninger. |

>[AZURE.NOTE] Hvis det program, du starter bruger afslutningskoder 1 og 2 til at angive bestemte fejl funktionsmåder, er derefter bruge afslutningskoder 1 og 2 for opgave processor fejl tvetydige. Du kan ændre disse opgave processor fejlkoder til karakteristisk afslutningskoder ved at redigere undtagelse sager i filen Program.cs.

Alle de oplysninger, der returneres af undtagelser er skrevet i stdout.txt og stderr.txt-filer. Yderligere oplysninger finder du se fejlhåndtering, i dokumentationen til batchen.

### <a name="client-considerations"></a>Klientovervejelser

**Lagerplads legitimationsoplysninger**

Hvis din opgave processor bruger Azure blob-lager til fastholdes output, f.eks ved hjælp af biblioteket fil konventioner hjælper derefter den skal have adgang til *enten* cloud storage firmaet legitimationsoplysninger *eller* en blob objektbeholder URL-adresse, der indeholder en delt adgang signatur (SAS). Skabelonen indeholder understøttelse af angiver legitimationsoplysninger via almindelige miljøvariabler. Kunden kan overføre lagerplads legitimationsoplysninger på følgende måde:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Kontoen lagerplads findes derefter i klassen TaskProcessor via den `_configuration.StorageAccount` egenskab.

Hvis du foretrækker at bruge en objektbeholder URL-adresse med SAS, du kan også sende det via et job almindelige miljø indstilling, men skabelonen opgave processor omfatter ikke aktuelt indbyggede understøttelse af dette.

**Konfiguration af lager**

Det anbefales, at opgaven klient på computeren eller job manager opretter en hvilken som helst beholdere, der kræves af opgaver, før du tilføjer opgaverne til jobbet. Dette er obligatorisk, hvis du bruger en objektbeholder URL-adresse med SAS, så en URL-adresse ikke omfatter tilladelse til at oprette objektbeholderen. Det anbefales selvom du overfører lagerplads kontolegitimationsoplysninger, som gemmer den hver opgave skulle kalde CloudBlobContainer.CreateIfNotExistsAsync på objektbeholderen.

## <a name="pass-parameters-and-environment-variables"></a>Parametre og miljøvariabler

### <a name="pass-environment-settings"></a>Overføre miljøindstillinger

En klient kan overføre oplysninger til manager jobopgave i form af miljøindstillinger. Disse oplysninger kan derefter bruges af manager jobopgave, når der genereres opgave processor opgaver, som kører som en del af kørslen Beregn. Eksempler på de oplysninger, du kan overføre som miljøindstillinger er:

* Lagerplads konto navn og konto taster
* Batchen konto URL-adresse
* Batchen kontonøgle

Tjenesten batchen har en enkelt metode til at overføre miljøindstillinger til en jobopgave manager ved hjælp af den `EnvironmentSettings` egenskab i [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

For at få den `BatchClient` forekomst for en Batch-konto, kan du overføre som miljøvariabler fra klienten kode URL-adresse og delte vigtige legitimationsoplysninger for kontoen batchen. På samme måde for at få adgang til kontoen lagerplads, der er knyttet til kontoen batchen, kan du overføre kontonavn lager og kontonøgle lagerplads som miljøvariabler.

### <a name="pass-parameters-to-the-job-manager-template"></a>Overføre parametre til skabelonen Job Manager

Det er praktisk at overføre-job parametre til jobbet manager opgaven til at styre jobbet opdele proces eller til at konfigurere opgaverne for jobbet i mange situationer. Du kan gøre dette ved at uploade en JSON-fil med navnet parameters.json som en ressourcefil for den sag manager. Parametrene, der kan derefter bliver tilgængelige i den `JobSplitter._parameters` i skabelonen Job Manager.

>[AZURE.NOTE] Den indbyggede parameter handler understøtter kun streng-til-streng ordbøger. Hvis du vil overføre komplekse JSON værdier som parameterværdier, skal du sende disse oplysninger som strenge og analysere dem i jobbet delelinjen eller ændre en ramme `Configuration.GetJobParameters` metode.

### <a name="pass-parameters-to-the-task-processor-template"></a>Overføre parametre til skabelonen opgave Processor

Du kan også overføre parametre til individuelle opgaver, der er implementeret ved hjælp af skabelonen opgave Processor. På samme måde som med jobskabelonen manager søger skabelonen opgave processor efter en ressourcefil med navnet

Parameters.JSON, og hvis du har fundet det indlæser som parameterordbogen. Der er et par muligheder at overføre parametre til opgave processor opgaver:

* Genbruge parametrene for jobbet JSON. Det fungerer godt, hvis de parametre, der kun er job hele dem (for eksempel, en Gengiv højde og bredde). For at gøre dette, når du opretter en CloudTask i tingene delelinjen, tilføje en reference til objektet parameters.json ressource filer fra job manager opgavens ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) til den CloudTask ResourceFiles af websteder.

* Oprette og overføre et opgave-specifikke parameters.json dokument som en del af jobbet fordeler udførelse af og henvise pågældende blob i opgavens ressource filer af websteder. Det er nødvendigt, hvis forskellige opgaver har forskellige parametre. Et eksempel kan være et 3D-gengivelse scenarie, hvor ramme indekset der overføres til opgaven som en parameter.

>[AZURE.NOTE] Den indbyggede parameter handler understøtter kun streng-til-streng ordbøger. Hvis du vil overføre komplekse JSON værdier som parameterværdier, skal du sende disse oplysninger som strenge og analysere dem i opgave-processor eller ændre en ramme `Configuration.GetTaskParameters` metode.

## <a name="next-steps"></a>Næste trin

### <a name="persist-job-and-task-output-to-azure-storage"></a>Fastholdes sag og output til Azure-lager

Et andet nyttigt værktøj i batchen løsningsudvikling er [Azure batchen fil konventioner][nuget_package]. Brug denne .NET klassebibliotek (i øjeblikket preview) i dine batchen .NET-programmer til at hurtigt og nemt at gemme og finde opgave output til og fra Azure-lager. [Fastholdes Azure kørslen og opgave output](batch-task-output.md) indeholder en fuld beskrivelse af biblioteket og dens brugen.

### <a name="batch-forum"></a>Batchen-Forum

[Azure batchen Forum] [ forum] på MSDN er et godt sted at diskutere batchen og Stil spørgsmål om tjenesten. Hoved på over til nyttige "sticky" indlæg, og stil dit spørgsmål, efterhånden som de opstår, mens du opretter dine batchen løsninger.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
