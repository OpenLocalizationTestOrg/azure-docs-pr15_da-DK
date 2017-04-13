<properties
    pageTitle="Ved hjælp af AES-128 dynamiske kryptering og vigtige leveringstjenesten | Microsoft Azure"
    description="Microsoft Azure Media Services gør det muligt at levere dit indhold, der er krypteret med AES 128-bit-krypteringsnøgler. Media Services indeholder også tjenesten nøgle levering, som leverer kryptering taster til autoriserede brugere. Dette emne viser, hvordan du dynamisk Kryptér med AES-128 og bruge de vigtigste leveringstjenesten."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/24/2016"
    ms.author="juliako"/>

#<a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>Ved hjælp af AES-128 dynamiske kryptering og vigtige leveringstjenesten

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-aes128.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Oversigt

>[AZURE.NOTE] Se [denne](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) video for at få et overblik over, hvordan du beskytter din Media-indhold med AES-kryptering.

Microsoft Azure Media Services gør det muligt at levere Http-Live-Streaming (HLS) og bløde Streams, der er krypteret med avancerede kryptering AES (Standard) (ved hjælp af 128-bit krypteringsnøgler). Media Services indeholder også tjenesten nøgle levering, som leverer kryptering taster til autoriserede brugere. Hvis du vil for Media Services til at kryptere et aktiv, skal du knytte en krypteringsnøgle til aktivet og også konfigurere politikker for godkendelse for nøglen. Når der anmodes om en stream ved en afspiller, bruger Media Services den angivne nøgle til at kryptere dynamisk dit indhold, der bruger AES-kryptering. Hvis du vil dekryptere strømmen, afspilleren anmoder om tasten fra de vigtigste leveringstjenesten. For at afgøre, om brugeren har tilladelse til at hente nøglen, evaluerer tjenesten de politikker, der tilladelse, du har angivet for nøglen.

