<properties 
    pageTitle="Azure Media Services begreber | Microsoft Azure" 
    description="Dette emne giver et overblik over Azure Media Services begreber" 
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

#<a name="azure-media-services-concepts"></a>Azure Media Services begreber 

Dette emne giver et overblik over de vigtigste begreber Media Services.

##<a id="assets"></a>Aktiver og lager

###<a name="assets"></a>Aktiver

Et [aktiv](https://msdn.microsoft.com/library/azure/hh974277.aspx) indeholder digitale filer (herunder video, lyd, billeder, miniaturebilleder af websteder, tekstspor og undertekster filer) og metadata om filerne. Når digitale filerne er blevet overført til et aktiv, kan de bruges i Media Services kodningen og streaming arbejdsprocesser.

Et aktiv er knyttet til en blob objektbeholder i kontoen Azure-lager og filerne i aktivet er gemt som BLOB i objektbeholderen.

Når du bestemmer dig hvilke medieindhold til at overføre og gemme i et aktiv, gælder følgende overvejelser:

- Et aktiv skal indeholde en enkelt, entydige forekomst af medieindhold. For eksempel en enkelt redigering af en tv-afsnit, film eller annonce.
- Et aktiv må ikke indeholde flere gengivelser eller redigering af en audiovisuelle fil. Et eksempel på en forkert brugen af et aktiv skal forsøger at gemme mere end én tv-afsnit, annonce eller flere kameravinkler fra en enkelt fremstilling i et aktiv. Gemme flere gengivelser eller redigering af en audiovisuelle fil i et aktiv, kan det medføre problemer, der sender kodning job, streaming og sikring af levering af aktivet senere i arbejdsprocessen.  

###<a name="asset-file"></a>Aktiver fil 
En [AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx) repræsenterer en faktisk video eller lyd-fil, der er gemt i en blob beholder. En aktiv-fil er altid knyttet til et aktiv, og et aktiv kan indeholde et eller flere filer. Den Media Services Encoder mislykkes, hvis et aktiv filobjekt ikke er knyttet til en digital fil i en blob beholder.

Forekomsten **AssetFile** og den faktiske mediefil er to forskellige objekter. Forekomsten AssetFile indeholder metadata om mediefil, mens mediefilen indeholder det faktiske indhold.

Du bør ikke forsøge at ændre indholdet af blob objektbeholdere, som er blevet oprettet af Media Services uden at bruge medier Service API'er.

###<a name="asset-encryption-options"></a>Aktiver krypteringsindstillinger

Afhængigt af typen indhold, du vil overføre, gemme og formidle, giver Media Services forskellige krypteringsindstillinger, som du kan vælge mellem.

**Ingen** Der bruges ingen kryptering. Dette er standardværdien. Bemærk, at når du bruger denne indstilling, hvis indholdet ikke er beskyttet undervejs eller i resten i lagerplads.

Hvis du planlægger at levere en MP4 ved hjælp af løbende overførsel, Brug denne indstilling til at overføre dit indhold.

**StorageEncrypted** – Brug denne indstilling til at kryptere Ryd indholdet lokalt ved hjælp af AES 256 bit kryptering og Overfør det derefter til Azure lager, hvor det er gemt, krypteret på resten. Aktiver, der er beskyttet med lagerplads kryptering er automatisk uden kryptering og placeret i en krypteret filsystem før kodning, og du kan også igen krypteret før uploade tilbage som et nyt output aktiv. Den primære use case for lagerplads kryptering er, når du vil sikre høj kvalitet input mediefilerne med stærk kryptering på resten på disk. 

Hvis du vil afholde et lager krypterede aktiv, skal du konfigurere aktivets levering politik, så Media Services ved, hvordan du vil vise dit indhold. Før din aktiv streames, streaming serveren fjerner lagerplads kryptering og streamer indholdet ved hjælp af den angivne levering politik (for eksempel AES, PlayReady eller ingen kryptering). 

**CommonEncryptionProtected** - Brug denne indstilling, hvis du vil kryptere (eller overføre allerede krypteret) indhold med almindelige kryptering eller PlayReady DRM (for eksempel udglattede Streaming beskyttet med PlayReady DRM).

**EnvelopeEncryptionProtected** – Brug denne indstilling, hvis du vil beskytte (eller overføre allerede beskyttet) HTTP Live Streaming (HLS) krypteret med avancerede kryptering AES (Standard). Bemærk, at hvis du sender HLS allerede krypteret med AES, den skal blevet krypteret med transformere Manager.

###<a name="access-policy"></a>Access-politik 

En [AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx) definerer tilladelser (som læst, skrive og listen) og varighed for adgang til et aktiv. Du vil normalt overføre et AccessPolicy objekt til en locator, der skal bruges til at få adgang til filerne i et aktiv.


###<a name="blob-container"></a>BLOB objektbeholder

En blob objektbeholder indeholder en gruppering af et sæt af BLOB. BLOB beholdere bruges i Media Services som rammen punkt til adgangskontrol og delt Access signatur (SAS) locators på Aktiver. En Azure-lager-konto kan indeholde et ubegrænset antal blob beholdere. En objektbeholder kan gemme et ubegrænset antal BLOB.

>[AZURE.NOTE]Du bør ikke forsøge at ændre indholdet af blob objektbeholdere, som er blevet oprettet af Media Services uden at bruge medier Service API'er.

###<a id="locators"></a>Locators

[Locator](https://msdn.microsoft.com/library/azure/hh974308.aspx)s giver et indgangspunkt til at få adgang til filerne i et aktiv. En access-politik bruges til at definere tilladelser og varighed, en klient har adgang til et bestemt aktiv. Locators kan have en mange-til-en relation med en access-politik, så forskellige locators kan angive forskellige starttidspunkter og forbindelsestyper af til forskellige klienter prøveversionen alle de samme tilladelser og varighed for; på grund af en delt adgang politik begrænsning af Azure lagerplads services, kan du har mere end fem entydige locators, der er knyttet til en given aktiv ad gangen. 

Media Services understøtter to typer locators: OnDemandOrigin locators, bruges til at streame medier (for eksempel MPEG TANKESTREG, HLS eller udglattet Streaming) eller gradvist hente medier og SAS URL-adressen locators bruges til at overføre eller hente medier filer to\from Azure-lager. 

Bemærk, at der tilladelsen liste (AccessPermissions.List) ikke bør benyttes, når du opretter en OrDemandOrigin locator. 

###<a name="storage-account"></a>Lagerplads konto

Alle adgang til Azure-lager sker via en lagerplads konto. En medier tjenestekonto kan tilknyttes en eller flere lagerplads konti. Et firma kan indeholde et ubegrænset antal beholdere, som deres samlede størrelse er under 500TB hver lagerplads konto.  Media Services indeholder SDK niveau værktøj gør det muligt at administrere flere lagerplads konti og justering af belastning fordelingen af dine aktiver under Overfør til disse konti, der er baseret på målepunkter eller tilfældige fordeling. Få mere at vide under arbejde med [Azure-lager](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

##<a name="jobs-and-tasks"></a>Job og opgaver

Et [job](https://msdn.microsoft.com/library/azure/hh974289.aspx) bruges typisk til proces (for eksempel indeksere eller kode) en lyd/video-præsentation. Hvis du behandler flere videoer, kan du oprette et job for hver video, der skal kodes.

Et job indeholder metadata om behandling skal udføres. Hvert job indeholder en eller flere [opgave](https://msdn.microsoft.com/library/azure/hh974286.aspx)-s, som angiver en atomisk behandlingsopgave, dens input Aktiver output aktiver, en medier processor og indstillingerne for den tilknyttede. Opgaver i et job kan være kædet sammen, hvor output aktiv for en opgave er angivet som input aktivet til den næste opgave. På denne måde kan ét job indeholder alle behandlingen nødvendige for en media-præsentation.

##<a id="encoding"></a>Kode

Azure Media Services er der flere muligheder for kodningen af medier i skyen.

Når du starter med Media Services, er det vigtigt at forstå forskellen mellem codecs og filformater.
Codecs er den software, der implementerer komprimering/kompression algoritmer, mens filformater er objektbeholdere, hold komprimeret videoen.

Media Services indeholder dynamiske emballagen, som du kan bruge til at fremvise din tilpassede bithastighed MP4 eller udglattet Streaming kodet indhold i streaming formater, der understøttes af Media Services (MPEG TANKESTREG, HLS, bløde Streaming, HD'ER) uden at du kan igen pakkes i disse streaming formater.

For at kunne udnytte [dynamiske emballagen](media-services-dynamic-packaging-overview.md), skal du gøre følgende:

- Kod filen mezzanine (kilde) i et sæt af tilpassede bithastighed MP4 filer eller tilpasset bithastighed udglattede Streaming filer (kodning trinnene vises senere i dette selvstudium).
- Få mindst én efter behov streaming enhed for streaming slutpunktet, som du planlægger at levering af dit indhold. Se, [hvordan du skala On Demand Streaming reserveret enheder](media-services-portal-manage-streaming-endpoints.md)kan finde flere oplysninger.

Media Services understøtter følgende på demand kodere, der er beskrevet i denne artikel:

- [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
- [Media Encoder Premium arbejdsproces](media-services-encode-asset.md#media-encoder-premium-workflow)

Du kan finde oplysninger om understøttede kodere [kodere](media-services-encode-asset.md).


##<a name="live-streaming"></a>Live Streaming

I Azure Media Services repræsenterer en kanal en rørledning til at behandle direkte streaming indhold. En kanal modtager direkte input streamet indhold på en af to måder:

- En lokal direkte encoder sender flere bithastighed RTMP eller udglattet Streaming (fragmenteret MP4) til kanalen. Du kan bruge følgende direkte kodere, som output multi-bithastighed udglattede Streaming: frit, Envivio, Cisco. De følgende direkte kodere output RTMP: Adobe Flash Live, Telestream Wirecast og Tricaster transcoders. Oralt streams passerer gennem kanaler uden videre behandling. Når du har skrevet, leverer Media Services strømmen til kunder.

- En enkelt bithastighed stream (i en af følgende formater: RTP (MPEG-TS)), RTMP eller udglattet Streaming (fragmenteret MP4)) sendes til den kanal, der er aktiveret til at udføre direkte kodning med Media Services. Kanalen udfører derefter direkte kode for indgående enkelt bithastighed stream til en video multi-bithastighed (tilpasset)-stream. Når du har skrevet, leverer Media Services strømmen til kunder.

###<a name="channel"></a>Kanal

[Kanal](https://msdn.microsoft.com/library/azure/dn783458.aspx)s er ansvarlig for behandling af direkte streaming indhold i Media Services. En kanal indeholder input ark (indtager URL-adresse), som du derefter give en direkte transcoder. Kanalen modtager direkte input streamet indhold fra den direkte transcoder og gør det tilgængeligt for streaming gennem en eller flere StreamingEndpoints. Kanaler giver også et eksempel slutpunkt (preview URL-adresse), som du kan bruge til at få vist og godkende din stream før videre behandling og levering.

Du kan hente ingest URL-adressen og preview URL-adressen, når du opretter kanalen. For at få disse URL-adresser, har ikke kanalen skal være i starttilstanden. Når du er klar til at starte skubbe data fra en direkte transcoder til kanalen, skal kanalen startes. Når direkte transcoder starter ingesting data, kan du få vist din stream.

Hver Media Services-konto kan indeholde flere kanaler, flere programmer og flere StreamingEndpoints. Afhængigt af de båndbredde og sikkerhed behov, kan være dedikeret StreamingEndpoint tjenester til en eller flere kanaler. En hvilken som helst StreamingEndpoint kan trække fra en hvilken som helst kanal.


###<a name="program"></a>Program

Et [Program](https://msdn.microsoft.com/library/azure/dn783463.aspx) gør det muligt at styre publicering og opbevaring af målgrupper i en live stream. Kanaler Administrer programmer. Kanal og Program relationen minder meget om traditionelle medier, hvor en kanal har en konstant strøm af indhold og et program, er fastsat til nogle tidsindstillede begivenhed på denne kanal.
Du kan angive antallet timer, du vil bevare det registrerede indhold for programmet ved at angive egenskaben **ArchiveWindowLength** . Denne værdi kan angives fra et minimum af 5 minutter til maksimalt 25 timer.

ArchiveWindowLength bestemmer også den maksimale mængde tid klienter kan søge efter tilbage i tiden fra den aktuelle direkte position. Programmer kan køre over det angivne antal timer, men indhold, der falder bag vinduet længden kasseres løbende. Denne værdi af denne egenskab også bestemmer, hvor lang tid klient manifester kan vokse.

Hvert program er knyttet til et aktiv. Hvis du vil publicere programmet skal du oprette en locator for den tilknyttede aktiv. Har du denne locator gør det muligt at opbygge en streaming URL-adresse, som du kan give dine kunder.

En kanal understøtter op til tre samtidig programmer, så du kan oprette flere Arkiver af samme indgående strømmen. Dette giver dig mulighed at udgive og arkivere forskellige dele af en begivenhed, efter behov. Din forretningsbehov er for eksempel at arkivere 6 timer i løbet af et program, men at transmittere kun sidste 10 minutter. Hvis du vil gøre dette, skal du oprette to samtidig programmer. Ét program er indstillet til at arkivere 6 timer for begivenheden, men programmet ikke er blevet publiceret. Andet programmet er indstillet til at arkivere til 10 minutter, og dette program er blevet publiceret.


Du kan finde flere oplysninger i:

- [Arbejde med kanaler, der er aktiveret til at udføre Live kodning med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
- [Arbejde med kanaler, modtager flere bithastighed Live Stream fra lokale kodere](media-services-live-streaming-with-onprem-encoders.md)
- [Kvoter og begrænsninger](media-services-quotas-and-limitations.md).

##<a name="protecting-content"></a>Beskytte indhold

###<a name="dynamic-encryption"></a>Dynamisk kryptering

Azure Media Services giver dig mulighed at sikre dine medier fra når den forlader computeren via lagerplads, behandling og levering. Media Services giver dig mulighed at levere dit indhold, der er krypteret dynamisk med avancerede kryptering AES (Standard) (ved hjælp af 128-bit krypteringsnøgler) og almindelige kryptering (CENC) ved hjælp af PlayReady og/eller Widevine DRM. Media Services indeholder også en tjeneste til at levere AES-nøgler og PlayReady licenser til autoriserede klienter.

I øjeblikket, du kan kryptere streaming følgende formater: HLS, MPEG TANKESTREG og jævn Streaming. Du kan ikke kryptere HD'ER streaming format, eller progressive henter.

Hvis du vil til Media Services til at kryptere et aktiv, skal du knytte en krypteringsnøgle (CommonEncryption eller EnvelopeEncryption) til din aktiv og også konfigurere politikker for godkendelse for nøglen.

Hvis du vil streame en krypteret aktiv lagerplads, skal du konfigurere aktivets levering politik for at angive, hvordan du vil til at fremvise din aktiv.

Når der anmodes om en stream ved en afspiller, bruger Media Services den angivne nøgle til at kryptere dynamisk indholdet ved hjælp af en konvolut kryptering (med AES) eller almindelige kryptering (med PlayReady eller Widevine). Hvis du vil dekryptere strømmen, afspilleren anmoder om tasten fra de vigtigste leveringstjenesten. For at afgøre, om brugeren har tilladelse til at hente nøglen, evaluerer tjenesten de politikker, der tilladelse, du har angivet for nøglen.


###<a name="token-restriction"></a>Token begrænsning

Politikken indhold vigtige tilladelse kan har en eller flere godkendelse begrænsninger: åbne, token begrænsning eller IP-begrænsning. Politikken token begrænset skal være ledsaget af et token, der er udstedt af et Token STS (Secure Service). Media Services understøtter tokens i formatet enkel Web Tokens (SWT) og formatet JSON Web Token (JWT). Media Services giver ikke Secure Token Services. Du kan oprette en brugerdefineret STS eller udnytte Microsoft Azure ACS til problem tokens. STS skal konfigureres til at oprette et token, der er signeret med de angivne nøgle og problem krav, du har angivet i konfigurationen token begrænsning. Tjenesten Media Services vigtige levering returnerer det ønskede nøgle (eller den licens) til klienten Hvis Tokenet er gyldige og krav i token matcher dem, der er konfigureret for nøgle (eller licens).

Når konfigurere tokenet begrænsede politik, skal du angive den primære bekræftelse nøgle, udsteder og målgruppe parametre. Tasten primære bekræftelse indeholder nøglen, som er blevet signeret tokenet med, udsteder er det sikkert token tjeneste, som problemer tokenet. Deltagerne (også kaldet omfang) i denne artikel beskrives formålet med tokenet eller ressourcen tokenet tillader adgang til. Tjenesten Media Services vigtige levering valideret, at disse værdier i tokenet matcher værdierne i skabelonen.

Du kan finde flere oplysninger i følgende artikler:

[Beskytte oversigt over celleindhold](media-services-content-protection-overview.md)
[Beskyt med AES-128](media-services-protect-with-aes128.md)
[Beskyt med DRM](media-services-protect-with-drm.md)

##<a name="delivering"></a>Fremvisning af

###<a id="dynamic_packaging"></a>Dynamisk emballagen

Når du arbejder med Media Services, anbefales det at kode mezzanine-filer i en tilpasset bithastighed MP4 angive og Konverter derefter sættet til det ønskede format ved hjælp af [Dynamiske emballagen](media-services-dynamic-packaging-overview.md).


###<a name="streaming-endpoint"></a>Streaming slutpunkt

En StreamingEndpoint repræsenterer en streaming tjeneste, som kan levere indhold direkte til en player-klientprogrammet eller til et indhold levering netværk (CDN) for yderligere fordeling (Azure Media Services giver nu Azure CDN-integration). Udgående strømmen fra en StreamingEndpoint tjeneste kan være en live stream eller en video on demand aktiv i kontoen Media Services. Desuden kan du styre kapaciteten af tjenesten StreamingEndpoint til at håndtere voksende båndbredde behov ved at justere skalaenheder (også kaldet streaming enheder). Det anbefales at tildele en eller flere skalaenheder til programmer i produktionsmiljø. Skalaenheder giver dig både dedikeret udgangspunkt kapacitet, der kan købes i tidsintervaller 200 Mbps og ekstra funktioner som i øjeblikket omfatter brug dynamiske emballagen.

Det anbefales at bruge dynamiske emballagen og/eller dynamiske kryptering. Hvis du vil bruge disse funktioner, skal du have mindst én streaming enhed for det slutpunkt, hvorfra du planlægger at streame. Du kan finde flere oplysninger, se [Skalering streaming enheder](media-services-portal-manage-streaming-endpoints.md).

Du kan have op til 2 streaming slutpunkter i kontoen Media Services, som standard. Se [kvoter og begrænsninger](media-services-quotas-and-limitations.md)for at få en højere grænse.

Du er kun faktureret, når din StreamingEndpoint er i kører tilstand.

###<a name="asset-delivery-policy"></a>Aktiver levering politik

Et af trinnene i arbejdsprocessen Media Services for levering af indhold konfiguration af [levering af politikker for Aktiver ](https://msdn.microsoft.com/library/azure/dn799055.aspx), du vil blive streamet. Politikken aktiv levering fortæller Media Services, hvordan du vil have for din aktiv blev leveret: i hvilke streaming protokol, skal din aktiv dynamisk pakkes (for eksempel, MPEG TANKESTREG, HLS, bløde Streaming eller alle), uanset om du vil kryptere dynamisk din aktiv, og hvordan (konvolut eller almindelige kryptering).

Hvis du har et lager krypteret aktiv, før din aktiv streames, streaming serveren fjerner lagerplads kryptering og streamer indholdet ved hjælp af den angivne levering politik. Angiv for eksempel typen for at give dine aktiver, der er krypteret med avancerede kryptering AES (Standard) krypteringsnøgle til DynamicEnvelopeEncryption. For at fjerne lagerplads kryptering og streame aktivet i Ryd skal du angive typen til NoDynamicEncryption.

###<a name="progressive-download"></a>Løbende overførsel

Løbende overførsel giver dig mulighed for at afspille medier, før hele filen er blevet hentet. Du kan kun gradvist hente en MP4-fil.

Bemærk, at du skal dekryptere krypterede aktiver, hvis du vil for at de skal være tilgængelige for løbende overførsel.

For at give brugere med løbende overførsel URL-adresser, skal du først oprette en OnDemandOrigin locator. Oprette locator, giver dig grundlæggende stien til aktivet. Derefter skal du tilføje navnet på MP4-fil. Eksempel:

http://amstest1.streaming.mediaservices.Windows.NET/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###<a name="streaming-urls"></a>Streaming URL-adresser

Streaming dit indhold til klienter. Hvis du vil give brugere streaming URL-adresser, skal du først oprette en OnDemandOrigin locator. Oprette locator, giver dig grundlæggende stien til det aktiv, der indeholder det indhold, du vil streame. Men hvis du vil kunne streame indholdet skal du redigere denne sti yderligere. Hvis du vil oprette en fuld URL-adressen til streaming manifestfilen, skal du sammenkæde den locator sti værdi og manifestet (filename.ism) filnavn. Føj derefter /Manifest og et passende format (hvis det er nødvendigt) til locator stien.

Du kan også streame indholdet via en SSL-forbindelse. Kontrollér, at din streaming URL-adresser begynder med HTTPS for at gøre dette.

Bemærk, at du kan kun streame over SSL Hvis streaming slutpunktet hvorfra du præsenterer dit indhold er blevet oprettet efter 10 September 2014. Hvis din streaming URL-adresser er baseret på streaming slutpunkterne oprettet efter September 10, indeholder URL-adressen "streaming.mediaservices.windows.net" (det nye format). Streaming URL-adresser, der indeholder "origin.mediaservices.windows.net" (det gamle format) understøtter ikke SSL. Hvis din URL-adresse er i det gamle format, og du vil kunne streame via SSL, kan du oprette et nyt streaming slutpunkt. Brug URL-adresser oprettes baseret på det nye streaming slutpunkt til at streame indholdet via SSL.

Følgende liste beskrives forskellige streaming formater og giver eksempler:

- Problemfri Streaming

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest


- MPEG BINDESTREG

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=mpd-time-CSF)



- Apple HTTP Live Streaming (HLS) V4

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-aapl)



- Apple HTTP Live Streaming (HLS) V3

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-aapl-v3)

- HD'ER (for kun Adobe PrimeTime/adgang til indehavere)

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=f4m-f4f)


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
