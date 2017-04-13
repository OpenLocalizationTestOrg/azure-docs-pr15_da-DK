<properties 
    pageTitle="Beskytte dit indhold med Apple FairPlay og/eller Microsoft PlayReady HLS | Microsoft Azure" 
    description="Dette emne giver et overblik og viser, hvordan du bruger Azure Media Services til at kryptere dynamisk HTTP Live Streaming (HLS) indholdet med Apple FairPlay. Det også viser, hvordan du bruger tjenesten Media Services licens til at holde FairPlay licenser for klienter." 
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
    ms.date="09/27/2016"
    ms.author="juliako"/>

# <a name="protect-your-hls-content-with-apple-fairplay-andor-microsoft-playready"></a>Beskytte dit indhold med Apple FairPlay og/eller Microsoft PlayReady HLS

Azure Media Services gør det muligt at kryptere dynamisk HTTP Live Streaming (HLS) indholdet i følgende formater:  

- **AES-128 konvolut Ryd nøgle** 

    Det hele afsnit er krypteret ved hjælp af **AES-128 CBC** tilstanden. Dekryptering af strømmen understøttes af iOS- og OSX player indbygget. Se [denne artikel](media-services-protect-with-aes128.md)kan finde flere oplysninger.

- **Apple FairPlay** 

    De enkelte eksempler på video og lyd er krypteret ved hjælp af **AES-128 CBC** tilstanden. **FairPlay Streaming** (FPS) er integreret i operativsystemerne enhed med oprindelige support på iOS- og Apple TV. Safari på OS X aktiverer FPS ved hjælp af krypteret medier Extensions (EME) interface support.
- **Microsoft PlayReady**

Følgende billede viser **HLS + FairPlay og/eller PlayReady dynamiske kryptering** arbejdsprocessen.

