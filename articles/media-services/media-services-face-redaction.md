<properties
    pageTitle="Forsiden redigering med Azure media analytics | Microsoft Azure"
    description="Dette emne beskrives, hvordan du kan redigere flader med Azure media analytics."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/12/2016"   
    ms.author="juliako;"/>
 
#<a name="face-redaction-with-azure-media-analytics"></a>Ansigt redigering med Azure media analytics

##<a name="overview"></a>Oversigt

**Azure Media Redactor** er en [Azure Media Analytics](media-services-analytics-overview.md) -medier processor (MP), der indeholder SVG ansigt redigering i skyen. Ansigt redigering gør det muligt at ændre din video for at kunne sløring flader udvalgte personer. Du overveje at bruge tjenesten ansigt redigering i offentlige sikkerhed og nyheder medier scenarier. Et par minutter af optagelser, der indeholder flere flader kan tage timer at Rediger manuelt, men med denne tjeneste ansigt redigering processen kræver blot nogle få enkle trin. Du kan finde yderligere oplysninger finder [denne](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Dette emne giver oplysninger om **Azure Media Redactor** og viser, hvordan du kan bruge det sammen med Media Services SDK til .NET.

**Azure Media Redactor** MP er i øjeblikket i Vis udskrift.

## <a name="face-redaction-modes"></a>Ansigt redigering tilstande

Facial redigering fungerer ved at opdage flader i hver ramme for video og registrere ansigt objektet begge fremad og bagud i gang, så den samme person kan sløres fra andre vinkler samt. Processen automatiseret redigering er meget komplekse og fungerer ikke altid vare 100% af ønsket afgang derfor medier Analytics giver dig med et par måder til at ændre det endelige resultat.

Ud over en fuldautomatisk tilstand er der en to-trins arbejdsproces, som giver mulighed for markeret/afinstallation-selection af fundet flader via en liste over id'er. Herudover bruger en metadatafil i JSON format for at gøre vilkårlig per ramme justeringer Administrationspanelet. Arbejdsprocessen er opdelt i **analysér** og **Redact** funktionsmåder. Du kan kombinere de to tilstande i en enkelt gang, der kører begge opgaver i ét job. Denne tilstand kaldes **kombineret**.

###<a name="combined-mode"></a>Kombinerede tilstand

Dette vil returnere en redigerede mp4 automatisk uden en manuel input.

Fase|Filnavn|Noter
---|---|---
Indtast aktiv|foo.bar|Video i WMV, MOV eller MP4-format
Skriv config|Konfiguration af jobbet forudindstillede|{'version':'1.0 ', 'indstillinger': {'mode': 'kombineret'}}
Output aktiv|foo_redacted.mp4|Video med sløring anvendt

####<a name="input-example"></a>Indtast eksempel:

[Se denne video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

####<a name="output-example"></a>Eksempel på output:

[Se denne video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

###<a name="analyze-mode"></a>Analysere tilstand

**Analysere** passet af arbejdsprocessen for to-trins tager en video input og giver en JSON fil af ansigt placeringer, og jpg-billeder af hver fundet ansigt.

Fase|Filnavn|Noter
---|---|----
Indtast aktiv|foo.bar|Video i WMV, MPV eller MP4-format
Skriv config|Konfiguration af jobbet forudindstillede|{'version':'1.0 ', 'indstillinger': {'mode': 'analysere'}}
Output aktiv|foo_annotations.JSON|Anmærkningsdata fra ansigt placeringer i JSON-formatet. Dette kan redigeres af brugeren for at ændre sløring afgrænser felterne. Se eksempel nedenfor.
Output aktiv|foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg]|En beskårne jpg af hver fundet ansigt, hvor antallet angiver labelId af flade

####<a name="output-example"></a>Eksempel på output:

    {
      "version": 1,
      "timescale": 50,
      "offset": 0,
      "framerate": 25.0,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 2,
          "interval": 2,
          "events": [
            [  
              {
                "id": 1,
                "x": 0.306415737,
                "y": 0.03199235,
                "width": 0.15357475,
                "height": 0.322126418
              },
              {
                "id": 2,
                "x": 0.5625317,
                "y": 0.0868245438,
                "width": 0.149155334,
                "height": 0.355517566
              }
            ]
          ]
        },

… afkortet


###<a name="redact-mode"></a>Rediger tilstand

Det andet gennemløb af arbejdsprocessen kræver et større antal input, der skal kombineres til en enkelt aktiv.

Dette omfatter en liste over id'er til sløring, den oprindelige video og anmærkninger JSON. Denne tilstand bruger anmærkningerne til at anvende sløring på input video.

Output fra analysér passet omfatter ikke den oprindelige video. Videoen skal overføres til input aktivet på opgaven Redact tilstand og valgt som den primære fil.

Fase|Filnavn|Noter
---|---|---
Indtast aktiv|foo.bar|Video i WMV, MPV eller MP4-format. Samme video som i trin 1.
Indtast aktiv|foo_annotations.JSON|anmærkninger metadatafil fra fase et, med valgfrit ændringer.
Indtast aktiv|foo_IDList.txt (valgfrit)|Valgfri ny linje adskilt liste over ansigt id'er til Rediger. Hvis tomt, slører dette alle flader.
Skriv config|Konfiguration af jobbet forudindstillede|{'version':'1.0 ', 'indstillinger': {'mode': 'Rediger'}}
Output aktiv|foo_redacted.mp4|Video med sløring anvendt baseret på anmærkninger

####<a name="example-output"></a>Eksempel på Output

Dette er output fra en IDList med ét ID, der er markeret.

[Se denne video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

##<a name="attribute-descriptions"></a>Attributten beskrivelser

Redigering Styringspanelet giver høj præcision ansigt placering registrering og registrering, der kan registrere op til 64 human flader i en videoramme. Frontal flader giver de bedste resultater, mens du sideflader og mindre flader (mindre end eller lig med 24 x 24 pixel), er udfordring.

De fundne og registrerede flader returneres med koordinater, der angiver placeringen af flader samt ansigt id-nummer, der angiver sporing af det enkelte. Ansigt id-numre er giver risiko for nulstille under omstændigheder, når den frontal ansigt mistet eller overlapper hinanden i tekstruden, hvilket resulterer i nogle få tildelt flere id'er enkeltpersoner.

Du kan finde detaljerede beskrivelser for attributterne, [registrerer ansigt og følelse med Azure Media Analytics](media-services-face-and-emotion-detection.md) emne.

## <a name="sample-code"></a>Eksempel på kode

Følgende program viser Sådan:

1. Oprette et aktiv, og Overfør en mediefil til aktivet.
1. Oprette et job med en ansigt redigering opgave, der er baseret på en konfigurationsfil, der indeholder de følgende json forudindstillet. 
                    
        {'version':'1.0', 'options': {'mode':'combined'}}

1. Downloade output JSON-filer. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceRedaction
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                static void Main(string[] args)
                {
        
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Run the FaceRedaction job.
                    var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                                                @"C:\supportFiles\FaceRedaction\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
                }
        
                static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Redaction Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Redaction Job");
        
                    // Get a reference to Azure Media Redactor.
                    string MediaProcessorName = "Azure Media Redactor";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Redaction Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);
        
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
                        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                        Console.WriteLine(string.Format("Error: {0}. {1}",
                                                        error.Code,
                                                        error.Message));
                        return null;
                    }
        
                    return job.OutputMediaAssets[0];
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
        
                static private void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
        
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished.");
                            Console.WriteLine();
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:
                            // Cast sender as a job.
                            IJob job = (IJob)sender;
                            // Display or log error details as needed.
                            // LogJobStop(job.Id);
                            break;
                        default:
                            break;
                    }
                }
        
            }
        }


##<a name="next-step"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Relaterede links

[Azure Media Services Analytics oversigt](media-services-analytics-overview.md)

[Azure Media Analytics demoer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
