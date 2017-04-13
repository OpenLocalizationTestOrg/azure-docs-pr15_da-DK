<properties
    pageTitle="Indeksering mediefiler med Azure Media indekseringsprogram"
    description="Azure Media indekseringsprogram gør det muligt at gøre indholdet af dine mediefiler kan søges efter og generere en hele teksten afskrift til lukket undertekster og nøgleord. Dette emne viser, hvordan du bruger Media indekseringsprogram."
    services="media-services"
    documentationCenter=""
    authors="Asolanki"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/12/2016"   
    ms.author="adsolank;juliako;johndeu"/>


# <a name="indexing-media-files-with-azure-media-indexer"></a>Indeksering mediefiler med Azure Media indekseringsprogram


Azure Media indekseringsprogram gør det muligt at gøre indholdet af dine mediefiler kan søges efter og generere en hele teksten afskrift til lukket undertekster og nøgleord. Du kan behandle en mediefil eller flere mediefilerne i en gruppe.  

>[AZURE.IMPORTANT] Når indeksering indhold, skal du sørge for at bruge mediefiler, der har klart tale (uden baggrundsmusik, støj, effekter eller mikrofon hvislen). Nogle eksempler på relevante indhold: optagede møder, forelæsninger eller præsentationer. Følgende indhold muligvis ikke passer til indeksering: film, tv-programmer, alt med blandede lyd- og lydeffekter, dårligt optaget indhold med baggrundsstøj (hvislen).


Et indeksering job kan generere følgende output:

- Lukket billedtekst filer i følgende formater: **SAMISK**, **TTML**og **WebVTT**.

    Undertekster filer omfatter en såkaldt Recognizability, hvilke pointtal, der er et indeksering job ud fra, hvordan genkendeligt tale i kilde videoen er.  Du kan bruge værdien af Recognizability til skærmen outputfiler for brugervenligheden. Lav point medfører dårlig indeksering resultater på grund af lydkvaliteten.
- Nøgleordsfil (XML).
- Lyd indeksering blob-fil (AIB) til brug med SQL server.

    Se [Brug af AIB filer med Azure Media indekseringsprogram og SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)kan finde flere oplysninger.


Dette emne viser, hvordan du opretter indekseringsjob mislykkedes til **indeks et aktiv** og **indeksere flere filer**.

