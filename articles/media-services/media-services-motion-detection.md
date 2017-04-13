<properties
    pageTitle="Registrere bevægelser med Azure Media Analytics | Microsoft Azure"
    description="Azure Media Bevægelsesdetektor medier processor (MP) gør det muligt at identificere effektivt sektioner af interesse i en anden måde lange og uneventful video."
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
    ms.date="10/10/2016"  
    ms.author="milanga;juliako;"/>
 
# <a name="detect-motions-with-azure-media-analytics"></a>Registrere bevægelser med Azure Media Analytics

##<a name="overview"></a>Oversigt

**Azure Media Bevægelsesdetektor** medier processor (MP) gør det muligt at identificere effektivt sektioner af interesse i en anden måde lange og uneventful video. Bevægelsessti registrering kan bruges på statisk kamera optagelser til at identificere sektioner af videoen, hvor bevægelsessti opstår. Den genererer en JSON-fil, der indeholder en metadata med tidsstempler og den omgivende område, hvor hændelsen indtraf.

Rettet mod sikkerhed video feeds, er denne teknologi i stand til at kategorisere bevægelse i relevante begivenheder og falsk positive såsom skygger og lyssætning ændringer. Her kan du oprette sikkerhedsadvarsler fra kamera feeds uden plejer med uendelig irrelevante begivenheder, at bruge samtidig kan udtrække tid af interesse fra meget lang overvågning videoer.

**Azure Media Bevægelsesdetektor** MP er i øjeblikket i Vis udskrift.

Dette emne giver oplysninger om **Azure Media Bevægelsesdetektor** og viser, hvordan du kan bruge det sammen med Media Services SDK til .NET


##<a name="motion-detector-input-files"></a>Bevægelsessti registrering ressourcer input filer

Videofiler. I øjeblikket, i følgende filformater understøttes: MP4, MOV og WMV.

##<a name="task-configuration-preset"></a>Konfiguration af opgave (forudindstillet)

Når du opretter en opgave med **Azure Media Bevægelsesdetektor**, skal du angive en konfiguration forudindstillet. 

###<a name="parameters"></a>Parametre

Du kan bruge følgende parametre:

Navn|Indstillinger|Beskrivelse|Standard
---|---|---|---
sensitivityLevel|Streng: 'lav', 'Mellemhøj',' '|Angiver følsomheden på hvilke bevægelser rapporteres. Justere her for at justere mængde forkerte forekomster.|'medium'
frameSamplingValue|Positivt heltal|Angiver, hvor ofte på hvilken algoritme kører. 1 er lig med hver ramme, 2 betyder, at hver 2. ramme osv.|1
detectLightChange|Boolesk: 'true', 'false'|Angiver, om der rapporteres light ændringer i resultaterne|'False'
mergeTimeThreshold|X'er og klokkeslæt: Hh:mm:ss<br/>Eksempel: 00:00:03|Angiver en tidsramme mellem bevægelsessti begivenheder, hvor 2 begivenheder kan kombineres og rapporteres som 1.|00:00:00
detectionZones|En matrix med registrering zoner:<br/>-Registrering Zone er en matrix med 3 eller flere punkter<br/>-Punkt er x og y koordinere fra 0 til 1.|I denne artikel beskrives på listen over sammen registrering områder, der skal bruges.<br/>Resultater rapporteres med zonerne som et ID med det første billede 'id', der: 0|Enkelt zone som dækker hele rammen.

