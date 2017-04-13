<properties
    pageTitle="Levering af indhold til kunder | Microsoft Azure"
    description="Dette emne er en oversigt over, hvad er involveret i fremvisning af indholdet med Azure Media Services."
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
    ms.date="09/19/2016"
    ms.author="juliako"/>


# <a name="deliver-content-to-customers"></a>Levere indhold til kunder
Når du leverer streaming eller video-on-demand indholdet til kunder, er dit mål at levere video i høj kvalitet til forskellige enheder under andet netværk betingelser.

Dette formål, kan du:

- Kod din stream til en video multi-bithastighed (tilpasset bithastighed)-stream. Det sig tager af kvalitet og netværk betingelser.
- Brug Microsoft Azure Media Services [dynamiske emballagen](media-services-dynamic-packaging-overview.md) dynamisk igen pakke din stream i forskellige protokoller. Det sig tager af streaming på forskellige enheder. Media Services understøtter leveringen af følgende tilpasset bithastigheden streaming teknologier: HTTP Live Streaming (HLS), bløde Streaming, MPEG-STREG og HD'ER (for kun Adobe Primetime/adgang til indehavere).

I denne artikel giver et overblik over vigtige levering af indhold begreber.

For at se kendte problemer, skal du se [kendte problemer](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dynamisk emballagen

Med dynamisk emballagen, som indeholder et Media Services, kan du præsenterer dit tilpassede bithastighed MP4 eller udglattet Streaming kodet indhold i streaming formater, der understøttes af Media Services (MPEG-STREG, HLS, bløde Streaming, HD'ER) uden at skulle igen pakke til disse streaming formater. Vi anbefaler, at levere dit indhold med dynamisk emballagen.

For at kunne udnytte dynamiske emballagen, skal du gøre følgende:

- Kod filen mezzanine (kilde) i et sæt af tilpassede bithastighed MP4 filer eller tilpasset bithastighed udglattede Streaming filer.
- Få mindst én efter behov streaming enhed for streaming slutpunktet, som du planlægger at levere dit indhold. Du kan finde flere oplysninger, se, [hvordan skalere streaming reserveret enheder efter behov](media-services-portal-manage-streaming-endpoints.md).

Med dynamisk emballagen, du gemmer og betale for filer i enkelt lagerplads format. Media Services, opbygge og fungere relevante svaret baseret på dine anmodninger.

Ud over at give adgang til dynamiske emballagen funktioner, giver efter behov streaming reserveret enheder dig dedikeret udgangspunkt kapacitet, der kan købes i tidsintervaller 200 Mbps. Som standard er efter behov streaming konfigureret i en delt forekomst model, for hvilken server ressourcer (for eksempel computerklynge eller udgangspunkt kapacitet) er delt med alle andre brugere. Du kan forbedre en streaming overførselshastighed efter behov ved at købe efter behov streaming reserveret enheder.

Du kan finde yderligere oplysninger finder [dynamiske emballagen](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtre og dynamisk manifester

Du kan definere filtre for dine aktiver med Media Services. Disse filtre er serverbaserede regler, der hjælper kunderne gøre ting ud til at afspille en bestemt sektion i en video eller angive et undersæt af lyd- og gengivelser, som din kundes enhed kan håndtere (i stedet for alle de gengivelser, der er knyttet til aktivet). Denne filtrering opnås gennem *dynamiske manifester* , der er oprettet, når kunden anmoder om at streame en video, der er baseret på en eller flere af de angivne filtre.

Se [filtre og dynamisk manifester](media-services-dynamic-manifest-overview.md)kan finde flere oplysninger.

## <a name="locators"></a>Locators

For at give din bruger med en URL-adresse, der kan bruges til at streame eller hente dit indhold, skal du først publicere din aktiv ved at oprette en locator. En locator indeholder et indgangspunkt for at få adgang til filerne i et aktiv. Media Services understøtter to typer locators:

- OnDemandOrigin locators. Disse bruges til at streame medier (for eksempel MPEG-STREG, HLS eller udglattet Streaming) eller hente gradvist filer.
-  Delt adgang signatur (SAS) URL-adressen locators. Disse bruges til at hente mediefiler til din lokale computer.

En *access-politik* bruges til at definere tilladelser (såsom Læs, Skriv og listen) og varighed, en klient har adgang til et bestemt aktiv. Bemærk, at der ikke bør benyttes i at oprette en OrDemandOrigin locator tilladelsen liste (AccessPermissions.List).

Locators have udløbsdatoer. Portalen Azure angiver en udløbsdato 100 år i fremtiden for locators.

>[AZURE.NOTE] Hvis du har brugt på Azure-portalen til at oprette locators før marts 2015, vil disse locators blev konfigureret til at udløbe efter to år.

For at opdatere en udløbsdato på en locator, skal du bruge [RESTEN](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) eller [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API'er. Bemærk, at når du opdaterer udløbsdatoen for en SAS locator, ændres URL-adressen.

Locators er ikke beregnet til at administrere adgangskontrol for hver bruger. Du kan give forskellige adgangsrettigheder til individuelle brugere ved hjælp af Digital Rights Management (DRM) løsninger. Du kan finde yderligere oplysninger finder [Sikring af medier](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Når du opretter en locator, kan der være en 30 sekunders forsinkelse på grund af nødvendige lager og overførsel processer i Azure-lager.


## <a name="adaptive-streaming"></a>Adaptiv streaming

Tilpasset bithastighed teknologier Tillad videoafspiller programmer til at bestemme netværk betingelser og Vælg flere bitrates. Når netværkskommunikation forringer, kan klienten vælge en lavere bithastighed, så afspilning kan fortsætte med lavere videokvalitet. Som netværk betingelser forbedre, kan klienten skifte til en højere bithastighed med forbedret videokvalitet. Azure Media Services understøtter følgende tilpasset bithastighed teknologier: HTTP Live Streaming (HLS), bløde Streaming, MPEG-STREG og HD'ER.

Hvis du vil give brugere streaming URL-adresser, skal du først oprette en OnDemandOrigin locator. Oprette locator giver dig grundlæggende stien til det aktiv, der indeholder det indhold, du vil streame. Men for at kunne streame indholdet, skal du redigere denne sti yderligere. Hvis du vil oprette en fuld URL-adressen til streaming manifestfilen, skal du sammenkæde den locator sti værdi og manifestet (filename.ism) filnavn. Derefter **Føj/manifestet** og et passende format (hvis det er nødvendigt) til locator stien.

>[AZURE.NOTE]Du kan også streame indholdet via en SSL-forbindelse. Kontrollér, at din streaming URL-adresser begynder med HTTPS for at gøre dette.

Du kan kun streame over SSL, hvis det streaming slutpunkt, hvorfra du præsenterer dit indhold er blevet oprettet efter 10 September 2014. Hvis din streaming URL-adresser er baseret på streaming slutpunkterne oprettet efter 10 September 2014, indeholder URL-adressen "streaming.mediaservices.windows.net." Streaming URL-adresser, der indeholder "origin.mediaservices.windows.net" (det gamle format) understøtter ikke SSL. Hvis din URL-adresse er i det gamle format, og du vil kunne streame via SSL, kan du oprette et nyt streaming slutpunkt. Brug URL-adresser baseret på det nye streaming slutpunkt til at streame indholdet via SSL.


## <a name="streaming-url-formats"></a>Streaming URL-adressen formater

### <a name="mpeg-dash-format"></a>MPEG-STREG format

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=mpd-time-CSF)



### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) V4-format

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) V3-format

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming (HLS) format med kun lyd filter

Som standard kun med lyd spor er inkluderet i HLS manifest. Dette er påkrævet til Apple Store certificering af mobilnetværk. Hvis en klient ikke har tilstrækkelig båndbredde eller er forbundet via en 2G-forbindelse, skifter afspilning i dette tilfælde til kun med lyd. Dette hjælper med at holde indhold streaming uden bufferen, men der er ingen video. I visse scenarier kan player bufferen være foretrukne over kun med lyd. Hvis du vil fjerne sporet kun med lyd, skal du tilføje **kun med lyd = falsk** til URL-adressen.

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-aapl-v3,Audio-only=False)

Få vist [dynamiske manifestet sammensætning support og HLS output yderligere funktioner](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)kan finde flere oplysninger.


### <a name="smooth-streaming-format"></a>Udjævne Streaming format

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Eksempel:

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a id="fmp4_v20"></a>Problemfri Streaming 2.0 manifestet (ældre manifest)

Som standard indeholder udglattede Streaming manifestformat Gentag mærket (r-tags). Dog understøtter visse afspillere ikke denne r-kode. Klienter med disse afspillere kan bruge et format, der deaktiverer denne r-kode:

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

### <a name="hds-for-adobe-primetimeaccess-licensees-only"></a>HD'ER (for kun Adobe PrimeTime/adgang til indehavere)

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)

## <a name="progressive-download"></a>Løbende overførsel

Du kan starte afspille medier, inden hele filen er blevet overført med løbende overførsel. Du kan ikke gradvist hente .ism * (ismv, isma, ismt eller ismc) filer.

For at hente gradvist indhold, skal du bruge OnDemandOrigin typen locator. I følgende eksempel vises URL-adressen, der er baseret på OnDemandOrigin typen locator:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Du skal dekryptere en hvilken som helst lagerplads-krypterede aktiver, du vil streame fra tjenesten origin til løbende overførsel.

## <a name="download"></a>Download

Hvis du vil hente dit indhold til en klientenhed, skal du oprette en SAS Locator. SAS locator giver dig adgang til objektbeholderen Azure-lager, hvor filen er placeret. For at opbygge Hent URL-adressen, er det nødvendigt at integrere filnavnet mellem værten og SAS signatur.

I følgende eksempel vises URL-adressen, der er baseret på SAS locator:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Overvej følgende gælder:

- Du skal dekryptere en hvilken som helst lagerplads-krypterede aktiver, du vil streame fra tjenesten origin til løbende overførsel.
- En overførsel, som ikke endnu senest 12 timer mislykkes.

## <a name="streaming-endpoints"></a>Streaming slutpunkter

Et streaming slutpunkt repræsenterer en streaming tjeneste, der kan levere indhold direkte til en player klientprogrammet eller til et levering af indhold netværk (CDN) til yderligere distribution. Udgående strømmen fra en streaming slutpunkt tjeneste kan være en live stream eller et video-on-demand aktiv i kontoen Media Services. Du kan også kontrollere kapaciteten af tjenesten streaming slutpunkt til at håndtere stigende båndbredde behov ved at justere streaming reserveret enheder. Du skal tildele mindst én reserveret enhed for programmer i et produktionsmiljø. Du kan finde flere oplysninger, se, [hvordan skalere en medier-tjeneste](media-services-portal-manage-streaming-endpoints.md).

## <a name="known-issues"></a>Kendte problemer

### <a name="changes-to-smooth-streaming-manifest-version"></a>Ændringer til jævn Streaming manifestet version

Før juli 2016 service release – når Aktiver produceret af Media Encoder Standard Media Encoder Premium arbejdsproces eller tidligere Azure Media Encoder blev streamet ved hjælp af dynamiske emballagen – udglattede Streaming manifest, der returneres, vil de svarer til version 2.0. I version 2.0 Brug fragment varigheden ikke koderne papirark Gentag (r). Eksempel:

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

I juli 2016 service release overholder genererede udglattede Streaming manifestet version 2.2, med fragment varighed ved hjælp af Gentag mærker. Eksempel:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Nogle af de ældre udglattede Streaming klienter understøtter muligvis ikke de gentagelse mærker og kan ikke indlæses manifestet. Hvis du vil reducere dette problem, kan du bruge parameteren ældre manifestformat **(format = fmp4 v20)** eller opdatere din klient til den nyeste version, som understøtter Gentag mærker. Du kan finde yderligere oplysninger finder [udglattede Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterede emner

[Opdater Media Services locators efter rullende lagerplads taster](media-services-roll-storage-access-keys.md)
