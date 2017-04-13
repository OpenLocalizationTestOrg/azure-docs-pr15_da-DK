<properties 
    pageTitle="Sådan gør du direkte streaming bruge Azure Media Services til at oprette flere bithastighed streams med .NET | Microsoft Azure" 
    description="Dette selvstudium vejleder dig gennem trinnene til at oprette en kanal, der modtager en enkelt bithastighed live stream og koder til flere bithastighed stream ved hjælp af .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-net"></a>Sådan gør du direkte streaming bruge Azure Media Services til at oprette flere bithastighed streams med .NET

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST-API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

>[AZURE.NOTE]
> For at fuldføre dette selvstudium skal have du en Azure konto. Yderligere oplysninger finder du [Azure gratis prøveversion](/pricing/free-trial/?WT.mc_id=A261C142F).

##<a name="overview"></a>Oversigt

Dette selvstudium vejleder dig gennem trinnene til at oprette en **kanal** , der modtager en enkelt bithastighed live stream og koder til flere bithastighed stream.

Se [Live streaming ved hjælp af Azure Media Services for at oprette flere bithastighed streams](media-services-manage-live-encoder-enabled-channels.md)yderligere grundlæggende oplysninger, der er relateret til kanaler, der er aktiveret til direkte kodning.


##<a name="common-live-streaming-scenario"></a>Almindelige direkte Streaming scenarie

I følgende trin beskrives opgaver, der er involveret i oprettelse af almindelige direkte streaming programmer.

>[AZURE.NOTE] I øjeblikket, er den maksimale anbefalede varighed for en direkte begivenhed otte timer. Kontakt amslived på Microsoft.com, hvis du vil køre en kanal i længere tid.

1. Oprette forbindelse et videokamera til en computer. Start og konfigurere en lokal direkte koder, der kan udskrive en enkelt bithastighed stream i en af følgende protokoller: RTMP, bløde Streaming eller RTP (MPEG-TS). Se [Azure Media Services RTMP Support og Live kodere](http://go.microsoft.com/fwlink/?LinkId=532824)kan finde flere oplysninger.

Dette trin kan også foretages, når du har oprettet din kanal.

1. Oprette og starte en kanal.

1. Hent kanalen indtager URL-adresse.

Ingest URL-adresse bruges af live encoder til at sende strømmen til kanalen.

1. Hente URL-adressen med kanal preview.

Brug denne URL-adresse til at kontrollere, at din kanal korrekt modtager direkte strømmen.

2. Oprette et aktiv.
3. Hvis du vil for aktivet skal krypteres dynamisk under afspilning, skal du gøre følgende:
1. Oprette en indhold nøgle.
1. Konfigurere tasten indhold politik.
1. Konfigurere aktiv levering politik (der anvendes af dynamiske emballagen og dynamiske kryptering).
3. Oprette et program, og angive, at de aktiver, du har oprettet.
1. Publicere på Aktiver, der er knyttet til programmet ved at oprette en anmodet arbejdsproces locator.

Sørg for at have mindst én streaming reserveret enhed på streaming slutpunktet hvorfra du vil at streame indhold.

1. Starte programmet, når du er klar til at starte streaming og arkivering.
2. Du kan også direkte encoder kan have en angivelse af at starte en annonce. Annoncen indsættes i output strømmen.
1. Stoppe automatisk, hver gang du vil stoppe streaming og arkivering begivenheden.
1. Slette programmet (og du kan også slette aktivet).

## <a name="what-youll-learn"></a>Hvad du lærer

Dette emne viser, hvordan du udføre forskellige handlinger på kanaler og ved hjælp af Media Services .NET SDK-programmer. Da mange handlinger længerevarende .NET API'er, der administrerer længerevarende bruges operationer.

Emnet viser, hvordan du gøre følgende:

1. Oprette og starte en kanal. Længerevarende API'er bruges.
1. Få kanalerne indtager (Skriv) slutpunkt. Dette slutpunkt skal gives encoder, der kan sende en enkelt bithastighed live stream.
1. Få preview slutpunktet. Dette slutpunkt bruges til at få vist din stream.
1. Oprette et aktiv, der skal bruges til at gemme dit indhold. Aktiver levering politikker skal være konfigureret samt, som vist i dette eksempel.
1. Oprette et program, og angive, at det aktiv, der tidligere blev oprettet. Starte programmet. Længerevarende API'er bruges.
1. Oprette en locator for aktivet, så indholdet bliver offentliggjort og kan sendes til dine kunder.
1. Vise og skjule slates. Starte og standse reklamer. Længerevarende API'er bruges.
1. Rydde op din kanal og alle de tilknyttede ressourcer.


##<a name="prerequisites"></a>Forudsætninger

Følgende er påkrævet til at fuldføre selvstudiet.

- For at fuldføre dette selvstudium skal have du en Azure konto.

Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](/pricing/free-trial/?WT.mc_id=A261C142F). Du får kredit, der kan bruges til at teste betalt Azure tjenester. Selvom kreditterne bruges, kan du beholde kontoen og bruge gratis Azure tjenester og funktioner, som funktionen Web Apps i Azure App Service.
- En Media Services-konto. Hvis du vil oprette en Media Services-konto, skal du se [Oprette konto](media-services-portal-create-account.md).
- Visual Studio 2010 SP1 (professionel, Premium, Ultimate eller Express) eller nyere versioner.
- Du skal bruge Media Services .NET SDK version 3.2.0.0 eller nyere.
- Et webcam og en koder, der kan sende en enkelt bithastighed live stream.