Du kan finde de seneste opdateringer i Azure Media indekseringsprogram [Media Services blogge](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Brug af konfiguration og manifestet filer til indeksering opgaver

Du kan angive flere detaljer for indeksering opgaver ved hjælp af en opgave konfiguration. For eksempel kan du angive, hvilke metadata du vil bruge til din mediefil. Disse metadata bruges af programmet sprog til at udvide dets ordliste og forbedrer i høj grad talegenkendelsen.  Du kan også angive dine ønskede outputfiler.

Du kan også behandle flere mediefiler på én gang ved hjælp af en manifestfil.

Se [Opgave foruddefinerede til Azure Media indekseringsprogram](https://msdn.microsoft.com/library/dn783454.aspx)kan finde flere oplysninger.

## <a name="index-an-asset"></a>Indeksere et aktiv

Følgende metode overfører en mediefil som et aktiv, og der oprettes et job for at indeksere aktivet.

Bemærk, at hvis ingen konfigurationsfil ikke er angivet, vil mediefilen indekseres med alle standardindstillinger.

    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
<!-- __ -->
### <a id="output_files"></a>Outputfiler

Som standard genererer en indeksering job følgende outputfiler. Filerne gemmes i den første output aktiv.

Når der er mere end én input mediefil, genererer indekseringsprogram en manifestfil til jobbet output, med navnet 'JobResult.txt'. For hver indtaster mediefil, er den resulterende AIB, SAMISK, TTML, WebVTT og nøgleord filer sekventielt nummererede og navne ved hjælp af "Alias".

Filnavn | Beskrivelse
----------|------------
__InputFileName.aib__ | Indeksering blob lydfil. <br /><br /> Lydfil indeksering Blob (AIB) er en binær fil, der kan søges i Microsoft SQL server ved hjælp af søgning i hele teksten.  Filen AIB er mere effektiv metode end simple billedtekst filer, fordi den indeholder alternativer for hvert ord, så en meget bedre søgeoplevelse. <br/> <br/>Det kræver installation af tilføjelsesprogrammet indekseringsprogram SQL på en computer, der kører Microsoft SQL server 2008 eller nyere. Søge AIB ved hjælp af Microsoft SQL indeholder server søgning i hele teksten mere nøjagtig søgeresultater end søgning undertekster filer genereres af WAMI. Dette skyldes, at AIB indeholder word alternativer hvilke lyd lignende mens undertekster filer indeholde det højeste konfidensinterval ord for hvert segment i lyden. Hvis søge efter talte ord er upmost betydning, så det anbefales at bruge AIB i sammen med Microsoft SQL Server.<br/><br/> Klik på <a href="http://aka.ms/indexersql">Azure Media indekseringsprogram SQL tilføjelsesprogram</a>, du kan hente tilføjelsesprogrammet ved. <br/><br/>Det er også muligt at anvende andre søgemaskiner som Apache Lucene/Solr blot indeksere den video, der er baseret på undertekster og nøgleord XML-filer, men vil det resultere i mindre præcis søgeresultaterne.
__InputFileName.smi__<br />__InputFileName.ttml__<br />__InputFileName.vtt__ |Lukket billedtekst (CC)-filer i formaterne SAMISK, TTML og WebVTT.<br/><br/>De kan bruges til at foretage lyd- og videofiler tilgængelige for personer med nedsat handicap.<br/><br/>Lukket billedtekst filer omfatte <b>Recognizability</b> hvilke pointtal, der er et indeksering job baseret på hvordan genkendeligt tale i kilden video er en såkaldt.  Du kan bruge værdien af <b>Recognizability</b> til skærmen outputfiler for brugervenligheden. Lav point medfører dårlig indeksering resultater på grund af lydkvaliteten.
__InputFileName.kw.xml<br />InputFileName.info__ |Nøgleord og info-filer. <br/><br/>Nøgleord filen er en XML-fil, der indeholder nøgleord ud fra indholdet tale med frekvens og forskydning oplysninger. <br/><br/>Oplysninger om filen er en fil med almindelig tekst der indeholder detaljerede oplysninger om hvert udtryk, der er registreret. Den første linje er speciel og indeholder Recognizability resultat. Hver efterfølgende linje er en tabulatorsepareret liste over de følgende data: starte tid, sluttidspunkt, word/sætning, KONFIDENSINTERVAL. Klokkeslæt er givet i sekunder og tillid er angivet som et tal fra 0-1. <br/><br/>Eksempel linje: "1,20 1,45 word 0,67" <br/><br/>Disse filer kan bruges til et tal formål, f.eks., til at udføre tale analytics eller vises for at søgemaskiner som Bing, Google eller Microsoft SharePoint for at oprette mediefilerne mere synlig eller endda bruges til at levere flere relevante reklamer.
__JobResult.txt__ |Output manifest, findes kun, når indeksering af flere filer, der indeholder følgende oplysninger:<br/><br/><table border="1"><tr><th>Inputfil</th><th>Alias</th><th>MediaLength</th><th>Fejl</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/>



Hvis ikke alle input mediefiler indekseres korrekt, kørslen indekseringsjob mislykkedes med fejlkode 4000. Du kan finde flere oplysninger [fejlkoder](#error_codes).

## <a name="index-multiple-files"></a>Indeksere flere filer

Følgende metode overførsler flere media-filer som et aktiv, og opretter et job for at indeksere alle filerne i en gruppe.

En manifestfil med filtypenavnet .lst er oprettet og blive overført til aktivet. Manifestfilen indeholder liste over alle aktiv-filerne. Se [Opgave foruddefinerede til Azure Media indekseringsprogram](https://msdn.microsoft.com/library/dn783454.aspx)kan finde flere oplysninger.

    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }

### <a name="partially-succeeded-job"></a>Delvist vellykkede Job

Hvis ikke alle input mediefiler indekseres korrekt, kørslen indekseringsjob mislykkedes med fejlkode 4000. Du kan finde flere oplysninger [fejlkoder](#error_codes).


Der oprettes på samme output (som er fuldført job). Du kan referere til output manifestfilen til at finde ud af, hvilke input filer er mislykkedes, efter kolonne fejlværdier. Filer kan ikke oprettes for input-filer, der mislykkedes, resulterende AIB, SAMISK, TTML, WebVTT og nøgleord.

### <a id="preset"></a>Opgave forudindstillet til Azure Media indekseringsprogram

Behandling fra Azure Media indekseringsprogram kan tilpasses ved at indsende en valgfri opgave forudindstillede sammen med opgaven.  Følgende beskriver formatet på denne konfiguration xml.

Navn | Kræver | Beskrivelse
----|----|---
__input__ | FALSK | Aktiver fil(er), du vil indeksere.</p><p>Azure Media indekseringsprogram understøtter følgende filformater: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Du kan angive navnet på den fil (s) i egenskaben **navn** eller en **liste over** elementet **input** (som vist nedenfor). Hvis du ikke angiver hvilken aktiv fil skal indekseres, valgte den primære fil. Hvis nogen primære aktiv fil er angivet, er den første fil i input aktivet indekseret.</p><p>Eksplicit angive filnavnet aktiv, skal du gøre:<br />`<input name="TestFile.wmv">`<br /><br />Du kan også indeksere flere aktiv filer på én gang (op til 10-filer). Sådan gør du:<br /><br /><ol class="ordered"><li><p>Oprette en tekstfil (manifestfilen) og give den et .lst filtypenavn. </p></li><li><p>Føje en liste over alle aktiv filnavnene i din Skriv aktiv til denne manifestfilen. </p></li><li><p>Føje (upload) thanifest fil til aktivet.  </p></li><li><p>Angiv navnet på manifestfilen i det input liste-attributten.<br />`<input list="input.lst">`</li></ol><br /><br />Bemærk: Hvis du føjer mere end 10 filer til manifestfilen, mislykkes indeksering jobbet med fejlkode 2006.
__metadata__ | FALSK | Metadata for den angivne aktiv eller filer, der bruges til tilpasning af ordliste.  Nyttig til at forberede indekseringsprogram at genkende de ikke-standard ordliste ord som stort.forbogstav navneord.<br />`<metadata key="..." value="..."/>` <br /><br />Du kan angive __værdier__ for foruddefinerede __taster__. De følgende nøgler understøttes i øjeblikket:<br /><br />"Titel" og "beskrivelse" - bruges til at tilpasse sproget for ordliste tilpasning model til dit arbejde og forbedre talegenkendelsen.  Værdierne podes søgninger på internettet for at finde konteksten relevante tekstdokumenter ved hjælp af indholdet til at øge den interne ordbog for varigheden af opgaven indeksering.<br />`<metadata key="title" value="[Title of the media file]" />`<br />`<metadata key="description" value="[Description of the media file] />"`
__Funktioner__ <br /><br /> Tilføjet i version 1.2. I øjeblikket, er den eneste understøttede facilitet talegenkendelse ("ASR").| FALSK | Funktionen talegenkendelse har tasterne følgende indstillinger:<table><tr><th><p>Nøgle</p></th>     <th><p>Beskrivelse</p></th><th><p>Eksempel på værdi</p></th></tr><tr><td><p>Sprog</p></td><td><p>Naturligt sprog til genkendes i multimediefilen.</p></td><td><p>Engelsk, spansk</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>en adskilt af semikolon liste over de ønskede billedtekst outputformater (hvis relevant)</p></td><td><p>ttml; Samisk; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Et boolesk flag, der angiver om, hvorvidt en AIB-fil er påkrævet (til brug med SQL Server og kunden indekseringsprogram IFilter).  Se <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Brug af AIB filer med Azure Media indekseringsprogram og SQL Server</a>kan finde flere oplysninger.</p></td><td><p>SAND. FALSK</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Et boolesk flag, der angiver hvorvidt en nøgleord XML-fil er påkrævet.</p></td><td><p>SAND. FALSK. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Et boolesk flag, der angiver hvorvidt tvinge fuld billedtekster (uanset konfidensniveau).  </p><p>Standard er falsk, i hvilket tilfælde ord og sætninger, der har en mindre end 50% konfidensniveau er udeladt i de endelige billedtekst output og erstattet af ellipser ("...").  På ellipserne er nyttige til billedtekst kvalitetskontrol og revision.</p></td><td><p>SAND. FALSK. </p></td></tr></table>

### <a id="error_codes"></a>Fejlkoder

Hvis det er en fejl, Azure Media indekseringsprogram skal rapportere tilbage et af følgende fejlkoder:

Kode | Navn | Mulige årsager
-----|------|------------------
2000 | Ugyldig konfiguration | Ugyldig konfiguration
2001 | Ugyldige input Aktiver | Manglende input Aktiver eller tom aktiv.
2002 | Ugyldigt manifest | Manifest er tom eller Manifestet indeholder ugyldige elementer.
2003 | Kunne ikke hentes mediefil | Ugyldig URL-adresse i manifestfilen.
2004 | Ikke-understøttede protokol | Protokol af medier URL-adressen er ikke understøttet.
2005 | Ikke-understøttet filtype | Indtast medier filtype understøttes ikke.
2006 | For mange input filer | Der er mere end 10 filer i input manifestet.
3000 | Det lykkedes ikke at afkode mediefil | Ikke-understøttede media-codec <br/>eller<br/> Beskadigede mediefil <br/>eller<br/> Ingen lydstreamet i input medier.
4000 | Batchen indeksering lykkedes delvist | Nogle af input mediefilerne kunne indekseres. Du kan finde yderligere oplysninger finder <a href="#output_files">outputfiler</a>.
andre | Interne fejl | Kontakt supportteamet. indexer@microsoft.com


## <a id="supported_languages"></a>Understøttede sprog

Engelsk og spansk sprog understøttes i øjeblikket. Du kan finde yderligere oplysninger finder [version 1.2 release blogindlæg](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Relaterede links

[Azure Media Services Analytics oversigt](media-services-analytics-overview.md)

[Brug af AIB filer med Azure Media indekseringsprogram og SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indeksering mediefiler med Azure Media indekseringsprogram 2 Preview](media-services-process-content-with-indexer2.md)