###<a name="json-example"></a>JSON eksempel

    
    {
      'version': '1.0',
      'options': {
        'sensitivityLevel': 'medium',
        'frameSamplingValue': 1,
        'detectLightChange': 'False',
        "mergeTimeThreshold":
        '00:00:02',
        'detectionZones': [
          [
            {'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}
           ],
          [
            {'x': 0.3, 'y': 0.3},
            {'x': 0.55, 'y': 0.3},
            {'x': 0.8, 'y': 0.3},
            {'x': 0.8, 'y': 0.55},
            {'x': 0.8, 'y': 0.8},
            {'x': 0.55, 'y': 0.8},
            {'x': 0.3, 'y': 0.8},
            {'x': 0.3, 'y': 0.55}
          ]
        ]
      }
    }


##<a name="motion-detector-output-files"></a>Bevægelsessti registrering ressourcer outputfiler

Et job til registrering af bevægelsessti vil returnere en JSON-fil i output aktivet som beskriver de bevægelsessti vigtige beskeder, og deres kategorier i videoen. Filen skal indeholde oplysninger om tid og varigheden af bevægelse, der er registreret i videoen.

Bevægelsessti registrering ressourcer API giver indikatorer, når der er objekter i bevægelse i en fast baggrund video (f.eks. en overvågning video). Den Bevægelsesdetektor uddannelse at reducere falsk alarmer, som lyssætning og skygge ændringer. Begrænsninger i algoritmerne, der omfatter NAT. syn videoer, halvgennemsigtigt objekter og små objekter.

###<a id="output_elements"></a>Elementer i JSON outputfilen

>[AZURE.NOTE]Output JSON-formatet er blevet ændret i den nyeste version og kan repræsentere en seneste ændring for nogle kunder.

I følgende tabel beskrives elementer i JSON outputfilen.

Element|Beskrivelse
---|---
Version|Dette henviser til versionen af API'EN Video. Den aktuelle version er 2.
Tidsskala|"Akser" sekundet af videoen.
Forskydning|Tidsforskydning for tidsstempler i "akser". I version 1.0 af Video API'er være dette altid 0. I fremtidige scenarier vi understøtter denne værdi kan blive ændret.
En billedhastighed|Rammer sekundet af videoen.
Bredde, højde|Refererer til bredden og højden på videoen i pixel.
Start|Start tidsstemplet i "akser".
Varighed|Længden af begivenheden, i "akser".
Interval|Intervallet for hver post i begivenheden, i "akser".
Begivenheder|Hver begivenhed fragment indeholder den bevægelse, der er registreret i varigheden.
Type|I den aktuelle version er det altid '2' for generisk bevægelse. Denne etiket giver Video API'er fleksibilitet til at kategorisere bevægelsessti i fremtidige versioner.
RegionID|Som beskrevet ovenfor, vil dette altid være 0 i denne version. Denne etiket giver Video API mulighed for at finde bevægelse i forskellige områder i fremtidige versioner.
Områder|Refererer til området i videoen, hvor du interesserer bevægelse. <br/><br/>-"-id" repræsenterer området område-i denne version er der kun er én, ID 0. <br/>-"type" repræsenterer udformningen af det område, der interesserer dig for bevægelse. I øjeblikket understøttes "rektangel" og "polygon".<br/> Hvis du har angivet "rektangel", har området dimensioner i X, Y, bredde og højde. X og Y-koordinaterne repræsenterer øverste venstre XY-koordinaterne for området i et standardiseret skala på 0,0 til 1,0. Bredden og højden repræsenterer størrelsen af området i et standardiseret skala på 0,0 til 1,0. I den aktuelle version fast X, Y, bredde og højde altid 0, 0 og 1, 1. <br/>Hvis du har angivet "polygon", har området dimensioner i punkter. <br/>
Fragmenter|Metadataene er chunked op til forskellige målgrupper kaldet fragmenter. Hver fragment indeholder en start, varighed, interval tal og hændelser. Et fragment med ingen hændelser betyder, at ingen bevægelsessti blev fundet under den pågældende starttidspunkt og varighed.
Kantede parenteser]|Hver parentes repræsenterer et interval i begivenheden. Tomme kantede parenteser for dette interval betyder, at ingen bevægelsessti blev registreret.
placeringer|Denne nye post under hændelser, der viser den placering, hvor bevægelse opstod. Dette er mere specifikke end zonerne registrering.

Følgende er en JSON output-eksempel

    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
    
    …
##<a name="limitations"></a>Begrænsninger

- Understøttede input videoformater omfatter MP4, MOV og WMV.
- Bevægelsessti registrering er optimeret til stationære baggrund videoer. Algoritmen fokuserer på at reducere falsk alarmer, såsom lyssætning ændringer og skygger.
- Nogle bevægelsessti måske ikke findes på grund af tekniske udfordringer FX NAT. syn videoer, halvgennemsigtigt og small objekter.


## <a name="sample-code"></a>Eksempel på kode

Følgende program viser Sådan:

1. Oprette et aktiv, og Overfør en mediefil til aktivet.
1. Opretter et job med en video bevægelsessti registrering opgave, der er baseret på en konfigurationsfil, der indeholder de følgende json forudindstillet. 
                    
        {
          'Version': '1.0',
          'Options': {
            'SensitivityLevel': 'medium',
            'FrameSamplingValue': 1,
            'DetectLightChange': 'False',
            "MergeTimeThreshold":
            '00:00:02',
            'DetectionZones': [
              [
                {'x': 0, 'y': 0},
                {'x': 0.5, 'y': 0},
                {'x': 0, 'y': 1}
               ],
              [
                {'x': 0.3, 'y': 0.3},
                {'x': 0.55, 'y': 0.3},
                {'x': 0.8, 'y': 0.3},
                {'x': 0.8, 'y': 0.55},
                {'x': 0.8, 'y': 0.8},
                {'x': 0.55, 'y': 0.8},
                {'x': 0.3, 'y': 0.8},
                {'x': 0.3, 'y': 0.55}
              ]
            ]
          }
        }

1. Henter output JSON-filerne. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace VideoMotionDetection
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
        
                    // Run the VideoMotionDetection job.
                    var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\VideoMotionDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
                }
        
                static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Video Motion Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Video Motion Detection Job");
        
                    // Get a reference to Azure Media Motion Detector.
                    string MediaProcessorName = "Azure Media Motion Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
        
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
[Azure Media Services Bevægelsesdetektor blog](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure Media Services Analytics oversigt](media-services-analytics-overview.md)

[Azure Media Analytics demoer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
