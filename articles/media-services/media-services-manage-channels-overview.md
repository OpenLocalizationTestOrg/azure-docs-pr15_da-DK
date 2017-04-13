<properties 
    pageTitle="Oversigt over Live vand ved hjælp af Azure Media Services | Microsoft Azure" 
    description="Dette emne giver et overblik over direkte vand ved hjælp af Azure Media Services." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="juliako"/>

#<a name="overview-of-live-steaming-using-azure-media-services"></a>Oversigt over Live vand ved hjælp af Azure Media Services

##<a name="overview"></a>Oversigt

Når du fremlægger direkte streaming begivenheder med Azure Media Services er ofte involveret følgende komponenter:

- Et kamera, der bruges til at transmittere en begivenhed.
- En direkte video koder, der konverterer signaler fra kameraet til streams, der sendes til en direkte streaming tjeneste.

    Du kan også flere direkte tid synkroniseret kodere. For visse kritiske direkte begivenheder, demand meget høj tilgængelighed og kvalitet af oplevelse, anbefales det at anvende aktiv-aktiv overflødige kodere med synkronisering af klokkeslæt for at opnå problemfri failover med intet datatab.
- En direkte streaming tjeneste, som gør det muligt at benytte følgende fremgangsmåde:
    
    - indtager direkte indhold ved hjælp af forskellige direkte streaming-protokoller (for eksempel RTMP eller udglattet Streaming)
    - (valgfrit) kode din stream til tilpasset bithastighed værdistrøm
    - få vist din direkte værdistrøm
    - optage og opbevaring af oralt indholdet for at blive streamet senere (Video-on-Demand)
    - levere indhold via almindelige streaming-protokoller (for eksempel MPEG TANKESTREG jævne HLS, og HD'ER) direkte til dine kunder eller til et indhold levering netværk (CDN) til yderligere distribution.


**Microsoft Azure Media Services** (AMS) giver mulighed for at indtager, kode, få vist et eksempel, gemme og formidle dit direkte streaming indhold.

Når du fremlægger dit indhold til kunder er dit mål at levere en video i høj kvalitet til forskellige enheder under andet netværk betingelser. Bruge live kodere til at kode din stream til en video stream multi-bithastighed (tilpasset bithastighed) for at opnå.  For at tage sig af streaming på forskellige enheder, du bruge Media Services [dynamiske emballagen](media-services-dynamic-packaging-overview.md) til at pakke dynamisk din stream til forskellige protokoller igen. Media Services understøtter leveringen af følgende tilpasset bithastigheden streaming teknologier: HTTP Live Streaming (HLS), bløde Streaming, MPEG TANKESTREG og HD'ER (for kun Adobe PrimeTime/adgang til indehavere).

Håndtere alle direkte streaming funktionerne herunder ingest, formatering, DVR, sikkerhed, skalerbarhed og redundans i Azure Media Services, **kanaler**, **programmer**og **StreamingEndpoints** .

En **kanal** repræsenterer en rørledning til at behandle direkte streaming indhold. En kanal kan modtage en direkte input streams på følgende måder:

- Et lokalt direkte encoder afsendere, der findes flere bithastighed **RTMP** eller **Udglattet Streaming** (fragmenterede MP4) til den kanal, der er konfigureret til levering af **pass-through** . **Pass-through** leveringen er, når oralt streams passerer gennem **kanal**s uden videre behandling. Du kan bruge følgende direkte kodere, som output multi-bithastighed udglattede Streaming: frit, Envivio, Cisco.  De følgende direkte kodere output RTMP: Adobe Flash-medier Live Encoder (FMLE), Telestream Wirecast og Tricaster transcoders.  En direkte encoder kan også sende en enkelt bithastighed stream til en kanal, der ikke er aktiveret til direkte kodning, men, der ikke anbefales. Når du har skrevet, leverer Media Services strømmen til kunder.

    >[AZURE.NOTE] Brug af en pass-through-metode er den mest økonomiske måde at live streaming, når du udfører flere hændelser over en længere stykke tid, og du allerede har investeret i lokalt kodere. Se [priser](/pricing/details/media-services/) oplysninger.
    
    
- En lokal direkte encoder sender en enkelt bithastighed stream til den kanal, der er aktiveret til at udføre direkte kodning med Media Services på en af følgende formater: RTMP eller udglattet Streaming (fragmenterede MP4). RTP (MPEG-TS) understøttes også, hvis du har en dedikeret forbindelse til Azure datacentret. De følgende direkte kodere med RTMP output er blevet konstateret, at arbejde med kanaler af denne type: Telestream Wirecast, FMLE. Kanalen udfører derefter direkte kode for indgående enkelt bithastighed stream til en video multi-bithastighed (tilpasset)-stream. Når du har skrevet, leverer Media Services strømmen til kunder.


Starter med udgave Media Services 2.10, når du opretter en kanal, kan du angive, hvordan du vil for din kanal modtage input strømmen og hvorvidt du vil til kanalen til at udføre direkte kodning af din stream. Har du to muligheder:

- **Ingen** (pass-through) – Angiv denne værdi, hvis du planlægger at bruge en lokal direkte encoder som output vil blive sendt flere bithastighed stream (en pass-through-stream). I dette tilfælde indgående strømmen passerede gennem til output uden en hvilken som helst kodning. Dette er funktionsmåden for en kanal før 2.10 version.  

- **Standard** – Vælg denne værdi, hvis du planlægger at bruge Media Services til at kode din enkelt bithastighed live stream til flere bithastighed stream. Denne metode er mere økonomisk for skalering hurtigt ved sjælden begivenheder. Vær opmærksom på, at der er en fakturering virkning til direkte kodning og skal du huske, at forlade en direkte kodning kanal i tilstanden "Kører" vil betale fakturering gebyrer.  Det anbefales, at du stopper dine igangværende kanaler med det samme, når direkte streaming begivenheden er fuldført for at undgå ekstra hver time gebyrer. 

##<a name="comparison-of-channel-types"></a>Sammenligning af kanaltyper

Følgende tabel indeholder en vejledning til at sammenligne de to kanaltyper, der understøttes i Media Services

Funktion|Pass-through-kanal|Standard kanal
---|---|---
Enkelt bithastighed input kodes til flere bitrates i skyen|Nej|Ja
Maksimal opløsning, antallet af lag|1080p, 8 lag 60 + fps|720p, 6 lag 30 fps
Indtast protokoller|RTMP, jævne Streaming|RTMP, bløde Streaming og RTP
Pris|Se [priser side](/pricing/details/media-services/) og klikke på fanen "Live Video"|Se den [priser side](/pricing/details/media-services/) 
Maksimale kørselstid|24 x 7|otte timer
Support til at indsætte slates|Nej|Ja
Understøttelse af ad-signal|Nej|Ja
Pass-through-CEA 608/708 billedtekster|Ja|Ja
Muligheden for at gendanne fra kort bokse i bidrag feed|Ja|Ikke (kanal begynder slating efter 6 + sekunder inputdataene)
Understøttelse af ikke-ensartet input GOPs|Ja|Nej – input skal være fastgjort 2sec GOPs
Understøttelse af variable ramme rente input|Ja|Nej – skal input være fastgjort ramme rente.<br/>Mindre variationer kan for eksempel tillades under høj bevægelsessti scener. Men encoder kan ikke slippe til 10 rammer/sekund.
Automatisk-shutoff over kanaler når input feed går tabt|Nej|Når du har 12 timer, hvis der ikke er nogen Program, der kører 

##<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Arbejde med kanaler, modtager flere bithastighed live stream fra lokale kodere (pass-through)

I det følgende diagram viser de vigtigste dele af AMS platformen, der er involveret i **pass-through** -arbejdsprocessen.

![Direkte arbejdsproces](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Kan finde flere oplysninger under [arbejde med kanaler, Modtag Multi-bithastighed Live Stream fra lokale kodere](media-services-live-streaming-with-onprem-encoders.md).

##<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Arbejde med kanaler, der er aktiveret til at udføre direkte kodning med Azure Media Services

I det følgende diagram viser de vigtigste dele af AMS platformen, der er involveret i Live Streaming arbejdsproces, hvor en kanal er aktiveret til at udføre direkte kodning med Media Services.

![Direkte arbejdsproces](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Få mere at vide under [arbejde med kanaler, der er aktiveret til at udføre Live kodning med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

##<a name="description-of-a-channel-and-its-related-components"></a>Beskrivelse af en kanal og dens relaterede komponenter

###<a name="channel"></a>Kanal

[Kanal](https://msdn.microsoft.com/library/azure/dn783458.aspx)s er ansvarlig for behandling af direkte streaming indhold i Media Services. En kanal giver input ark (indtager URL-adresse), som du derefter give en direkte transcoder. Kanalen modtager direkte input streamet indhold fra den direkte transcoder og gør det tilgængeligt for streaming gennem en eller flere StreamingEndpoints. Kanaler giver også et eksempel slutpunkt (preview URL-adresse), som du kan bruge til at få vist og godkende din stream før videre behandling og levering.

Du kan hente ingest URL-adressen og preview URL-adressen, når du opretter kanalen. For at få disse URL-adresser, har ikke kanalen skal være i starttilstanden. Når du er klar til at starte skubbe data fra en direkte transcoder til kanalen, skal kanalen startes. Når direkte transcoder starter ingesting data, kan du få vist din stream.

Hver Media Services-konto kan indeholde flere kanaler, flere programmer og flere StreamingEndpoints. Afhængigt af de båndbredde og sikkerhed behov, kan være dedikeret StreamingEndpoint tjenester til en eller flere kanaler. En hvilken som helst StreamingEndpoint kan trække fra en hvilken som helst kanal.


###<a name="program"></a>Program 

Et [Program](https://msdn.microsoft.com/library/azure/dn783463.aspx) gør det muligt at styre publicering og opbevaring af målgrupper i en live stream. Kanaler Administrer programmer. Kanal og Program relationen minder meget om traditionelle medier, hvor en kanal har en konstant strøm af indhold og et program, er fastsat til nogle tidsindstillede begivenhed på denne kanal.
Du kan angive antallet timer, du vil bevare det registrerede indhold for programmet ved at angive egenskaben **ArchiveWindowLength** . Denne værdi kan angives fra et minimum af 5 minutter til maksimalt 25 timer. 

ArchiveWindowLength bestemmer også den maksimale mængde tid klienter kan søge efter tilbage i tiden fra den aktuelle direkte position. Programmer kan køre over det angivne antal timer, men indhold, der falder bag vinduet længden kasseres løbende. Denne værdi af denne egenskab også bestemmer, hvor lang tid klient manifester kan vokse.

Hvert program er knyttet til et aktiv. Hvis du vil publicere programmet skal du oprette en locator for den tilknyttede aktiv. Har du denne locator gør det muligt at opbygge en streaming URL-adresse, som du kan give dine kunder.

En kanal understøtter op til tre samtidig programmer, så du kan oprette flere Arkiver af samme indgående strømmen. Dette giver dig mulighed at udgive og arkivere forskellige dele af en begivenhed, efter behov. Din forretningsbehov er for eksempel at arkivere 6 timer i løbet af et program, men at transmittere kun sidste 10 minutter. Hvis du vil gøre dette, skal du oprette to samtidig programmer. Ét program er indstillet til at arkivere 6 timer for begivenheden, men programmet ikke er blevet publiceret. Andet programmet er indstillet til at arkivere til 10 minutter, og dette program er blevet publiceret.


##<a name="billing-implications"></a>Fakturering konsekvenserne

En kanal begynder fakturering som det er tilstandsovergange til "Kører" via API.  

Tabellen nedenfor viser, hvordan kanal stater knyttes til fakturering tilstande i portalen API og Azure. Bemærk, at staterne er en smule anderledes mellem API og Portal elevationsmeddelelse Så snart en kanal er i tilstanden "Løbende" via API, eller i tilstanden "Klar" eller "Streaming" i portalen Azure fakturering skal være aktiv.

For at stoppe kanalen fra fakturering du yderligere, har du stoppe kanalen, via API eller i portalen Azure.
Du er ansvarlig for at stoppe dine kanaler, når du er færdig med kanalen. Fejl ved at stoppe kanalen medfører fortsat fakturering.

>[AZURE.NOTE]Når du arbejder med almindelige kanaler, automatisk AMS shutoff en kanal, som stadig findes i "Kører" tilstand 12 timer efter input feedet går tabt, og der er ingen programmer kører. Men du vil stadig blive faktureret for gang kanalen var i "Kører" tilstand.

###<a id="states"></a>Kanal stater, og hvordan de kan knyttes til fakturering tilstand 

Den aktuelle status for en kanal. Mulige værdier omfatter:

- **Ikke længere**. Dette er den oprindelige tilstand for kanalen efter oprettelsen (medmindre starte blev valgt i portalen.) Ingen fakturering forekommer i denne tilstand. I denne tilstand egenskaberne kanal kan blive opdateret, men streaming er ikke tilladt.
- **Starter**. Kanalen startes. Ingen fakturering forekommer i denne tilstand. Ingen opdateringer eller streaming er tilladt under denne tilstand. Hvis der opstår fejl, returnerer kanalen, til showet afbrydes tilstand.
- **Kører**. Kanalen er kan behandle direkte streams. Det nu fakturering brugen. Du skal stoppe kanal for at forhindre yderligere fakturering. 
- **Stoppe**. Kanalen standses. Ingen fakturering forekommer i denne midlertidige tilstand. Ingen opdateringer eller streaming er tilladt under denne tilstand.
- **Slette**. Kanalen slettes. Ingen fakturering forekommer i denne midlertidige tilstand. Ingen opdateringer eller streaming er tilladt under denne tilstand.

Tabellen nedenfor viser, hvordan kanal stater knyttes til fakturering tilstand. 
 
Kanal tilstand|Portalen Brugergrænsefladen indikatorer|Er det fakturering?
---|---|---
Starte|Starte|Ingen (midlertidige tilstand)
Kører|Klar til (ingen programmer)<br/>eller<br/>Streaming (mindst ét program, der kører)|Ja
Stoppe|Stoppe|Ingen (midlertidige tilstand)
Ikke længere|Ikke længere|Nej


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="related-topics"></a>Relaterede emner

[Azure Media Services fragmenterede MP4 Live indtager specifikation](media-services-fmp4-live-ingest-overview.md)

[Arbejde med kanaler, der er aktiveret til at udføre Live kodning med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Arbejde med kanaler, modtager flere bithastighed Live Stream fra lokale kodere](media-services-live-streaming-with-onprem-encoders.md)

[Kvoter og begrænsninger](media-services-quotas-and-limitations.md).  

[Media Services begreber](media-services-concepts.md)
