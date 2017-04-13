<properties
    pageTitle="Registrere ansigt og følelse med Azure Media Analytics | Microsoft Azure"
    description="Dette emne beskrives, hvordan du kan registrere flader og følelser med Azure Media Analytics."
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
 
#<a name="detect-face-and-emotion-with-azure-media-analytics"></a>Registrere ansigt og følelse med Azure Media Analytics

##<a name="overview"></a>Oversigt

**Azure Media ansigt registrering ressourcer** medier processor (MP) gør det muligt at tælle, spore bevægelser og endda måle publikum deltage og reaktionsprodukter via ansigtsudtryk. Denne tjeneste indeholder to funktioner: 

- **Registrering af ansigt**

    Ansigt registrering finder og registrerer human flader i en video. Flere flader kan findes og efterfølgende spores, når de flyttes rundt, med de klokkeslæt og sted metadata, der returneres i en JSON-fil. Under registrering forsøge det at give et ensartet ID til den samme ansigt, mens personen, der bevæge dig rundt på skærmen, selvom de er blokeres eller forlade kortvarigt rammen.

    >[AZURE.NOTE]Denne services udfører ikke facial talegenkendelse. En person, der forlader rammen eller bliver blokeres længe får et nyt ID når de vender tilbage.

- **Følelser registrering**
    
    Følelser registrering er en valgfri komponent i ansigt registrering medier Processor, der returnerer analyse på flere følelsesmæssige attributter fra de flader registreres, herunder lykke, sadness, frygt, anger og meget mere. 

**Azure Media ansigt registrering ressourcer** MP er i øjeblikket i Vis udskrift.

Dette emne giver oplysninger om **Azure Media ansigt registrering ressourcer** og viser, hvordan du kan bruge det sammen med Media Services SDK til .NET.

##<a name="face-detector-input-files"></a>Forsiden registrering ressourcer input filer

Videofiler. I øjeblikket, i følgende filformater understøttes: MP4, MOV og WMV.

##<a name="face-detector-output-files"></a>Forsiden registrering ressourcer outputfiler

Ansigt registrering og sporing API giver høj præcision ansigt placering registrering og registrering, der kan registrere op til 64 human flader i en video. Frontal flader giver de bedste resultater, mens du sideflader og mindre flader (mindre end eller lig med 24 x 24 pixel) muligvis ikke som nøjagtige.

De fundne og registrerede flader returneres med koordinaterne (venstre, øverst, bredde og højde), der angiver placeringen af flader på billede i pixel, samt et ansigt id-nummer, der angiver sporing af det enkelte. Ansigt id-numre er giver risiko for nulstille under omstændigheder, når den frontal ansigt mistet eller overlapper hinanden i tekstruden, hvilket resulterer i nogle få tildelt flere id'er enkeltpersoner.

###<a id="output_elements"></a>Elementer i JSON outputfilen

Til ansigt registrering og sporing af handlingen, indeholder output resultatet metadata fra flader inden for den angivne fil i JSON-formatet.

Ansigt påvisning og registrering JSON omfatter følgende egenskaber:

Element|Beskrivelse
---|---
Version|Dette henviser til versionen af API'EN Video.
Tidsskala|"Akser" sekundet af videoen.
Forskydning|Dette er tidsforskydningen for tidsstempler. I version 1.0 af Video API'er være dette altid 0. I fremtidige scenarier vi understøtter denne værdi kan blive ændret.
En billedhastighed|Rammer sekundet af videoen.
Fragmenter|Metadataene er chunked op til forskellige målgrupper kaldet fragmenter. Hver fragment indeholder en start, varighed, interval tal og hændelser.
Start|Starttidspunktet for den første hændelse i 'aksemærker'.
Varighed|Længden af fragmentet i "akser".
Interval|Intervallet af hver enkelt hændelse post i fragment i "akser".
Begivenheder|Hver enkelt hændelse indeholder flader fundet og registreres i pågældende varighed. Det er en matrix med matrix af hændelser. Ydre matrixen repræsenterer et tidsinterval. Indre matrixen består af 0 eller flere begivenheder, der er foregået på dette tidspunkt. En tom parentes [] betyder, at ingen flader ikke blev registreret.
ID| ID for den flade, der er registreret. Dette nummer kan utilsigtet har ændret Hvis ansigt bliver øget. En bestemt person skal have samme-ID i hele den overordnede video, men dette kan sikres på grund af begrænsninger i registreringsalgoritmen (occlusion osv.)
X, Y|Det øverste venstre X og Y-koordinaterne for den flade afgrænser feltet i et standardiseret skala på 0,0 til 1,0. <br/>-X og Y er koordinaterne relative til liggende altid, så hvis du har en stående video (roteres eller spejlvendes, hvis det er iOS), du har transponere koordinaterne i overensstemmelse hermed.
Bredde, højde|Bredden og højden på ansigt afgrænser felt i en standardiseret skala på 0,0 til 1,0.
facesDetected|Dette findes i slutningen af JSON resultaterne og indeholder en oversigt over antallet af flader, algoritmen registreres under videoen. Fordi id'erne kan nulstille ved et uheld Hvis ansigt bliver øget (fx ansigt går uden for skærmen, ser ikke til stede), dette nummer kan ikke altid er lig med sand antallet af flader i videoen.

