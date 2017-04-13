<properties 
    pageTitle="Azure Media Services oversigt og almindelige scenarier | Microsoft Azure" 
    description="Dette emne giver et overblik over Azure Media Services" 
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
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>

#<a name="azure-media-services-overview-and-common-scenarios"></a>Azure Media Services oversigt og almindelige scenarier

Microsoft Azure Media Services er en extensible skybaseret platform, som udviklere kan opbygge SVG medier administration og levering programmer. Media Services er baseret på REST API'er, der gør det muligt at sikkert overføre, gemme, kode og pakke video eller lyd indhold til både efter behov og direkte streaming levering til forskellige klienter (for eksempel TV, PC og mobilenheder).

Du kan oprette til slut arbejdsprocesser ved hjælp af helt Media Services. Du kan også vælge at bruge tredjeparts-komponenter for visse dele af arbejdsprocessen. For eksempel kodning ved hjælp af en tredjepartsudbyder af encoder. Overfør derefter, beskytte pakke, og levere ved hjælp af Media Services.

Du kan vælge at stream dit indhold live eller levere indhold efter behov. Dette emne viser almindelige scenarier til at levere dit indhold [direkte](media-services-overview.md#live_scenarios) eller [efter behov](media-services-overview.md#vod_scenarios). Emnet, der også linker til andre relevante emner.

## <a name="sdks-and-tools"></a>SDK'er og værktøjer

For at opbygge Media Services-løsninger, kan du bruge:

- [Media Services REST-API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- En af de tilgængelige klient SDK'er:
- [Azure Media Services SDK til .NET](https://github.com/Azure/azure-sdk-for-media-services)
- [Azure SDK til Java](https://github.com/Azure/azure-sdk-for-java),
- [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
- [Azure Media Services til Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Dette er en ikke-Microsoft-version af en Node.js SDK. Det er vedligeholdes af et community og i øjeblikket har ikke en 100% beskrivelser af de AMS APIs).
- Eksisterende værktøjer:
- [Azure-portalen](https://portal.azure.com/)
- [Azure-Media-tjenester-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) er en Winforms / C#-programmet til Windows)

##<a name="media-services-learning-paths"></a>Media Services læringsstier

Du kan få vist AMS læringsstier her:

- [AMS Live Streaming arbejdsproces](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS on Demand Streaming arbejdsproces](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##<a name="prerequisites"></a>Forudsætninger

Hvis du vil begynde at bruge Azure Media Services, skal du have følgende:

3. En Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com).
2. En Azure Media Services-konto. Bruge Azure-portalen, .NET eller REST-API til at oprette Azure Media Services-konto. Du kan finde flere oplysninger [Opret konto](media-services-portal-create-account.md).
3. (Valgfrit) Konfigurere udviklingsmiljø. Vælg .NET eller REST-API til dit udviklingsmiljø. Se [konfigurere miljøet](media-services-dotnet-how-to-use.md)kan finde flere oplysninger.

Desuden Lær at oprette forbindelse fra et program [Opret forbindelse](media-services-dotnet-connect-programmatically.md).
4. (Anbefales) Tildele en eller flere skalaenheder. Det anbefales at tildele en eller flere skalaenheder til programmer i produktionsmiljø.   Se [administrere streaming slutpunkter](media-services-portal-manage-streaming-endpoints.md)kan finde flere oplysninger.

##<a name="concepts-and-overview"></a>Oversigt over og begreber

Du kan finde Azure Media Services begreber [begreber](media-services-concepts.md).

Du kan finde en vejledning til serie, der introducerer dig for alle de vigtigste komponenter i Azure Media Services, [Azure Media Services trinvise selvstudier](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Denne serie er en god oversigt over begreber og værktøjet AMSE bruges til at vise AMS opgaver. Bemærk, at AMSE værktøj er et værktøj i Windows. Dette værktøj understøtter de fleste af de opgaver, kan du opnå fra et program med [AMS SDK til .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK til Java](https://github.com/Azure/azure-sdk-for-java)eller [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php).

##<a id="vod_scenarios"></a>Fremvisning af medier efter behov med Azure Media Services: almindelige scenarier og opgaver

Dette afsnit beskrives almindelige scenarier og indeholder links til relevante emner. I det følgende diagram viser de vigtigste dele af Media Services-platformen, der er involveret i levering af indhold efter behov. 

![VoD arbejdsproces](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


###<a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Beskytte indhold i lager og leverer streaming af medier i Ryd (ikke-krypteret)

1. Overføre en fil i høj kvalitet mezzanine til et aktiv.
    
    Det anbefales at anvende kryptering datalager på dine aktiver for at beskytte dit indhold under overførsel og samtidig med resten i lagerplads.
 
1. Kod til et tilpasset bithastighed MP4-filer. 

    Det anbefales at anvende kryptering datalager på output aktiv for at beskytte dit indhold på resten.
    
1. Konfigurere aktiv levering politik (der anvendes af dynamiske emballagen). 
    
    Hvis din aktiv er lagerplads krypteret, konfigurere du **skal** aktiv levering politik. 

1. Publicere aktivet ved at oprette en anmodet arbejdsproces locator.

    Sørg for at have mindst én streaming reserveret enhed på streaming slutpunktet hvorfra du vil at streame indhold.

1. Stream udgivet indhold.

###<a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Beskytte indhold i lagerplads, levere dynamisk krypterede streaming-medier  

Du skal have mindst én streaming reserveret enhed på streaming slutpunktet hvorfra du vil streame krypteret indhold, hvis du vil kunne bruge dynamiske kryptering.

1. Overføre en fil i høj kvalitet mezzanine til et aktiv. Anvende kryptering datalager til aktivet.
1. Kod til et tilpasset bithastighed MP4-filer. Anvende kryptering datalager til output aktiv.
1. Oprette indhold krypteringsnøgle for aktivet, du vil blive dynamisk krypteret under afspilning.
2. Konfigurere indhold vigtige politik.
1. Konfigurere aktiv levering politik (der anvendes af dynamiske emballagen og dynamiske kryptering).
1. Publicere aktivet ved at oprette en anmodet arbejdsproces locator.
1. Stream udgivet indhold. 

###<a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Bruge medier Analytics for at udlede handlings indsigt fra dine videoer 

Medier Analytics er en samling af tale og syn komponenter, der gør det nemmere for organisationer og virksomheder at udlede handlings indsigt fra deres videofiler. Se [Oversigt over Azure Media Services Analytics](media-services-analytics-overview.md)kan finde flere oplysninger.

1. Overføre en fil i høj kvalitet mezzanine til et aktiv.
2. Bruge en af følgende medier Analytics tjenester til at behandle dine videoer:
    
    - **Indekseringsprogram** – [processen videoer med Azure Media indekseringsprogram 2](media-services-process-content-with-indexer2.md)
    - **Hyperlapse** – [Hyperlapse mediefiler med Azure Media Hyperlapse](media-services-hyperlapse-content.md)
    - **Bevægelsessti registrering** – [Bevægelsessti registrering for Azure Media analyser](media-services-motion-detection.md).
    - **Ansigt registrering og ansigt følelser** – [ansigt og følelse registrering for Azure Media analyser](media-services-face-and-emotion-detection.md).
    - **Video opsummering** – [Brug Azure Media Video miniaturebilleder for at oprette en Video opsummering](media-services-video-summarization.md)
3. Medier Analytics medier processorer landbrugsprodukter MP4 filer eller JSON-filer. Hvis en medier processor produceret en MP4-fil, kan du gradvist hente filen. Hvis en medier processor produceret en JSON-fil, kan du hente filen fra Azure blob-lager. 


###<a name="deliver-progressive-download"></a>Levere løbende overførsel 

1. Overføre en fil i høj kvalitet mezzanine til et aktiv.
1. Kod til en enkelt MP4-fil.
1. Publicere aktivet ved at oprette en anmodet arbejdsproces eller SAS locator.

    Hvis bruger anmodet arbejdsproces locator, skal du sørge for at have mindst én streaming reserveret enhed på streaming slutpunktet hvorfra du vil hente gradvist indhold.

    Hvis bruger SAS locator, hentes indholdet fra Azure blob-lager. I dette tilfælde, behøver du ikke har streaming reserveret enheder.
  
1. Gradvist Hent indhold.

##<a id="live_scenarios"></a>Fremvisning af direkte Streaming begivenheder med Azure Media Services

Når du arbejder med Live Streaming følgende komponenter er ofte involveret:

- Et kamera, der bruges til at transmittere en begivenhed.
- En direkte video koder, der konverterer signaler fra kameraet til streams, der sendes til en direkte streaming tjeneste.

Du kan også flere direkte tid synkroniseret kodere. For visse kritiske direkte begivenheder, demand meget høj tilgængelighed og kvalitet af oplevelse, anbefales det at anvende aktiv-aktiv overflødige kodere med tid synchronizationto opnå problemfri failover med uden tab af data.
- En direkte streaming tjeneste, som gør det muligt at benytte følgende fremgangsmåde:

- indtager direkte indhold ved hjælp af forskellige direkte streaming-protokoller (for eksempel RTMP eller udglattet Streaming)
- (valgfrit) kode din stream til tilpasset bithastighed værdistrøm
- få vist din direkte værdistrøm
- optage og opbevaring af oralt indholdet for at blive streamet senere (Video-on-Demand)
- levere indhold via almindelige streaming-protokoller (for eksempel MPEG TANKESTREG jævne HLS, og HD'ER) direkte til dine kunder eller til et indhold levering netværk (CDN) til yderligere distribution.


**Microsoft Azure Media Services** (AMS) giver mulighed for at indtager, kode, få vist et eksempel, gemme og formidle dit direkte streaming indhold.

Når du fremlægger dit indhold til kunder er dit mål at levere en video i høj kvalitet til forskellige enheder under andet netværk betingelser. For at tage sig af kvalitet og netværk betingelser, du bruge direkte kodere til at kode din stream til flere bithastighed (tilpasset bithastighed) video stream.  For at tage sig af streaming på forskellige enheder, du bruge Media Services [dynamiske emballagen](media-services-dynamic-packaging-overview.md) til at pakke dynamisk din stream til forskellige protokoller igen. Media Services understøtter leveringen af følgende tilpasset bithastigheden streaming teknologier: HTTP Live Streaming (HLS), bløde Streaming, MPEG TANKESTREG og HD'ER (for kun Adobe PrimeTime/adgang til indehavere).

Håndtere alle direkte streaming funktionerne herunder ingest, formatering, DVR, sikkerhed, skalerbarhed og redundans i Azure Media Services, **kanaler**, **programmer**og **StreamingEndpoints** .

En **kanal** repræsenterer en rørledning til at behandle direkte streaming indhold. En kanal kan modtage en direkte input streams på følgende måder:

- Et lokalt direkte encoder afsendere, der findes flere bithastighed **RTMP** eller **Udglattet Streaming** (fragmenterede MP4) til den kanal, der er konfigureret til levering af **pass-through** . **Pass-through** leveringen er, når oralt streams passerer gennem **kanal**s uden videre behandling. Du kan bruge følgende direkte kodere, som output multi-bithastighed udglattede Streaming: frit, Envivio, Cisco.  De følgende direkte kodere output RTMP: Adobe Flash Live, Telestream Wirecast og Tricaster transcoders.  En direkte encoder kan også sende en enkelt bithastighed stream til en kanal, der ikke er aktiveret til direkte kodning, men, der ikke anbefales. Når du har skrevet, leverer Media Services strømmen til kunder.

>[AZURE.NOTE] Brug af en pass-through-metode er den mest økonomiske måde at live streaming, når du udfører flere hændelser over en længere stykke tid, og du allerede har investeret i lokalt kodere. Se [priser](/pricing/details/media-services/) oplysninger.

- En lokal direkte encoder sender en enkelt bithastighed stream til den kanal, der er aktiveret til at udføre direkte kodning med Media Services på en af følgende formater: RTP (MPEG-TS), RTMP eller udglattet Streaming (fragmenteret MP4). Kanalen udfører derefter direkte kode for indgående enkelt bithastighed stream til en video multi-bithastighed (tilpasset)-stream. Når du har skrevet, leverer Media Services strømmen til kunder.


###<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Arbejde med kanaler, modtager flere bithastighed live stream fra lokale kodere (pass-through)

I det følgende diagram viser de vigtigste dele af AMS platformen, der er involveret i **pass-through** -arbejdsprocessen.

![Direkte arbejdsproces][live-overview2]

Kan finde flere oplysninger under [arbejde med kanaler, Modtag Multi-bithastighed Live Stream fra lokale kodere](media-services-live-streaming-with-onprem-encoders.md).

###<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Arbejde med kanaler, der er aktiveret til at udføre direkte kodning med Azure Media Services

I det følgende diagram viser de vigtigste dele af AMS platformen, der er involveret i Live Streaming arbejdsproces, hvor en kanal er aktiveret til at udføre direkte kodning med Media Services.

![Direkte arbejdsproces][live-overview1]

Få mere at vide under [arbejde med kanaler, der er aktiveret til at udføre Live kodning med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).


##<a name="consuming-content"></a>Bruger indholdstyper

Azure Media Services indeholder de værktøjer, du vil oprette avancerede, dynamiske player klientprogrammer for de fleste platforme, herunder: iOS-enheder, Android-enheder, Windows, Windows Phone, Xbox og Set-top felterne. Følgende emner indeholder links til SDK'er og Player strukturer, som du kan bruge til at udvikle dine egne klientprogrammer, der kan forbruge streaming-medier fra Media Services.

[Videoafspiller udviklingsprogrammer](media-services-develop-video-players.md)

##<a name="enabling-azure-cdn"></a>Aktivere Azure CDN

Media Services understøtter integration med Azure CDN. Du kan finde oplysninger om, hvordan du aktiverer Azure CDN, se, [hvordan du administrerer Streaming slutpunkter på en Media Services-konto](media-services-portal-manage-streaming-endpoints.md).

##<a name="scaling-a-media-services-account"></a>Skalering en Media Services-konto

Du kan skalere **Media Services** ved at angive det antal **Streaming reserveret enheder** og **Kodning reserveret enheder** , du vil have din konto for at blive klargjort med.

Du kan også skalere kontoen Media Services ved at tilføje lagerplads konti til den. Hver lagerplads konto er begrænset til 500 TB. Hvis du vil udvide din lagerplads ud over begrænsningerne standard, kan du vælge at vedhæfte flere lagerplads konti til en enkelt Media Services-konto.

[Dette](media-services-portal-scale-streaming-endpoints.md) emne links til relevante emner.

##<a name="support"></a>Support

Supportmuligheder for Azure, herunder Media Services indeholder [Azure understøtter](https://azure.microsoft.com/support/options/) .

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="service-level-agreement-sla"></a>Serviceniveauaftale (SLA)

- Til Media Services kodning garantere vi 99,9% tilgængeligheden af REST-API transaktioner.
- Til Streaming, vil vi korrekt serviceanmodninger med en 99,9% tilgængelighed garanti for eksisterende medieindhold når mindst én Streaming enhed er købt.
- For Live kanaler en vi, at køre kanaler har eksterne connectivity mindst 99,9% af tiden.
- For indholdsbeskyttelse en vi, at vi vil korrekt udføre vigtige anmodninger mindst 99,9% af tiden.
- For indekseringsprogram kan vi vil korrekt serviceanmodninger indekseringsprogram opgave behandles med en kodning reserveret enhed 99,9% af tiden.

Du kan finde yderligere oplysninger finder [Microsoft Azure SERVICENIVEAUAFTALE](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 
