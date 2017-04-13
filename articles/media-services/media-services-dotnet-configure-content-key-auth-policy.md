<properties 
    pageTitle="Konfigurere godkendelse af indhold vigtige politik ved hjælp af Media Services .NET SDK | Microsoft Azure" 
    description="Lær, hvordan du konfigurerer en politik for en indhold nøgle ved hjælp af Media Services .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;mingfeiy"/>



# <a name="dynamic-encryption-configure-content-key-authorization-policy"></a>Dynamisk kryptering: konfigurere indhold vigtige politik

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

##<a name="overview"></a>Oversigt

Microsoft Azure Media Services gør det muligt at levere MPEG-STREG, bløde Streaming og HTTP Live Streaming (HLS) streams, der er beskyttet med avancerede kryptering AES (Standard) (ved hjælp af 128-bit krypteringsnøgler) eller [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS gør det også muligt at levere TANKESTREG streams krypteret med Widevine DRM. Både PlayReady og Widevine krypteres per specifikationen almindelige kryptering (ISO/IEC 23001-7 CENC).

Media Services indeholder også en **Tjeneste til levering af tasten/licens** , som kan kunder få AES-nøgler eller PlayReady/Widevine licenser til at afspille den krypterede indhold.

Hvis du vil til Media Services til at kryptere et aktiv, skal du knytte en krypteringsnøgle (**CommonEncryption** eller **EnvelopeEncryption**) til aktiv (som beskrevet [her](media-services-dotnet-create-contentkey.md)) og også konfigurere politikker for godkendelse for nøglen (som beskrevet i denne artikel).

Når der anmodes om en stream ved en afspiller, bruger Media Services den angivne nøgle til at kryptere dynamisk indholdet ved hjælp af AES eller DRM kryptering. Hvis du vil dekryptere strømmen, afspilleren anmoder om tasten fra de vigtigste leveringstjenesten. For at afgøre, om brugeren har tilladelse til at hente nøglen, evaluerer tjenesten de politikker, der tilladelse, du har angivet for nøglen.

Media Services understøtter flere måder at godkendelse af brugere, der foretage vigtige anmodninger. Politikken indhold vigtige tilladelse kan har en eller flere godkendelse begrænsninger: **åbne** eller **token** begrænsning. Politikken token begrænset skal være ledsaget af et token, der er udstedt af et Token STS (Secure Service). Media Services understøtter tokens i formatet **Enkel Web Tokens** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) og formatet **JSON Web Token** ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)).

Media Services giver ikke Secure Token Services. Du kan oprette en brugerdefineret STS eller udnytte Microsoft Azure ACS til problem tokens. STS skal være konfigureret til at oprette et token, der er signeret med de angivne nøgle og problem krav, du har specificeret i konfigurationen af sikkerhedstoken begrænsning (som beskrevet i denne artikel). Tjenesten Media Services vigtige levering returnerer krypteringsnøglen til klienten, hvis Tokenet er gyldig og krav i tokenet til dem, der er konfigureret til tasten indhold.

Finde flere oplysninger