##<a name="considerations"></a>Overvejelser i forbindelse med

- I øjeblikket, er den maksimale anbefalede varighed for en direkte begivenhed otte timer. Kontakt amslived på Microsoft.com, hvis du vil køre en kanal i længere tid.
- Sørg for at have mindst én streaming reserveret enhed på streaming slutpunktet hvorfra du vil at streame indhold.

##<a name="download-sample"></a>Hente eksempel

Hente og køre en stikprøve fra [her](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/).


##<a name="set-up-for-development-with-media-services-sdk-for-net"></a>Konfigurere til udvikling med Media Services SDK til .NET

1. Oprette et console-program ved hjælp af Visual Studio.
1. Føje Media Services SDK til .NET til dit console-program ved hjælp af Media Services NuGet pakke.

##<a name="connect-to-media-services"></a>Oprette forbindelse til Media Services
Som en bedste fremgangsmåde, skal du bruge en app.config-filen til at gemme tasten Media Services navn og konto.

>[AZURE.NOTE]Gå til Azure-portalen for at finde navnet på og nøgle værdier, og vælg din konto. Vinduet indstillinger vises til højre. Vælg taster i vinduet indstillinger. At klikke på ikonet ud for hver tekstboks kopieres til Udklipsholder.

Føje sektionen appSettings til app.config-filen, og angiv værdier for din Media Services navn og konto kontonøgle.


    <?xml version="1.0"?>
    <configuration>
      <appSettings>
          <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
          <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
      </appSettings>
    </configuration>
     
    
