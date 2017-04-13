<properties 
    pageTitle="Avanced Media Encoder Premium arbejdsproces selvstudier" 
    description="Dette dokument indeholder gennemgange, der viser, hvordan du udfører avancerede opgaver med Media Encoder Premium arbejdsproces, og hvordan du opretter komplekse arbejdsprocesser med Workflow Designer." 
    services="media-services" 
    documentationCenter="" 
    authors="xstof" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"  
    ms.author="xstof;xpouyat;juliako"/>

#<a name="advanced-media-encoder-premium-workflow-tutorials"></a>Avancerede Media Encoder Premium arbejdsproces selvstudier

##<a name="overview"></a>Oversigt 

Dette dokument indeholder gennemgange, der viser, hvordan du tilpasser arbejdsprocesser med **Workflow Designer**. Du kan finde de faktiske arbejdsproces filer [her](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

##<a name="toc"></a>INDHOLDSFORTEGNELSE

De følgende emner:

- [Kodning MXF til en enkelt bithastighed MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
    - [Starte en ny arbejdsproces](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new) 
    - [Brug af medier fil Input](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
    - [Undersøge mediestreams](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
    - [Tilføje en video-koder til. Generering af MP4-fil](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
    - [Kodning lydstreamet](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
    - [Multiplexing lyd og Video til en MP4-objektbeholder](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
    - [Skrive MP4-fil](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
    - [Oprette et Media Services aktiv fra outputfilen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
    - [Teste færdig arbejdsprocessen lokalt](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
- [Kodning MXF til multibitrate MP4s - dynamisk emballagen aktiveret](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
    - [Tilføje en eller flere yderligere MP4 output](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
    - [Konfiguration af output filnavnene](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
    - [Tilføje en separat lyd-registrering](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
    - [Tilføje den. ISM-SMIL fil](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
- [Kodning MXF til multibitrate MP4 - udvidet grundrids](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
    - [Oversigt over arbejdsproces til at forbedre](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_overview)
    - [Arkivere navngivningskonventioner](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
    - [Egenskaber for publicering på roden arbejdsproces](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
    - [Har oprettet outputfilen navne, der er afhængige af publicerede egenskabsværdier](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
- [Hvis du tilføjer miniaturebilleder til multibitrate MP4 output](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
    - [Oversigt over arbejdsproces til at tilføje miniaturer til](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to_multibitrate_MP4_overview)
    - [Tilføje JPG kodning](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
    - [Håndtere Color Space konvertering](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
    - [Skrive miniature](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
    - [Registrere fejl i en arbejdsproces](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
    - [Færdig arbejdsproces](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
- [Tidsbaserede justering af af multibitrate MP4 output](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
    - [Oversigt over arbejdsproces kan begynde at tilføje justering af til](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
    - [Brug af strømmen Trimmer](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
    - [Færdig arbejdsproces](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
- [Introduktion til komponenten script](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
    - [Scripting i en arbejdsproces: Hej verden](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
- [Ramme-baserede justering af af multibitrate MP4 output](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
    - [Oversigt over grundrids kan begynde at tilføje justering af til](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
    - [Ved hjælp af listen med klip XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
    - [Ændring af listen med klip fra en scripts komponent](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
    - [Tilføje en ClippingEnabled nemmere egenskab](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

##<a id="MXF_to_MP4"></a>Kodning MXF til en enkelt bithastighed MP4
 
I denne gennemgang skal vi oprette en enkelt bithastighed. MP4-fil med AAC-HE-kodet lyd fra en. MXF input-fil. 

###<a id="MXF_to_MP4_start_new"></a>Starte en ny arbejdsproces 

Åbn Workflow Designer, og vælg "Filer"-"nyt arbejdsområde"-"kode et grundrids" 

Den nye arbejdsproces vil vise 3 elementer: 

- Primære kildefil
- Clipart-listen XML
- Output fil/aktiv  

![Ny kodning arbejdsproces](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Ny kodning arbejdsproces*

###<a id="MXF_to_MP4_with_file_input"></a>Brug af medier fil Input

For at acceptere vores input mediefil, starter en med tilføjelse af en medier fil Input komponent. Søge efter den i feltet lager Søg for at tilføje en komponent til arbejdsprocessen, og træk det ønskede element til ruden Forespørgselsdesigner. Gøre dette for medier fil Input, og forbind komponenten primære kildefilen til filnavn input pinkoden fra medier fil Input.

![Forbundne mediefiler Input](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Forbundne mediefiler Input*

Før vi kan gøre meget andet, skal vi først at vise workflow designer hvad vi vil gerne bruge til at designe vores arbejdsgang med eksempelfilen. Klik på Forespørgselsdesigner ruden baggrunden for at gøre det, og se efter egenskaben primære kildefilen i egenskabsruden højre. Klik på mappeikonet, og vælg den ønskede fil til at teste arbejdsproces med. Så snart dette er gjort, vil komponenten medier fil Input Undersøg filen og udfylde dens output ben afspejler den kontrolleres filen.

![På forhånd mediefiler Input](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*På forhånd mediefiler Input*

Mens angiver med hvilke input vi vil gerne arbejde med, det ikke Fortæl endnu hvor kodet output skal flyttes til. Svarer til, hvordan den primære kildefilen blev konfigureret, nu konfigurere egenskaben Output mappe Variable lige under den.

![Konfigureret Input og Output egenskaber](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Konfigureret Input og Output egenskaber*

###<a id="MXF_to_MP4_streams"></a>Undersøge mediestreams

Ofte har den ønskede at vide, hvordan strømmen ser ud med dette flyder gennem arbejdsprocessen. Hvis du vil undersøge en stream på en hvilken som helst sted i arbejdsprocessen, bare klikke på en output eller input pinkode på alle komponenter. I dette tilfælde kan du prøve at klikke på dekomprimerede Video output PIN-kode fra Input vores Media-fil. Vil åbne en dialogboks op, som giver mulighed for at undersøge den udgående video.

![Undersøge dekomprimerede Video output pinkode](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Undersøge dekomprimerede Video output pinkode*

I dette tilfælde den fortæller os, for eksempel, vi håndtere en 1920 x 1080 input på 24 rammer sekundet i 4:2:2 udvalg for en video af næsten 2 minutter.

###<a id="MXF_to_MP4_file_generation"></a>Tilføje en video-koder til. Generering af MP4-fil

Bemærk, at nu, en dekomprimerede Video og flere dekomprimerede lyd output ben er tilgængelige til brug på vores medier fil Input. Hvis du vil kode indgående videoen, vi har brug for en kodning komponent - i dette tilfælde til at oprette. MP4-filer.

Hvis du vil kode video strømmen til H.264, tilføje komponenten AVC Video Encoder Forespørgselsdesigner overflade. Denne komponent tager en uncompress video stream som input og leverer en AVC komprimeret video stream på dens output pinkode.

![Afbrudt forbindelsen AVC Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Afbrudt forbindelsen AVC Encoder*

Egenskaberne finde ud af, hvordan den kodning nøjagtigt sker. Lad os har nærmere på nogle af de vigtigste indstillinger:

- Output bredde og højde Output: bestemmer opløsningen på kodet videoen. I dette tilfælde skal du går med 640 x 360
- Ramme rente: Når den er indstillet til passthrough vil den kun træffe kilde ramme rente, er det muligt at overskrive denne via. Bemærk, at disse konvertering af en billedhastighed ikke er bevægelsessti-kompensation.
- Profil og niveau: bestemmer AVC profil- og niveau. Klik på ikonet spørgsmålstegn på komponenten AVC Video Encoder nemt finde flere oplysninger om de forskellige niveauer og profiler, og Hjælp-siden vises flere detaljer om hvert af niveauerne. Lad os til vores eksempel med Main profil på niveau 3.2 (standard).
- Bedømme kontrolelement tilstand og bithastighed (kbps): i vores scenarie vi vælge en konstant bithastighed (CBR) output ad 1200 kbps
- Videoformat: Dette er om VUI (Video brugervenligheden oplysninger), der bliver skrevet til H.264 strømmen (side oplysninger, der kan være bruges af en dekoder til at forbedre visningen, men ikke er nødvendige for korrekt afkode):
- NTSC (typisk for USA eller Japan, ved hjælp af 30 fps)
- VEN (typisk for Europa, ved hjælp af 25 fps)
- GOP størrelse tilstand: Vi vil konfigurere fast GOP størrelse til vores formål med en nøgle Interval på to sekunder med lukket GOPs. Dette sikrer kompatibilitet med dynamisk emballagen Azure Media Services indeholder.

Hvis du vil feed vores AVC encoder, kan du oprette forbindelse dekomprimerede Video output pinkode fra komponenten medier fil Input til dekomprimerede Video input pinkoden fra AVC encoder.

![Forbundne AVC Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Forbundne AVC Main encoder*

###<a id="MXF_to_MP4_audio"></a>Kodning lydstreamet

Vi har kodet video på dette tidspunkt, men det oprindelige dekomprimerede lydstreamet stadig skal komprimeres. I dette går vi med AAC kodning af komponenten AAC Encoder (Dolby). Føje den til arbejdsprocessen.

![Afbrudt forbindelsen AVC Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Afbrudt forbindelsen AAC encoder*

Nu er der inkompatibilitet: der er kun en enkelt dekomprimerede lyd input pinkode fra AAC Encoder, mens højst sandsynligt medier fil Input har to forskellige dekomprimerede lydstreamet tilgængelig: et til venstre lyd kanalen og et til højre. (Hvis du håndtere Indram lyd, der er 6 kanaler). Så er det ikke muligt at forbinde direkte lyden fra medier fil Input kilden til det lyd AAC-encoder. Komponenten AAC forventer en såkaldt "sekvensopdelt" lydstreamet: en enkelt stream, der indeholder både venstre og de rigtige kanaler interleaved med hinanden. Når vi mestrer fra vores medier kildefil er hvilke lydspor på hvilken position i kilden, kan vi generere sådanne sekvensopdelt lydstreamet med korrekt tildelte højttaler stillinger til venstre og højre.

Først skal en genereres en sekvensopdelt stream fra de nødvendige kilde lyd kanaler. Komponenten lyd Stream Interleaver håndtere sagen til os. Føje den til arbejdsprocessen og forbinde lyd output fra medier fil Input i den.

![Forbundne Lydstreamet Interleaver](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Forbundne Lydstreamet Interleaver*

Nu hvor vi har en sekvensopdelt lydstreamet, ikke har vi stadig angive hvor du kan tildele venstre eller højre højttaler stillinger til. For at angive, kan vi udnytte højttaler placering tilsendt.

![Tilføje en højttaler placering tilsendt](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Tilføje en højttaler placering tilsendt*

Konfigurere højttaler placering tilsendt til brug med en stereo input stream ved hjælp af en Encoder forudindstillede Filter bestående af "Custom", og kanal forudindstillede kaldet "2.0 (L, R)". (Dette vil tildele den venstre højttaler position kanal 1 og højre højttaler placering kanal 2.)

Output fra højttaler placering tilsendt tilsluttes input fra AAC Encoder. Fortæl derefter AAC-koder til at arbejde med en "2.0 (L, R)" kanal forudindstillede, så det ved for at håndtere stereo lyd som input.

###<a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexing lyd og Video til en MP4-objektbeholder

Den angivne vores AVC kodet video stream og vores AAC-kodet lydstreamet, vi kan registrere begge til en. MP4 beholder. Processen med at blande forskellige streams i en enkelt hedder "multiplexing" (eller "muxing"). I dette tilfælde vi interleaving lyd og video streams i en sammenhængende enkelt. MP4-pakke. Komponenten, koordinater dette for en. MP4 objektbeholder hedder ISO MPEG-4-mulitplekser. Føje et til Forespørgselsdesigner overfladen og forbinde både AVC Video Encoder og AAC Encoder til dens input.

![Forbundne MPEG4 mulitplekser](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Forbundne MPEG4 mulitplekser*

###<a id="MXF_to_MP4_writing_mp4"></a>Skrive MP4-fil

Når du skriver en outputfil, bruges komponenten fil Output. Vi kan forbinde dette til output fra ISO MPEG-4-mulitplekser, så dens output bliver skrevet på disken. Oprette forbindelse objektbeholder (MPEG-4) output pinkode til Skriv input pinkoden af fil Output for at gøre dette.

![Forbundne fil Output](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Forbundne fil Output*

Filnavnet, der skal bruges, bestemmes af egenskaben fil. Selvom denne egenskab kan være hårdt til en given værdi, vil sandsynligvis en til at angive den via et udtryk i stedet.

At arbejdsprocessen automatisk bestemme output filen navnet i egenskaben fra et udtryk, skal du klikke på knappen er ud for filnavnet (ud for mappeikonet). Vælg "Udtryk" på rullelisten. Dette vil få vist redigeringsprogrammet udtryk. Rydde indholdet af editoren først.

![Tom Expression Editor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Tom Expression Editor*

Redigeringsprogrammet udtryk giver mulighed for at angive en konstant værdi, blandet med en eller flere variabler. Variabler starter med et dollartegn. Når du har trykket på $ for, vises editoren afkrydsningsfeltet rulleliste med et udvalg af tilgængelige variabler. I dette tilfælde bruger vi en kombination af variablen output directory og variablen base input fil navn:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Udfyldt af Expression Editor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Udfyldt af Expression Editor*

>[AZURE.NOTE]For at kunne se få vist en outputfil af din kodning sag i Azure, skal du angive en værdi i editoren udtryk. 

Når du bekræfter udtrykket ved at trykke på ok, egenskabsvinduet få vist til hvilken værdi fil egenskaben fortolkes på dette tidspunkt.

![Fil udtrykket giver output dir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Fil udtrykket giver output dir*

###<a id="MXF_to_MP4_asset_from_output"></a>Oprette et Media Services aktiv fra outputfilen

Mens vi har skrevet en MP4 outputfil, nødt vi stadig til at angive, at denne fil tilhører output aktivet som media services genererer som et resultat af udførelse af denne arbejdsgang. Noden Output fil/aktiv på lærredet for arbejdsgangen bruges til denne slutningen. Alle indgående filer til denne node bliver en del af den resulterende Azure Media Services aktiv.

Forbind komponenten fil Output til komponenten Output fil/aktiv for at afslutte arbejdsprocessen.

![Færdig arbejdsproces](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Færdig arbejdsproces*

###<a id="MXF_to_MP4_test"></a>Teste færdig arbejdsprocessen lokalt

For at teste arbejdsprocessen lokalt, tryk på afspilningsknappen i værktøjslinjen øverst. Når arbejdsprocessen fuldført, kan du undersøge output oprettes i outputmappen konfigureret. Du får vist færdig MP4 outputfilen, der er kodet fra filen MXF inputkilde.

##<a id="MXF_to_MP4_with_dyn_packaging"></a>Kodning MXF i MP4 - multibitrate dynamiske emballagen aktiveret

I denne gennemgang vil vi oprette et sæt af flere bithastighed MP4-filer med AAC-kodet lyd fra en enkelt. MXF input-fil.

Når en multi-bithastighed aktiv output ønskes til brug sammen med de dynamiske emballagen funktioner, som Azure Media Services, flere GOP venstrejusteret MP4-filer for hver en anden bithastighed og opløsning skal oprettes. Hvis du vil gøre det, giver gennemgangen [Kodning MXF til en enkelt bithastighed MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) os et godt udgangspunkt.

![Starte arbejdsprocessen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Starte arbejdsprocessen*

###<a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Tilføje en eller flere yderligere MP4 output

Hver MP4-fil i vores resulterende Azure Media Services aktiv understøtter en anden bithastighed og opløsning. Lad os tilføje en eller flere MP4 outputfiler til arbejdsprocessen.

For at sikre, at vi har alle vores video kodere, der er oprettet med de samme indstillinger, er det passer bedst til at duplikere den eksisterende AVC Video Encoder og konfigurere en anden kombination af opløsning og bithastighed (Lad os tilføje en af 960 x 540 på 25 rammer sekundet med 2,5 Mbps). Hvis du vil duplikere eksisterende encoder, Indsæt Kopiér den på Forespørgselsdesigner overfladen.

Oprette forbindelse dekomprimerede Video output pinkode af medier fil Input til vores nye AVC-komponent.

![Anden AVC encoder forbindelse](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Anden AVC encoder forbindelse*

Nu tilpasse konfigurationen til vores nye AVC encoder til at få vist 960 x 540 med 2,5 Mbps. (Brug dens egenskaber "Output bredde", "Output højde" og "Bithastighed (kbps)" i dette).

Givet vi vil bruge den resulterende aktiv sammen med Azure Media Services dynamiske emballagen, streaming slutpunktet skal kunne frembringe fra filerne MP4 HLS/Fragmented MP4/TANKESTREG fragmenter, der er justeret nøjagtigt til hinanden på en måde, klienter, der skifter mellem forskellige bitrates få en enkelt udglattede uafbrudt video og lyd oplevelse. Hvis du vil gøre, der udføres, har vi brug at sikre, at i egenskaberne for begge AVC kodere GOP ("gruppe af billeder") størrelse for begge MP4-filer er indstillet til 2 sekunder, hvilket kan udføres af:

- konfiguration af GOP størrelse tilstand til fast GOP størrelse og
- Tasten ramme intervallet til to sekunder.
- også angive stående GOP IDR kontrolelementet til lukket GOP at sikre, at alle GOP på deres egne uden afhængigheder

Hvis du vil gøre vores arbejdsproces praktisk at forstå, omdøbe den første AVC encoder til "AVC Video Encoder 640 x 360 1200kbps" og den anden AVC encoder "AVC Video Encoder 960 x 540 2500 kbps".

Tilføj nu en anden ISO MPEG-4 multiplekser og en anden fil Output. Oprette forbindelse på mulitplekser til den nye AVC encoder, og Sørg for dens output omdirigeres til Output-fil. Forbind AAC lyd encoder output til den nye mulitplekser input også. Fil Output kan tur. derefter forbindelse til noden Output fil/aktiv for at føje det til det Media Services aktiv, der oprettes.

![Anden Muxer og fil Output forbindelse](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Anden Muxer og fil Output forbindelse*

Konfigurere tilstanden for den mulitplekser afsnit til GOP antal eller varighed for kompatibilitet med Azure Media Services dynamiske emballagen, og Angiv GOPs per afsnit til 1. (Dette skal være standard).

![Muxer afsnit tilstande](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer afsnit tilstande*

Bemærk: kan du gentage denne proces for eventuelle andre bithastighed og opløsning kombinationer skal har føjet til aktiv output.

###<a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfiguration af output filnavnene

Vi har mere end én enkelt fil, der er føjet til output aktiv. Dette giver behov for at kontrollere filnavnene for hver af outputfilerne er forskellige fra hinanden og måske også anvende en filnavngivning konferencen så det bliver Ryd fra navnet på fil, du har med.

Navngivning af output kan styres gennem udtryk i designer. Ruden med egenskaber for en fil Output komponenter og åbne redigeringsprogrammet udtryk for egenskaben fil. Vores første outputfilen blev konfigureret gennem følgende udtryk (se selvstudium for at gå fra [MXF til en enkelt bithastighed MP4 output](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Det betyder, at vores filnavn bestemmes af to variabler: outputmappen til at skrive i og navnet på kildefilen base. Den tidligere der vises som en egenskab i roden arbejdsproces, og disse bestemmes af den indgående fil. Bemærk, at outputmappen er, hvad du skal bruge til lokale test; Denne egenskab, tilsidesættes af arbejdsproces-program, når arbejdsprocessen udføres med skybaseret media-processor i Azure Media Services.
Ændre den første fil navngivning af udtryk til for at give begge vores outputfiler, en ensartet output navngivning skal:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

og andet til at:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Udføre en mellemliggende test køre for at sikre at begge MP4 outputfiler genereres korrekt.

###<a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Tilføje en separat lyd-registrering

Som vi ser senere, når vi genererer en .ism filer for at gå med vores MP4 outputfiler, kræver vi også en ren MP4-fil som den lydspor til vores tilpasset streaming. Føj en ekstra muxer til arbejdsprocessen (ISO-MPEG-4 mulitplekser) for at oprette denne fil, og forbind AAC-koder output pinkode med dens input pinkode for Registrer 1.

![Lyd Muxer tilføjet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Lyd Muxer tilføjet*

Oprette en tredje fil Output komponent output udgående strømmen fra muxer og konfigurere filen navngivning af udtryk som:
    
    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Lyd Muxer oprette fil Output](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Lyd Muxer oprette fil Output*

###<a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Tilføje den. ISM-SMIL fil

For dynamiske emballagen til at fungere sammen med både MP4-filer (og kun med lyd MP4) i vores Media Services aktiv, skal vi også en manifestfil (også kaldet en "SMIL"-fil: synkroniseret multimedier Integration sprog). Denne fil angiver til Azure Media Services, hvilke MP4-filer er tilgængelige for dynamisk emballagen, og hvilke af dem, skal du tænke på at lyd streaming. En typisk manifestfil for et sæt MP4's med en enkelt lydstreamet ser sådan ud:
    
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>

Filen .ism indeholder inden for en switch-sætning, en reference til hver af de enkelte MP4 videofiler og ud over disse også en (eller flere) lydfil referencer til en MP4, der kun indeholder lyden.

Generering af Manifestfilen for vores sæt af MP4's kan udføres via en komponent, kaldet "AMS manifestet forfatteren". Træk den til overfladen for at bruge det, og forbind "Skrive fuldført" output benene fra de tre fil Output-komponenter til AMS manifestet Writer input. Sørg for at oprette forbindelse output fra AMS manifestet Writer til Output fil/aktiv.

Konfigurere output .ism filnavn med et udtryk, som i vores andre fil output komponenter:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Vores færdig arbejdsproces ligner den nedenfor:

![Færdig MXF til multibitrate MP4 arbejdsproces](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Færdig MXF til multibitrate MP4 arbejdsproces*

##<a id="MXF_to__multibitrate_MP4"></a>Kodning MXF til multibitrate MP4 - udvidet grundrids

I den [forrige arbejdsproces gennemgang](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) har vi set, hvordan en enkelt MXF input aktiv kan konverteres til et output aktiv med flere bithastighed MP4-filer, en ren MP4-fil og en manifestfil til brug sammen med Azure Media Services dynamiske emballagen.

Denne gennemgang viser, hvordan nogle af aspekter kan udvidet og foretaget mere praktisk.

###<a id="MXF_to_multibitrate_MP4_overview"></a>Oversigt over arbejdsproces til at forbedre

![Multibitrate MP4 arbejdsproces til at forbedre](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Multibitrate MP4 arbejdsproces til at forbedre*

###<a id="MXF_to__multibitrate_MP4_file_naming"></a>Arkivere navngivningskonventioner

Vi angivet et simpelt udtryk som grundlag for oprettelse af output filnavne i den forrige arbejdsproces. Vi har nogle kopiering via: alle de enkelte output fil komponenterne angivet sådanne udtryk.

For eksempel parametrene vores fil output til den første videofil med dette udtryk:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Mens anden output, til video har vi et udtryk som:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Ville det ikke være renere mindre fejl giver risiko og mere praktisk, hvis vi kunne fjerne nogle af denne kopiering og gøre tingene mere kan konfigureres i stedet? Heldigvis kan vi: den designer udtryk funktioner sammen med muligheden for at oprette brugerdefinerede egenskaber på vores arbejdsproces roden kan give os et ekstra lag af nemmere.

Lad os antage vi vil drive filnavn konfiguration fra bitrates af de enkelte MP4-filer. Disse bitrates, får vi har til formål at konfigurere i et centralt sted (i roden af vores graf), fra hvor de skal være adgang til at konfigurere og drev filnavnet genereres. For at gøre dette, er vi starter ved at publicere egenskaben bithastighed fra begge AVC kodere i roden af vores arbejdsproces, så det bliver tilgængelig både i roden fra AVC kodere. (Også selvom vises to forskellige steder, der er kun én underliggende værdi).

###<a id="MXF_to__multibitrate_MP4_publishing"></a>Egenskaber for publicering på roden arbejdsproces

Åbn den første AVC encoder, gå til egenskaben bithastighed (kbps), og vælg Publicer på rullelisten.

![Publicering af egenskaben bithastighed](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publicering af egenskaben bithastighed*

Konfigurere dialogboksen Udgiv på Udgiv i roden af vores arbejdsproces graph med en publiceret navnet "video1bitrate" og et læsevenligt visningsnavn af "Video 1 bithastighed". Konfigurere en brugerdefineret gruppenavn kaldet "Streaming Bitrates", og klik på Publicer.

![Publicering af egenskaben bithastighed](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Publicering af dialogboksen for bithastighed egenskab*

Gentag den samme for egenskaben bithastighed for den anden AVC encoder, og kald den "video2bitrate" med et visningsnavn af "Video 2 bithastighed" i den samme brugerdefinerede gruppe "Streaming Bitrates".

Hvis vi nu undersøge arbejdsproces rod egenskaber, ser vi vores brugerdefineret gruppe med de to publicerede egenskaber vises. Begge afspejler værdien af deres respektive AVC encoder bithastighed.

![Rekvisitter til publicerede bithastighed på arbejdsproces rod](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Når vi vil have adgang til disse egenskaber fra kode eller et udtryk, kan vi gøre således:

- fra indbygget kode fra en komponent lige under roden: node.getPropertyAsString('.. / video1bitrate', null)
- i et udtryk: ${ROOT_video1bitrate}
 
Lad os udføre gruppen "Streaming Bitrates" ved at publicere vores lydspor bithastighed på den samt. Søg efter indstillingen bithastighed i egenskaberne for AAC Encoder, og vælg Publicer på rullelisten ud for den. Udgive i roden af diagrammet med navnet "audio1bitrate", og få vist navn "Lyd 1 bithastighed" i vores brugerdefineret gruppe "Streaming Bitrates".

![Dialogboks for lyd bithastighed udgivelse](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Dialogboks for lyd bithastighed udgivelse*

![Resulterende rekvisitter til video og lyd på rod](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Resulterende rekvisitter til video og lyd på rod*

Bemærk, at hvis du ændrer nogen af disse tre værdier også konfigurerer igen og ændrer værdierne i de respektive komponenter, de er sammenkædet med (og hvor publiceret fra).

###<a id="MXF_to__multibitrate_MP4_output_files"></a>Har oprettet outputfilen navne, der er afhængige af publicerede egenskabsværdier

I stedet for hardcoding vores genererede filnavne skal vi kan nu ændre vores filnavn udtryk på hver af fil Output komponenterne, der er afhængige af egenskaberne bithastighed vi blot udgives på graf roden. Starter med vores første fil output, finder du egenskaben fil og redigere udtryk som dette:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

De forskellige parametre i dette udtryk kan være adgang til og angives af nå dollartegn på tastaturet i vinduet udtryk. En af de tilgængelige parametre er vores video1bitrate egenskab, som vi tidligere publiceret.

![Få adgang til parametre i et udtryk](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Få adgang til parametre i et udtryk*

Gør det samme fil output til vores anden video til:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

og kun med lyd fil output til:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Hvis vi nu ændrer bithastighed på grund af video eller lyd filerne, de respektive encoder kan konfigureres og bithastighed-baseret fil navn konferencen anvendes al automatisk.

##<a id="thumbnails_to__multibitrate_MP4"></a>Hvis du tilføjer miniaturebilleder til multibitrate MP4 output

Start fra en arbejdsproces, som genererer [en multibitrate MP4 output fra en MXF input](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), og kigger vi nu på at tilføje miniaturer til output.

###<a id="thumbnails_to__multibitrate_MP4_overview"></a>Oversigt over arbejdsproces til at tilføje miniaturer til

![Multibitrate MP4 arbejdsprocessen skal starte fra](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Multibitrate MP4 arbejdsprocessen skal starte fra*

###<a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Tilføje JPG kodning

Centrale vores miniature generering af bliver komponenten JPG Encoder, kunne output JPG-filer.

![JPG Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG Encoder*

Vi kan dog direkte Forbind vores dekomprimerede Video-stream fra medier fil Input i JPG-encoder. I stedet forventes skal afleveres enkelte billeder. Det, kan vi via Video ramme port komponenten.

![Oprette forbindelse port en ramme til JPG encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Oprette forbindelse port en ramme til JPG encoder*

Ramme port én gang hver så mange sekunder eller rammer giver en videoramme til at videregive. Intervallet og det klokkeslæt, forskydning med som dette sker kan konfigureres i egenskaberne.

![Egenskaber for video ramme port](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Egenskaber for video ramme port*

Lad os oprette en miniature hvert minut ved at angive tilstanden til tid (sekunder) og intervallet skal 60.

###<a id="thumbnails_to__multibitrate_MP4_color_space"></a>Håndtere Color Space konvertering

Mens forekommer logiske begge dekomprimerede Video ben på rammen port og medier fil Input kan nu forbindes, vil vi får en advarsel, hvis vi vil gøre det.

![Farve mellemrum fejl ved indtastning](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Farve mellemrum fejl ved indtastning*

Dette skyldes, at den måde, i hvilken farver oplysninger er repræsenteret i vores oprindelige rå dekomprimerede video-stream, kommer fra vores MXF er forskellig fra hvad JPG Encoder forventer. Mere specifikt forventes en såkaldt "farve mellemrum" "RGB" eller "Gråtoneskala" at gå ind. Det betyder, at Video ramme tilpasses indgående video stream skal have en konvertering, der er anvendt angående dens farve diskplads først.

Træk til arbejdsprocessen farve mellemrum konverteringsprogram - Intel og forbinde den til vores ramme port.

![Oprette forbindelse til en farve mellemrum konverteringsprogram:](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Oprette forbindelse til en farve mellemrum konverteringsprogram:*

Vælg BGR 24 posten fra listen forudindstillet i egenskabsvinduet.

###<a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Skrive miniature

Forskellig fra vores MP4-video, komponenten JPG Encoder output vil blive sendt mere end én fil. For at løse dette, der kan bruges en Scene Søg JPG fil Writer komponent: det kan tage indgående JPG miniature og skrive dem ud hvert filnavn, der indsamlingsforespørgslen efterfulgt af et andet tal. (Tal, der typisk angiver antallet sekunder/enheder i strømmen som miniaturen for er tegnet fra.)


![Introduktion til Scene Søg JPG fil Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Introduktion til Scene Søg JPG fil Writer*

Konfigurere egenskaben Output mappesti med udtrykket: ${ROOT_outputWriteDirectory} 

og egenskaben præfiks for filnavn med: 

    ${ROOT_sourceFileBaseName}_thumb_

Præfikset afgør, hvordan de miniature filer der navngives. De vil skal efterstilles med et tal, der angiver den rulleboks position i strømmen.


![Scene Søg JPG fil Writer egenskaber](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Scene Søg JPG fil Writer egenskaber*

Forbinde Scene Søg JPG fil Writer til noden Output fil/aktiv.

###<a id="thumbnails_to__multibitrate_MP4_errors"></a>Registrere fejl i en arbejdsproces

Oprette forbindelse input fra farve mellemrum konverteringsprogram til rå dekomprimerede video output. Nu udføre en lokal test køre til arbejdsprocessen. Der er en god mulighed for arbejdsprocessen pludselig stopper afholde og angive med en rød kontur på komponenten, der opstod en fejl:

![Farve mellemrum konverteringsprogram fejl](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Farve mellemrum konverteringsprogram fejl*

Klik på det lille røde "E" ikon i øverste højre hjørne af komponenten farve mellemrum konverteringsprogram til at se hvad er årsagen til kodning forsøg mislykkedes.

![Farve mellemrum konverteringsprogram fejldialogboks](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Farve mellemrum konverteringsprogram fejldialogboks*

Den omdannes ud, som du kan se, at indgående farve afstanden standarden for farve mellemrum konverteringsprogram skal være rec601 til vores ønskede konvertering af YUV til RGB. Vores stream angiver ikke tilsyneladende, at det er rec601. (Modtaget 601 er et standard for kodning interlaced analoge video signaler i digital video form. Det angiver et aktive område, som dækker 720 lysstyrke eksempler og 360 chrominance eksempler hver linje. Den farve, kodning system, der kaldes YCbCr 4:2:2.)

Du kan løse problemet, skal vi angive på metadataene i vores stream, som vi håndtere rec601 indhold. Gøre det vi bruger en Video Data Type Updater komponent, som vi sætte mellem vores rå kilde- og komponenten farve mellemrum konvertering. Denne data type updater giver mulighed for manuel opdatering af visse video data typeegenskaber. Konfigurer den til at angive farve mellemrum Standard af "Modtaget 601". Dette medfører Video Data Type Updater til at mærke strømmen med "Modtaget 601" farve mellemrum, hvis der er ikke farve ledig plads, der er defineret endnu. (Det tilsidesætter ikke alle eksisterende metadata, medmindre Tilsidesæt afkrydsningsfeltet blev tjekket.)

![Opdatering af farve mellemrum Standard på Data Type Updater](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Opdatering af farve mellemrum Standard på Data Type Updater*

###<a id="thumbnails_to__multibitrate_MP4_finish"></a>Færdig arbejdsproces

Nu, hvor vores vores arbejdsprocessen er fuldført, skal du gøre en anden test Kør for at se den overfører.

![Færdig arbejdsproces for flere mp4 output med miniaturer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Færdig arbejdsproces for flere mp4 output med miniaturer*

##<a id="time_based_trim"></a>Tidsbaserede justering af af multibitrate MP4 output

Start fra en arbejdsproces, som genererer [en multibitrate MP4 output fra en MXF input](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), og kigger vi nu på justering af kilde videoen baseret på tidsstempler.

###<a id="time_based_trim_start"></a>Oversigt over arbejdsproces kan begynde at tilføje justering af til

![Starte arbejdsprocessen skal tilføjes justering af til](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Starte arbejdsprocessen skal tilføjes justering af til*

###<a id="time_based_trim_use_stream_trimmer"></a>Brug af strømmen Trimmer

Komponenten Stream Trimmer kan trimme starten og slutningen af en input-stream base på timing oplysninger (sekunder, minutter,...). Trimmeren understøtter ikke ramme-baserede justering af.

![Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Stream Trimmer*

I stedet for sammenkædning AVC kodere og højttaler placering tilsendt med medier fil Input direkte, sætte vi mellem dem stream trimmer. (En for det video signal og en for det sekvensopdelt lyd signal.)

![Placere Stream Trimmer ind imellem](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Placere Stream Trimmer ind imellem*

Lad os konfigurere Trimmeren, så vi kun behandler video og lyd mellem 15 sekunder og 60 sekunder i videoen.

Gå til egenskaberne for den Video Stream Trimmer og konfigurere både starttidspunkt (15s) og sluttidspunkt (60s) egenskaber. For at sikre, at både vores lyd- og trimmer konfigureres altid de samme start og Afslut værdier, kan vi publicere dem i roden af arbejdsprocessen.

![Publicere start klokkeslæt egenskab fra Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicere start klokkeslæt egenskab fra Stream Trimmer*

![Publicere egenskaben dialogboksen til starttidspunktet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publicere egenskaben dialogboksen til starttidspunktet*

![Publicere egenskaben dialogboksen til sluttidspunkt](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publicere egenskaben dialogboksen til sluttidspunkt*


Hvis vi nu undersøge i roden af vores arbejdsproces, bliver begge egenskaber pænt viste og konfigurerbar derfra.

![Publicerede egenskaber, der er tilgængelige på rod](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publicerede egenskaber, der er tilgængelige på rod*

Nu åbner justering af egenskaberne fra lyd trimmer og konfigurere både start- og sluttidspunkter med et udtryk, der refererer til de publicerede egenskaber i roden af vores arbejdsproces.

Til justering af audio starttidspunkt:

    ${ROOT_TrimmingStartTime}

og dens sluttidspunkt:

    ${ROOT_TrimmingEndTime}

###<a id="time_based_trim_finish"></a>Færdig arbejdsproces

![Færdig arbejdsproces](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Færdig arbejdsproces*


##<a id="scripting"></a>Introduktion til komponenten script

Script komponenter kan udføre vilkårlig scripts i udførelse af faserne i vores arbejdsproces. Der er fire forskellige scripts, der kan udføres, hver med specifikke egenskaber og deres egen plads i arbejdsproces livscyklus:

- **commandScript**
- **realizeScript**
- **processInputScript**
- **lifeCycleScript**

Dokumentationen for komponenten scripts går mere detaljeret for hver af ovenstående. I [følgende afsnit](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)bruges komponenten **realizeScript** scripting til at oprette en cliplist xml på farten, når arbejdsprocessen startes. Dette script kaldes under installationen komponent sker kun én gang i dets livscyklus.


###<a id="scripting_hello_world"></a>Scripting i en arbejdsproces: Hej verden

Træk en scripts komponent til Forespørgselsdesigner overfladen, og Omdøb den (eksempelvis "SetClipListXML").

![Tilføje en script-komponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Tilføje en script-komponent*

Når du undersøge egenskaberne for komponenten scripts, bliver de fire forskellige script typer vist, hver især kan konfigureres til et andet script.

![Egenskaber for script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Egenskaber for script*

Fjern processInputScript og åbne redigeringsprogrammet for realizeScript. Vi er nu konfigureret og klar til at starte scripting.

Scripts er skrevet i Groovy, en dynamisk kompileret scriptsproget for Java platform, der bevarer kompatibilitet med Java. De fleste Java-kode er faktisk gyldige Groovy kode.

Lad os oprette et simpelt Hej verden groovy script i forbindelse med vores realizeScript. Angiv følgende oplysninger i editoren:

    node.log("hello world");

Udføre en lokal test Kør nu. Når denne kørsel Undersøg (via fanen System på komponenten scripts) egenskaben logfiler.

![Hej verden log output](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hej verden log output*

Objektet node vi kalder metoden log på, refererer til vores aktuelle "node" eller komponenten vi scripting i. Hver komponent har således muligheden for at output logføring data tilgængelige via fanen system. I dette tilfælde output vi strengkonstant "Hej verden". Vigtigt at forstå her er, at det kan være skal være en uvurderlig fejlfindingsværktøj, giver dig indsigt i, hvilke scriptet faktisk udfører.

Fra vores scripting miljø har vi også adgang til egenskaber på andre komponenter. Prøv dette:


    //inspect current node: 
    def nodepath = node.getNodePath(); 
    node.log("this node path: " + nodepath);
    
    //walking up to other nodes: 
    def parentnode = node.getParentNode(); 
    def parentnodepath = parentnode.getNodePath(); 
    node.log("parent node path: " + parentnodepath);
    
    //read properties from a node: 
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null ); 
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null); 
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);

Vinduet vores log vil vise os følgende:

![Log output til at få adgang til node stier](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Log output til at få adgang til node stier*


##<a id="frame_based_trim"></a>Ramme-baserede justering af af multibitrate MP4 output

Start fra en arbejdsproces, som genererer [en multibitrate MP4 output fra en MXF input](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), og kigger vi nu på justering af kilde videoen baseret på rammen tæller.

###<a id="frame_based_trim_start"></a>Oversigt over grundrids kan begynde at tilføje justering af til

![Arbejdsprocessen til at begynde at tilføje justering af til](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Arbejdsprocessen til at begynde at tilføje justering af til*

###<a id="frame_based_trim_clip_list"></a>Ved hjælp af listen med klip XML

I alle tidligere arbejdsproces selvstudier brugte vi komponenten medier fil Input som vores video inputkilde. For dette scenarie dog bruger vi komponenten Clip kildelisten i stedet. Bemærk, at det ikke skal være den foretrukne metode til at arbejde; Brug kun kildelisten clipart, når der er et reelt tal grund til at gøre det (som i den under tilfælde, hvor vi foretager brug af funktionerne klip listen justering af).

Træk komponenten klip kildelisten til designoverfladen for at skifte fra vores medier fil Input til kildelisten clipart, og forbinde klip listen XML pinkoden til noden klip XML-liste i workflow designer. Dette skal udfylde kildelisten klip med output ben ifølge vores input video. Nu oprette forbindelse fra dekomprimerede Video og lyd dekomprimerede benene på kildelisten klip til de respektive AVC kodere og lyd Stream Interleaver. Nu fjerne medier fil Input.

![Erstattet medier fil Input med kildelisten multimedieklip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Erstattet medier fil Input med kildelisten multimedieklip*

Komponenten klip kildelisten tager som input "Klip listen XML". Når du vælger kildefilen til at teste med lokalt, er denne klip listen xml automatisk udfyldt for dig.

![Automatisk udfyldt klip listen XML-egenskab](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automatisk udfyldt klip listen XML-egenskab*

Leder lidt tættere på xml, er dette, hvordan det ser ud:

![Redigere multimedieklip listen dialogboksen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Redigere multimedieklip listen dialogboksen*

Dette afspejler dog ikke funktionerne i clip listen xml. Vi har en af mulighederne er at tilføje et "Trim" element under begge video og lyd kilden, således:

![Tilføje et Fjern.overflødige.blanke element til listen med klip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Tilføje et Fjern.overflødige.blanke element til listen med klip*

Hvis du ændrer klip listen xml ud over og udføre en lokal test, der kører, kan du se videoen korrekt blevet trimmet mellem 10 og 20 sekunder i videoen.

I modsætning til hvad sker der, når du gør en lokal Kør via, vil denne meget samme cliplist xml ikke har den samme effekt, når anvendes i en arbejdsproces, der kører i Azure Media Services. Når Azure Premium Encoder starter, genereres cliplist xml, hver gang igen, baseret på input filen blev angivet kodning jobbet. Det betyder, at de ændringer, vi gør på xml desværre skal tilsidesættes.

Hvis du vil tæller cliplist xml der sletning, når et kodning job startes, kan vi igen generere den løbende lige efter starten af vores arbejdsproces. Sådan brugerdefinerede handlinger kan blive ført til det, der kaldes en "scripts komponent". Du kan finde flere oplysninger i afsnittet [Introduktion komponenten scripts](media-services-media-encoder-premium-workflow-tutorials.md#scripting).


Træk en scripts komponent til Forespørgselsdesigner overfladen, og Omdøb den til "SetClipListXML".

![Tilføje en script-komponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Tilføje en script-komponent*

Når du undersøge egenskaberne for komponenten scripts, bliver de fire forskellige script typer vist, hver især kan konfigureres til et andet script.

![Egenskaber for script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Egenskaber for script*


###<a id="frame_based_trim_modify_clip_list"></a>Ændring af listen med klip fra en scripts komponent

Før vi kan skrive cliplist xml, der oprettes under arbejdsgangen Start igen, skal vi have adgang til cliplist XML-egenskab og indhold. Vi kan du gøre således:

    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);

![Indgående klip liste bliver logget](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Indgående klip liste bliver logget*

Først skal vi en metode til at afgøre fra hvilke punkt indtil hvor vi vil trimme videoen. Hvis dette praktisk til mindre tekniske brugeren af arbejdsprocessen, skal du publicere to egenskaber i roden af diagrammet. Højreklik på den Forespørgselsdesigner overflade for at gøre dette, og vælg "Tilføj egenskab":

- Første egenskab: "ClippingTimeStart" af typen: "TIDSKODE"
- Anden egenskab: "ClippingTimeEnd" af typen: "TIDSKODE"

![Tilføje egenskaben dialogboksen til skærmklip starttidspunktet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Tilføje egenskaben dialogboksen til skærmklip starttidspunktet*

![Publiceret skærmklip rekvisitter til tid på arbejdsproces rod](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publiceret skærmklip rekvisitter til tid på arbejdsproces rod*

Konfigurere begge egenskaber til en passende værdi:

![Konfigurere skærmklip start og Afslut egenskaber](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurere skærmklip start og Afslut egenskaber*

Nu, fra i vores script vi kan få adgang til begge egenskaber, som denne:

    
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);

![Log vindue, der viser starten og slutningen af skærmklip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Log vindue, der viser starten og slutningen af skærmklip*

Lad os Fortolk tidskode strengene til en mere praktisk at bruge format ved hjælp af en simpel regulære udtryk:
    
    //parse the start timing: 
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1); 
    node.log("timecode start is: " + starttimecode); 
    def startframerate = startregresult.group(2); 
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend); 
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2); 
    node.log("framerate end is: " + endframerate);

![Log af vinduet med output fra fortolket tidskode](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Log af vinduet med output fra fortolket tidskode*

Disse oplysninger ved hånden, kan vi nu redigere cliplist xml afspejler start- og sluttidspunkter for det ønskede ramme nøjagtige udklip af filmen.

![Script-kode til at tilføje trim elementer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Script-kode til at tilføje trim elementer*

Dette skete gennem normal strengmanipulation strengfunktioner. Den resulterende ændrede clip listen xml skrives tilbage til egenskaben clipListXML i roden arbejdsproces gennem metoden "AngivEgenskab". Vinduet log, når en anden test kørt vil vise os følgende:

![Logføring clipart på listen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Logføring clipart på listen*

Gøre en test og Kør for at se, hvordan video- og lydstreams er beskåret. Når du bruger mere end én test og Kør med forskellige værdier for justering af punkter, vil du bemærke, de ikke vil være taget i betragtning dog! Årsagen til dette er, at designeren, i modsætning til Azure kørsel, ikke tilsidesætter cliplist xml hver Kør. Det betyder, at kun første gang du har angivet i og ud af punkter, der forårsager xml til at transformere, alle de andre gange, vores vagt-delsætningen (hvis (clipListXML.indexOf ("<trim>") == -1)), kan det blokere arbejdsprocessen fra at føje en anden trim element, når der er allerede en Præsenter.

For at skabe vores arbejdsproces god ide at teste lokalt, tilføje vi bedste house bevare kode, inspicerer Hvis er allerede blevet et trim element. Hvis det er tilfældet, kan vi fjerne den, før du fortsætter ved at ændre XML-filen med de nye værdier. I stedet for at bruge almindelig strengbehandlinger, er det sandsynligvis mere sikkert at gøre dette via reelle XML-objektmodellen parsing af.

Før vi kan tilføje denne kode, selvom vi skal først tilføje et antal Importér erklæringer i starten af vores script:
    
    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;

Efter det, kan vi tilføje den nødvendige markedet kode:

    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already: 
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes: 
    elementsToDelete.each{ 
        e -> e.getParentNode().removeChild(e);
    }; 
    node.log("deleted any existing trim nodes");
    
    //serialize the modified clip list xml dom into a string: 
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result); 
    clipListXML = result.getWriter().toString();
    
Denne kode går lige over det sted, hvor vi tilføje de trim elementer i cliplist XML-filen.

På dette tidspunkt kan vi Kør og rediger vores arbejdsproces som nogenlunde samme måde som vi vil under har du de ændringer, der er anvendt nogensinde før tid.    

###<a id="frame_based_trim_clippingenabled_prop"></a>Tilføje en ClippingEnabled nemmere egenskab

Som ikke ønsker du måske altid justering af for at opstå, Lad os færdiggøre fra vores arbejdsprocessen ved at tilføje et praktisk boolesk flag, der angiver eller ej vi vil aktivere Justering af / skærmklip.

På samme måde som før, du publicerer en ny egenskab i roden af vores kaldet "ClippingEnabled" arbejdsproces af typen "boolesk".

![Publiceret en egenskab for aktivering af skærmklip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publiceret en egenskab for aktivering af skærmklip*

Med den under enkel vagt-delsætning, kan vi kontrollere Hvis justering af er påkrævet og beslutter, at hvis vores clip liste som sådan skal ændres eller ej.

    //check if clipping is required: 
    def clippingrequired = node.getProperty("../ClippingEnabled"); 
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML); 
        node.log("no clipping required"); 
        return; 
    }


###<a id="code"></a>Fuldført kode

    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;
    
    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping: 
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);
    
    //for local testing: delete any pre-existing trim elements 
    //from the clip list xml by parsing the xml into a DOM:
    
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder(); 
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath(); 
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }
    
    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e -> 
        e.getParentNode().removeChild(e); 
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return; 
    }

    //add trim elements to cliplist xml 
    if ( clipListXML.indexOf("<trim>") == -1 ) 
    {
        //trim video 
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode + 
            " </outPoint>\n </trim> \n"); 
        //trim audio 
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" + 
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML ); 
        node.setProperty("../clipListXml",clipListXML); 
    }


##<a name="also-see"></a>Se også 

[Introduktion til Premium-kodning i Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Sådan bruger du Premium kodning i Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Kodning On Demand-indhold med tjenesten Azure Media](media-services-encode-asset.md#media_encoder_premium_workflow)

[Media Encoder Premium arbejdsproces formater og Codecs](media-services-premium-workflow-encoder-formats.md)

[Arbejdsproces eksempelfiler](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Værktøjet til Azure Media Services Explorer](http://aka.ms/amse)

##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
