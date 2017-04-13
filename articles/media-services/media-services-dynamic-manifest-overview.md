<properties 
    pageTitle="Filtre og dynamisk manifester | Microsoft Azure" 
    description="Dette emne beskrives det, hvordan du opretter filtre, så kunden kan bruge dem i stream bestemte dele af en stream. Media Services opretter dynamiske manifester Hvis du vil arkivere dette selektiv streaming." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="cenkdin;juliako"/>

# <a name="filters-and-dynamic-manifests"></a>Filtre og dynamisk manifester

Media Services, der starter med 2.11 udgave, kan du angive filtre til dine aktiver. Disse filtre er server side regler, der gør dine kunder til vælger at gøre ting som: afspilning kun en del af en video (i stedet for afspilning af hele video), eller angive kun et undersæt af lyd- og gengivelser, som din kundes enhed kan håndtere (i stedet for alle de gengivelser, der er knyttet til aktivet). Denne filtrering af dine aktiver arkiveres gennem **Dynamiske manifestet**s, der er oprettet på kundens anmodning til at streame en video, der er baseret på de angivne filtre.

Denne emner i denne artikel beskrives almindelige scenarier, hvor ved hjælp af filtre kan være meget nyttige for dine kunder og links til emner, der viser, hvordan du opretter filtre fra et program (i øjeblikket, kan du oprette filtre med REST API'er kun).

##<a name="overview"></a>Oversigt

Når du fremlægger dit indhold til kunder (streaming direkte begivenheder eller video-on-demand) er dit mål at levere en video i høj kvalitet til forskellige enheder under andet netværk betingelser. At opnå denne mål, skal du gøre følgende:

- kode din stream til multi-bithastighed ([tilpasset bithastighed](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) video stream (Dette tager sig af betingelser for kvalitet og netværk) og 
- Brug Media Services [Dynamiske emballagen](media-services-dynamic-packaging-overview.md) dynamisk igen pakke din stream i forskellige protokoller (Dette tager sig af streaming på forskellige enheder). Media Services understøtter leveringen af følgende tilpasset bithastigheden streaming teknologier: HTTP Live Streaming (HLS), bløde Streaming, MPEG TANKESTREG og HD'ER (for kun Adobe PrimeTime/adgang til indehavere). 

###<a name="manifest-files"></a>Manifest-filer 

Når du koder et aktiv til tilpasset bithastighed streaming, der oprettes en **manifestet** (afspilningsliste)-fil (filen er tekstbaserede eller XML-baseret). **Manifestfilen** omfatter streaming metadata f.eks.: registrere type (lyd, video eller tekst), registrere navn starttidspunkt og sluttidspunkt bithastighed (egenskaber), og Registrer sprog præsentationsvindue (skyderen vindue af fast varighed), video-codec (FourCC). Det får også afspilleren til at hente det næste fragment ved at angive oplysninger om det næste kan afspilles video antal fragmenter tilgængelige og deres placering. Fragmenter (eller segmenter) er de faktiske "udsnit" med en videoindhold.


Her er et eksempel på en manifestfil: 

    
    <?xml version="1.0" encoding="UTF-8"?>  
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">
    
    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />
    
    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    </SmoothStreamingMedia>
    
###<a name="dynamic-manifests"></a>Dynamisk manifester

Der findes [scenarier](media-services-dynamic-manifest-overview.md#scenarios) , når kunden har brug for mere fleksibel end det er beskrevet i standard aktivets manifestfilen. Eksempel:

- Enhed bestemte: vise kun angivne gengivelser og/eller angivet sprog numre, der understøttes af den enhed, der er brugt til at afspille indholdet ("gengivelse filtrering"). 
- Reducere manifestet for at få vist en underordnet multimedieklip af en direkte begivenhed ("underordnede klip filtrering").
- Trimme starten af en video ("trimme en video").
- Justere præsentation vindue (DVR) for at angive en begrænset længden af vinduet DVR i afspilleren ("tilpasning af præsentationsvindue").
 
For at opnå denne fleksibilitet, indeholder Media Services **Dynamiske manifester** baseret på foruddefinerede [filtre](media-services-dynamic-manifest-overview.md#filters).  Når du definerer filtrene, kan dine kunder kan bruge dem til at streame en bestemt gengivelse eller underordnede udklip af videoen. De vil angive filtre i streaming URL-adressen. Filtre kan anvendes på tilpasset bithastighed streaming protokoller, der understøttes af [Dynamiske emballagen](media-services-dynamic-packaging-overview.md): HLS, MPEG-STREG, bløde Streaming og HD'ER. Eksempel:

MPEG TANKESTREG URL-adresse med filter

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Problemfri Streaming URL-adresse med filter

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Du kan finde flere oplysninger om, hvordan du præsenterer dit indhold og opbygge streaming URL-adresser, [levere oversigt over celleindhold](media-services-deliver-content-overview.md).


>[AZURE.NOTE]Bemærk, at dynamisk manifester ikke ændre aktivet og standard manifestet for aktivet. Kunden kan vælge at anmode om en stream med eller uden filtre. 


###<a id="filters"></a>Filtre 

Der findes to typer aktiv filtre: 

- Globale filtre (kan anvendes på et aktiv i Azure Media Services-konto, har en levetid på kontoen) og 
- Lokale filtre (kan kun anvendes på et aktiv, som filteret er tilknyttet efter oprettelse, har en levetid for aktivet). 

Globale og lokale filtertyper har de samme egenskaber. Primære forskellen mellem to er, for hvilke scenarier hvilken type en midlertidige filer er mere passende. Globale filtre er generelt velegnet til enhedsprofiler (gengivelse filtrering), hvor lokale filtre kan bruges til at trimme et bestemt aktiv.


##<a id="scenarios"></a>Almindelige scenarier 

Når der er nævnt før, når du fremlægger dit indhold til kunder (streaming direkte begivenheder eller video-on-demand) dit mål er at levere en video i høj kvalitet til forskellige enheder under andet netværk betingelser. Desuden din fungerer muligvis har andre krav, der involverer filtrere dine aktiver og bruger **Dynamiske manifestet**s. De følgende afsnit giver en kort oversigt over forskellige spamfiltrering scenarier.

- Angiv kun et undersæt af lyd- og gengivelser, der kan håndtere visse enheder (i stedet for alle de gengivelser, der er knyttet til aktivet). 
- Afspilning af kun en del af en video (i stedet for afspilning af hele video).
- Justere DVR præsentationsvindue.

##<a name="rendition-filtering"></a>Filtrering af billedgengivelse 

Du kan vælge at kode dine aktiver flere kodning profiler (H.264 oprindelig H.264 høj AACL, AACH, og Dolby Digital Plus) og flere kvalitet bitrates. Men ikke alle klientenheder understøtter alle dine aktivets profiler og bitrates. For eksempel ældre Android-enheder understøtter kun H.264 oprindelige + AACL. Sende højere bitrates til en enhed, som ikke kan få adgang til fordelene, spild af båndbredde og enhed beregning. Sådan enhed skal afkode alle de angivne oplysninger kun at indskrænke den til visning.

Med dynamisk manifestet, kan du oprette enhedsprofiler konsol HD/SD osv som mobile, og Medtag spor og egenskaber, der skal være en del af hver profil.

 
![Gengivelse spamfiltrering eksempel][renditions2]

I følgende eksempel blev en encoder bruges til at kode et mezzanine aktiv i syv ISO MP4s video gengivelser (fra 180p til 1080p). Kodet aktivet kan pakkes dynamisk i et af følgende streaming-protokoller: HLS, jævne, MPEG TANKESTREG og HD'ER.  Øverst i diagrammet HLS manifestet for aktivet uden filtre vises (den indeholder alle syv gengivelser).  I nederste venstre vises HLS manifestet, er anvendt et filter med navnet "ott". Filteret "ott" Angiver, at fjerne alle bitrates under 1Mbps, som udløste de nederste to kvalitetsniveau der fjernes i svaret.  I nederste højre vises HLS manifestet, er anvendt et filter med navnet "mobile". Filteret "mobile" Angiver, hvis du vil fjerne gengivelser hvor opløsningen er større end 720p, som udløste i to 1080p gengivelser der fjernes.

![Filtrering af billedgengivelse][renditions1]

##<a name="removing-language-tracks"></a>Fjerne sprog numre

Dine aktiver kan indeholde flere lyd sprog som engelsk, spansk, fransk, osv. Normalt finder Player SDK lederne lydspor standardmarkering og tilgængelige lyd registrerer per bruger markeringen. Det er en udfordring at udvikle sådanne Player SDK'er, kræver forskellige installationer på tværs af enhed-specifikke player-strukturer. Også på nogle platforme Player API'er er begrænset, og du skal ikke medtage lyd markering funktion, hvor brugerne ikke kan vælge eller ændre standard lydspor. Du kan styre funktionsmåden ved at oprette filtre, der kun omfatter ønskede lyd sprog med aktiv filtre.

![Sprogspor filtrering][language_filter]


##<a name="trimming-start-of-an-asset"></a>Justering af startdatoen for et aktiv 

I de fleste direkte streaming hændelser Kør operatorer nogle test før den faktiske hændelse. For eksempel, de kan indeholde en tavle således før starten af begivenheden: "Program begynder et øjeblik". Hvis programmet arkivering, test og tavle data arkiveres også, og der medtages i præsentationen. Disse oplysninger bør dog ikke blive vist til klienter. Du kan oprette filteret start tid og fjerne uønskede dataene fra manifestet med dynamisk manifestet.

![Justering af start][trim_filter]

##<a name="creating-sub-clips-views-from-a-live-archive"></a>Oprette underordnede klip (visninger) fra en direkte arkiv

Mange direkte hændelser er lange kører og direkte arkiv kan indeholde flere begivenheder. Efter hændelsen live eventuelt-selskaber ender bryde direkte arkiv til logiske program skal starte og stoppe serier. Derefter kan udgive programmerne virtuelle separat uden indlæg behandler direkte arkivet og ikke oprette separate Aktiver (som ikke får fordel af de eksisterende cachelagrede fragmenter i CDNs). Eksempler på sådanne virtuelle programmer (underordnede klip) er kvartaler af en fodbold eller basketball game, innings i fodbold eller enkelte hændelser af en eftermiddag ol-program.

Du kan oprette filtre ved hjælp af start/slut gange og oprette virtuelle visninger over toppen af din live arkiv med dynamisk manifestet. 

![Subclip filter][subclip_filter]

Filtrerede aktiv:

![Skiløb][skiing]

##<a name="adjusting-presentation-window-dvr"></a>Justere præsentationsvindue (DVR)

Azure Media Services tilbyder på nuværende tidspunkt, cirkulær arkiv, hvor varigheden kan konfigureres mellem 5 minutter - 25 timer. Manifest filtrering kan bruges til at oprette et rullende DVR vindue over øverst del af arkivet, uden at slette medier. Der er mange situationer, hvor-selskaber, vil angive et begrænset DVR-vindue, hvilket flytter med den direkte kant og samtidig bevare et større arkivering vindue. Et tv-spredningsforetagende overveje at bruge de data, der er uden for vinduet DVR til at fremhæve klip, eller he\she overveje at give forskellige DVR vinduer til forskellige enheder. De fleste af mobile enheder håndterer ikke stor DVR windows (du kan have et 2 minut DVR vindue til mobilenheder og 1 time for desktopklienter).

![DVR vindue][dvr_filter]

##<a name="adjusting-livebackoff-live-position"></a>Justere LiveBackoff (direkte position)

Manifest filtrering kan bruges til at fjerne flere sekunder fra direkte kanten af et direkte program. Dette giver mulighed for-selskaber, til at se præsentationen på punktet preview publikation og oprette annonce indsætningspunktet punkter, før fremvisere modtager strømmen (som regel sikkerhedskopier-fra ved 30 sekunder). -Selskaber, kan derefter push disse reklamer til deres klient strukturer i gang for at de kan modtaget og procesoplysninger før annonce mulighed.

Ud over support annonce kan LiveBackoff bruges til at justere klienten direkte download placering, så når klienter drift og trykke på den direkte kant kan de stadig hente fragmenter fra server i stedet for at få 404 eller 412 HTTP-fejl.

![livebackoff_filter][livebackoff_filter]


##<a name="combining-multiple-rules-in-a-single-filter"></a>Kombinere flere regler i et enkelt filter

Du kan kombinere flere spamfiltrering regler i et enkelt filter. Du kan angive en regel for område for at fjerne tavle fra en direkte arkiv og også filtrere tilgængelige bitrates som et eksempel. For flere spamfiltrering regler er det resultat sammensætning (kun skæringspunkt) af disse regler.

![flere regler][multiple-rules]

##<a name="create-filters-programmatically"></a>Oprette filtre fra et program

I følgende emne beskrives Media Services-objekter, der er relateret til filtre. Emnet viser også, hvordan du opretter filtre fra et program.  

[Oprette filtre med REST API'er](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Kombinere flere filtre (filter sammensætning)

Du kan også kombinere flere filtre i en enkelt URL-adresse. 

I følgende scenario demonstrerer, hvorfor du måske kombinere filtre:

1. Du vil filtrere din video egenskaber til mobilenheder som Android eller iPAD (for at begrænse video egenskaber). Hvis du vil fjerne de uønskede egenskaber, vil du oprette et globalt filter, som egner sig til enhedsprofiler. Som nævnt ovenfor, er det globale filtre, der kan bruges til alle dine aktiver under den samme media services konto uden yderligere tilknytning. 
2. Du også vil trimme klokkeslæt for start og slut for et aktiv. For at opnå, vil du oprette et lokalt filter og angive start-/ sluttidspunkt. 
3. Du kan kombinere begge af disse filtre (uden at du har brug for at føje kvalitet filtrering til justering af filteret, vil gøre filter brugen svært kombination).

Hvis du vil kombinere filtre, skal du oprette filter navnene til manifest/listen URL-adresse med semikolonsepareret. Lad os antage, du har et filter med navnet *MyMobileDevice* , filtre egenskaber, og du har en anden navngivne *MyStartTime* til at angive et bestemt starttidspunkt. Du kan kombinere dem således:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Du kan kombinere op til 3 filtre. 

Finde flere oplysninger i [denne](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.


##<a name="know-issues-and-limitations"></a>Kende problemer og begrænsninger

- Dynamisk manifest fungerer i GOP grænser (tasten rammer) derfor justering af har GOP nøjagtighed. 
- Du kan bruge samme Filternavn for lokale og globale filtre. Bemærk, at lokale filteret har højere prioritet, som tilsidesætter globale filtre.
- Hvis du opdaterer et filter, kan det tage op til 2 minutter streaming slutpunkt til opdatering af reglerne. Hvis indholdet blev served ved hjælp af nogle filtre (og cachelagret i proxyer og CDN cache), kan ved at opdatere disse filtre medføre player mislykkede forsøg. Det er anbefalet til at rydde cachen efter opdatering af filteret. Hvis denne indstilling ikke er muligt, kan du overveje at bruge et andet filter.


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Se også

[Levering af indhold til kunder oversigt](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
 