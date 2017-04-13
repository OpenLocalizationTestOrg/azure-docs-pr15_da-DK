<properties 
    pageTitle="Oprette ContentKeys med .NET" 
    description="Lær at oprette indhold nøgler, som giver sikker adgang til aktiver." 
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
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="create-contentkeys-with-net"></a>Oprette ContentKeys med .NET

> [AZURE.SELECTOR]
- [RESTEN](media-services-rest-create-contentkey.md)
- [.NET](media-services-dotnet-create-contentkey.md)

Media Services gør det muligt at oprette og levere krypteret aktiver. En **ContentKey** giver sikker adgang til dine **Aktiver**s. 

Når du opretter et nyt aktiv (for eksempel, før du [overfører filer](media-services-dotnet-upload-files.md)), kan du angive de følgende krypteringsindstillinger: **StorageEncrypted**, **CommonEncryptionProtected**eller **EnvelopeEncryptionProtected**. 

Når du leverer Aktiver til dine kunder, kan du [konfigurere til aktiver skal krypteres dynamisk](media-services-dotnet-configure-asset-delivery-policy.md) med en af de følgende to kryptering af administration: **DynamicEnvelopeEncryption** eller **DynamicCommonEncryption**.

Krypteret aktiver skal være knyttet til **ContentKey**s. I denne artikel beskrives, hvordan du opretter en indhold nøgle.

>[AZURE.NOTE] Når du opretter et nyt **StorageEncrypted** aktiv ved hjælp af Media Services .NET SDK, oprettes og er sammenkædet med aktivet **ContentKey** automatisk.

##<a name="contentkeytype"></a>ContentKeyType

Oprette en af værdierne, skal du angive, hvornår en indhold nøgle er den vigtige indholdstype. Vælg en af følgende værdier. 

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

##<a id="envelope_contentkey"></a>Oprette konvolut type ContentKey

Følgende kodestykke opretter en indhold nøgle af typen konvolut kryptering. Det derefter knytter nøglen til den angivne aktiv.

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

        asset.ContentKeys.Add(key);

        return key;
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

opkald

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>Oprette udbredte type ContentKey    

Følgende kodestykke opretter en indhold nøgle af krypteringstypen almindelige. Det derefter knytter nøglen til den angivne aktiv.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
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
opkald

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
