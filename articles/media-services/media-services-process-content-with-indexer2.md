<properties
    pageTitle="Indeksering mediefiler med Azure Media indekseringsprogram 2 Preview | Microsoft Azure"
    description="Azure Media indekseringsprogram gør det muligt at gøre indholdet af dine mediefiler kan søges efter og generere en hele teksten afskrift til lukket undertekster og nøgleord. Dette emne viser, hvordan du bruger Media indekseringsprogram 2 Preview."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016" 
    ms.author="adsolank;juliako;"/>


# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indeksering mediefiler med Azure Media indekseringsprogram 2 Preview

##<a name="overview"></a>Oversigt

**Azure Media indekseringsprogram 2 Preview** medier processor (MP) gør det muligt at foretage mediefiler og indhold søgbare samt generere lukket undertekster numre. **Azure Media indekseringsprogram 2 Preview** sammenlignet med den tidligere version af [Azure Media indekseringsprogram](media-services-index-content.md), udfører hurtigere indeksering og giver mere omfattende sprogunderstøttelse. Understøttede sprog omfatter engelsk, spansk, fransk, tysk, italiensk, kinesisk, portugisisk og arabisk.

**Azure Media indekseringsprogram 2 Preview** MP er i øjeblikket i Vis udskrift.

Dette emne viser, hvordan du opretter indekseringsjob mislykkedes med **Azure Media indekseringsprogram 2 Preview**.

>[AZURE.NOTE]Overvej følgende gælder:
>
>Indekseringsprogram 2 understøttes ikke i Azure Kina og Azure Government.
>
>Eksempel er begrænset til behandling ~ 10 minutter, men er gratis til alle kunder.
>
>Når indeksering indhold, skal du sørge for at bruge mediefiler, der har klart tale (uden baggrundsmusik, støj, effekter eller mikrofon hvislen). Nogle eksempler på relevante indhold: optagede møder, forelæsninger eller præsentationer. Følgende indhold muligvis ikke passer til indeksering: film, tv-programmer, alt med blandede lyd- og lydeffekter, dårligt optaget indhold med baggrundsstøj (hvislen).


Dette emne indeholder detaljer om **Azure Media indekseringsprogram 2 Preview** og viser, hvordan du kan bruge det sammen med Media Services SDK til .NET

##<a name="input-and-output-files"></a>Input- og filer

###<a name="input-files"></a>Input-filer

Lyd- eller videofiler

###<a name="output-files"></a>Outputfiler

Et indeksering job kan generere undertekster filer i følgende formater:  

- **samisk**
- **TTML**
- **WebVTT**

Lukket billedtekst (CC) filer i disse formater kan bruges til at foretage lyd- og videofiler tilgængelige for personer med nedsat handicap.

##<a name="task-configuration-preset"></a>Konfiguration af opgave (forudindstillet)

Når du opretter en indeksering opgave med **Azure Media indekseringsprogram 2 Preview**, skal du angive en konfiguration forudindstillet.

Følgende JSON angiver tilgængelige parametre.

    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }

##<a name="supported-languages"></a>Understøttede sprog  

Azure Media indekseringsprogram 2 Preview understøtter tale til tekst til følgende sprog (når du angiver navnet på sproget i opgavekonfigurationen af skal brug 4-tegnkode i parenteser, som vist nedenfor):

- Engelsk [EnUs]
- Spansk [EsEs]
- Kinesisk [ZhCn]
- Fransk [FrFr]
- Tysk [DeDe]
- Italiensk [ItIt]
- Portugisisk [PtBr]
- Arabisk (egyptiske) [ArEg]


## <a name="sample-code"></a>Eksempel på kode

Følgende program viser Sådan:

1. Oprette et aktiv, og Overfør en mediefil til aktivet.
1. Opretter et job med en indeksering opgave baseret på en konfigurationsfil, der indeholder de følgende json forudindstillet.
            
        {
          "version":"1.0",
          "Features":
            [
               {
               "Options": {
                    "Formats":["WebVtt","ttml"],
                    "Language":"enUs",
                    "Type":"RecoOptions"
               },
               "Type":"SpReco"
            }]
        }

1. Henter outputfiler til. 
    
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace IndexContent
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
        
                    // Run indexing job.
                    var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                                @"C:\supportFiles\Indexer\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
                }
        
                static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Indexing Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Indexing Job");
        
                    // Get a reference to Azure Media Indexer 2 Preview.
                    string MediaProcessorName = "Azure Media Indexer 2 Preview";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
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


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Relaterede links

[Azure Media Services Analytics oversigt](media-services-analytics-overview.md)

[Azure Media Analytics demoer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)