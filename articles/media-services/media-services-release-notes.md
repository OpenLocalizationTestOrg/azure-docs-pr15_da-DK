<properties 
    pageTitle="Media Services produktbemærkninger | Microsoft Azure" 
    description="Media Services produktbemærkninger" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="media" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako"/>

# <a name="azure-media-services-release-notes"></a>Azure Media Services produktbemærkninger

Disse produktbemærkninger opsummere ændringer fra tidligere versioner og kendte problemer.

>[AZURE.NOTE] Vi vil gerne høre fra vores kunder og fokusere på løse problemer, der påvirker du. Rapportere problemer eller stille spørgsmål, skal du skrive i [Azure Media Services MSDN-Forum].


##<a id="issues"></a>Aktuelt kendte problemer

### <a id="general_issues"></a>Media Services generelt problemer

Problem|Beskrivelse
---|---
Flere almindelige HTTP-headere findes ikke i REST-API.|Hvis du udvikler Media Services-programmer ved hjælp af REST-API, du synes, at nogle almindelige felter i brevhoveder HTTP (herunder klient-anmodning-ID, anmodning-ID, og vend tilbage-klient-anmodning-ID) understøttes ikke. Overskrifterne tilføjes i en senere opdatering.
Procent-kodning er ikke tilladt.|Media Services bruger værdien i egenskaben IAssetFile.Name, når du opbygger en URL-adresser for streaming indholdet (for eksempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Derfor er procent-kodning ikke tilladt. Værdien af egenskaben **navn** må ikke være følgende [procent-kodning-reserveret tegn](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Desuden kan kun der en "." for filtypenavnet.
Metoden ListBlobs, der er en del af Azure lagerplads SDK version 3.x opstår.|Media Services genererer SAS URL-adresser, der er baseret på [2012-02-12](http://msdn.microsoft.com/library/azure/dn592123.aspx) -versionen. Hvis du vil bruge Azure lagerplads SDK til listen BLOB i en blob beholder, bruge metoden [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) , der er en del af Azure lagerplads SDK version 2.x. Metoden ListBlobs, der er en del af versionen af Azure lagerplads SDK 3.x mislykkes.
Media Services, throttling ordning begrænser visningen Ressourceforbrug for programmer, der gør unødvendig anmodning til tjenesten. Tjenesten kan returnere tjenesten er ikke tilgængelig (503) HTTP-statuskode.|Se beskrivelsen af den 503 HTTP-statuskode i emnet [Azure Media Services fejlkoder](http://msdn.microsoft.com/library/azure/dn168949.aspx) kan finde flere oplysninger.
Når forespørgsler enheder, er der en grænse på 1000 objekter, der returneres ad gangen, fordi offentlige RESTEN v2 begrænser forespørgselsresultater 1000 resultater. | Du skal bruge **Spring over** og **tage** (.NET) / **toppen** (RESTEN) som beskrevet i [dette eksempel .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) og [eksemplet REST-API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
Nogle klienter kan komme på tværs af et problem med Gentag mærke i manifestet udglattede Streaming.|Du kan finde yderligere oplysninger finder [i dette](media-services-deliver-content-overview.md#known-issues) afsnit.
Azure Media Services .NET SDK objekter kan ikke serialiseres og derfor fungerer ikke med Azure cachelagring.|Hvis du forsøger at sekventielt SDK AssetCollection objektet for at føje det til Azure cachelagring, der genereres en undtagelse.
Kodning job mislykkes med en meddelelse "fase: DownloadFile. Kode: System.NullReferenceException ".|Typisk kodning arbejdsprocessen er at overføre input video filer til et input aktiv og sende et eller flere kodning job for pågældende input anlægsaktiver uden yderligere at ændre, indtast aktiv. Men hvis du ændrer input aktivet (for eksempel ved at tilføje/slette/omdøbe filer i aktivet), derefter efterfølgende job mislykkes muligvis med en DownloadFile fejl. Løsningen er at slette input aktiv, og Overfør input filer til et nyt aktiv igen. 

##<a id="rest_version_history"></a>REST-API versionshistorik

Du kan finde oplysninger om versionshistorik Media Services REST-API [Azure Media Services RESTEN API Reference].

##<a id="july_changes16"></a>Juli 2016 version

###<a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>Opdateringer til manifestet fil (*. ISM) genereres af kodning opgaver

Når en kodning opgave er sendt til Media Encoder Standard eller Azure Media Encoder, kodning opgaven genererer en [streaming manifestfilen](media-services-deliver-content-overview.md) (* .ism) fil i output aktiv. Syntaksen for denne streaming manifestfilen er blevet opdateret med den seneste service release.

>[AZURE.NOTE]Syntaksen for filen streaming manifestet (.ism) er reserveret til intern brug og kan ændres i fremtidige versioner. Kan du ikke at ændre eller redigere indholdet af denne fil.

###<a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>En ny klient manifestet (*. ISMC)-fil er oprettet i outputtet aktiv, når en opgave i en kodning skriver en eller flere MP4-filer

Starte med den seneste service release, efter at færdiggøre en kodning opgave, som genererer en flere MP4-filer, output indeholder aktiv også en streaming klient manifestet (*.ismc)-fil. Filen .ismc hjælper med at forbedre ydeevnen for dynamisk streaming. 

>[AZURE.NOTE]Syntaksen i filen klient manifestet (.ismc) er reserveret til intern brug og kan ændres i fremtidige versioner. Kan du ikke at ændre eller redigere indholdet af denne fil.

Du kan finde yderligere oplysninger finder [denne](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) blog.

### <a name="known-issues"></a>Kendte problemer

Nogle klienter kan komme aross et problem med Gentag mærke i manifestet udglattede Streaming. Du kan finde yderligere oplysninger finder [i dette](media-services-deliver-content-overview.md#known-issues) afsnit.

##<a id="apr_changes16"></a>April 2016 version

### <a name="azure-media-analytics"></a>Azure Media Analytics

Azure Media Servces introduceret Azure Media Analytics til effektive video intelligence. Oplysninger, skal du se [Oversigt over Analytics Azure Media Services](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (Preview)

Azure Media Services kan du nu dynamisk kryptere din HTTP Live Streaming (HLS) indhold med Apple FairPlay. Du kan også bruge tjenesten til levering af AMS licens til at holde FairPlay licenser for klienter. Yderligere oplysninger finder du i [Brug Azure Media Services til at streame dine HLS indhold beskyttet med Apple FairPlay ](media-services-protect-hls-with-fairplay.md).
  
##<a id="feb_changes16"></a>Februar 2016 version

Den seneste version af Azure Media Services SDK til .NET (3.5.3) indeholder en relateret rettelse Widevine. Problemet var: AssetDeliveryPolicy kunne ikke genbruges til flere aktiver krypteret med Widevine. Som en del af denne rettelse følgende egenskab blev føjet til SDK: **WidevineBaseLicenseAcquisitionUrl**.
    
    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
        
    };

##<a id="jan_changes_16"></a>Januar 2016 version

Kodning reserveret enheder omdøbt for at reducere forvirring med Encoder navne.

Basic, som Standard og Premium kodning reserveret enhederne er omdøbt til S1, S2, og S3 reserveret enheder, henholdsvis.  Kunder, der bruger grundlæggende kodning RUs i dag vises S1 som etiketten Azure-portalen (og på faktura,), mens Standard og Premium får vist etiketterne S2 og S3 henholdsvis. 

##<a id="dec_changes_15"></a>December 2015 version

Azure SDK teamet udgives en ny version af [Azure SDK til PHP](http://github.com/Azure/azure-sdk-for-php) -pakken, der indeholder opdateringer og nye funktioner til Microsoft Azure Media Services. Azure Media Services SDK til PHP understøtter især nu de nyeste funktioner [indholdsbeskyttelse](media-services-content-protection-overview.md) : dynamiske kryptering med AES og DRM (PlayReady og Widevine) med og uden Token begrænsning. Det understøtter også skalering [Kodning enheder](media-services-dotnet-encoding-units.md).

Du kan finde flere oplysninger i:

- [Microsoft Azure Media Services SDK til PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) blog.
- I følgende [kodeeksempler](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) til at komme hurtigt i gang:
    - **vodworkflow_aes.php**: Dette er en PHP-fil, der viser, hvordan du bruger AES-128 dynamiske kryptering og nøgle Leveringstjenesten. Det er baseret på den .NET-eksempel, der er beskrevet i [denne](media-services-protect-with-aes128.md) artikel.
    - **vodworkflow_aes.php**: Dette er en PHP-fil, der viser, hvordan du bruger PlayReady dynamiske kryptering og licens Leveringstjenesten. Det er baseret på den .NET-eksempel, der er beskrevet i [denne](media-services-protect-with-drm.md) artikel.
    - **scale_encoding_units.php**: Dette er en PHP-fil, der viser, hvordan du kan skalere kodning reserveret enhed.


##<a id="nov_changes_15"></a>November 2015 version

Azure Media Services tilbyder nu Google Widevine licens leveringstjenesten i skyen. Få mere at vide ved at referere til [denne meddelelse blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Se også [selvstudiet](media-services-protect-with-drm.md) og [GitHub lager](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Bemærk, at Widevine licens levering tjenesterne Azure Media Services i Vis udskrift. Finde flere oplysninger i [denne blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

##<a id="oct_changes_15"></a>Oktober 2015 version

Azure Media Services (AMS) er nu direkte i de følgende datacentre: Brasilien Syd, Indien vest, Indien syd og Indien Central. Du kan nu bruge Azure klassisk Portal til at [oprette medier tjenestekonti](media-services-portal-create-account.md) og udføre forskellige opgaver, der er beskrevet [her](https://azure.microsoft.com/documentation/services/media-services/). Live kodning er dog ikke aktiveret i disse datacentre. Yderligere, findes ikke i alle typer kode reserveret enheder i disse datacentre.

- Brasilien Syd: Kun Standard og grundlæggende kodning reserveret enheder er tilgængelige
- Indien vest, Indien syd og Indien Central: kun grundlæggende kodning reserveret enheder er tilgængelige


##<a id="september_changes_15"></a>September 2015 version 

- AMS tilbyder nu mulighed for at beskytte både Video-On-Demand (VOD) og direkte Streams med Widevine modul DRM teknologi. Du kan bruge følgende levering servicepartnere kan hjælpe dig med at levere Widevine licenser: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) [castLabs](http://castlabs.com/company/partners/azure/). Du kan finde yderligere oplysninger finder [denne blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).

    Du kan bruge [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (begyndende med versionen 3.5.1 til) eller REST-API til at konfigurere din AssetDeliveryConfiguration for at bruge Widevine.  

- AMS tilføjet understøttelse af Apple ProRes videoer. Nu kan du overføre filerne QuickTime kilde videoer, der bruger Apple ProRes eller andre codecs. Du kan finde yderligere oplysninger finder [denne blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).

- Du kan nu bruge Media Encoder Standard til udtrækning af underordnede skærmklip og live arkiv. Du kan finde yderligere oplysninger finder [denne blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

- De følgende opdateringer til filtrering foretaget: 

    - Du kan nu bruge Apple HTTP Live Streaming (HLS) format med kun lyd filter. Denne opdatering gør det muligt at fjerne kun med lyd Registrer ved at angive (kun med lyd = false) i URL-adressen.
    - Når du definerer filtre for dine aktiver, du har mulighed for at kombinere flere nu (op til 3) filtre i en enkelt URL-adresse.

    Finde flere oplysninger i [denne](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

- AMS understøtter nu jeg-rammer i HLS v4. Jeg-ramme support optimerer hurtig frem- og spole tilbage handlinger. Som standard medtage alle HLS v4 output jeg-ramme afspilningsliste (EXT-X-I-FRAME-STREAM-INF).
 
    Finde flere oplysninger i [denne](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

##<a id="august_changes_15"></a>August 2015 version

- Azure Media Services SDK til Java V0.8.0 version og de nye prøver er nu tilgængelig. Du kan finde flere oplysninger i:

    - [Blogindlæg](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
    - [Java eksempler lager](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- Azure Media Player opdatering med understøttelse af flere lydstreamet. Du kan finde flere oplysninger i:
    - [Blogindlæg](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>Juli 2015 version

- Præsentation af generelle tilgængeligheden af Media Encoder Standard. Se [dette blogindlæg](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)kan finde flere oplysninger.

    Media Encoder Standard bruger forudindstillinger, der er beskrevet i [dette](http://go.microsoft.com/fwlink/?LinkId=618336) afsnit. Bemærk, at når ved hjælp af en forudindstillet til 4k koder, skal du få enhedstype **Premium** reserveret. Se, [hvordan du skala kodning](media-services-scale-media-processing-overview.md)kan finde flere oplysninger.
- Live realtid billedtekster med Azure Media Services og Player. Du kan finde flere oplysninger, se [dette blogindlæg](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

###<a name="media-services-net-sdk-updates"></a>Media Services .NET SDK opdateringer

Azure Media Services .NET SDK er nu version 3.4.0.0. Følgende funktioner er tilføjet i denne version:  

- Implementeret understøttelse af live arkiv. Bemærk, at du ikke kan hente et aktiv, der indeholder en direkte arkiv.
- Implementeret understøttelse af dynamiske filtre.
- Implementeret funktionalitet, så brugerne kan holde objektbeholder til lagring under sletning af aktiv.
- Fejlrettelser relateret til prøv igen politikker i kanaler.
- Aktiveret **Media Encoder Premium arbejdsproces**.

##<a id="june_changes_15"></a>Juni 2015 version

###<a name="media-services-net-sdk-updates"></a>Media Services .NET SDK opdateringer

Azure Media Services .NET SDK er nu version 3.3.0.0. Følgende funktioner er tilføjet i denne version:  

- understøttelse af OpenId forbinde registrering specifikation
- understøttelse af håndtering af taster overgang identitet udbyder side. 

Hvis du bruger en identitetsudbyder, som viser OpenID forbinde discovery-dokumentet (som udbyderne af følgende: Azure Active Directory, Google, Salesforce), kan du bede Azure Media Services til at hente signerende taster til validering af JWT token fra OpenID forbinde registrering specifikation. 

Du kan finde yderligere oplysninger finder [Ved hjælp af Json Web nøgler fra OpenID forbinde registrering specifikation til at arbejde med JWT token godkendelse i Azure Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).


##<a id="may_changes_15"></a>Maj 2015 version

Præsentation af følgende nye funktioner:

- [Et eksempel på Live kodning med Media Services](media-services-manage-live-encoder-enabled-channels.md)
- [Dynamisk manifest](media-services-dynamic-manifest-overview.md)
- [Et eksempel på Azure Media Hyperlapse medier processor](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>April 2015 version

 ###<a name="general-media-services-updates"></a>Generelt Media Services opdateringer

- [Præsentation af Azure Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
- Starte med Media Services RESTEN 2.10 kanaler, der er konfigureret til at indtager en RTMP protokol, er oprettet med primær og sekundær indtager URL-adresser. Du kan finde flere oplysninger, se [kanal indtager konfigurationer](media-services-live-streaming-with-onprem-encoders.md#channel_input)
- Azure Media indekseringsprogram opdateringer
- Understøttelse af spansk sprog
- Ny konfiguration XML-format

Finde flere oplysninger i [denne blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).
###<a name="media-services-net-sdk-updates"></a>Media Services .NET SDK opdateringer

Azure Media Services .NET SDK er nu version 3.2.0.0.

Følgende er nogle af kunden modstående opdateringer:

- **Bryd Skift**: ændret **TokenRestrictionTemplate.Issuer** og **TokenRestrictionTemplate.Audience** skal være af strengtypen.
- Opdateringer, der relaterer til at oprette brugerdefinerede gentagne politikker.
- Fejlrettelser relateret til overførsel af/overførsel af filer.
- Klassen **MediaServicesCredentials** accepterer nu primære og sekundære access kontrolelement slutpunkt til godkendelse mod.



##<a id="march_changes_15"></a>Marts 2015 version

### <a name="general-media-services-updates"></a>Generelt Media Services opdateringer

- Media Services indeholder nu Azure CDN integration. Egenskaben **CdnEnabled** blev føjet til **StreamingEndpoint**for at understøtte integrationen.  **CdnEnabled** kan bruges med REST API'er fra og med version 2.9 (få flere oplysninger under [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)).  **CdnEnabled** kan bruges med .NET SDK fra og med version 3.1.0.2 (få flere oplysninger under [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)).
- Meddelelse på **Media Encoder Premium arbejdsproces**. Du kan finde flere oplysninger, kan du se [Introduktion til Premium-kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).
 


##<a id="february_changes_15"></a>Februar 2015 version

### <a name="general-media-services-updates"></a>Generelt Media Services opdateringer

Media Services REST-API er nu version 2.9. Start med denne version, og kan du aktivere Azure CDN integrationen med streaming slutpunkter. Du kan finde flere oplysninger [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

##<a id="january_changes_15"></a>Januar 2015 version

### <a name="general-media-services-updates"></a>Generelt Media Services opdateringer

Meddelelse af generelle tilgængelighed (GA) af indholdsbeskyttelse med dynamisk kryptering. Se [Azure Media Services forbedrer streaming sikkerhed med generelle tilgængelighed af DRM teknologi](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/)kan finde flere oplysninger.

###<a name="media-services-net-sdk-updates"></a>Media Services .NET SDK opdateringer

Azure Media Services .NET SDK er nu version 3.1.0.1.

Denne version markeret Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate Standardkonstruktøren som forældet. Den nye parametre tager TokenType som argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>December 2014-udgivelsen

###<a name="general-media-services-updates"></a>Generelt Media Services opdateringer

- Nogle opdateringer og nye funktioner er blevet føjet til Azure indekseringsprogram medier processor. Du kan finde flere oplysninger [Azure Media indekseringsprogram Version 1.1.6.7 produktbemærkninger](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- Tilføjet en ny REST-API, der gør det muligt at opdatere kodning reserveret enheder: [EncodingReservedUnitType med RESTEN](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- Tilføjede CORS support til vigtige leveringstjenesten.
- Forbedring af ydeevnen af forespørgsler indstillinger for godkendelse af politik er færdig.
- I Kina datacenter [Nøgle levering URL-adresse](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) er nu hver kunde (på samme måde som i andre datacentre).
- Tilføjede HLS automatisk target varighed. Når du udfører direkte streaming, pakket HLS altid dynamisk. Som standard beregner Media Services automatisk HLS segmentet emballagen forholdet (FragmentsPerSegment) baseret på keyframe interval (KeyFrameInterval), også kaldet gruppe af billeder – GOP, der modtages fra direkte encoder. Få mere at vide under [arbejde med Azure Media Services Live Streaming].
 
###<a name="media-services-net-sdk-updates"></a>Media Services .NET SDK opdateringer

- [Azure Media Services.NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) er nu version 3.1.0.0.
- Opgraderet .net SDK afhængigheden til .NET Framework 4.5.
- Tilføjet en ny API, der gør det muligt at opdatere kodning reserveret enheder. Få mere at vide under [opdatere reserveret enhedstype og øget kodning RUs brug af .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- Tilføjede JWT (JSON Web Token) understøttelse token godkendelse. Du kan finde yderligere oplysninger finder [JWT token godkendelse i Azure Media Services og dynamiske kryptering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Tilføjede relative forskydning for BeginDate og ExpirationDate i skabelonen PlayReady licens.


##<a id="november_changes_14"></a>November 2014-udgivelsen

- Media Services kan du nu indtager direkte indhold med jævne Streaming (FMP4) via en SSL-forbindelse. Hvis du vil indtager over SSL, Sørg for at opdatere ingest URL-adressen til HTTPS.  Du kan finde flere oplysninger om live streaming, skal du se [arbejde med Azure Media Services Live Streaming].
- Bemærk, at i øjeblikket, du ikke kan indtager en RTMP live stream via en SSL-forbindelse.
- Du kan også streame indholdet via en SSL-forbindelse. Kontrollér, at din streaming URL-adresser begynder med HTTPS for at gøre dette.
- Bemærk, at du kan kun streame over SSL Hvis streaming slutpunktet hvorfra du præsenterer dit indhold er blevet oprettet efter 10 September 2014. Hvis din streaming URL-adresser er baseret på streaming slutpunkterne oprettet efter September 10, indeholder URL-adressen "streaming.mediaservices.windows.net" (det nye format). Streaming URL-adresser, der indeholder "origin.mediaservices.windows.net" (det gamle format) understøtter ikke SSL. Hvis din URL-adresse er i det gamle format, og du vil kunne streame via SSL, [oprette et nyt streaming slutpunkt](media-services-portal-manage-streaming-endpoints.md). Brug URL-adresser oprettes baseret på det nye streaming slutpunkt til at streame indholdet via SSL.

##<a id="october_changes_14"></a>Oktober 2014-udgivelsen

### <a id="new_encoder_release"></a>Media Services Encoder Release

Præsentation af den nye version af Media Services Azure Media Encoder. Med seneste Azure Media Encoder du betaler kun for output GB, men ellers nye encoder er kompatible med tidligere encoder funktion. Yderligere oplysninger [Media Services priser detaljer]).

### <a id="oct_sdk"></a>Media Services .NET SDK 

Media Services SDK til .NET Extensions er nu version 2.0.0.3.

Media Services SDK til .NET er nu version 3.0.0.8.

Der er foretaget følgende ændringer:

- Der opstod i forsøg politik klasser.
- Tilføj bruger agent streng til HTTP-anmodning om headere.
- Tilføje nuget Gendan build trin.
- Løse scenarie test for at bruge x509 cert fra lager.
- Validere indstillinger, når du opdaterer kanal og streaming slutningen.
 

### <a name="new-github-repository-to-host-media-services-samples"></a>Ny GitHub lager til host Media Services-eksempler

Eksempler er placeret i [Azure Media Services eksempler GitHub lager](https://github.com/Azure/Azure-Media-Services-Samples).


##<a id="september_changes_14"></a>September 2014-udgivelsen

Media Services RESTEN metadata er nu version 2.7. Du kan finde flere oplysninger om de seneste opdateringer til RESTEN, [Azure Media Services RESTEN API Reference].

Media Services SDK til .NET er nu version 3.0.0.7
 
### <a id="sept_14_breaking_changes"></a>Bryd ændringer

* **Origin** er omdøbt til [StreamingEndpoint].
* En ændring i standardfunktionsmåden, når du bruger **Azure klassisk Portal** at kode og derefter publicere MP4-filer.

Tidligere, hvornår ved hjælp af portalen Azure klassisk publicere en enkelt fil MP4 video Aktiver en SAS URL-adresse skal oprettes (SAS URL-adresser gør det muligt at hente videoen fra en blob-lager). I øjeblikket, når du bruger portalen Azure klassisk at kode og derefter publicere en enkelt fil MP4 video aktiv, peger den oprettede URL-adresse på en Azure Media Services streaming slutpunkt.  Denne ændring påvirker ikke MP4-videoer, der er overført til Media Services og publiceret uden at blive kodet via Azure Media Services direkte.

Du har i øjeblikket, på følgende to muligheder for at løse problemet.

* Aktivere streaming enheder og bruge dynamiske emballagen til at streame .mp4 aktiv som en jævn streaming præsentation.

* Oprette en SAS URL-adresse for at hente (eller afspille gradvist) på .mp4. Du kan finde flere oplysninger om, hvordan du opretter en SAS locator i [Levering af indhold].


### <a id="sept_14_GA_changes"></a>Nye funktioner/scenarier, der er en del af GA version

* **Indekseringsprogram medier Processor**. Flere oplysninger under [Indeksering mediefiler med Azure Media indekseringsprogram].

* Objektet [StreamingEndpoint] nu kan du tilføje brugerdefinerede domænenavne (host).

    For et brugerdefineret domænenavn skal bruges som Media Services streaming slutpunktsnavnet, skal du føje brugerdefinerede host navne til din streaming slutpunkt. Brug Media Services REST API'er eller .NET SDK tilføje brugerdefinerede host navne.
    
    Overvej følgende gælder:
    
    * Du skal have ejerskabet af det brugerdefinerede domænenavn.
    
    * Ejerskabet af domænenavnet skal godkendes af Azure Media Services. For at validere domænenavn, skal du oprette en CName, der knytter <MediaServicesAccountId>.<parent domain> til verifydns. < mediaservices-dns-zone >. 
    
    * Du skal oprette en anden CName, der knytter brugerdefinerede værtsnavnet (for eksempel sports.contoso.com) til din Media Services StreamingEndpont værtsnavn (for eksempel amstest.streaming.mediaservices.windows.net).


    Se egenskaben **CustomHostNames** i emnet [StreamingEndpoint] kan finde flere oplysninger.

### <a id="sept_14_preview_changes"></a>Nye funktioner/scenarier, der er en del af offentlige preview-versionen

* Direkte Streaming eksempelvisning. Få mere at vide under [arbejde med Azure Media Services Live Streaming].

* Vigtige Leveringstjenesten. Få mere at vide under [ved hjælp af AES-128 dynamiske kryptering og nøgle Leveringstjenesten].

* Dynamisk AES-kryptering. Få mere at vide under [ved hjælp af AES-128 dynamiske kryptering og nøgle Leveringstjenesten].

* Tjenesten til levering af PlayReady licens. Se [Brug af PlayReady dynamiske kryptering og licens Leveringstjenesten]kan finde flere oplysninger.

* PlayReady dynamisk kryptering. Se [Brug af PlayReady dynamiske kryptering og licens Leveringstjenesten]kan finde flere oplysninger.

* Media Services PlayReady licens skabelon. Se [Media Services PlayReady licens skabelonen oversigt over]kan finde flere oplysninger.

* Aktiver streaming lagerplads krypteret. Du kan finde flere oplysninger, se [Indhold af krypteret af Streaming-lagerplads].

##<a id="august_changes_14"></a>August 2014-udgivelsen

Når du koder et aktiv, oprettes der et output aktiv på kodning er fuldført. Indtil denne version produceret Azure Media Services Encoder metadata om output aktiver. Start med denne version af encoder giver også metadata om input aktiver. Se i emnerne [Input Metadata] og [Output Metadata] kan finde flere oplysninger.


##<a id="july_changes_14"></a>Juli 2014-udgivelsen

Der er foretaget følgende fejlrettelser til Azure Media Services Packager og kryptering:

* Kun lyden afspilles tilbage, når transmuxing et live arkiv aktiv til HTTP Live Streaming – problemet er løst og nu både lyd og video afspilles.

* Når emballagen et aktiv HTTP Live Streaming og AES 128-bit konvolut kryptering pakket streams afspilles ikke på Android-enheder – denne fejl er blevet løst og pakket strømmen afspiller på Android-enheder, der understøtter HTTP Live Streaming.

##<a id="may_changes_14"></a>Maj 2014-udgivelsen

### <a id="may_14_changes"></a>Generelt Media Services opdateringer

Du kan nu bruge [Dynamiske emballagen] til stream HTTP Live Streaming (HLS) v3. Streame HLS v3, skal du tilføje følgende format til origin locator stien: * .ism/manifest(format=m3u8-aapl-v3). Få mere at vide under [Nick Drouin Blog].

Dynamisk emballagen nu også understøtter fremvisning af HLS (v3 og v4) krypteret med PlayReady baseret på udglattede Streaming statisk krypteret med PlayReady. Du kan finde oplysninger om, hvordan du krypterer udglattede Streaming med PlayReady [Protecting udglattede Stream med PlayReady].

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK opdateringer

Følgende forbedringer er inkluderet i Media Services .NET SDK 3.0.0.5 version:

* Bedre hastighed og fleksibilitet til at overføre/hente medieaktiver.

* Forbedringer i retry-logik og forbigående undtagelseshåndtering: 

    * Midlertidig fejl registrering, og prøv igen logik er blevet forbedret i undtagelser, der er forårsaget af forespørgsler, gemme ændringer, overføre eller hente filer. 
    
    * Når du modtager Web undtagelser (for eksempel i løbet af en token anmodning ACS), vil du bemærke, at alvorlige fejl mislykkes hurtigere nu.

Du kan finde yderligere oplysninger finder [Prøv igen logik i Media Services SDK til .NET].

##<a id="april_changes_14"></a>April 2014 Encoder Release

### <a name="april_14_enocer_changes"></a>Media Services Encoder opdateringer

* Understøttelse af ingesting AVI filer har oprettet ved hjælp af ikke-lineære editor græs Valley EDIUS, hvor videoen er let tilføjet komprimeret ved hjælp af græs Valley HQ/HQX codec. Du kan finde yderligere oplysninger finder [Græs Valley læser EDIUS 7 Streaming gennem i skyen].

* Understøttelse af angive Navnekonventionen for de filer, der er oprettet med Media Encoder tilføjet. Se [Styre medier Service Encoder outputfilnavne]kan finde flere oplysninger.

* Understøttelse af video og/eller lyd overlejrer tilføjet. Se [Oprette overlejrer]kan finde flere oplysninger.

* Understøttelse af kombinering sammen flere video segmenter tilføjet. Du kan finde yderligere oplysninger finder [Kombinering Video målgrupper].

* Fast en fejl, der er relateret til kodning af MP4s, hvor lyden er kodet med MPEG-1 Audio layer 3 (også kendt MP3).


##<a id="jan_feb_changes_14"></a>Januar/februar 2014 versioner

### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.1, 3.0.0.2 og 3.0.0.3

Ændringer i 3.0.0.1 og 3.0.0.2 omfatter:

* Fast problemer i forbindelse med brugen af LINQ forespørgsler med SorterEfter sætninger.

* Opdel test løsninger i [GitHub] til enhed-baserede test og scenariebaserede test.

Du kan finde flere oplysninger om ændringerne, se: [Azure Media Services .NET SDK 3.0.0.1 og 3.0.0.2 versioner].

Følgende ændringer er foretaget i 3.0.0.3:

* Opgraderede Azure lagerplads afhængigheder til at bruge version 3.0.3.0. 

* Fast bagudkompatibilitet problemet for 3.0. *.* udgiver. 


##<a id="december_changes_13"></a>December 2013 version

### <a name="dec_13_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.0

>[AZURE.NOTE] 3.0.x.x versioner er ikke bagudkompatible med 2.4.x.x versioner.

Den seneste version af Media Services SDK er nu 3.0.0.0. Du kan hente den nyeste pakke fra Nuget eller finde de enkelte dele fra [GitHub].

Start med version 3.0.0.0 af Media Services SDK, og kan du genbruge tokens [Azure Active Directory Access ACS (Control Service)] . Du kan finde flere oplysninger i afsnittet "Genbruge Access Control Service Tokens" i [tilslutning til Media Services med Media Services SDK til .NET] -emne.

### <a name="dec_13_donnet_ext_changes"></a>Azure Media Services-.NET SDK udvidelser 2.0.0.0

Azure Media Services .NET SDK-udvidelser er et sæt af lokalnummer metoder og hjælpefunktioner, der kan forenkle din kode og gøre det nemmere at udvikle med Azure Media Services. Du kan få de seneste bit fra [Azure Media Services .NET SDK filtypenavne].

##<a id="november_changes_13"></a>November 2013 version

### <a name="nov_13_donnet_changes"></a>Azure Media Services .NET SDK ændringer

Start med denne version, og håndterer Media Services SDK til .NET midlertidige fejl fejl, der kan opstå, når du ringer op til Media Services REST-API-lag.

##<a id="august_changes_13"></a>August 2013 version

### <a name="aug_13_powershell_changes"></a>Media Services PowerShell-cmdlet'er inkluderet i Azure Sdk værktøjer

De følgende Media Services PowerShell-cmdletter medtages nu i [azure-sdk-værktøjer].

* Get-AzureMediaServices 

    For eksempel `Get-AzureMediaServicesAccount`.

* Ny AzureMediaServicesAccount 

    For eksempel `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* Ny AzureMediaServicesKey 

    For eksempel `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Fjern AzureMediaServicesAccount 

    For eksempel `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>Juni 2013 version

### <a name="june_13_general_changes"></a>Azure Media Services ændringer

De ændringer, der er nævnt i dette afsnit er opdateringer i juni 2013 Media Services-versioner.

* Mulighed for at sammenkæde flere lagerplads konti til en medier tjenestekonto. 

    StorageAccount
    
    Asset.StorageAccountName og Asset.StorageAccount

* Mulighed for at opdatere Job.Priority. 

* Meddelelse om relateret objekter og egenskaber: 

    JobNotificationSubscription
    
    NotificationEndPoint
    
    Job

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Azure Media Services .NET SDK ændrer

Følgende ændringer er inkluderet i juni 2013 Media Services SDK versioner. Seneste Media Services SDK er tilgængelig på GitHub.

* Start med versionen 2.3.0.0, og Media Services SDK understøtter sammenkædning flere lagerplads konti til en Media Services-konto. Følgende API'er understøtter denne funktion:
    
    Typen IStorageAccount.
    
    Egenskaben Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
    
    Egenskaben StorageAccount.
    
    Egenskaben StorageAccountName.
    
    Du kan finde yderligere oplysninger finder [Administration af Media Services Aktiver på tværs af flere lagerplads konti].

* Meddelelse, der drejer API'er. Starte med den version 2.2.0.0, du har mulighed for at lytte til Azure kø lagerplads beskeder. Flere oplysninger under [Håndtering af Media Services Job beskeder].
    
    Egenskaben Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
    
    Typen Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
    
    Typen Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
    
    Typen Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
    
    Typen Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
    
    Typen Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Afhængighed af Azure lagerplads klienten SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Afhængighed af OData 5.5 (Microsoft.Data.OData.dll).


##<a id="december_changes_12"></a>December 2012 version

### <a name="dec_12_dotnet_changes"></a>Azure Media Services .NET SDK ændrer

* IntelliSense: Tilføjet manglende Intellisense dokumentation for mange typer.

* Microsoft.Practices.TransientFaultHandling.Core: Fast et problem, hvor SDK stadig havde en afhængighed af en gammel version af denne samling. SDK referencer nu version 5.1.1209.1 af denne samling.

Løsninger på problemer med findes i November 2012 SDK:

* IAsset.Locators.Count: Denne Tæl nu korrekt rapporteres i nye IAsset grænseflader når alle locators er blevet slettet.

* IAssetFile.ContentFileSize: Denne værdi er nu korrekt sat efter en overførsel af IAssetFile.Upload(filepath).

* IAssetFile.ContentFileSize: Denne egenskab kan nu angives, når du opretter en aktiv-fil. Det var tidligere skrivebeskyttet.

* IAssetFile.Upload(filepath): Fast et problem, hvor denne synkron Overfør metode blev aktiverende følgende fejl, når overførsel af flere filer til aktivet. Fejlen var "Server kunne ikke godkende anmodningen. Sørg for, at værdien af Authorization-header dannes korrekt herunder signaturen."

* IAssetFile.UploadAsync: Fast et problem, hvor højst 5 filer kan ikke overføres samtidig.

* IAssetFile.UploadProgressChanged: Denne hændelse er nu leveres af SDK.

* IAssetFile.DownloadAsync (streng, BlobTransferClient, ILocator, CancellationToken): Denne metode overbelastning leveres nu.

* IAssetFile.DownloadAsync: Fast et problem, hvor højst 5 filer kan hentes samtidig.

* IAssetFile.Delete(): Fast et problem, hvor opkald Slet kan udløse en undtagelse, hvis ingen fil blev overført til IAssetFile.

* Job: Fast et problem, der hvor sammenkædning af en "MP4 udglattede Streams opgave" med en "PlayReady beskyttelse opgave" ved hjælp af en jobskabelon ikke vil oprette en hvilken som helst opgaver overhovedet.

* EncryptionUtils.GetCertificateFromStore(): Denne metode viser ikke længere en undtagelse på grund af en mislykkede forsøg på at finde det certifikat, der er baseret på certifikat konfigurationsproblemer.

##<a id="november_changes_12"></a>November 2012 version

De ændringer, der er nævnt i dette afsnit er opdateringer i November 2012 (version 2.0.0.0) SDK. Disse ændringer kan kræve en hvilken som helst kode, der er skrevet til juni 2012 preview SDK slip for at være ændret eller omskrives.

* Aktiver
    
    IAsset.Create(assetName) er kun aktiv oprettelse af funktion. IAsset.Create uploader ikke længere filer som en del af Metodeopkaldet. Brug IAssetFile til overførsel.
    
    Metoden IAsset.Publish og værdien AssetState.Publish optælling, der er blevet fjernet fra Services SDK. En hvilken som helst kode, der er afhænger af denne værdi skal være igen skrevne.

* Filinfo

    Denne klasse er fjernet og erstattet af IAssetFile.

    IAssetFiles

    IAssetFile erstatter Filinfo og har en anden funktionsmåde. For at bruge det, skal du indsætte IAssetFiles objektet, efterfulgt af en filoverførsel, enten ved hjælp af Media Services SDK eller Azure lagerplads SDK. De følgende IAssetFile.Upload overloads kan bruges:

    * IAssetFile.Upload(filePath): En synkron metode, der blokerer tråden og anbefales kun, når overførslen af en enkelt fil.
    
    * IAssetFile.UploadAsync (filePath blobTransferClient locator, og cancellationToken): en asynkron metode. Dette er den foretrukne Overfør ordning. 

    Kendte fejl: ved hjælp af cancellationToken faktisk annullere upload; tilstanden annullering af opgaverne, kan dog være en af et antal stater. Du skal korrekt fange og håndtere undtagelser.

* Locators
    
    Origin-specifikke versioner er blevet fjernet. SAS-specifikke kontekst. Locators.CreateSasLocator (aktiv, accessPolicy) markeres frarådede eller fjernet fra GA. Se afsnittet Locators under nye funktioner for opdaterede funktionsmåde.


##<a id="june_changes_12"></a>Juni 2012 Preview-versionen

Følgende funktioner er nye i November-versionen af SDK.

* Slette objekter

    IAsset, IAssetFile, ILocator, IAccessPolicy, slettes IContentKey objekter nu på objektniveau, det vil sige IObject.Delete(), i stedet for at kræve en Slet i gruppen af websteder, der er cloudMediaContext.ObjCollection.Delete(objInstance).

* Locators

    Locators skal nu oprettes ved hjælp af metoden CreateLocator og bruge LocatorType.SAS eller LocatorType.OnDemandOrigin Optæl værdierne som et argument til de bestemte typer locator, du vil oprette.

    Egenskaber for ny blev føjet til Locators at gøre det nemmere at få brugbare URI'er til indholdet. Denne nyt design af Locators var beregnet til at give større fleksibilitet til fremtidige udvidelsesmuligheder af tredjeparters og Forøg øget for anvendelse for medier klientprogrammer.

* Understøttelse af asynkrone metode

    Asynkron support er blevet føjet til alle metoder.


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services MSDN-Forum]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services RESTEN API Reference]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[Media Services prisoplysninger]: http://azure.microsoft.com/pricing/details/media-services/
[Indtast Metadata]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Output Metadata]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Levering af indhold]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indeksering mediefiler med Azure Media indekseringsprogram]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Arbejde med Azure Media Services Live Streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Ved hjælp af AES-128 dynamiske kryptering og vigtige Leveringstjenesten]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Brug af PlayReady dynamisk kryptering og licens Leveringstjenesten]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Media Services PlayReady licens skabelon oversigt]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Streaming lagerplads krypteret indhold]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[Dynamisk emballagen]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin Blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Beskytte udglattede Stream med PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Retry-logik i Media Services SDK til .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Græs Valley læser EDIUS 7 Streaming via skyen]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Styrende medier Service Encoder outputfilnavne]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Oprette overlejrer]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Kombinering Video målgrupper]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 og 3.0.0.2 versioner]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory Access Control Service (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Oprette forbindelse til Media Services med Media Services SDK til .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure Media Services .NET SDK filtypenavne]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure-sdk-værktøjer]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Administration af Media Services Aktiver på tværs af flere lagerplads konti]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Håndtering af Media Services Job beskeder]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 
