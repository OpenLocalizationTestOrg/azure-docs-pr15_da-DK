<properties
    pageTitle="Brug Azure Media Video miniaturer til at oprette en Video opsummering | Microsoft Azure"
    description="Video opsummering kan hjælpe dig med at oprette oversigter over lange videoer ved automatisk at vælge interessante kodestykker fra kilde videoen. Dette er nyttigt, når du vil give et hurtigt overblik over, hvad du kan forvente i en lang video."
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
    ms.date="09/26/2016"   
    ms.author="milanga;juliako;"/>

#<a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Brug Azure Media Video miniaturer til at oprette en Video opsummering
##<a name="overview"></a>Oversigt

**Azure Media Video miniaturer** medier processor (MP) gør det muligt at oprette en oversigt over en video, som egner sig til kunder, der kun vil have vist en oversigt over en lang video. For eksempel kunder måske at se en kort "konklusion video" når de Hold markøren over en miniature. Du kan bruge den MP effektive skærmbillede registrering og sammenkædning teknologi til at generere algorithmically en beskrivende subclip ved at tilpasse parametrene for **Azure Media Video miniaturer** via en konfiguration forudindstillet.  

**Azure Media Video miniature** MP er i øjeblikket i Vis udskrift.

Dette emne giver oplysninger om **Azure Media Video miniature** og viser, hvordan du kan bruge det sammen med Media Services SDK til .NET

##<a name="video-summary-example"></a>Eksempel på video oversigt 

Her er nogle eksempler på, hvad Azure Media Video miniaturer medier processor kan gøre:

###<a name="original-video"></a>Oprindelige video

[Oprindelige video](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

###<a name="video-thumbnail-result"></a>Video miniature resultat

[Video miniature resultat](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##<a name="task-configuration-preset"></a>Konfiguration af opgave (forudindstillet)

Når du opretter en video miniature opgave med **Azure Media Video miniaturer**, skal du angive en konfiguration forudindstillet. Eksemplet ovenfor miniature blev oprettet med følgende grundlæggende JSON konfiguration:

    {"version":"1.0"}

I øjeblikket, kan du ændre følgende parametre:

Parameteren|Beskrivelse
---|---
outputAudio|Angiver eller ej resulterende videoen indeholder lyd. <br/>Tilladte værdier er: True eller False. Standard er sand.
fadeInFadeOut|Angiver hvorvidt udtoning overgange bruges mellem miniaturebillederne separat bevægelse.  <br/>Tilladte værdier er: True eller False.  Standard er sand.
maxMotionThumbnailDurationInSecs|Heltal, der angiver, hvor lang tid en hel resulterende video skal være.  Standard, afhænger af oprindelige video varighed.


I følgende tabel beskrives standardvarigheden, når **maxMotionThumbnailInSecs** ikke bruges.

||||
---|---|---|---|---
Video varighed|d < 3 min|3 min < d < 15 min|15 min < d < 30 min| 30 min < d
Miniature varighed|15 sec (2-3 scener)| 30 sec (scener 3-5)|60 sec (5-10 scener)|90 sec (10-15 scener)


Følgende JSON angiver tilgængelige parametre.
    
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }

## <a name="sample-code"></a>Eksempel på kode

Følgende program viser Sådan:

1. Oprette et aktiv, og Overfør en mediefil til aktivet.
1. Opretter et job med en video miniature opgave ud fra en konfigurationsfil, der indeholder de følgende json forudindstillet. 
        
        {               
            "version": "1.0",
            "options": {
                "outputAudio": "true",
                "maxMotionThumbnailDurationInSecs": "30",
                "fadeInFadeOut": "false"
            }
        }

1. Henter outputfiler til. 

###<a name="net-code"></a>.NET kode
     
    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;
    
    namespace VideoSummarization
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
    

                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");
    
                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }
    
            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);
    
                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");
    
                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";
    
                var processor = GetLatestMediaProcessorByName(MediaProcessorName);
    
                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);
    
                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);
    
                // Specify the input asset.
                task.InputAssets.Add(asset);
    
                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);
    
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

###<a name="video-thumbnail-output"></a>Video-miniature output

[Video-miniature output](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Relaterede links

[Azure Media Services Analytics oversigt](media-services-analytics-overview.md)

[Azure Media Analytics demoer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)