<properties 
    pageTitle="Beskytte oversigt over celleindhold | Microsoft Azure" 
    description="I denne artikel giver et overblik over indholdsbeskyttelse med Media Services." 
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

#<a name="protecting-content-overview"></a>Oversigt over beskytte indhold


Microsoft Azure Media Services giver dig mulighed at sikre dine medier fra når den forlader computeren via lagerplads, behandling og levering. Media Services gør det muligt at levere dit direkte og on demand indhold, der er krypteret dynamisk med avancerede kryptering AES (Standard) (ved hjælp af 128-bit krypteringsnøgler) eller en af de vigtigste DRMs: Microsoft PlayReady, Google Widevine og Apple FairPlay. Media Services indeholder også en tjeneste til at levere AES-nøgler og DRM (PlayReady, Widevine og FairPlay) licenser til autoriserede klienter. 

Følgende billede viser de indholdsbeskyttelse arbejdsprocesser, der understøtter AMS. 

![Beskytte med PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[AZURE.NOTE]Du skal have mindst én streaming reserveret enhed på streaming slutpunktet hvorfra du vil streame krypteret indhold, hvis du vil kunne bruge dynamiske kryptering.

Dette emne forklares [begreber og terminologi](media-services-content-protection-overview.md) relevante at forstå indholdsbeskyttelse med AMS. Emnet indeholder også [links](media-services-content-protection-overview.md#common-scenarios) til emner, der viser, hvordan du opnå indholdsbeskyttelse opgaver. 

##<a name="dynamic-encryption"></a>Dynamisk kryptering

Microsoft Azure Media Services gør det muligt at levere dit indhold, der er krypteret dynamisk AES Ryd vigtige eller DRM kryptering: Microsoft PlayReady, Google Widevine og Apple FairPlay.

I øjeblikket, du kan kryptere streaming følgende formater: HLS, MPEG TANKESTREG og jævn Streaming. Du kan ikke kryptere HD'ER streaming format, eller progressive henter.

Hvis du vil til Media Services til at kryptere et aktiv, skal du knytte en krypteringsnøgle (CommonEncryption eller EnvelopeEncryption) til din aktiv og også konfigurere politikker for godkendelse for nøglen.

Du skal også konfigurere aktivets levering politik. Hvis du vil streame en krypteret aktiv lagerplads, skal du sørge for at angive, hvordan du vil holde det ved at konfigurere aktiv levering politik.

Når der anmodes om en stream ved en afspiller, bruger Media Services den angivne nøgle til at kryptere dynamisk din indhold ved hjælp af AES Ryd vigtige eller DRM kryptering. Hvis du vil dekryptere strømmen, afspilleren anmoder om tasten fra de vigtigste leveringstjenesten. For at afgøre, om brugeren har tilladelse til at hente nøglen, evaluerer tjenesten de politikker, der tilladelse, du har angivet for nøglen.

>[AZURE.NOTE]For at kunne udnytte dynamiske kryptering, skal du først få mindst én efter behov streaming enhed for streaming slutpunktet, som du planlægger at levering krypterede indholdet. Se, [hvordan du skala Media Services](media-services-portal-manage-streaming-endpoints.md)kan finde flere oplysninger.

##<a name="storage-encryption"></a>Lagerplads kryptering

Bruge lagerplads kryptering til at kryptere Ryd indholdet lokalt ved hjælp af AES 256 bit kryptering og Overfør det derefter til Azure lager, hvor det er gemt, krypteret på resten. Aktiver, der er beskyttet med lagerplads kryptering er automatisk uden kryptering og placeret i en krypteret filsystem før kodning, og du kan også igen krypteret før uploade tilbage som et nyt output aktiv. Den primære use case for lagerplads kryptering er, når du vil sikre høj kvalitet input mediefilerne med stærk kryptering på resten på disk.

Hvis du vil afholde et lager krypterede aktiv, skal du konfigurere aktivets levering politik, så Media Services ved, hvordan du vil vise dit indhold. Før din aktiv streames, streaming serveren fjerner lagerplads kryptering og streamer indholdet ved hjælp af den angivne levering politik (for eksempel AES, almindelige kryptering eller ingen kryptering).

## <a name="common-encryption-cenc"></a>Almindelige kryptering (CENC)

Almindelige kryptering bruges, når du krypterer indholdet med PlayReady eller / og Widewine.

## <a name="using-cbcs-aapl-encryption"></a>Ved hjælp af cbcs aapl kryptering

Cbcs aapl bruges, når du krypterer dit indhold med FairPlay.

## <a name="envelope-encryption"></a>Konvolut kryptering 

Brug denne indstilling, hvis du vil beskytte dit indhold med AES-128 Ryd nøgle. Hvis du vil valgmuligheden mere sikker, skal du vælge en af DRMs er angivet i dette emne. 

##<a name="licenses-and-keys-delivery-service"></a>Tjenesten til levering af licenser og nøgler

Media Services indeholder en tjeneste til at levere DRM (PlayReady, Widevine, FairPlay) licenser og AES fjerne taster til autoriserede klienter. Du kan bruge [portalen Azure](media-services-portal-protect-content.md), REST-API eller Media Services SDK til .NET godkendelse og godkendelse politikkerne for din licenser og nøgler skal konfigureres.

##<a name="token-restriction"></a>Token begrænsning

Politikken indhold vigtige tilladelse kan har en eller flere godkendelse begrænsninger: åbne eller token begrænsning. Politikken token begrænset skal være ledsaget af et token, der er udstedt af et Token STS (Secure Service). Media Services understøtter tokens i formatet enkel Web Tokens (SWT) og formatet JSON Web Token (JWT). Media Services giver ikke Secure Token Services. Du kan oprette en brugerdefineret STS eller udnytte Microsoft Azure ACS til problem tokens. STS skal konfigureres til at oprette et token, der er signeret med de angivne nøgle og problem krav, du har angivet i konfigurationen token begrænsning. Tjenesten Media Services vigtige levering returnerer det ønskede nøgle (eller den licens) til klienten Hvis Tokenet er gyldige og krav i token matcher dem, der er konfigureret for nøgle (eller licens).

Når konfigurere tokenet begrænsede politik, skal du angive den primære bekræftelse nøgle, udsteder og målgruppe parametre. Tasten primære bekræftelse indeholder nøglen, som er blevet signeret tokenet med, udsteder er det sikkert token tjeneste, som problemer tokenet. Deltagerne (også kaldet omfang) i denne artikel beskrives formålet med tokenet eller ressourcen tokenet tillader adgang til. Tjenesten Media Services vigtige levering valideret, at disse værdier i tokenet matcher værdierne i skabelonen.

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

##<a name="common-scenarios"></a>Almindelige scenarier

De følgende emner viser, hvordan du beskytte indhold i lagerplads, levere dynamisk krypterede streaming-medier, bruge tjenesten til levering af AMS nøgle/licens

- [Beskytte med AES](media-services-protect-with-aes128.md) 
- [Beskytte med PlayReady og/eller Widevine](media-services-protect-with-drm.md)
- [Streame dine HLS indhold beskyttet med Apple FairPlay og/eller PlayReady](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>Ekstra scenarier

- Se, [hvordan du integrerer tjenesten Azure PlayReady licenser med din egen kryptering/streaming server](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).
- [Brug af castLabs til at levere DRM licenser til Azure Media Services](media-services-castlabs-integration.md)
 
##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Relaterede Links

[Præsentation af PlayReady som en tjeneste og dynamiske AES-kryptering med Azure Media Services](http://mingfeiy.com/playready)

[Azure Media Services PlayReady licens levering priser forklaring](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Sådan foretages fejlfinding for AES krypterede stream i Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT token authenitcation](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrere Azure Media Services OWIN MVC baseret app med Azure Active Directory og begrænse baseret på JWT krav vigtige levering af indhold](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Brug Azure ACS til problem tokens](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