[JWT token authenitcation](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrere Azure Media Services OWIN MVC baseret app med Azure Active Directory og begrænse baseret på JWT krav vigtige levering af indhold](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Brug Azure ACS til problem tokens](http://mingfeiy.com/acs-with-key-services).

###<a name="some-considerations-apply"></a>Nogle overvejelser gælder:

- Hvis du vil kunne bruge dynamiske emballagen og dynamiske kryptering, skal du sikre dig at have mindst én streaming reserveret enhed. Du kan finde flere oplysninger, se, [hvordan skalere en medier-tjeneste](media-services-portal-manage-streaming-endpoints.md).
- Din aktiv skal indeholde et sæt af tilpassede bithastighed MP4s eller tilpasset bithastighed udglattede Streaming filer. Du kan finde flere oplysninger [kodes et aktiv](media-services-encode-asset.md).
- Overfør og kode dine aktiver ved hjælp af **AssetCreationOptions.StorageEncrypted** indstillingen.
- Hvis du vil have flere indhold taster, der kræver samme politik konfiguration, er det anbefales at oprette en enkelt tilladelse politik og genbruge den med flere indhold taster.
- Tjenesten nøgle gemmer ContentKeyAuthorizationPolicy og dens relaterede objekter (indstillinger for politik og begrænsninger) i 15 minutter.  Hvis du opretter en ContentKeyAuthorizationPolicy og angive, at en "Token" begrænsning, test, og derefter opdatere politikken "Åbn" begrænsning, tager det omkring 15 minutter, før politikken skifter til den "Åbn" version af politikken.
- Hvis du tilføjer eller opdaterer din aktiv levering politik, skal du slette en eksisterende locator (hvis relevant) og oprette en ny locator.
- I øjeblikket, du ikke kan kryptere HD'ER streaming format, eller progressive henter.


##<a name="aes-128-dynamic-encryption"></a>AES-128 dynamiske kryptering

###<a name="open-restriction"></a>Åbn begrænsning

Åbn begrænsning betyder, at systemet vil køre tasten for alle, der gør en anmodning om vigtige. Denne begrænsning kan være nyttige til testformål.

I følgende eksempel opretter en politik for åben godkendelse og føjer den til tasten indhold.

statisk offentlige ugyldig AddOpenAuthorizationPolicy (IContentKey contentKey) {/ / oprette ContentKeyAuthorizationPolicy med Åbn begrænsninger / / og oprette politik IContentKeyAuthorizationPolicy politik = _context.
ContentKeyAuthorizationPolicies.
CreateAsync ("åben godkendelse politik"). Resultat

Listen<ContentKeyAuthorizationPolicyRestriction> begrænsninger = ny liste<ContentKeyAuthorizationPolicyRestriction>();
    
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


###<a name="token-restriction"></a>Token begrænsning

I dette afsnit beskrives, hvordan du opretter en politik for indhold vigtige godkendelse og knytte den til tasten indhold. Politikken godkendelse beskriver, hvilke betingelserne for tilladelse skal være opfyldt for at afgøre, hvis brugeren har tilladelse til at modtage tasten (for eksempel, bruges til listen "bekræftelse tast" indeholder nøglen, som er blevet signeret tokenet med).

Hvis du vil konfigurere indstillingen token begrænsning, skal du bruge en XML-streng til at beskrive betingelserne for det token tilladelse. Token begrænsning konfigurationen XML-skal opfylde følgende XML-skemaet.

####<a id="schema"></a>Token begrænsning skema
    
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>

Når konfiguration af **token** begrænsede politik, skal du angive de primære** bekræftelse nøgle**, **udsteder** og **Målgruppe** parametre. Den **primære bekræftelse nøgle **indeholder nøglen, som er blevet signeret tokenet med, **udsteder** er det sikkert token tjeneste, som problemer tokenet. For **publikum** (også kaldet **omfang**) i denne artikel beskrives formålet med tokenet eller ressourcen tokenet tillader adgang til. Tjenesten Media Services vigtige levering valideret, at disse værdier i tokenet matcher værdierne i skabelonen. 

Når du bruger **Media Services SDK til .NET**, kan du bruge klassen **TokenRestrictionTemplate** til at generere begrænsning tokenet.
I følgende eksempel oprettes en politik med en token begrænsning. I dette eksempel klienten ville have til at præsentere et token, der indeholder: logge nøgle (VerificationKey), en token udsteder og nødvendige krav.
    
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

####<a id="test"></a>Test token

For at få et test-token, der er baseret på token begrænsning, der blev brugt til politikken vigtige godkendelse skal du gøre følgende.
    
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
    
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();


##<a name="playready-dynamic-encryption"></a>PlayReady dynamisk kryptering 

Media Services kan du konfigurere rettigheder og begrænsninger, som du vil til PlayReady DRM runtime gennemtvinge, når en bruger forsøger at afspille beskyttet indhold. 

Når du beskytter dit indhold med PlayReady, er en af de ting, du skal angive i din politik en XML-streng, der definerer [PlayReady licens skabelon](media-services-playready-license-template-overview.md). I Media Services SDK til .NET hjælper **PlayReadyLicenseResponseTemplate** og **PlayReadyLicenseTemplate** klasser dig med at definere skabelonen PlayReady licens.

[Dette emne](media-services-protect-with-drm.md) viser, hvordan du krypterer dit indhold med **PlayReady** og **Widevine**.

###<a name="open-restriction"></a>Åbn begrænsning
    
Åbn begrænsning betyder systemet leverer nøglen til alle, der gør en anmodning om vigtige. Denne begrænsning kan være nyttige til testformål.

I følgende eksempel opretter en politik for åben godkendelse og føjer den til tasten indhold.

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
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
    
    
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }

###<a name="token-restriction"></a>Token begrænsning

Hvis du vil konfigurere indstillingen token begrænsning, skal du bruge en XML-streng til at beskrive betingelserne for det token tilladelse. Token begrænsning konfigurationen XML skal overholde XML-skemaet vises i [dette](#schema) afsnit.
    
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();
    
        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
                
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
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


Hvis du vil have en test-token, der er baseret på den token begrænsning, der blev brugt til vigtige tilladelsen se politik [i dette](#test) afsnit. 

##<a id="types"></a>Datatyper, der bruges, når du definerer ContentKeyAuthorizationPolicy

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

###<a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Næste trin
Nu hvor du har konfigureret indhold nøgle politik, kan du gå til emnet [Sådan konfigureres aktiv levering politik](media-services-dotnet-configure-asset-delivery-policy.md) .
 
