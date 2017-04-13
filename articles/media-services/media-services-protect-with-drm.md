<properties
    pageTitle="Ved hjælp af PlayReady og/eller Widevine dynamiske almindelige kryptering | Microsoft Azure"
    description="Microsoft Azure Media Services gør det muligt at levere MPEG-STREG, bløde Streaming og Http Live Streaming (HLS) streams, der er beskyttet med Microsoft PlayReady DRM. Gør det også muligt at levering TANKESTREG, der er krypteret med Widevine DRM. Dette emne viser, hvordan du dynamisk Kryptér med PlayReady og Widevine DRM."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="09/27/2016"
    ms.author="juliako"/>


#<a name="using-playready-andor-widevine-dynamic-common-encryption"></a>Ved hjælp af PlayReady og/eller Widevine dynamiske almindelige kryptering

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Microsoft Azure Media Services gør det muligt at levere MPEG-STREG, bløde Streaming og HTTP Live Streaming (HLS) streams, der er beskyttet med [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). Det kan du også til at levere krypteret TANKESTREG stream med Widevine DRM licenser. Både PlayReady og Widevine er krypteret per specifikationen almindelige kryptering (ISO/IEC 23001-7 CENC). Du kan bruge [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (begyndende med versionen 3.5.1 til) eller REST-API til at konfigurere din AssetDeliveryConfiguration for at bruge Widevine.

Media Services indeholder en tjeneste til at levere PlayReady og Widevine DRM licenser. Media Services indeholder også API'er, som du kan konfigurere rettigheder og begrænsninger, som du vil til PlayReady eller Widevine DRM runtime gennemtvinge, når en bruger afspiller beskyttet indhold. Når en bruger anmoder om en DRM-beskyttet indhold, skal player programmet have en licens fra tjenesten AMS licens. Tjenesten AMS licens udstede en licens til afspilleren, hvis den er godkendt. En PlayReady eller Widevine licens indeholder krypteringsnøglen, der kan bruges af klienten afspilleren dekryptere og streame indholdet.


Du kan også bruge de følgende AMS partnere kan hjælpe dig med at levere Widevine licenser: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) [castLabs](http://castlabs.com/company/partners/azure/). Finde flere oplysninger: integration med [Axinom](media-services-axinom-integration.md) og [castLabs](media-services-castlabs-integration.md).

Media Services understøtter flere måder at godkendelse af brugere, der foretage vigtige anmodninger. Politikken indhold vigtige tilladelse kan har en eller flere godkendelse begrænsninger: åbne eller token begrænsning. Politikken token begrænset skal være ledsaget af et token, der er udstedt af et Token STS (Secure Service). Media Services understøtter tokens i formatet [Enkel Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) og formatet [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Yderligere oplysninger, du konfigurerer tasten indhold godkendelse politik.

For at kunne udnytte dynamiske kryptering, skal du have et aktiv, der indeholder et sæt af multi-bithastighed MP4 filer eller flere bithastighed udglattede Streaming kildefiler. Du skal også konfigurere levering politikker for aktivet (beskrevet senere i dette emne). Derefter sikrer baseret på det format, der er angivet i streaming URL-adressen, serveren On Demand Streaming, at strømmen leveres i den protokol, du har valgt. Som et resultat, du skal kun bruge til at gemme og betale for filer i et enkelt lagerplads format og Media Services, opbygge og udskrive den relevante HTTP-svar, der er baseret på hver enkelt anmodning fra en klient.

Dette emne kan være nyttige for udviklere, der arbejder på programmer, der leverer medier, der er beskyttet med flere DRMs, som PlayReady og Widevine. Emnet viser, hvordan du konfigurerer tjenesten PlayReady licens med godkendelse politikker, så der kun autoriserede klienter kunne modtage PlayReady eller Widevine licenser. Det kan også, hvordan du bruger dynamiske kryptering kryptering med PlayReady eller Widevine DRM over bindestreg.

>[AZURE.NOTE]Hvis du vil begynde at bruge dynamiske kryptering, skal du først få mindst én enhed for tidsskala (også kaldet streaming enhed). Du kan finde flere oplysninger, se, [hvordan skalere en medier-tjeneste](media-services-portal-manage-streaming-endpoints.md).


##<a name="download-sample"></a>Hente eksempel

Du kan hente den eksempel, der er beskrevet i denne artikel fra [her](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

##<a name="configuring-dynamic-common-encryption-and-drm-license-delivery-services"></a>Konfiguration af dynamiske almindelige kryptering og tjenester til levering af DRM licens

Følgende er generelle trin, du har brug for til at udføre, når du beskytter dine aktiver med PlayReady, ved hjælp af tjenesten Media Services licens levering, og også ved hjælp af dynamiske kryptering.

1. Oprette et aktiv, og overføre filer til aktivet.
1. Kod på Aktiver, der indeholder fil, der tilpasset bithastigheden MP4 angive.
1. Oprette en indhold nøgle og knytte den til den kodet aktiv. I Media Services indeholder nøglen indhold aktivets krypteringsnøgle.
1. Konfigurere tasten indhold politik. Politikken indhold vigtige godkendelse skal konfigureres ved du og opfyldt af klienten i rækkefølge for nøglen indhold blev leveret til kunden.

Når du opretter politikken indhold vigtige godkendelse, skal du angive følgende: levering metode (PlayReady eller Widevine), begrænsninger (åbne eller token) og oplysninger, der er specifikke for leveringstypen vigtige, der definerer, hvordan tasten er leveret til klienten ([PlayReady](media-services-playready-license-template-overview.md) eller [Widevine](media-services-widevine-license-template-overview.md) licens skabelon).
1. Konfigurere politikken levering for et aktiv. Levering Politikkonfigurationen omfatter: levering protokol (for eksempel MPEG TANKESTREG, HLS, HD'ER, bløde Streaming eller alle), hvilken type dynamiske kryptering (for eksempel almindelige kryptering) PlayReady eller Widevine licens acquisition URL-adresse.

Du kan anvende forskellige politik til hver protokol på det samme aktiv. For eksempel kan du anvende PlayReady kryptering jævne/TANKESTREG og AES konvolut til HLS. En hvilken som helst protokoller, som ikke er defineret i en politik for levering af (for eksempel du tilføje en enkelt politik, som kun angiver HLS som protokollen) blokeres fra streaming. Undtagelsen er, hvis du har ingen defineret overhovedet aktiv levering politik. Alle protokoller tillades derefter på Ryd.
1. Oprette en anmodet arbejdsproces locator Hvis du vil have en streaming URL-adresse.

Du kan finde en komplet .NET eksempel i slutningen af emnet.

Følgende billede viser den arbejdsproces, der er beskrevet ovenfor. Her bruges tokenet til godkendelse.

![Beskytte med PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

Resten af dette emne indeholder detaljerede beskrivelser, kodeeksempler og links til emner, der viser, hvordan du opnår de opgaver, der er beskrevet ovenfor.

##<a name="current-limitations"></a>Aktuelle begrænsninger

Hvis du tilføjer eller opdatere en politik til levering af aktiv, skal du slette den tilknyttede locator (hvis relevant) og opretter en ny locator.

Begrænsning, når du krypterer med Widevine med Azure Media Services:, flere indhold taster understøttes ikke i øjeblikket.

##<a name="create-an-asset-and-upload-files-into-the-asset"></a>Oprette et aktiv og overføre filer til aktivet

For at administrere, kode og streame dine videoer, skal du først overføre dit indhold til Microsoft Azure Media Services. Når overført, er dit indhold gemt sikkert i skyen til videre behandling og streaming.

Du kan finde detaljerede oplysninger, [Overføre filer til en Media Services-konto](media-services-dotnet-upload-files.md).

##<a name="encode-the-asset-containing-the-file-to-the-adaptive-bitrate-mp4-set"></a>Kode på Aktiver, der indeholder fil, der tilpasset bithastigheden MP4 angive

Alt, du skal er at oprette et aktiv, der indeholder et sæt af multi-bithastighed MP4 filer eller flere bithastighed udglattede Streaming kildefiler med dynamisk kryptering. Derefter sikrer baseret på det angivne format i manifestet og fragment anmodningen, serveren On Demand Streaming, at du modtager strømmen i den protokol, du har valgt. Som et resultat, du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format Media Services-tjenesten, så og opbygge fungere relevante svaret baseret på anmodninger fra en klient. Du kan få mere at vide under emnet [Dynamisk emballagen oversigt](media-services-dynamic-packaging-overview.md) .

Yderligere oplysninger om kode, skal du se, [hvordan du kode en aktiv, ved hjælp af Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).


##<a id="create_contentkey"></a>Oprette en indhold nøgle og knytte den til kodet aktivet

I Media Services indeholder nøglen indhold nøglen, som du vil kryptere et aktiv med.

Du kan finde detaljerede oplysninger, [oprette indhold nøgle](media-services-dotnet-create-contentkey.md).


##<a id="configure_key_auth_policy"></a>Konfigurere tasten indhold politik

Media Services understøtter flere måder at godkendelse af brugere, der foretage vigtige anmodninger. Politikken indhold vigtige godkendelse skal konfigureres ved du og opfyldt af klienten (player) for at tasten blev leveret til kunden. Politikken indhold vigtige tilladelse kan har en eller flere godkendelse begrænsninger: åbne eller token begrænsning.

Oplysninger, skal du se [Konfigurere indhold nøgle godkendelse politikken](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

##<a id="configure_asset_delivery_policy"></a>Konfigurere aktiv levering politik 

Konfigurere politikken levering for din aktiv. Nogle ting, som aktiv levering Politikkonfigurationen omfatter:

- DRM licens acquisition URL-adressen. 
- Aktiver levering protokollen (for eksempel MPEG TANKESTREG, HLS, HD'ER, bløde Streaming eller alle). 
- Typen af dynamiske kryptering (i dette tilfælde almindelige kryptering). 

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

##<a id="example"></a>Eksempel


Følgende eksempel demonstrerer funktionalitet, som blev introduceret i Azure Media Services SDK til .net-Version 3.5.2 (nærmere betegnet kan muligheden for at definere en Widevine licens skabelon og anmode om en Widevine licens fra Azure Media Services). Følgende kommando for Nuget pakke blev brugt til at installere pakken:

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. Oprette et nyt Console-projekt.
1. Brug NuGet til at installere og tilføje Azure Media Services .NET SDK.
2. Tilføje yderligere referencer: system.Configuration i stor.
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

1. Få mindst én streaming enhed for streaming slutpunktet, som du planlægger at levering af dit indhold. Finde flere oplysninger: [konfigurere streaming slutpunkter](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal).

1. Overskrive koden i filen Program.cs med den kode, der vises i dette afsnit.
    
    Sørg for at opdatere variabler til at pege på mapper, hvor dine input filer er placeret.
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
        using Newtonsoft.Json;
        
        namespace DynamicEncryptionWithDRM
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
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
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
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
        
                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, 
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
                     
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
        
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
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
        
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
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";
            
                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));
            
                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
            
                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
            
                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),    AssetCreationOptions.StorageEncrypted);
            
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
            
                    return job.OutputMediaAssets[0];
                }
        
        
                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {
                    
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
        
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy          
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Open",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null
                        }
                    };
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("", 
                            ContentKeyDeliveryType.Widevine, 
                            restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with no restrictions").
                                Result;
        
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Token Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                            Requirements = tokenTemplateString,
                        }
                    };
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.Widevine,
                                restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
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
        
                static private string ConfigurePlayReadyLicenseTemplate()
                {
                    // The following code configures PlayReady License Template using .NET classes
                    // and returns the XML string.
        
                    //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
                    //It contains a field for a custom data string between the license server and the application 
                    //(may be useful for custom app logic) as well as a list of one or more license templates.
                    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
        
                    // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
                    // to be returned to the end users. 
                    //It contains the data on the content key in the license and any rights or restrictions to be 
                    //enforced by the PlayReady DRM runtime when using the content key.
                    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
                    //Configure whether the license is persistent (saved in persistent storage on the client) 
                    //or non-persistent (only held in memory while the player is using the license).  
                    licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
        
                    // AllowTestDevices controls whether test devices can use the license or not.  
                    // If true, the MinimumSecurityLevel property of the license
                    // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
                    licenseTemplate.AllowTestDevices = true;
        
                    // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
                    // It grants the user the ability to playback the content subject to the zero or more restrictions 
                    // configured in the license and on the PlayRight itself (for playback specific policy). 
                    // Much of the policy on the PlayRight has to do with output restrictions 
                    // which control the types of outputs that the content can be played over and 
                    // any restrictions that must be put in place when using a given output.
                    // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
                    //then the DRM runtime will only allow the video to be displayed over digital outputs 
                    //(analog video outputs won’t be allowed to pass the content).
        
                    //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
                    // If the output protections are configured too restrictive, 
                    // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.
        
                    // For example:
                    //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);
        
                    responseTemplate.LicenseTemplates.Add(licenseTemplate);
        
                    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
                }
        
        
                private static string ConfigureWidevineLicenseTemplate()
                {
                    var template = new WidevineMessage
                    {
                        allowed_track_types = AllowedTrackTypes.SD_HD,
                        content_key_specs = new[]
                        {
                            new ContentKeySpecs
                            {
                                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                        policy_overrides = new
                        {
                            can_play = true,
                            can_persist = true,
                            can_renew = false
                        }
                    };
        
                    string configuration = JsonConvert.SerializeObject(template);
                    return configuration;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    // Get the PlayReady license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
                
                    // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
                    // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
                    // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
                    // to append /? KID =< keyId > to the end of the url when creating the manifest.
                    // As a result Widevine license acquisition URL will have KID appended twice, 
                    // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.
            
                    Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
                    UriBuilder uriBuilder = new UriBuilder(widevineUrl);
                    uriBuilder.Query = String.Empty;
                    widevineUrl = uriBuilder.Uri;
        
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                            {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}
        
                        };
        
                    // In this case we only specify Dash streaming protocol in the delivery policy,
                    // All other protocols will be blocked from streaming.
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);
        
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
        
                }
        
                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
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
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int length)
                {
                    var returnValue = new byte[length];
        
                    using (var rng =
                        new System.Security.Cryptography.RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(returnValue);
                    }
        
                    return returnValue;
                }
            }
        }


## <a name="next-step"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Se også

[CENC med flere DRM og adgangskontrol](media-services-cenc-with-multidrm-access-control.md)

[Konfigurere Widevine emballagen med AMS](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Præsentation af Google Widevine tjenester til levering af licens i Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