![Beskytte med FairPlay](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

Dette emne beskrives, hvordan du bruger Azure Media Services til at kryptere dynamisk HLS indholdet med Apple FairPlay. Det også viser, hvordan du bruger tjenesten Media Services licens til at holde FairPlay licenser for klienter.

>[AZURE.NOTE] Hvis du vil også kryptere HLS indholdet med PlayReady, skal du oprette en fælles indhold nøgle og knytte den til din aktiv. Du skal også konfigurere tasten indhold politik, som det er beskrevet i emnet med [ved hjælp af PlayReady dynamiske almindelige kryptering](media-services-protect-with-drm.md) .

    
## <a name="requirements-and-considerations"></a>Kravene samt de overvejelser

- Følgende er påkrævet ved brug af AMS til at levere HLS, der er krypteret med FairPlay og til at levere FairPlay licenser.

    - En Azure-konto. Yderligere oplysninger finder du [Azure gratis prøveversion](/pricing/free-trial/?WT.mc_id=A261C142F).
    - En Media Services-konto. Hvis du vil oprette en Media Services-konto, skal du se [Oprette konto](media-services-portal-create-account.md).
    - Tilmelde dig [Apple udvikling Program](https://developer.apple.com/).
    - Apple kræver ejeren af indholdet til at hente [installationspakken](https://developer.apple.com/contact/fps/). Angive anmodningen du allerede implementeret KSM (tasten sikkerhedsmodul) med Azure Media Services og, du anmoder om den endelige FPS pakke. Der vil være instruktionerne i den endelige FPS pakke til at generere certificering og få Spørg, hvor du vil bruge til at konfigurere FairPlay. 

    - Azure Media Services .NET SDK version **3.6.0** eller nyere.

- Følgende ting skal angives på AMS vigtige levering side:
    - **App Cert (AC)** - .pfx-fil, der indeholder privat nøgle. Denne fil er oprettet af kunden og krypteret med en adgangskode ved den samme kunde. 
        
        Når kunden konfigurerer vigtige levering politik, skal de angive adgangskoden og .pfx i base64 format.

        Følgende trin beskriver, hvordan til at generere et pfx certifikat til FairPlay.
        
        1. Installere OpenSSL fra https://slproweb.com/products/Win32OpenSSL.html
        
            Gå til den mappe, hvor certifikatet, der FairPlay og andre filer, der er leveret af Apple er.
        
        2. Kommandolinjen til at konvertere virksomheden til pem:
        
            "C:\OpenSSL-Win32\bin\openssl.exe" x509-underrette der-i fairplay.cer-ud fairplay out.pem
        
        3. Kommandolinje til at konvertere pem til pfx med den private nøgle (adgangskoden til pfx-fil er derefter stillede ved OpenSSL).
        
            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-Eksporter – ud fairplay out.pfx-inkey privatekey.pem-i fairplay out.pem - passin file:privatekey-pem-pass.txt 
        
    - **App Cert adgangskode** - kunde adgangskode til at oprette .pfx-fil.
    - **App Cert adgangskode-ID** - kunden skal overføre adgangskoden ligner hvordan de overføre andre AMS nøgler og bruge **ContentKeyType.FairPlayPfxPassword** optællingsværdi. Som et resultat, får de vist AMS-id dette er, hvad de skal bruge i indstillingen vigtige levering politik.
    - **iv** - 16 byte tilfældig værdi, skal stemme overens iv i politikken aktiv levering. Kunden genererer IV og placerer den i begge steder: aktiv levering politik og nøgle levering indstilling politik. 
    - **ASK** - Spørg (programtasten hemmeligt) modtages, når du opretter ved hjælp af portalen Apple udvikler certificering. Hver development-teamet, modtager en entydig Spørg. Gemme en kopi af ASK, og gemme den i et sikkert sted. Du skal konfigurere ASK som FairPlayAsk til Azure Media Services senere. 
    -  **Spørg ID** - hentes, når kunden overførsler ASk i AMS. Kunden skal overføre Spørg ved hjælp af **ContentKeyType.FairPlayASk** optællingsværdi. AMS id returneres som et resultat, og det er, hvad der skal bruges, når du vælger indstillingen vigtige levering politik.

- Følgende ting skal angives af klientsiden FPS:
    - **App Cert (AC)** -.cer/.der fil, der indeholder offentlig nøgle, som bruger OS til at kryptere nogle data. AMS skal vide om det, fordi den er påkrævet af afspilleren. De vigtigste leveringstjenesten dekrypteres den med den tilhørende private nøgle.

- Til afspilning af en krypteret stream FairPlay skal du få reelle Spørg først og derefter generere et reelt tal certifikat. Denne proces opretter alle 3 dele:

    -  .der, 
    -  .pfx og 
    -  adgangskoden til .pfx.
 
- Klienter, der understøtter HLS med **AES-128 CBC** kryptering: Safari på OS X, Apple TV iOS.

##<a name="steps-for-configuring-fairplay-dynamic-encryption-and-license-delivery-services"></a>Trin til konfiguration af FairPlay dynamisk kryptering og licens tjenester til levering af

Følgende er generelle trin, du har brug for til at udføre, når du beskytter dine aktiver med FairPlay, ved hjælp af tjenesten Media Services licens levering, og også ved hjælp af dynamiske kryptering.

1. Oprette et aktiv, og overføre filer til aktivet. 
1. Kod på Aktiver, der indeholder fil, der tilpasset bithastigheden MP4 angive.
1. Oprette en indhold nøgle og knytte den til den kodet aktiv.  
1. Konfigurere tasten indhold politik. Når du opretter politikken indhold vigtige godkendelse, skal du angive følgende: 
    
    - leveringsmetode (i dette tilfælde FairPlay), 
    - Konfiguration af FairPlay politik indstillinger. Du kan finde oplysninger om, hvordan du konfigurerer FairPlay, ConfigureFairPlayPolicyOptions() metode i eksemplet nedenfor.
    
        >[AZURE.NOTE] Som regel, vil du konfigurere indstillinger for FairPlay for kun én gang, da du kun har ét sæt certificering og Spørg.
-begrænsninger (åben eller token)- og oplysninger, der er specifikke for leveringstypen vigtige, der definerer, hvordan nøglen er leveret til kunden. 
    
2. Konfigurere aktiv levering politik. Levering Politikkonfigurationen omfatter: 

    - levering protocol (HLS) 
    - hvilke typer dynamisk kryptering (almindelige CBC kryptering) 
    - licens acquisition URL-adressen. 
    
    >[AZURE.NOTE]Hvis du vil vise en stream, der er krypteret med FairPlay + en anden DRM, skal du konfigurere politikker for separat levering 
    >
    >- Én IAssetDeliveryPolicy til at konfigurere TANKESTREG med CENC (PlayReady + WideVine) og jævne med PlayReady. 
    >- En anden IAssetDeliveryPolicy at konfigurere FairPlay til HLS

1. Oprette en anmodet arbejdsproces locator for at få en streaming URL-adresse.

##<a name="using-fairplay-key-delivery-by-playerclient-apps"></a>Bruge FairPlay vigtige levering ved player/client apps

Kunder kan udvikle player apps ved hjælp af iOS SDK. For at kunne afspille FairPlay indhold har kunder at implementere licens exchange protocol. Licens exchange protocol er ikke angivet af Apple. Det er op til hver app hvordan du kan sende vigtige leveringen af anmodninger om. AMS FairPlay vigtige levering tjenesterne forventer SPC kommer som www-form-url kodet indlæg meddelelse i følgende format: 

    spc=<Base64 encoded SPC>

>[AZURE.NOTE] Azure Media Player understøtter ikke FairPlay afspilning af feltet. Kunderne skal indhente eksempel afspilleren fra Apple udvikler konto for at hente FairPlay Afspil på MAC OSX. 
 
##<a name="streaming-urls"></a>Streaming URL-adresser

Hvis din aktiv blev krypteret med mere end én DRM, skal du bruge en kryptering mærke i streaming URL-adressen: (format = 'm3u8-aapl' kryptering = 'xxx').

Overvej følgende gælder:

- Kun nul eller én krypteringstype kan angives.
- Krypteringstype skal ikke være angivet i URL-adressen, hvis der kun én kryptering blev anvendt på aktivet.
- Kryptering er bogstaver.
- Du kan angive følgende krypteringstyper:  
    - **cenc**: almindelige kryptering (Playready eller Widevine)
    - **cbcs aapl**: Fairplay
    - **CBC**: AES konvolut kryptering.


##<a name="net-example"></a>.NET eksempel


Følgende eksempel demonstrerer funktionalitet, som blev introduceret i Azure Media Services SDK til .net-Version 3.6.0 (muligheden for at bruge Azure Media Services til at levere dit indhold, der er krypteret med FairPlay). Følgende kommando for Nuget pakke blev brugt til at installere pakken:

    PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. Oprette et Console-projekt.
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
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
        using Newtonsoft.Json;
        using System.Security.Cryptography.X509Certificates;
        
        namespace DynamicEncryptionWithFairPlay
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
        
                    IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
        
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);
        
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
        
                static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
                {
                    // Create HLS SAMPLE AES encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryptionCbcs);
        
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
        
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.FairPlay,
                        restrictions,
                        FairPlayConfiguration);
        
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
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
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                               ContentKeyDeliveryType.FairPlay,
                               restrictions,
                               FairPlayConfiguration);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
                }
        
                private static string ConfigureFairPlayPolicyOptions()
                {
                    // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK. 
                    // However, for production you must use a real ASK from Apple bound to a real prod certificate.
                    byte[] askBytes = Guid.NewGuid().ToByteArray();
                    var askId = Guid.NewGuid();
                    // Key delivery retrieves askKey by askId and uses this key to generate the response.
                    IContentKey askKey = _context.ContentKeys.Create(
                                            askId,
                                            askBytes,
                                            "askKey",
                                            ContentKeyType.FairPlayASk);
        
                    //Customer password for creating the .pfx file.
                    string pfxPassword = "<customer password for creating the .pfx file>";
                    // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
                    var pfxPasswordId = Guid.NewGuid();
                    byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
                    IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                                            pfxPasswordId,
                                            pfxPasswordBytes,
                                            "pfxPasswordKey",
                                            ContentKeyType.FairPlayPfxPassword);
        
                    // iv - 16 bytes random value, must match the iv in the asset delivery policy.
                    byte[] iv = Guid.NewGuid().ToByteArray();
        
                    //Specify the .pfx file created by the customer.
                    var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);
        
                    string FairPlayConfiguration =
                        Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                            appCert,
                            pfxPassword,
                            pfxPasswordId,
                            askId,
                            iv);
        
                    return FairPlayConfiguration;
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
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();
        
                    var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);
        
                    FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);
        
                    // Get the FairPlay license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);
        
                    // The reason the below code replaces "https://" with "skd://" is because
                    // in the IOS player sample code which you obtained in Apple developer account, 
                    // the player only recognizes a Key URL that starts with skd://. 
                    // However, if you are using a customized player, 
                    // you can choose whatever protocol you want. 
                    // For example, "https". 

                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                            {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
                        };
        
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
                        AssetDeliveryProtocol.HLS,
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


##<a name="next-steps-media-services-learning-paths"></a>Næste trin: Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