Ansigt registrering ressourcer bruger teknikker fragmentering (hvor metadataene kan deles op i tidsbaserede dele og kan du hente, hvad du skal kun) og segmentering (hvor hændelserne, der er delt op i tilfælde af, at de bliver for stor). Nogle enkle beregninger kan hjælpe dig med at transformere dataene. Eksempelvis hvis en begivenhed i gang med 6300 (akser) med en tidsskala 2997 (aksemærker/sec) og en billedhastighed af 29.97 derefter (rammer/sec):

- Start/tidsskala = 2.1 sekunder
- Sekunder x (en billedhastighed/tidsskala) = 63 rammer

Nedenfor vises et eksempel på uddrage JSON til en per rammeformat for ansigt registrering og registrering:
    
    var faceDetectionResultJsonString = operationResult.ProcessingResult;
    var faceDetecionTracking = 
         JsonConvert.DeserializeObject<FaceDetectionResult>(faceDetectionResultJsonString, settings);


##<a name="face-detection-input-and-output-example"></a>Forsiden registrering input og output-eksempel

###<a name="input-video"></a>Indtast video

[Indtast Video](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Konfiguration af opgave (forudindstillet)

Når du opretter en opgave med **Azure Media ansigt registrering ressourcer**, skal du angive en konfiguration forudindstillet. Den følgende konfiguration forudindstillet er lige til ansigt registrering.

    {"version":"1.0"}

###<a name="json-output"></a>JSON output

Følgende eksempel på JSON output blev afkortet.

    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],

        . . . 

##<a name="emotion-detection-input-and-output-example"></a>Følelser registrering input og output-eksempel


###<a name="input-video"></a>Indtast video

[Indtast Video](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Konfiguration af opgave (forudindstillet)

Når du opretter en opgave med **Azure Media ansigt registrering ressourcer**, skal du angive en konfiguration forudindstillet. Den følgende konfiguration forudindstillet angiver, at oprette JSON baseret på følelse registrering.
    
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


####<a name="attribute-descriptions"></a>Attributten beskrivelser

Attributnavn|Beskrivelse
---|---
Tilstand|Flader: Vises kun registrering <br/>AggregateEmotion: Returnerede gennemsnitlige følelse værdier for alle flader i ramme.
AggregateEmotionWindowMs|Brug Hvis AggregateEmotion tilstand er markeret. Angiver længden af video, der bruges til at producere hver samling resultat, i millisekunder.
AggregateEmotionIntervalMs|Brug Hvis AggregateEmotion tilstand er markeret. Angiver med hvilken frekvens til at producere aggregere resultater.

####<a name="aggregate-defaults"></a>Sammenlæg standarder

Under anbefales værdier for de samlede indstillinger for vindue og interval. AggregateEmotionWindowMs skal være længere end AggregateEmotionIntervalMs.

   |Standardindstillinger for (s)|Max(s)|Min(s)
---|---|---|---
AggregateEmotionWindowMs|0,5|2|0,25
AggregateEmotionIntervalMs|0,5|1|0,25

###<a name="json-output"></a>JSON output

JSON outputtet til samling følelse (afkortet):
 
    
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,


##<a name="limitations"></a>Begrænsninger

- Understøttede input videoformater omfatter MP4, MOV og WMV.
- Kan registreres ansigt størrelsesområde er 24 x 24 til 2048 x 2048 pixel. Flader uden dette område, der registreres ikke.
- For hver video er det maksimale antal flader returneres 64.
- Nogle flader måske ikke findes på grund af tekniske udfordringer FX meget store ansigt vinkler (hoved-frembyde), og store occlusion. Frontal og nær nøgne flader har de bedste resultater.


## <a name="sample-code"></a>Eksempel på kode

Følgende program viser Sådan:

1. Oprette et aktiv, og Overfør en mediefil til aktivet.
1. Opretter et job med en ansigt registrering opgave, der er baseret på en konfigurationsfil, der indeholder de følgende json forudindstillet. 
                    
        {
            "version": "1.0"
        }

1. Henter output JSON-filerne. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceDetection
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
        
                    // Run the FaceDetection job.
                    var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\FaceDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
                }
        
                static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Detection Job");
        
                    // Get a reference to Azure Media Face Detector.
                    string MediaProcessorName = "Azure Media Face Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);
        
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

[Azure Media Analytics demoer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)