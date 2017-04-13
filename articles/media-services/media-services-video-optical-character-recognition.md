<properties
    pageTitle="Brug Azure Media Analytics til at konvertere tekst-indhold i videofiler til digital tekst | Microsoft Azure"
    description="Azure Media Analytics OCR (optisk tegngenkendelse) gør det muligt at konvertere tekst-indhold i videofiler til kan redigeres, søgbar digitale tekst.  Dette kan du automatisere udtrækning af sigende metadata fra det video signal dine medier."
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
    ms.author="juliako"/>
 
#<a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Brug Azure Media Analytics til at konvertere tekst-indhold i videofiler til digital tekst 

##<a name="overview"></a>Oversigt

Hvis du har brug for til at udtrække tekst-indhold fra dine videofiler og generere en digital tekst kan redigeres, kan søges efter, skal du bruge Azure Media Analytics OCR (optisk tegngenkendelse). Denne Azure Media Processor registrerer tekst-indhold i dine videofiler og danner tekstfiler til brug. OCR gør det muligt at automatisere udtrækning af sigende metadata fra det video signal dine medier.

Når den bruges sammen med en søgemaskine, kan du nemt indeksere dine medier af tekst og forbedre synlighed af dit indhold. Dette er meget nyttig i meget tekstdata video, som en videooptagelse eller et skærmbillede af et diasshow. Azure OCR medier Processor er udviklet til digital tekst.

**Azure Media OCR** medier processor er i øjeblikket i Vis udskrift.

Dette emne giver oplysninger om **Azure Media OCR** og viser, hvordan du kan bruge det sammen med Media Services SDK til .NET. Yderligere oplysninger og eksempler under [denne blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

##<a name="ocr-input-files"></a>OCR input filer

Videofiler. I øjeblikket, i følgende filformater understøttes: MP4, MOV og WMV.

##<a name="task-configuration"></a>Konfiguration af opgave 

Konfiguration af opgave (forudindstillet). Når du opretter en opgave med **Azure Media OCR**, skal du angive en forudindstillet ved hjælp af JSON eller XML-konfiguration. 

###<a name="attribute-descriptions"></a>Attributten beskrivelser

Attributnavn|Beskrivelse
---|---
Sprog|(valgfrit) beskrives sproget for tekst der skal søges efter. Et af følgende: automatisk genkendelse af (standard), arabisk, ChineseSimplified, ChineseTraditional, tjekkisk dansk, nederlandsk, engelsk, finsk, fransk, tysk, græsk, ungarsk, italiensk, japansk, koreansk, norsk, polsk, portugisisk, rumænsk, russisk, SerbianCyrillic, SerbianLatin, slovakisk, spansk, svensk, tyrkisk.
TextOrientation|(valgfrit) beskrives retningen af tekst der skal søges efter.  "Venstre" betyder, at øverst del af alle bogstaver er henvist mod venstre.  Standardteksten (f.eks., som kan findes i en bog) kan kaldes "Op" rundt.  Et af følgende: automatisk genkendelse af (standard), op, højre, ned, til venstre.
TimeInterval|(valgfrit) beskrives udvalg rente.  Standard er 1/2 i sekundet.<br/>JSON format – HH:mm:ss. SSS (standard 00:00:00.500)<br/>XML-format – W3C XSD varighed primitiv (standard PT0.5)
DetectRegions|(valgfrit) En matrix med DetectRegion objekter, der angiver områder i rammen med videoen, til at registrere tekst.<br/>Et DetectRegion objekt består af følgende fire heltalsværdier:<br/>Venstre-pixel fra venstre margen<br/>Top-pixel fra topmargen<br/>Bredde-bredden af området i pixel<br/>Højde-højden på området i pixel

####<a name="json-preset-example"></a>JSON forudindstillede eksempel
        
    {
        'Version':'1.0', 
        'Options': 
        {
        'Language':'English', 
            'TimeInterval':'00:00:01.5',
            'DetectRegions': 
             [
                {'Left':'1','Top':'1','Width':'1','Height':'1'},
                {'Left':'2','Top':'2','Width':'2','Height':'2'}
             ],
            'TextOrientation':'Up'
        }
    }

####<a name="xml-preset-example"></a>XML-forudindstillede eksempel

    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>1</Left>
                   <Top>1</Top>
                   <Width>1</Width>
                   <Height>1</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>

##<a name="ocr-output-files"></a>OCR outputfiler

Output fra OCR medier processor er en JSON-fil.

###<a name="elements-of-the-output-json-file"></a>Elementer i JSON outputfilen

Video OCR output indeholder tid opdelt data på de tegn, der findes i din video.  Du kan bruge attributter som sprog eller retning til telefonop i præcis ord, du er interesseret i analyse. 

Output indeholder følgende attributter:

Element|Beskrivelse
---|---
Tidsskala|"akser" sekundet af videoen
Forskydning|tid forskydning for tidsstempler. I version 1.0 af Video API'er være dette altid 0.
En billedhastighed|Rammer sekundet af videoen
bredde|bredden af video i pixel
højde|højden på videoen i pixel
Fragmenter|matrix med tidsbaserede udsnit med video, hvor metadata chunked
Start|starttidspunktet for en fragment "akser"
varighed|længden af et fragment i "akser"
interval|interval af hver enkelt hændelse inden for det angivne fragment
begivenheder|matrix, der indeholder områder
område|objekt, der repræsenterer fundet ord eller sætninger
sprog|sproget i den tekst, der er registreret i et område
retning|retningen af den tekst, der er registreret i et område
streger|matrix af linjer med tekst, der er registreret i et område
tekst|den faktiske tekst

###<a name="json-output-example"></a>Eksempel på JSON output

I følgende eksempel output indeholder de generelle video oplysninger og flere video fragmenter. I hver video fragment, den indeholder hver region, der er registreret af OCR MP med sproget og dens tekstretning. Området indeholder også alle word linje i dette område med den linje tekst, den linje placering og alle oplysninger om word (word indhold, placering og konfidensinterval) i denne linje. Følgende er et eksempel, og jeg placerede nogle kommentarer i selve teksten.

    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }

## <a name="sample-code"></a>Eksempel på kode

Følgende program viser Sådan:

1. Oprette et aktiv, og Overfør en mediefil til aktivet.
1. Opretter et job med en OCR konfiguration/forudindstillet fil.
1. Henter output JSON-filerne. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace OCR
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
        
                    // Run the OCR job.
                    var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                                @"C:\supportFiles\OCR\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
                }
        
                static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My OCR Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My OCR Job");
        
                    // Get a reference to Azure Media OCR.
                    string MediaProcessorName = "Azure Media OCR";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My OCR Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);
        
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

##<a name="related-links"></a>Relaterede links

[Azure Media Services Analytics oversigt](media-services-analytics-overview.md)
