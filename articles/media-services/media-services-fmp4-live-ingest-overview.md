<properties 
    pageTitle="Azure Media Services fragmenterede MP4 live indtager specifikation | Microsoft Azure" 
    description="Denne specifikation beskrives protokollen og formatet for fragmenteret MP4 baseret direkte streaming indtagelse til Microsoft Azure Media Services. Microsoft Azure Media Services indeholder direkte streaming tjeneste, der gør det muligt at streame direkte begivenheder og transmittere indhold i realtid ved hjælp af Microsoft Azure som skyen platform. Dette dokument omhandler også bedste fremgangsmåder i opbygning af meget overflødige og robust live indtager mekanismer." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"     
    ms.author="cenkdin;juliako"/>

#<a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services fragmenterede MP4 live indtager specifikation

Denne specifikation beskrives protokollen og formatet for fragmenteret MP4 baseret direkte streaming indtagelse til Microsoft Azure Media Services. Microsoft Azure Media Services indeholder direkte streaming tjeneste, der gør det muligt at streame direkte begivenheder og transmittere indhold i realtid ved hjælp af Microsoft Azure som skyen platform. Dette dokument omhandler også bedste fremgangsmåder i opbygning af meget overflødige og robust live indtager mekanismer.


##<a name="1-conformance-notation"></a>1. overensstemmelse Notation

Ord key "skal", er "Må ikke", "REQUIRED", "Gælder", "Skal ikke", "SHOULD", "Må ikke", "Anbefalede", "Kan" og "VALGFRIT" i dette dokument at blive fortolket som beskrevet i RFC 2119.

##<a name="2-service-diagram"></a>2. Service-Diagram 

Diagrammet nedenfor viser højt niveau arkitekturen i tjenesten direkte streaming i Microsoft Azure Media Services:

