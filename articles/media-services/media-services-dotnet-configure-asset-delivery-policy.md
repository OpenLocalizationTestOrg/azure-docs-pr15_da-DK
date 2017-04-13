<properties 
    pageTitle="Konfigurere politikker for levering af aktiver med .NET SDK | Microsoft Azure" 
    description="Dette emne viser, hvordan du konfigurerer andet aktiv levering politikker med Azure Media Services .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako;mingfeiy"/>

#<a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurere politikker for levering af aktiver med .NET SDK
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##<a name="overview"></a>Oversigt

Hvis du planlægger at levering krypteret aktiver, et af trinnene i arbejdsprocessen for levering af indhold Media Services konfiguration af levering af politikker for aktiver. Politikken aktiv levering fortæller Media Services, hvordan du vil have for din aktiv blev leveret: i hvilke streaming protokol, skal din aktiv dynamisk pakkes (for eksempel, MPEG TANKESTREG, HLS, bløde Streaming eller alle), uanset om du vil kryptere dynamisk din aktiv, og hvordan (konvolut eller almindelige kryptering).

Dette emne beskrives det, hvorfor og hvordan du kan oprette og konfigurere politikker for levering af aktiv.

>[AZURE.NOTE]Hvis du vil kunne bruge dynamiske emballagen og dynamiske kryptering, skal du sikre dig at have mindst én enhed for tidsskala (også kaldet streaming enhed). Du kan finde flere oplysninger, se, [hvordan skalere en medier-tjeneste](media-services-portal-manage-streaming-endpoints.md).
>
>Din aktiv skal også indeholde et sæt af tilpassede bithastighed MP4s eller tilpasset bithastighed udglattede Streaming filer.

Du kan anvende forskellige politikker til det samme aktiv. For eksempel kan du anvende PlayReady kryptering på udglattede Streaming og AES konvolut kryptering MPEG TANKESTREG og HLS. En hvilken som helst protokoller, som ikke er defineret i en politik for levering af (for eksempel du tilføje en enkelt politik, som kun angiver HLS som protokollen) blokeres fra streaming. Undtagelsen er, hvis du har ingen defineret overhovedet aktiv levering politik. Alle protokoller tillades derefter på Ryd.

Hvis du vil afholde et lager krypterede aktiv, skal du konfigurere aktivets levering politik. Før din aktiv streames, streaming serveren fjerner lagerplads kryptering og streamer indholdet ved hjælp af den angivne levering politik. Angiv for eksempel typen for at give dine aktiver, der er krypteret med avancerede kryptering AES (Standard) konvolut krypteringsnøgle til **DynamicEnvelopeEncryption**. For at fjerne lagerplads kryptering og streame aktivet i Ryd skal du angive typen til **NoDynamicEncryption**. Eksempler på, hvordan du konfigurerer disse politiktyper Følg.

Afhængigt af hvordan du konfigurerer politikken aktiv levering vil du ikke kunne dynamisk pakke, dynamisk kryptere og streame følgende streaming-protokoller: jævn Streaming, HLS, MPEG TANKESTREG og HD'ER.

Den nedenstående liste viser formaterne, at du bruger til at streame Udjævn, HLS, bindestreg og HD'ER.

Udjævne Streaming:

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG BINDESTREG

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

HD'ER

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Flere oplysninger om, hvordan du publicerer et aktiv og opbygge en streaming URL-adresse, under [oprette en streaming URL-adresse](media-services-deliver-streaming-content.md).

##<a name="considerations"></a>Overvejelser i forbindelse med

