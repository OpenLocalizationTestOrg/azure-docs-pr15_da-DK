<properties
    pageTitle="Hyperlapse medier filer med Azure Media Hyperlapse | Microsoft Azure"
    description="Azure Media Hyperlapse opretter jævn tid udløb videoer fra første person eller handling kamera indhold. Dette emne viser, hvordan du bruger Media indekseringsprogram."
    services="media-services"
    documentationCenter=""
    authors="asolanki"
    manager="johndeu"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/19/2016"  
    ms.author="adsolank"/>


# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Hyperlapse medier filer med Azure Media Hyperlapse

Azure Media Hyperlapse er en medier Processor (MP), der opretter jævn tid udløb videoer fra første person eller handling kamera indhold.  Sidestillet skybaserede [Microsoft Research computeren Hyperlapse Pro](http://aka.ms/hyperlapse)og telefonbaseret Hyperlapse Mobile Microsoft Hyperlapse til Azure Media Services anvender massive skalaen på behandling af medier Azure Media Services platformen vandret skalere og parallelize flere Hyperlapse behandling.

>[AZURE.IMPORTANT]Microsoft Hyperlapse er udviklet til at fungere bedst på førsteperson indhold med et glidende kamera.  Selvom stadig kamera optagelser kan stadig virker, kan den ydeevne og kvaliteten af Azure Media Hyperlapse medier Processor sikres for andre typer af indhold.  Se den [indledende blogindlæg](http://aka.ms/azurehyperlapseblog) fra offentlige eksempel for at få mere at vide om Microsoft Hyperlapse Azure Media Services, og se nogle eksempel videoer.

En Azure Media Hyperlapse-job benytter som input en MP4, MOV eller WMV aktiv fil sammen med en konfigurationsfil, der angiver, hvilke billeder i videoen skal være tid udløb og til hvilke hastighed (fx første 10.000 rammer på 2 x).  Output er en konstante og tid udløb gengivelse af input video.

Du kan finde de seneste opdateringer i Azure Media Hyperlapse [Media Services blogge](https://azure.microsoft.com/blog/topics/media-services/).

## <a name="hyperlapse-an-asset"></a>Hyperlapse et aktiv

Du skal først at overføre filen ønskede input til Azure Media Services.  Hvis du vil vide mere om koncepter med overførsel af og administrere indhold, du læse [Indholdsstyring artikel](media-services-portal-vod-get-started.md).

###  <a id="configuration"></a>Konfiguration forudindstillet for Hyperlapse

Når dit indhold er i kontoen Media Services, skal du oprette din konfiguration forudindstillet.  I følgende tabel beskrives de brugerdefinerede felter:

 Felt | Beskrivelse
-------|-------------
StartFrame|Den ramme, som Microsoft Hyperlapse behandling skal begynde.
NumFrames|Antallet af rammer til at behandle
Hastighed|Faktor, som at øge hastigheden af input videoen.

Følgende er et eksempel på en importsætninger, så konfigurationsfil i XML- og JSON:

**XML-format:**

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>

**JSON forudindstillet:**

    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }

###  <a id="sample_code"></a>Microsoft Hyperlapse med AMS .NET SDK

Følgende metode overfører en mediefil som et aktiv og opretter et job med Azure Media Hyperlapse medier Processor.

> [AZURE.NOTE] Du bør allerede har en CloudMediaContext i området med navnet "kontekst" for denne kode til at arbejde.  Hvis du vil vide mere om dette, skal du læse [Indholdsstyring artikel](media-services-dotnet-get-started.md).

> [AZURE.NOTE] Strengargumentet "hyperConfig" forventes at være en importsætninger, så konfiguration forudindstillede i JSON eller XML-, som beskrevet ovenfor.

statisk boolesk RunHyperlapseJob (streng som input, streng output, streng hyperConfig) {/ / oprette aktiv med input fil IAsset aktiv = kontekst. Aktiver. CreateAssetAndUploadSingleFile (input, "Min Hyperlapse Input", AssetCreationOptions.None);

tage forekomster af Azure Media Hyperlapse MP IMediaProcessor mp = kontekst. MediaProcessors. GetLatestMediaProcessorByName ("Azure Media Hyperlapse");

oprette Job med Hyperlapse opgave IJob job = kontekst. Job. Oprette (String.Format ("Hyperlapse {0}", input));

Hvis (String.IsNullOrEmpty(hyperConfig)) {/ / config må ikke være tomme returnerede falsk;}

hyperConfig = File.ReadAllText(hyperConfig);

ITask hyperlapseTask = job. Tasks.AddNew ("Hyperlapse opgave", mp, hyperConfig, TaskOptions.None) hyperlapseTask.InputAssets.Add(asset); hyperlapseTask.OutputAssets.AddNew ("Hyperlapse output", AssetCreationOptions.None);


job. Submit();

Oprette fremdrift udskrivning og forespørgsler opgaver opgave progressPrintTask = nye Task(() = > {}

IJob jobQuery = null; gøre {varians progressContext = kontekst; jobQuery = progressContext.Jobs. Hvor (j = > j.Id == job. Id). First(); Console.WriteLine (streng. Format ("{0} \t {1} \t {2}", DateTime.Now, jobQuery.State, jobQuery.Tasks[0]. Udvikling)) Thread.Sleep(10000); } mens (jobQuery.State! = JobState.Finished & & jobQuery.State! = JobState.Error & & jobQuery.State! = JobState.Canceled); }); progressPrintTask.Start();

            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                                error.Code,
                                                error.Message));  
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }

### <a id="file_types"></a>Understøttede filtyper

- MP4
- MOV
- WMV



##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-links"></a>Relaterede links

[Azure Media Services Analytics oversigt](media-services-analytics-overview.md)

[Azure Media Analytics demoer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