1.  Direkte Encoder flytter direkte feeds til kanaler, som er oprettet og klargjort via Microsoft Azure Media Services SDK.
2.  Kanaler, programmer og Streaming slutpunkt i Microsoft Azure Media Services håndtaget alle direkte streaming funktionerne herunder ingest, formatering, i skyen DVR, sikkerhed, skalerbarhed og redundans.
3.  Kunder kan eventuelt vælge at installere et CDN lag mellem Streaming slutpunkt og klienten slutpunkterne.
4.  Klient slutpunkter stream fra Streaming slutpunktet ved hjælp af HTTP tilpasset Streaming protokoller (fx udglattede Streaming, bindestreg, HD'ER eller HLS).

![image1][image1]


##<a name="3-bit-stream-format--iso-14496-12-fragmented-mp4"></a>3. bit-stream Format – ISO 14496-12 fragmenteret MP4

Formatet tråd er til direkte streaming indtager, der er beskrevet i dette dokument baseret på [ISO-14496-12]. Refererer til [[MS-SSTR]](http://msdn.microsoft.com/library/ff469518.aspx) detaljeret forklaring af fragmenteret MP4-format og udvidelser for begge video-on-demand filer, og live streaming indtagelse.

###<a name="live-ingest-format-definitions"></a>Live indtager format definitioner 

Nedenfor vises en liste over specielt format definitioner, der gælder for live indtager til Microsoft Azure Media Services:

1. Afkrydsningsfeltet 'ftyp', LiveServerManifestBox, og 'moov' skal sendes med hver enkelt anmodning (HTTP POST).  Det skal sendes i starten af strømmen, og når encoder skal oprette forbindelse igen for at fortsætte stream indtager.  Se afsnittet 6 i [1] for at få flere oplysninger.
2. Punkt 3.3.2 i [1] definerer et valgfrit felt kaldet StreamManifestBox for direkte indtager. På grund af routing logik om Microsoft Azure justering af belastning, frarådes brugen af dette felt og må ikke være til stede, når ingesting til Microsoft Azure Media-tjeneste. Hvis dette felt er til stede, ignorerer Azure Media Services automatisk den.
3. Den TrackFragmentExtendedHeaderBox, der er defineret i 3.2.3.2 i [1] skal være angivet for hver fragment.
4. Version 2 af TrackFragmentExtendedHeaderBox skal bruges for at producere mediesegmenter med identiske URL-adresser i flere datacentre. Feltet fragment indeks er PÅKRÆVET til datacenter i tværs failover af indeks-baserede streaming formater som Apple HTTP Live Streaming (HLS) og indeks-baserede MPEG-STREG.  For at aktivere kryds-datacenter failover skal skal fragment indekset være synkroniseret på tværs af flere kodere, og Forøg ved 1 til hver efterfølgende medier fragment endda på tværs af encoder genstarter eller mislykkede.
5. Afsnit 3.3.6 i [1] definerer feltet kaldet MovieFragmentRandomAccessBox (mfra), der bliver sendt i slutningen af live indtagelse til at angive EOS (slutningen af Stream) til kanalen. Brugen af EOS (slutningen af Stream) frarådes på grund af ingest logik om Azure Media Services, og feltet 'mfra' for direkte indtagelse skal ikke sendes. Hvis sendt, ignorerer Azure Media Services automatisk den. Det anbefales at bruge [Kanal nulstille](https://msdn.microsoft.com/library/azure/dn783458.aspx#reset_channels) til at nulstille tilstanden af ingest punktet og også det anbefales at bruge [Programmet stoppe](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) til at afslutte en præsentation og stream.
6. MP4-fragment varigheden skal være konstant, før du kan reducere størrelsen på klient-manifester og forbedre klient download heuristik ved brug af Gentag mærker.  Varigheden kan vise udsving kompensation for end heltal ramme satser.
7. MP4-fragment varigheden skal være mellem cirka 2 og 6 sekunder.
8. MP4-fragment tidsstempler og indeks (TrackFragmentExtendedHeaderBox fragment_ absolute_ tid og fragment_index) skal ender i stigende rækkefølge.  Selvom Azure Media Services er tolerant for dublerede fragmenter, der er meget begrænset mulighed for at ændre rækkefølgen af fragmenter ifølge medier tidslinjen.

##<a name="4-protocol-format--http"></a>4. protocol Format – HTTP

ISO fragmenteret MP4 baseret live indtager til Microsoft Azure Media Services bruger en standard længerevarende HTTP POST anmode om at overføre kodet medier data pakket i fragmenteret MP4-format til tjenesten. De enkelte HTTP INDLÆG sender en komplet fragmenteret MP4 bit-stream ("Bækkens") begyndende fra begynder med sidehovedfelter ('ftyp' "Live manifestet feltet Server" og 'moov' felt) og fortsætter med en sekvens af fragmenter ('moof' og 'mdat' bokse). Se afsnittet 9.2 i [1] til URL-syntaks for HTTP POST-anmodning. Et eksempel på Send URL-adresse er: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

###<a name="requirements"></a>Krav

Her er de detaljerede krav:

1. Encoder skal starte Udsend ved at sende en anmodning om en HTTP POST med en tom "brødtekst" (længden nul indhold) ved hjælp af den samme indtagelse URL-adresse. Dette kan hjælpe med at registrere hurtigt Hvis direkte indtagelse slutpunktet er gyldig, og hvis der er en hvilken som helst godkendelse eller andre betingelser, der er påkrævet. Hver HTTP-protokollen, serveren ikke kunne sende tilbage HTTP-svar, indtil du har modtaget hele anmodningen herunder INDLÆG brødtekst. Længerevarende karakter af live begivenheden, uden at dette trin skal encoder muligvis ikke kunne registrere en fejl, indtil den er færdig med at sende alle dataene.
2. Encoder skal håndtere fejl eller godkendelse udfordringer som resultat (1). Hvis (1) er oprettet med svar 200 fortsætte.
3. Encoder skal starte en ny anmodning om HTTP POST med fragmenterede MP4 strømmen.  Data skal starte med felterne sidehoved efterfulgt af fragmenter.  Bemærk feltet 'ftyp' "Live manifestet feltet Server" og 'moov' (i denne rækkefølge) skal sendes med hver anmodning, selvom encoder skal genoprette forbindelsen, fordi forrige anmodningen blev afbrudt før slutningen af strømmen. 
4. Encoder skal bruge overføre kodestykker til overførsel, da det er ikke muligt at forudsige hele indhold længden af hændelsen direkte.
5. Når begivenheden er slut, når du har sendt det sidste fragment skal afsluttes encoder problemfrit meddelelse overføre kodestykker sekvens (de fleste HTTP-klientstakke håndtere den automatisk). Encoder skal vente tjenesten til at returnere den endelige svarkode og derefter afbryde forbindelsen. 
6. Encoder må ikke bruge Events() substantiver, som beskrevet i 9.2 i [1] til direkte indtagelse til Microsoft Azure Media Services.
7. Hvis HTTP POST-anmodningen ophører eller udløber før slutningen af strømmen med en TCP-fejl, skal encoder problem med en ny POST-anmodning ved hjælp af en ny forbindelse og følge kravene over med flere kravet, encoder skal sende de forrige to MP4 fragmenter for hvert spor i strømmen, og genoptag uden Introduktion til discontinuities på medier tidslinjen.  Sende de sidste to MP4 fragmenter for hvert spor sikrer, at der er ingen tab af data.  Hvis en stream indeholder begge en lyd- og registrering, og den aktuelle POST-anmodning mislykkes, skal encoder med andre ord genoprette forbindelse og sende de sidste to fragmenter for lydspor, der tidligere er blevet sendt, og de sidste to fragmenter for video sporet, der er tidligere er blevet sendt, for at sikre, at der er ingen tab af data.  Encoder skal bevare en "videresende" bufferen af medier fragmenter, som den sender, når du opretter forbindelse igen.

##<a name="5-timescale"></a>5. tidsskala 

[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) beskriver brugen af "Tidsskala" for SmoothStreamingMedia (punkt 2.2.2.1), StreamElement (punkt 2.2.2.3), StreamFragmentElement(2.2.2.6) og LiveSMIL (punkt 2.2.7.3.1). Hvis tidsskalaen værdi ikke er installeret, er standardværdien bruges 10.000.000 (10 MHz). Selvom bløde specifikation af Streaming Format ikke blokere brugen af andre tidsskala værdier, de fleste af encoder installationer anvendelser standardværdien (10 MHz) til at generere indtager udglattede Streaming data. På grund af [Azure Media dynamiske emballagen](media-services-dynamic-packaging-overview.md) funktion er det anbefalet til brug af 90 kHz tidsskalaen for videostreams og 44.1 eller 48.1 kHz til lydstreams. Hvis der anvendes forskellige tidsskala værdier til forskellige streams, skal stream niveau tidsskalaen sendes. Se [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

##<a name="6-definition-of-stream"></a>6. definitionen af "Bækkens"  

"Bækkens" er den grundlæggende enhed af handling direkte indtagelse for skriver direkte præsentation, håndtering af streaming failover og redundans scenarier. "Bækkens" er defineret som et entydigt fragmenteret MP4 bit-stream som kan indeholde en enkelt registrering eller flere spor. En fuldstændig direkte præsentation kan indeholde en eller flere indholdsstreams afhængigt af konfigurationen af direkte encoder(s). Eksemplerne nedenfor illustrerer forskellige indstillinger ved at bruge stream(s) til at oprette en fuldstændig direkte præsentation.

**Eksempel:** 

Kunden ønsker at oprette en direkte streaming præsentation, som omfatter følgende lyd/video-bitrates:

Video – 3000kbps, 1500kbps, 750kbps

Lyd-128kbps

###<a name="option-1-all-tracks-in-one-stream"></a>Mulighed 1: Alle numre i én strøm

I denne indstilling er, en enkelt encoder genererer alle lyd/video-numre og samle dem i én fragmenteret MP4 bit-stream som derefter bliver sendt via en enkelt HTTP POST-forbindelse. I dette eksempel er der kun én stream for denne direkte præsentation:

![image2][image2]

###<a name="option-2-each-track-in-a-separate-stream"></a>Mulighed 2: Hvert spor i en separat stream

I denne indstilling, encoder(s) kun lægge et registrere til hver Fragment MP4-bit-stream og stil alle streams over flere separate HTTP-forbindelser. Dette kan gøres med én kodere eller flere kodere. Denne direkte præsentation består af fire streams fra direkte indtagelse synspunkt.

![image3][image3]

###<a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Mulighed 3: Samle lydspor med laveste bithastighed video sporet i én værdistrøm

I denne indstilling er, vælger kunden samle lydspor med den laveste bithastighed video Registrer i én Fragment MP4-bit-stream og lade de andre to video numre, der sin egen stream. 


![image4][image4]


###<a name="summary"></a>Oversigt

Hvad er vist ovenfor er ikke en omfattende liste over alle mulige indtagelse indstillingerne i dette eksempel. Gruppering af spor til streams understøttes som endte faktisk, af live indtagelse. Kunder og encoder leverandører kan vælge deres egne installationer, der er baseret på teknisk kompleksitet, encoder kapacitet og redundans og failover overvejelser i forbindelse med. Men Bemærk, at i de fleste tilfælde der er kun én lydspor i hele direkte præsentationen så det er vigtigt at sikre sundhed af strømmen ingest, der indeholder den lydspor. Denne overvejelser ofte resulterer i lydspor ibrugtagning opdateringerne (som i indstillingen 2) eller samler med den laveste bithastighed video Registrer (som i indstillingen 3). Også bedre redundans og fejltolerance ved indtager at sende den samme lydspor i to forskellige streams (indstillingen 2 med overflødige lydspor) eller samler lyd mindst to registrering af de laveste bithastighed video spor (indstillingen 3 med lyd er pakket i mindst to videostreams) anbefales til live til Microsoft Azure Media Services.

##<a name="7-service-failover"></a>7. Service Failover 

Det angivne art direkte streaming, er understøttelse af god failover vigtige for at sikre tilgængeligheden af tjenesten. Microsoft Azure Media Services er udviklet til at håndtere forskellige typer fejl, herunder netværksfejl, serverfejl, problemer med lagerplads, osv. Når den bruges sammen med stort failover logik fra direkte encoder side, kan kunde opnå en meget pålidelig direkte streaming service fra skyen.

I dette afsnit beskriver vi service failover scenarier. I dette tilfælde fejlen sker et sted, hvor inden for tjenesten og manifester sig selv som et netværksfejl. Her er nogle anbefalinger til encoder gennemførelse til håndtering af tjenesten failover:


1. Bruge en 10 anden timeout for oprettelse af TCP-forbindelsen.  Hvis et forsøg på at oprette forbindelsen tager længere tid end 10 sekunder, afbryde handlingen og prøve igen. 
2. Bruge timeout for en kort til at sende HTTP-anmodningen meddelelse dele.  Hvis destinationen MP4-fragment varigheden er N sekunder, kan du bruge timeout for en send mellem N og 2N sekunder; for eksempel bruge et timeout på 6 til 12 sekunder, hvis MP4-fragment varighed er 6 sekunder.  Hvis der opstår en timeout, nulstille forbindelsen, Åbn en ny forbindelse og genoptage stream indtager på den nye forbindelse. 
3. Vedligeholde en rullende bufferen, der indeholder de sidste to fragmenter for hvert spor, der blev korrekt og helt sendt til tjenesten.  Hvis HTTP POST-anmodning om en stream afsluttes, eller der sker timeout forud for slutningen af strømmen, Åbn en ny forbindelse og starte en anden HTTP POST-anmodning, sende stream overskrifterne, sende de sidste to fragmenter for hvert spor og genoptage strømmen uden Introduktion til en diskontinuiteten på medier tidslinjen.  Dette vil reducere risikoen for tab af data.
4. Det anbefales, at encoder ikke begrænse antallet af nye forsøg at oprette en forbindelse eller genoptage streaming efter en TCP-fejl.
5. Når du har en TCP-fejl:
    1. Den aktuelle forbindelse skal lukkes, og skal oprettes en ny forbindelse til en ny anmodning om HTTP POST.
    2. Den nye HTTP INDLÆG URL-adresse, skal være den samme som den indledende INDLÆG URL-adresse.
    3. Den nye HTTP INDLÆG skal medtage stream sidehoveder ('ftyp' "Live manifestet feltet Server" og 'moov' felt) identisk med stream overskrifterne i det indledende INDLÆG.
    4. De sidste to fragmenter sendt til hver enkelt registrering skal sendes igen, og streaming genoptages uden Introduktion til en diskontinuiteten på medier tidslinjen.  MP4-fragment tidsstempler skal øge løbende, endda på tværs af HTTP POST-anmodninger.
6. Encoder skal afbryde HTTP POST-anmodning, hvis data ikke bliver sendt med en hastighed, der svarer til MP4-fragment varighed.  En anmodning om en HTTP POST, der ikke sender data kan forhindre Azure Media Services i hurtigt afbryde forbindelsen til encoder i tilfælde af en tjenesteopdatering til.  Derfor HTTP-POST til sparse (ad signal) spor skal være korte leve, afsluttes, så snart sparse fragment sendes.

##<a name="8-encoder-failover"></a>8. encoder Failover

Encoder failover er den anden type failover scenarie, der skal løses til streaming levering af direkte til slut. I dette scenarie skal fejlsituationen er der sket i encoder side. 

![image5][image5]


Nedenfor er direkte indtagelse slutpunktet forventningerne, når encoder failover sker:

1. Der skal oprettes en ny encoder forekomst for at fortsætte med at streaming, som vist i diagrammet ovenfor (Stream til 3000 k video med stiplet linje).
2. Den nye encoder skal bruge den samme URL-adresse til HTTP POST-anmodninger som forekomsten mislykkedes.
3. Den nye encoder POST-anmodning skal indeholde de samme fragmenterede MP4 sidehovedfelter som forekomsten mislykkedes.
4. Den nye encoder skal synkroniseres korrekt med alle andre, der kører kodere til den samme direkte præsentation til at generere synkroniserede lyd/video eksempler med justerede fragment grænser.
5. Ny strømmen skal være semantisk svarer til den forrige strøm og kan anvendes på sidehoved og fragment niveau.
6. Den nye encoder skal prøve at minimere tab af data.  Fragment_absolute_time og fragment_index af medier fragmenter skal øge fra det sted, hvor encoder stoppede sidst.  Fragment_absolute_time og fragment_index skal øge i en sammenhængende måde, men det er tilladt at introducere en diskontinuiteten, hvis det er nødvendigt.  Azure Media Services ignorerer fragmenter, den allerede har modtaget og behandlet, så det er bedre til fejl på siden gensendelse fragmenter end at introducere discontinuities på medier tidslinjen. 

##<a name="9-encoder-redundancy"></a>9. encoder redundans 

For visse kritiske direkte begivenheder, demand endnu højere tilgængelighed og kvalitet af oplevelse, anbefales det at anvende aktiv-aktiv overflødige kodere at opnå problemfri failover med uden tab af data.

![image6][image6]

Som vist i diagrammet ovenfor, er der to gruppe af kodere samtidig trykke på to kopier af hver stream direkte ibrugtagning. Denne konfiguration understøttes, fordi Microsoft Azure Media Services har mulighed for at filtrere dublerede fragmenter baseret på stream-ID og fragment tidsstempel. Den resulterende live stream og arkiv bliver en enkelt kopi af alle streams, der er det bedst mulige sammenlægning fra to kilder. For eksempel i en hypotetiske meget store og små bogstaver, så længe der er en encoder (ikke nødvendigt at være identisk) kører på et bestemt tidspunkt tidspunkt for hver stream, vil den resulterende live stream fra tjenesten være fortløbende uden tab af data. 

Krav til dette scenario er næsten på samme måde som kravene i Encoder Failover tilfældet med den undtagelse, det andet sæt af kodere der kører på samme tid som de primære kodere.

##<a name="10-service-redundancy"></a>10. Service redundans  

I forbindelse med meget overflødige globale fordeling er det nogle gange nødvendigt at have tværs område sikkerhedskopi til at håndtere internationale nedbrud. Udvide på "Encoder redundans" topologien, kan kunder vælge at lade en overflødige service-installation i et andet område, som er forbundet med det 2. sæt kodere. Kunder kan også arbejde med et CDN udbyder du skal installere en GTM (Global trafik Manager) foran to service installationer til problemfrit omdirigere trafikken klient. Krav til kodere er den samme som "Encoder redundans" forbogstav med den eneste undtagelse, som det andet sæt af kodere skal være henvist til en anden live indtager slutpunkt. Denne opsætning vises i diagrammet nedenfor:

![image7][image7]

##<a name="11-special-types-of-ingestion-formats"></a>11. specielle typer indtagelse formater 

Dette afsnit beskrives en speciel type direkte indtagelse formater, som er designet til at håndtere nogle bestemte scenarier.

###<a name="sparse-track"></a>Sparse Registrer

Når du fremlægger en direkte streaming præsentation med omfattende klient oplevelse, er ofte nødvendigt at overføre tid synkroniseret begivenheder eller signaler in-band-med dataene, primære medier. Et eksempel på dette er dynamisk direkte reklamer indsætningspunktet. Denne type begivenhed-signal er anderledes end normalt lyd/video streaming på grund af dens sparse art. Med andre ord protokol dataene som regel sker ikke løbende og intervallet, der kan være svært at forudsige. Begrebet Sparse Registrer blev udviklet specielt til indtager og transmittere in-band-protokol data.

Nedenfor vises en anbefalede implementering til ingesting sparse registrering:

1. Oprette en separat fragmenteret MP4 bit-stream som kun indeholder sparse numre uden lyd/video numre.
2. Bruge "parentTrackName" parameter til at angive navnet på den overordnede Registrer i "Live Server manifestet feltet" som defineret i punkt 6 i [1]. Se afsnittet 4.2.1.2.1.2 i [1] for at få mere at vide.
3. I feltet "Live Server Manifestet skal" manifestOutput skal være angivet til "true".
4. Givet hændelsen protokol sparse art, anbefales det, at:
    1. I starten af live begivenheden sender encoder de indledende sidehovedfelter til den tjeneste, der giver tjenesten til at registrere sparse sporet i klienten manifestet.
    2. Encoder skal afbryde HTTP POST-anmodningen, når data ikke bliver sendt.  En længerevarende HTTP-POST, der ikke sender data kan forhindre Azure Media Services hurtigt afbryde forbindelsen til encoder i tilfælde af en tjeneste update eller server-genstart, som media server midlertidigt blokeret i handlingen Modtag på denne socket. 
    3. Mens når protokol data ikke er tilgængelig, encoder SHOULD Luk HTTP POST anmode om.  Mens POST-anmodningen er aktiv, skal skal encoder sende data 
    4. Når du sender sparse fragmenter, kan encoder angive eksplicitte indhold længden sidehoved, hvis den er tilgængelig.
    5. Når du sender sparse fragment med en ny forbindelse, skal encoder Begynd at sende fra felterne sidehoved efterfulgt af det nye antal fragmenter. Dette er at håndtere de tilfælde hvor failover er der sket ind imellem, og der oprettes den nye sparse forbindelse til en ny server, som ikke har brugt sparse sporet før.
    6. Sparse Registrer fragment gøres tilgængelige til klienten når det tilsvarende overordnede sporer fragment, der er lig med eller større tidsstempel værdi er gjort tilgængelig for klienten. Eksempelvis hvis sparse fragment er et tidsstempel af t = 1000, det er meningen, når klienten ser video (hvis det overordnede Registrer navn er video) fragmentere tidsstempel 1000 eller andre steder, det kan hente det sparse fragment t = 1000. Vær opmærksom på, at det faktiske signal kan meget vel bruges til en anden placering på præsentation tidslinjen til det angivne formål. I eksemplet ovenfor, det er muligt, det sparse fragment af t = 1000 har en XML-data, som er for at indsætte en annonce i en placering, der er et par sekunder senere.
    7. Data i sparse Registrer fragment kan være i forskellige forskellige formater (fx XML- eller tekst eller binær, osv.) afhængigt af forskellige scenarier. 


###<a name="redundant-audio-track"></a>Overflødige lydspor

I et typisk HTTP tilpasset Streaming scenarie (fx udglattede Streaming eller bindestreg) er der ofte kun én lydspor i hele præsentationen. I modsætning til video numre, der har flere kvalitetsniveau at vælge mellem i fejltilstande-klienten, kan den lydspor være et enkelt punkt af fejl, hvis indtagelse af strømmen, der indeholder den lydspor er brudt. 

Du kan løse dette problem ved understøtter Microsoft Azure Media Services direkte indtagelse af overflødige lydspor. Formålet er, at den samme lydspor sendes flere gange i forskellige streams. Mens tjenesten registreres kun den lydspor én gang i klienten manifestet, er det kan du bruge overflødige lydspor som sikkerhedskopier til at hente lyd fragmenter, hvis den primære lydspor er problemer. For at kunne indtager overflødige lydspor, encoder skal:

1. Oprette den samme lydspor i flere Fragment MP4-bit-streams. Overflødige lydspor skal være semantisk tilsvarende med præcis det samme fragment tidsstempler og kan anvendes på sidehoved og fragment niveau.
2. Sørg for, at "lyd" post i den Live Server manifestet (sektion 6 i [1]) være ens for alle overflødige lydspor.

Nedenfor finder du en anbefalede implementering til overflødige lydspor:

1. Send hver entydige lydspor i en stream alene.  Også sende en overflødige stream for hver af disse lydspor streams, hvor 2. strømmen er forskellig fra 1st kun af id i HTTP Send URL-adresse: {protokol} :// {Serveradressen} / {publicering af punkt path}/Streams({identifier}).
2. Brug separat streams til at sende to laveste video bitrates. Hver af disse streams bør også indeholde en kopi af hver entydige lydspor.  Når flere sprog, der understøttes, skal disse streams for eksempel indeholder lydspor for de enkelte sprog.
3. Brug separat server (encoder) forekomster at kode og sende de overflødige streams, der er nævnt i (1) og (2). 



##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png

 