- Du kan ikke slette en AssetDeliveryPolicy, der er knyttet til et aktiv, mens en anmodet arbejdsproces (streaming) locator findes for den pågældende aktiv. Det anbefales, at fjerne politikken fra aktivet før du sletter politikken.
- En streaming locator kan ikke oprettes på en krypteret aktiv lagerplads, når ingen aktiv levering politik er indstillet.  Hvis aktivet ikke lagerplads, der er krypteret, vil systemet kan du oprette en locator og streame aktivet i Ryd uden en aktiv levering politik.
- Du kan have flere aktiv levering politikker, som er knyttet til en enkelt aktiv, men du kan kun angive en metode til at håndtere en given AssetDeliveryProtocol.  Hvilket betyder, at hvis du forsøger at sammenkæde to levering politikker, som angiver den AssetDeliveryProtocol.SmoothStreaming protokol, der medfører en fejl, fordi systemet ikke ved, hvilket operativsystem du vil det skal anvendes, når en klient gør en jævn Streaming anmodning.
- Hvis du har et aktiv med en eksisterende streaming locator, du kan ikke sammenkæde en ny politik for aktivet (du kan enten fjerne sammenkædningen af en eksisterende politik fra aktivet eller opdatere en politik for levering, der er knyttet til aktivet).  Du først skal fjerne streaming locator, justere politikkerne og Opret streaming locator igen.  Du kan bruge den samme locatorId, når du genskabe streaming locator, men du skal sikre dig, der ikke give problemer med for klienter, da indhold kan cachelagres af udgangspunktet eller et efterfølgende CDN.


##<a name="clear-asset-delivery-policy"></a>Ryd aktiv levering politik

Følgende **ConfigureClearAssetDeliveryPolicy** metode angiver ikke anvende dynamiske kryptering og til at levere strømmen i en af følgende protokoller: MPEG TANKESTREG, HLS og jævn Streaming protokoller. Du vil anvende denne politik skal skrives på dine aktiver lagerplads, der er krypteret.

Oplysninger om værdierne, kan du angive, når du opretter en AssetDeliveryPolicy finder du i afsnittet [typer bruges, når du definerer AssetDeliveryPolicy](#types) .

statisk offentlige ugyldig ConfigureClearAssetDeliveryPolicy (IAsset aktiv) {IAssetDeliveryPolicy politik = _context. AssetDeliveryPolicies.Create ("Ryd politikken", AssetDeliveryPolicyType.NoDynamicEncryption, AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

aktiv. DeliveryPolicies.Add(policy); }

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption aktiv levering politik


Følgende **CreateAssetDeliveryPolicy** metode opretter **AssetDeliveryPolicy** , der er konfigureret til at anvende dynamisk almindelige kryptering (**DynamicCommonEncryption**) til en jævn streaming protokol (andre protokoller, blokeres fra streaming). Metoden kræver to parametre: **aktiv** (aktivet, du vil anvende politikken levering) og **IContentKey** (indhold nøglen af typen **CommonEncryption** , du kan finde flere oplysninger, se: [oprette en indhold nøgle](media-services-dotnet-create-contentkey.md#common_contentkey)).

Oplysninger om værdierne, kan du angive, når du opretter en AssetDeliveryPolicy finder du i afsnittet [typer bruges, når du definerer AssetDeliveryPolicy](#types) .


statisk offentlige ugyldig CreateAssetDeliveryPolicy (IAsset aktiv, IContentKey nøgle) {Uri acquisitionUrl = nøgle. GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

Ordbog < AssetDeliveryPolicyConfigurationKey, streng > assetDeliveryPolicyConfiguration = nye ordbog < AssetDeliveryPolicyConfigurationKey, streng > {{AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()}};

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }

Azure Media Services gør det også muligt at tilføje Widevine kryptering. I følgende eksempel vises både PlayReady og Widevine blive føjet til politikken aktiv levering.


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
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

>[AZURE.NOTE]Når du krypterer med Widevine, vil du kun skal kunne levere ved hjælp af bindestreg. Sørg for at angive TANKESTREG i aktiv levering protokollen.


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption aktiv levering politik 

Følgende **CreateAssetDeliveryPolicy** metode opretter **AssetDeliveryPolicy** , der er konfigureret til at anvende dynamisk konvolut kryptering (**DynamicEnvelopeEncryption**) til protokoller udglattede Streaming, HLS og TANKESTREG (Hvis du beslutter at ikke angiver nogle protokoller, de blokeres fra streaming). Metoden kræver to parametre: **aktiv** (aktivet, du vil anvende politikken levering) og **IContentKey** (indhold nøglen af typen **EnvelopeEncryption** , du kan finde flere oplysninger, se: [oprette en indhold nøgle](media-services-dotnet-create-contentkey.md#envelope_contentkey)).


Oplysninger om værdierne, kan du angive, når du opretter en AssetDeliveryPolicy finder du i afsnittet [typer bruges, når du definerer AssetDeliveryPolicy](#types) .   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
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
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>Datatyper, der bruges, når du definerer AssetDeliveryPolicy

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