##<a name="code-example"></a>Eksempel på en kode

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Net;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    
    namespace EncodeLiveStreamWithAmsClear
    {
        class Program
        {
            private const string ChannelName = "channel001";
            private const string AssetlName = "asset001";
            private const string ProgramlName = "program001";
    
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
    
                IChannel channel = CreateAndStartChannel();
    
                // The channel's input endpoint:
                string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Intest URL: {0}", ingestUrl);
    
    
                // Use the previewEndpoint to preview and verify 
                // that the input from the encoder is actually reaching the Channel. 
                string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Preview URL: {0}", previewEndpoint);
    
                // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
                // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
                // The thumbnail is exposed via the same end-point as the Channel Preview URL.
                string thumbnailUri = new UriBuilder
                {
                    Scheme = Uri.UriSchemeHttps,
                    Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
                    Path = "thumbnails/input.jpg"
                }.Uri.ToString();
    
                Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
    
                // Once you previewed your stream and verified that it is flowing into your Channel, 
                // you can create an event by creating an Asset, Program, and Streaming Locator. 
                IAsset asset = CreateAndConfigureAsset();
    
                IProgram program = CreateAndStartProgram(channel, asset);
    
                ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
    
                // You can use slates and ads only if the channel type is Standard.  
                StartStopAdsSlates(channel);
    
                // Once you are done streaming, clean up your resources.
                Cleanup(channel);
    
            }
    
            public static IChannel CreateAndStartChannel()
            {
                var channelInput = CreateChannelInput();
                var channePreview = CreateChannelPreview();
                var channelEncoding = CreateChannelEncoding();
    
    
                ChannelCreationOptions options = new ChannelCreationOptions
                {
                    EncodingType = ChannelEncodingType.Standard,
                    Name = ChannelName,
                    Input = channelInput,
                    Preview = channePreview,
                    Encoding = channelEncoding
                };
    
                Log("Creating channel");
                IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
                string channelId = TrackOperation(channelCreateOperation, "Channel create");
    
                IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
    
                Log("Starting channel");
                var channelStartOperation = channel.SendStartOperation();
                TrackOperation(channelStartOperation, "Channel start");
    
                return channel;
            }
    
            /// <summary>
            /// Create channel input, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelInput CreateChannelInput()
            {
                return new ChannelInput
                {
                    StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelInput001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel preview, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelPreview CreateChannelPreview()
            {
                return new ChannelPreview
                {
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelPreview001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel encoding, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelEncoding CreateChannelEncoding()
            {
                return new ChannelEncoding
                {
                    SystemPreset = "Default720p",
                    IgnoreCea708ClosedCaptions = false,
                    AdMarkerSource = AdMarkerSource.Api,
                    // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
                    AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
                };
            }
    
            /// <summary>
            /// Create an asset and configure asset delivery policies.
            /// </summary>
            /// <returns></returns>
            public static IAsset CreateAndConfigureAsset()
            {
                IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
    
                IAssetDeliveryPolicy policy =
                    _context.AssetDeliveryPolicies.Create("Clear Policy",
                    AssetDeliveryPolicyType.NoDynamicEncryption,
                    AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
    
                asset.DeliveryPolicies.Add(policy);
    
                return asset;
            }
    
            /// <summary>
            /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
            /// however each Program must have a unique name within your Media Services account.
            /// </summary>
            /// <param name="channel"></param>
            /// <param name="asset"></param>
            /// <returns></returns>
            public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
            {
                IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
                Log("Program created", program.Id);
    
                Log("Starting program");
                var programStartOperation = program.SendStartOperation();
                TrackOperation(programStartOperation, "Program start");
    
                return program;
            }
    
            /// <summary>
            /// Create locators in order to be able to publish and stream the video.
            /// </summary>
            /// <param name="asset"></param>
            /// <param name="ArchiveWindowLength"></param>
            /// <returns></returns>
            public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
            {
                // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                var locator = _context.Locators.CreateLocator
                    (
                        LocatorType.OnDemandOrigin,
                        asset,
                        _context.AccessPolicies.Create
                            (
                                "Live Stream Policy",
                                ArchiveWindowLength,
                                AccessPermissions.Read
                            )
                    );
    
                return locator;
            }
    
            /// <summary>
            /// Perform operations on slates.
            /// </summary>
            /// <param name="channel"></param>
            public static void StartStopAdsSlates(IChannel channel)
            {
                int cueId = new Random().Next(int.MaxValue);
                var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));
    
                Log("Creating asset");
                var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
                Log("Slate asset created", slateAsset.Id);
    
                Log("Uploading file");
                var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
                assetFile.Upload(path);
                assetFile.IsPrimary = true;
                assetFile.Update();
    
                Log("Showing slate");
                var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
                TrackOperation(showSlateOpeartion, "Show slate");
    
                Log("Hiding slate");
                var hideSlateOperation = channel.SendHideSlateOperation();
                TrackOperation(hideSlateOperation, "Hide slate");
    
                Log("Starting ad");
                var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
                TrackOperation(startAdOperation, "Start ad");
    
                Log("Ending ad");
                var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
                TrackOperation(endAdOperation, "End ad");
    
                Log("Deleting slate asset");
                slateAsset.Delete();
            }
    
            /// <summary>
            /// Clean up resources associated with the channel.
            /// </summary>
            /// <param name="channel"></param>
            public static void Cleanup(IChannel channel)
            {
                IAsset asset;
                if (channel != null)
                {
                    foreach (var program in channel.Programs)
                    {
                        asset = _context.Assets.Where(se => se.Id == program.AssetId)
                                                .FirstOrDefault();
    
                        Log("Stopping program");
                        var programStopOperation = program.SendStopOperation();
                        TrackOperation(programStopOperation, "Program stop");
    
                        program.Delete();
    
                        if (asset != null)
                        {
                            Log("Deleting locators");
                            foreach (var l in asset.Locators)
                                l.Delete();
    
                            Log("Deleting asset");
                            asset.Delete();
                        }
                    }
    
                    Log("Stopping channel");
                    var channelStopOperation = channel.SendStopOperation();
                    TrackOperation(channelStopOperation, "Channel stop");
    
                    Log("Deleting channel");
                    var channelDeleteOperation = channel.SendDeleteOperation();
                    TrackOperation(channelDeleteOperation, "Channel delete");
                }
            }
    
    
            /// <summary>
            /// Track long running operations.
            /// </summary>
            /// <param name="operation"></param>
            /// <param name="description"></param>
            /// <returns></returns>
            public static string TrackOperation(IOperation operation, string description)
            {
                string entityId = null;
                bool isCompleted = false;
    
                Log("starting to track ", null, operation.Id);
                while (isCompleted == false)
                {
                    operation = _context.Operations.GetOperation(operation.Id);
                    isCompleted = IsCompleted(operation, out entityId);
                    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
                }
                // If we got here, the operation succeeded.
                Log(description + " in completed", operation.TargetEntityId, operation.Id);
    
                return entityId;
            }
    
            /// <summary> 
            /// Checks if the operation has been completed. 
            /// If the operation succeeded, the created entity Id is returned in the out parameter.
            /// </summary> 
            /// <param name="operationId">The operation Id.</param> 
            /// <param name="channel">
            /// If the operation succeeded, 
            /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
            /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
            private static bool IsCompleted(IOperation operation, out string entityId)
            {
    
                bool completed = false;
    
                entityId = null;
    
                switch (operation.State)
                {
                    case OperationState.Failed:
                        // Handle the failure. 
                        // For example, throw an exception. 
                        // Use the following information in the exception: operationId, operation.ErrorMessage.
                        Log("operation failed", operation.TargetEntityId, operation.Id);
                        break;
                    case OperationState.Succeeded:
                        completed = true;
                        entityId = operation.TargetEntityId;
                        break;
                    case OperationState.InProgress:
                        completed = false;
                        Log("operation in progress", operation.TargetEntityId, operation.Id);
                        break;
                }
                return completed;
            }
    
    
            private static void Log(string action, string entityId = null, string operationId = null)
            {
                Console.WriteLine(
                    "{0,-21}{1,-51}{2,-51}{3,-51}",
                    DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
                    action,
                    entityId ?? string.Empty,
                    operationId ?? string.Empty);
            }
        }
    }   


##<a name="next-step"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>Leder du efter noget andet?

Hvis dette emne ikke indeholder hvad du forventede, der mangler noget eller i en anden måde opfylder ikke dine behov, skal du give os du feedback ved at bruge den Disqus tråd nedenfor.