Media Services understøtter flere måder at godkendelse af brugere, der foretage vigtige anmodninger. Politikken indhold vigtige tilladelse kan har en eller flere godkendelse begrænsninger: åbne eller token begrænsning. Politikken token begrænset skal være ledsaget af et token, der er udstedt af et Token STS (Secure Service). Media Services understøtter tokens i formatet [Enkel Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) og formatet [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Se [konfigurere tasten indhold politik](media-services-protect-with-aes128.md#configure_key_auth_policy)kan finde flere oplysninger.

For at kunne udnytte dynamiske kryptering, skal du have et aktiv, der indeholder et sæt af multi-bithastighed MP4 filer eller flere bithastighed udglattede Streaming kildefiler. Du skal også konfigurere politikken levering for aktivet (beskrevet senere i dette emne). Derefter sikrer baseret på det format, der er angivet i streaming URL-adressen, serveren On Demand Streaming, at strømmen leveres i den protokol, du har valgt. Som et resultat, du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format Media Services-tjenesten, så og opbygge fungere relevante svaret baseret på anmodninger fra en klient.

Dette emne kan være nyttige for udviklere, der arbejder på programmer, der leverer beskyttet medie. Emnet viser, hvordan du konfigurerer tjenesten for vigtige med godkendelse politikker, så kun godkendte klienter kunne modtage tasterne kryptering. Det kan også, hvordan du bruger dynamiske kryptering.

>[AZURE.NOTE]Hvis du vil begynde at bruge dynamiske kryptering, skal du først få mindst én enhed for tidsskala (også kaldet streaming enhed). Du kan finde flere oplysninger, se, [hvordan skalere en medier-tjeneste](media-services-portal-manage-streaming-endpoints.md).

##<a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 dynamiske kryptering og vigtige levering tjenesten arbejdsproces

Følgende er generelle trin, du har brug for til at udføre, når du krypterer dine aktiver med AES, ved hjælp af tjenesten Media Services vigtige levering, og også ved hjælp af dynamiske kryptering.

1. [Oprette et aktiv og overføre filer til aktivet](media-services-protect-with-aes128.md#create_asset).
1. [Kodes de aktiver, der indeholder fil, der tilpasset bithastigheden MP4 angive](media-services-protect-with-aes128.md#encode_asset).
1. [Oprette en indhold nøgle og knytte den med den kodet aktiv](media-services-protect-with-aes128.md#create_contentkey). I Media Services indeholder nøglen indhold aktivets krypteringsnøgle.
1. [Konfigurere tasten indhold politik](media-services-protect-with-aes128.md#configure_key_auth_policy). Politikken indhold vigtige godkendelse skal konfigureres ved du og opfyldt af klienten for at tasten indhold blev leveret til kunden.
1. [Konfigurer politikken levering for et aktiv](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Levering Politikkonfigurationen omfatter: acquisition URL-adresse og initialisering vektor (IV) (AES 128 kræver de samme IV skal gives, når kryptere og dekryptere), levering protokol (for eksempel MPEG TANKESTREG, HLS, HD'ER, bløde Streaming eller alle), hvilken type dynamiske kryptering (for eksempel konvolut eller ingen dynamiske kryptering).

Du kan anvende forskellige politik til hver protokol på det samme aktiv. For eksempel kan du anvende PlayReady kryptering jævne/TANKESTREG og AES konvolut til HLS. En hvilken som helst protokoller, som ikke er defineret i en politik for levering af (for eksempel du tilføje en enkelt politik, som kun angiver HLS som protokollen) blokeres fra streaming. Undtagelsen er, hvis du har ingen defineret overhovedet aktiv levering politik. Alle protokoller tillades derefter på Ryd.

1. [Opret en anmodet arbejdsproces locator](media-services-protect-with-aes128.md#create_locator) Hvis du vil have en streaming URL-adresse.

Emnet viser også, [hvordan en klientprogrammet kan anmode om en nøgle fra den vigtige leveringstjenesten](media-services-protect-with-aes128.md#client_request).

Du kan finde en komplet .NET- [eksempel](media-services-protect-with-aes128.md#example) i slutningen af emnet.

Følgende billede viser den arbejdsproces, der er beskrevet ovenfor. Her bruges tokenet til godkendelse.

![Beskytte med AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Resten af dette emne indeholder detaljerede beskrivelser, kodeeksempler og links til emner, der viser, hvordan du opnår de opgaver, der er beskrevet ovenfor.

##<a name="current-limitations"></a>Aktuelle begrænsninger

Hvis du tilføjer eller opdaterer din aktiv levering politik, skal du slette en eksisterende locator (hvis relevant) og oprette en ny locator.

##<a id="create_asset"></a>Oprette et aktiv og overføre filer til aktivet

For at administrere, kode og streame dine videoer, skal du først overføre dit indhold til Microsoft Azure Media Services. Når overført, er dit indhold gemt sikkert i skyen til videre behandling og streaming. 

Du kan finde detaljerede oplysninger, [Overføre filer til en Media Services-konto](media-services-dotnet-upload-files.md).

##<a id="encode_asset"></a>Kode på Aktiver, der indeholder fil, der tilpasset bithastigheden MP4 angive

Alt, du skal er at oprette et aktiv, der indeholder et sæt af multi-bithastighed MP4 filer eller flere bithastighed udglattede Streaming kildefiler med dynamisk kryptering. Derefter sikrer baseret på det angivne format i manifest eller fragment anmodningen, On Demand Streaming server, at du modtager strømmen i den protokol, du har valgt. Som et resultat, du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format Media Services-tjenesten, så og opbygge fungere relevante svaret baseret på anmodninger fra en klient. Du kan få mere at vide under emnet [Dynamisk emballagen oversigt](media-services-dynamic-packaging-overview.md) .

Yderligere oplysninger om kode, skal du se, [hvordan du kode en aktiv, ved hjælp af Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

##<a id="create_contentkey"></a>Oprette en indhold nøgle og knytte den til kodet aktivet

I Media Services indeholder nøglen indhold nøglen, som du vil kryptere et aktiv med.

Du kan finde detaljerede oplysninger, [oprette indhold nøgle](media-services-dotnet-create-contentkey.md).

##<a id="configure_key_auth_policy"></a>Konfigurere tasten indhold politik

Media Services understøtter flere måder at godkendelse af brugere, der foretage vigtige anmodninger. Politikken indhold vigtige godkendelse skal konfigureres ved du og opfyldt af klienten (player) for at tasten blev leveret til kunden. Politikken indhold vigtige tilladelse kan har en eller flere godkendelse begrænsninger: åbne, token begrænsning eller IP-begrænsning.

Oplysninger, skal du se [Konfigurere indhold nøgle godkendelse politikken](media-services-dotnet-configure-content-key-auth-policy.md).

##<a id="configure_asset_delivery_policy"></a>Konfigurere aktiv levering politik 

Konfigurere politikken levering for din aktiv. Nogle ting, som aktiv levering Politikkonfigurationen omfatter:

- Tasten acquisition URL-adressen. 
- Initialisering vektor (IV) skal bruges til konvolut kryptering. AES 128 kræver de samme IV skal gives, når kryptere og dekryptere. 
- Aktiver levering protokollen (for eksempel MPEG TANKESTREG, HLS, HD'ER, bløde Streaming eller alle).
- Hvilke typer dynamisk kryptering (for eksempel AES konvolut) eller ingen dynamiske kryptering. 

Oplysninger, skal du se [konfigurere aktiv levering politik ](media-services-rest-configure-asset-delivery-policy.md).

##<a id="create_locator"></a>Oprette en anmodet arbejdsproces streaming locator Hvis du vil have en streaming URL-adresse

Du skal give din bruger streaming URL-adressen for jævne, STREG eller HLS.

>[AZURE.NOTE]Hvis du tilføjer eller opdaterer din aktiv levering politik, skal du slette en eksisterende locator (hvis relevant) og oprette en ny locator.

Flere oplysninger om, hvordan du publicerer et aktiv og opbygge en streaming URL-adresse, under [oprette en streaming URL-adresse](media-services-deliver-streaming-content.md).

##<a name="get-a-test-token"></a>Få en test token

Få en test token, baseret på token begrænsning, der blev brugt til politikken vigtige godkendelse.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

Du kan bruge [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) til at teste din stream.

##<a id="client_request"></a>Hvordan kan kunden anmode om en nøgle fra tjenesten vigtige?

I det forrige trin, du har oprettet den URL-adresse, der peger på en manifestfil. Kunden skal udtrække de nødvendige oplysninger fra streaming manifestfilerne for at gøre en anmodning til de vigtigste leveringstjenesten.

###<a name="manifest-files"></a>Manifest-filer

Klienten skal udtrækkes URL-adressen (som også indeholder indhold nøgle Id (børn)) værdi fra manifestfilen. Klienten derefter forsøger at få krypteringsnøglen fra de vigtigste leveringstjenesten. Klienten skal også udtrække IV værdi og brug den dekryptere strømmen. Følgende kodestykke viser den <Protection> element i manifestet udglattede Streaming.

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

Hvis det er HLS opdeles roden manifestet i segmentet filer. 

Roden manifestet er for eksempel: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) og den indeholder en liste over segmentet filnavne.
    
    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Hvis du åbner en af segmentet filerne i tekstredigeringsprogram (for eksempel http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels (514369)/Manifest(video,format=m3u8-aapl), den skal indeholde #EXT-X-nøgle, som angiver, at filen er krypteret.
    
    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST
    
###<a name="request-the-key-from-the-key-delivery-service"></a>Anmode om tasten fra tjenesten vigtige

Følgende kode viser, hvordan du sender en anmodning til Media Services vigtige leveringstjenesten ved hjælp af en vigtige levering Uri (som er hentet fra manifestet) og et token (i dette emne ikke tale om, hvordan du få enkle Web Tokens fra en Secure Token Service).

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);
                
        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;
    
        var response = request.GetResponse();
     
        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }
    
        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();
    
        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
    
##<a id="example"></a>Eksempel

1. Oprette et nyt Console-projekt.
1. Brug NuGet til at installere, og Tilføj Azure Media Services .NET SDK udvidelser. Installationen af denne pakke, også installerer Media Services .NET SDK og tilføjer alle andre nødvendige afhængigheder.
2. Tilføje config-fil, der indeholder det kontonavn og vigtige oplysninger:

    
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>
            
                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>
            
                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>

1. Overskrive koden i filen Program.cs med den kode, der vises i dette afsnit.
    
    Sørg for at opdatere variabler til at pege på mapper, hvor dine input filer er placeret.
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace AESDynamicEncryptionAndKeyDeliverySvc
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // A Uri describing the issuer of the token.  
                // Must match the value in the token for the token to be considered valid.
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                // The Audience or Scope of the token.  
                // Must match the value in the token for the token to be considered valid.
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateEnvelopeTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine();
        
                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);
        
                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();
        
                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();
        
                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
        
                        // Generate a test token based on the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
        
                        //The GenerateTestToken method returns the token without the word “Bearer” in front
                        //so you have to add it in front of the token string. 
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the bit.ly/aesplayer Flash player to test the URL 
                    // (with open authorization policy). 
                    // Paste the URL and click the Update button to play the video. 
                    //
                    string URL = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Smooth Streaming Url: {0}/manifest", URL);
                    Console.WriteLine();
                    Console.WriteLine("HLS Url: {0}/manifest(format=m3u8-aapl)", URL);
                    Console.WriteLine();
        
                    Console.ReadLine();
                }
        
                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }
        
                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);
        
                    var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
        
                    Console.WriteLine("Created assetFile {0}", assetFile.Name);
        
                    var policy = _context.AccessPolicies.Create(
                                            assetName,
                                            TimeSpan.FromDays(30),
                                            AccessPermissions.Write | AccessPermissions.List);
        
                    var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
        
                    Console.WriteLine("Upload {0}", assetFile.Name);
        
                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);
        
                    locator.Delete();
                    policy.Delete();
        
                    return inputAsset;
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Create a task with the encoding details, using a string preset.
                    // In this case "H264 Multiple Bitrate 720p" preset is used.
                    ITask task = job.Tasks.AddNew("My encoding task",
                        processor,
                        "H264 Multiple Bitrate 720p",
                        TaskOptions.None);
        
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.StorageEncrypted);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
                static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
                {
                    // Create envelope encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.EnvelopeEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy             
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("Open Authorization Policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                        new List<ContentKeyAuthorizationPolicyRestriction>();
        
                    ContentKeyAuthorizationPolicyRestriction restriction =
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "HLS Open Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null // no requirements needed for HLS
                                };
        
                    restrictions.Add(restriction);
        
                    IContentKeyAuthorizationPolicyOption policyOption =
                        _context.ContentKeyAuthorizationPolicyOptions.Create(
                        "policy",
                        ContentKeyDeliveryType.BaselineHttp,
                        restrictions,
                        "");
        
                    policy.Options.Add(policyOption);
        
                    // Add ContentKeyAutorizationPolicy to ContentKey
                    contentKey.AuthorizationPolicyId = policy.Id;
                    IContentKey updatedKey = contentKey.UpdateAsync().Result;
                    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("HLS token restricted authorization policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                            new List<ContentKeyAuthorizationPolicyRestriction>();
        
                    ContentKeyAuthorizationPolicyRestriction restriction =
                            new ContentKeyAuthorizationPolicyRestriction
                            {
                                Name = "Token Authorization Policy",
                                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                                Requirements = tokenTemplateString
                            };
        
                    restrictions.Add(restriction);
        
                    //You could have multiple options 
                    IContentKeyAuthorizationPolicyOption policyOption =
                        _context.ContentKeyAuthorizationPolicyOptions.Create(
                            "Token option for HLS",
                            ContentKeyDeliveryType.BaselineHttp,
                            restrictions,
                            null  // no key delivery data is needed for HLS
                            );
        
                    policy.Options.Add(policyOption);
        
                    // Add ContentKeyAutorizationPolicy to ContentKey
                    contentKey.AuthorizationPolicyId = policy.Id;
                    IContentKey updatedKey = contentKey.UpdateAsync().Result;
                    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
        
                    return tokenTemplateString;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        
                    string envelopeEncryptionIV = Convert.ToBase64String(GetRandomBuffer(16));
            
                    // When configuring delivery policy, you can choose to associate it
                    // with a key acquisition URL that has a KID appended or
                    // or a key acquisition URL that does not have a KID appended  
                    // in which case a content key can be reused. 

                    // EnvelopeKeyAcquisitionUrl:  contains a key ID in the key URL.
                    // EnvelopeBaseKeyAcquisitionUrl:  the URL does not contains a key ID

                    // The following policy configuration specifies: 
                    // key url that will have KID=<Guid> appended to the envelope and
                    // the Initialization Vector (IV) to use for the envelope encryption.
                    
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                    {
                                {AssetDeliveryPolicyConfigurationKey.EnvelopeKeyAcquisitionUrl, keyAcquisitionUri.ToString()}
                    };
        
                    IAssetDeliveryPolicy assetDeliveryPolicy =
                        _context.AssetDeliveryPolicies.Create(
                                    "AssetDeliveryPolicy",
                                    AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                                    AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                                    assetDeliveryPolicyConfiguration);
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
                    Console.WriteLine();
                    Console.WriteLine("Adding Asset Delivery Policy: " +
                        assetDeliveryPolicy.AssetDeliveryPolicyType);
                }
        
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                EndsWith(".ism")).
                                                FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);
        
                    // Create a locator to the streaming content on an origin. 
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));
        
                    // Create a URL to the manifest file. 
                    return originLocator.Path + assetFile.Name;
                }
        
                static private string GenerateTokenRequirements()
                {
                    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
        
                    template.PrimaryVerificationKey = new SymmetricVerificationKey();
                    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
                    template.Audience = _sampleAudience.ToString();
                    template.Issuer = _sampleIssuer.ToString();
        
                    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
        
                    return TokenRestrictionTemplateSerializer.Serialize(template);
                }
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int size)
                {
                    byte[] randomBytes = new byte[size];
                    using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(randomBytes);
                    }
        
                    return randomBytes;
                }
            }
        }


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
