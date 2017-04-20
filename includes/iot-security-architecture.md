# <a name="internet-of-things-security-architecture"></a>Sikkerhedsarkitekturen i Internet af ting

Når du designer et system, er det vigtigt at forstå de potentielle trusler til det pågældende system og tilføje passende forsvar i overensstemmelse hermed, som systemet er designet og skabt. Det er især vigtigt at designe produktet fra starten med fokus på sikkerhed, fordi forståelse, hvor en hacker kan muligvis svække et system gør det lettere at sikre, at relevante afhjælpninger er på plads fra starten. 

## <a name="security-starts-with-a-threat-model"></a>Sikkerhed begynder med en trussel-model
 
Microsoft har længe brugt trusselsmodeller for sine produkter og har foretaget virksomhedens trussel modeling proces, der er offentligt tilgængelige. Virksomhed-oplevelse demonstrerer, at der ved modellering er uventede fordele ud over den umiddelbare forståelse af, hvad trusler er den mest om. For eksempel oprettes også en Boulevard en åben diskussion med andre uden for udviklingsgruppen, som kan føre til nye ideer og forbedringer i produktet.
  
Formålet med trussel modellering er at forstå, hvordan en hacker kan muligvis svække et system, og derefter sørge for passende afhjælpninger er på plads. Trussel modellering styrker design team for at overveje afhjælpninger, som systemet er udviklet i stedet for efter et system er installeret. Denne oplysning er kritisk vigtigt, fordi omprogrammerbar sikkerhed forsvar til et utal af enheder i feltet er umuligt, fejl og efterlader kunder i fare.

Mange udviklingsteams gør et fremragende job hentning af de funktionelle krav til systemet, kundernes fordel. Identificere ikke-oplagt måde, at en person kan misbruge systemet er dog mere udfordrende. Trussel modellering hjælper udviklingsteams, der forstår, hvad en hacker kan gøre og hvorfor. Trussel modellering er en struktureret proces, der opretter en diskussion om sikkerheden designbeslutninger i systemet, samt ændringer i designet, der foretages undervejs, sikkerhed, virkning. Mens en trussel-model er ganske enkelt et dokument, repræsenterer denne dokumentation også en ideel måde at sikre kontinuiteten i viden, tilbageholdelse af lektioner lært og hjælp ny team hurtigt ombord. Endelig er et resultat af trussel modellering til at overveje andre aspekter af sikkerhed, såsom hvilke sikkerhed forpligtelser, du vil anvende til dine kunder. Disse forpligtelser i forbindelse med modellering trussel vil informere og drive test af din løsning på internettet af ting (IoT).
 

### <a name="when-to-threat-model"></a>Hvornår skal trussel model

[Trussel modeling](http://www.microsoft.com/security/sdl/adopt/threatmodeling.aspx) tilbyder den største værdi, hvis det er integreret i designfasen. Når du designer, har du den største fleksibilitet til at foretage ændringer til at eliminere trusler. At eliminere risici ved design er det ønskede resultat. Det er meget nemmere end at tilføje afhjælpninger, teste dem og sikrer, at de stadig aktuelle, og desuden sådanne eliminering er ikke altid muligt. Bliver det sværere at eliminere trusler som et produkt bliver mere moden, og derefter i sidste ende kræver mere arbejde og meget sværere afvejninger end trussel modellering på tidligt i udviklingen.

### <a name="what-to-threat-model"></a>Hvad trussel model

Tråd model løsning som helhed, og også fokusere på følgende områder:

- Funktionerne for sikkerhed og beskyttelse af personlige oplysninger
- De funktioner, hvis fejl er relevante sikkerhed
- De funktioner, der berører en grænse for tillid 

### <a name="who-threat-models"></a>Som trussel modeller

Trussel modellering er en proces, som alle andre.  Det er en god idé at behandle modeldokument trussel som enhver anden komponent i løsningen, og valider den. Mange udviklingsteams gør et fremragende job hentning af de funktionelle krav til systemet, kundernes fordel. Identificere ikke-oplagt måde, at en person kan misbruge systemet er dog mere udfordrende. Trussel modellering hjælper udviklingsteams, der forstår, hvad en hacker kan gøre og hvorfor.

### <a name="how-to-threat-model"></a>Sådan trussel model

Truslen modeling processen består af fire trin; trinene er:

- Model programmet
- Optælle trusler
- Afhjælpning af trusler
- Valider afhjælpninger

#### <a name="the-process-steps"></a>Processen

Tre tommelfingerregler at huske på, når du bygger en trussel-model:

1. Oprette et diagram af referencearkitektur. 
2. Start bredden først. Få et overblik og forstå systemet som helhed, før dykning dyb.  Dette sikrer, at du gå i dybden på de rette steder.
3. Drive processen, ikke kan drive du processen. Hvis du finder et problem i fasen modellering og vil undersøge det, kan du gå til det!  Ikke mener, at du skal følge disse trin slavishly.  

#### <a name="threats"></a>Trusler

De fire kerneelementer i en trussel model er:

- Processer (web services, Win32-tjenester * nix daemons osv. Bemærk, at visse komplekse objekter (f.eks. feltet gateways og sensorer) kan repræsenteres som en proces, når en teknisk Dyk ned i disse områder ikke er muligt.
- Dataene gemmes (overalt data gemmes som en konfigurationsfil eller database)
- Dataflow (hvor flyttes data mellem andre elementer i programmet)
- Eksterne enheder (noget, der interagerer med systemet, men kan ikke styres af programmet, eksempler omfatter brugere og satellit feeds)

Alle elementer i diagram over arkitektur, der er omfattet af forskellige trusler; Vi bruger STRIDE huskeværdien. Læs [Trussel Modeling igen, trinvis](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) at vide mere om STRIDE elementerne.

Forskellige elementer i programmet diagrammet er underlagt visse STRIDE trusler:

- Processer, der er omfattet af STRIDE
- Dataflow, der er omfattet af TID
- Datalagre er underlagt TID, og nogle gange R, hvis datalagrene logfiler.
- Eksterne enheder, der er omfattet af SRD

## <a name="security-in-iot"></a>Sikkerhed i IoT

Tilsluttede enheder med særlige formål har et betydeligt antal mulige interaktion arealer og interaktion mønstre, der skal anses for at udgøre en ramme til sikring af digital adgang til disse enheder. Udtrykket "digital adgang" bruges her til at skelne fra alle handlinger, der udføres via direct-enhed interaktion hvor adgang sikkerhed leveres gennem fysisk adgangskontrol. For eksempel at sætte enheden i et lokale med en lås på døren. Mens fysisk adgang ikke kan nægtes, ved hjælp af software og hardware, kan der træffes foranstaltninger for at forhindre fysisk adgang fra førende system interferens. 

Når vi udforsker interaktion mønstrene, ser vi nærmere på "enhedskontrol" og "device data" med det samme niveau af opmærksomhed. "Enhedskontrol" kan klassificeres som alle oplysninger, der leveres til en enhed af enhver part med formålet at ændre eller har indflydelse på funktionsmåden mod dens tilstand eller tilstanden for sit miljø. "Enhedsdata" kan klassificeres som alle oplysninger, der udsender en enhed til en anden part om dens tilstand og dets miljø observerede tilstand.
   
For at optimere Sikkerhed bedste fremgangsmåder, anbefales det, at en typisk IoT arkitektur opdeles i flere komponenter, der er zoner som en del af truslen modeling opgave. Disse zoner er beskrevet indgående i hele dette afsnit og indeholder:

-   Enhed,
-   Feltet Gateway
-   Skyen gateways, og
-   Tjenester.

Zoner, der er bredt ud til at inddele en løsning hver zone har ofte sine egne krav til data og godkendelse og autorisation. Zoner kan også bruges til isolering af skader og begrænse virkningerne af lave tillid zoner på højere tillid zoner.

Hver zone er adskilt af en tillid grænse, der er angivet som stiplet rød linje i diagrammet nedenfor. Det repræsenterer en overgang af dataene/oplysningerne fra én kilde til en anden. Oplysninger kan være underlagt Spoofing, Tampering, afvisning, afsløring af oplysninger, Denial of Service og udvidelse af rettigheder (STRIDE) under denne overgang.

![IoT sikkerhedszoner](media/iot-security-architecture/iot-security-architecture-fig1.png) 

De komponenter, der er afbilledet i hver ramme underkastes også STRIDE, så en fuld 360 trussel modeling visning af løsningen. I nedenstående afsnit uddybes de enkelte komponenter og specifikke sikkerhedsproblemer og løsninger, der skal lægges på plads.

De afsnit, der følger efter diskuterer standardkomponenter, der normalt findes i disse zoner.

### <a name="the-device-zone"></a>Zonen enhed

Enhed-miljø er den umiddelbare fysiske plads omkring enheden, hvor fysisk adgang og/eller "lokalt netværk" peer-to-peer-digital adgang til enheden er umuligt. Et "lokalt netværk" antages for at være et netværk, der er adskilt og isoleret fra – men potentielt områdelinks til – det offentlige Internet, og omfatter alle kortrækkende trådløs radioteknologi, der tillader peer-to-peer-kommunikation for enheder. Det gør *ikke* omfatte alle netværk virtualiseringsteknologi, oprette en illusion af et lokalt netværk og også omfatter ikke offentlige operatør netværk, der kræver to enhederne kan kommunikere på tværs af offentlige netværk plads, hvis de var til at angive en relation til peer-to-peer-kommunikation.

### <a name="the-field-gateway-zone"></a>Feltet Gateway Zone

Feltet gateway er en enhed/apparat eller nogle all-round server computersoftware, der fungerer som kommunikation enabler og eventuelt som en enhed kontrolsystem og enheden databehandling hub. Feltet gateway zone omfatter selve gatewayen felt og alle enheder, der er knyttet til den. Som navnet antyder, felt gateways fungere uden for dedikerede databehandlingsanlæg, er normalt placering, der er bundet, er potentielt underlagt fysiske indtrængen og har begrænsede operationelle redundans. Alle for at sige, at en felt-gateway er ofte en ting en touch og sabotage samtidig at vide, hvad dens funktion er. 

En felt-gateway er forskellig fra en ren trafik router, den har haft en aktiv rolle i adgangskontrol og informationsstrømme, hvilket betyder, at det er et program rettet enhed og netværksforbindelsen eller terminal session. En NAT-enhed eller en firewall, derimod ikke opfylder betingelserne som felt gateways, da de ikke er eksplicitte forbindelse eller session terminaler, men snarere en rute (eller blok) forbindelser eller foretaget gennem disse sessioner. Gatewayen felt har to forskellige arealer. En flader de enheder, der er knyttet til det og repræsenterer inden for zonen, og den anden vender alle eksterne parter og er kanten af zonen.   

### <a name="the-cloud-gateway-zone"></a>Sky gateway zone

Sky gateway er et system, der gør det muligt for eksterne kommunikation fra og til enheder eller felt gateways fra flere forskellige steder på tværs af offentlige netværk plads, normalt mod en skybaseret kontrol og data analysesystem, en sammenslutning af sådanne systemer. I nogle tilfælde kan kan en sky gateway umiddelbart lette adgangen til særlige formål enheder fra terminaler som tabletter eller telefoner. I den kontekst, der er beskrevet her, er "sky" beregnet til at henvise til en dedikeret edb-system, der ikke er bundet til det samme sted som den tilsluttede enheder eller felt gateways. Også i en sky-Zone, operationelle foranstaltninger forhindre målrettet fysisk adgang og er ikke nødvendigvis udsat for en "offentlig sky"-infrastruktur.  

En sky-gateway kan potentielt være knyttet til et netværk virtualisering overlejring til at isolere sky-gateway og alle dens tilsluttede enheder eller felt gateways fra andre netværkstrafik. Gatewayen sky sig selv er hverken en kontrolordning til enheden eller en behandling eller oplag for enhedsdata. disse faciliteter grænseflade med sky gateway. Sky gateway zone omfatter sky gatewayen selve sammen med alle felt gateways og enheder, der direkte eller indirekte er knyttet til den. Kanten af zonen er en særskilt overfladeareal, hvor alle eksterne parter kan kommunikere gennem.

### <a name="the-services-zone"></a>Zonen services

"service" er defineret for denne sammenhæng som en softwarekomponent eller et modul, der har en grænseflade med enheder gennem et felt- eller cloud gateway til dataindsamling og -analyse samt til kommando og kontrol.  Tjenester er mæglere. De handler under deres identitet mod gateways og andre delsystemer, lagre og analysere data, selvstændigt udstede kommandoer til enheder baseret på data indsigt eller tidsplaner og vise oplysninger og styre funktioner til autoriserede slutbrugere.

### <a name="information-devices-vs-special-purpose-devices"></a>Oplysninger-enheder vs. enheder med særlige formål

Pc'er, telefoner og tablets er primært interaktive oplysninger enheder. Telefoner og tablets optimeret udtrykkeligt omkring optimering af batteriets levetid. De helst deaktivere delvist ved ikke straks interaktion med en person, eller når der ikke udbyder tjenester såsom afspilning af musik eller lede deres ejer til en bestemt placering. Fra et perspektiv af systemer fungerer disse oplysninger teknologi enheder hovedsagelig som proxyservere mod personer. De er "personer aktuatorer" forslag til handlinger og "personer sensorer" indsamle input. 

Specielt enheder er fra simple Temperatursensorer til komplekse factory produktionslinjer med tusindvis af komponenter i dem, forskellige. Disse enheder er meget mere begrænset i formål og selv om de giver nogle brugergrænseflade, de er primært beregnet til samspil med eller integreres i anlægsaktiver i den fysiske verden. De måle og rapportere miljømæssige forhold, slå ventiler, styre servos, sound alarmer, skifte lys og udføre mange andre opgaver. De hjælper til at udføre arbejde som en enhed med oplysninger, der er enten for Generelt, for dyrt, for stor eller for skør. Det konkrete formål bestemmer straks deres tekniske udformning som samt det disponible monetære budget for deres produktion og levetid drift. Kombinationen af disse to vigtige faktorer begrænser de tilgængelige operationelle energi budget, fysiske flademål og dermed tilgængelig lagerplads, compute og sikkerhedsfunktioner.  

Hvis noget "går galt" med automatiseret eller remote kontrollerbare enheder, for eksempel defekter fysiske defekter eller logik willful uautoriseret adgang og manipulation. Produktion-partier kan blive ødelagt, bygninger kan looted eller brændt, og personer kan være tilskadekomne eller endda terning. Dette er naturligvis en helt anden klasse for skade end en person maxing ud af et stjålne kreditkort grænse. Sikkerhedslinje for enheder, der gør ting, flytte og for sensordata, sidste ende resulterer i kommandoer, der er få ting, du vil flytte, skal være højere end i e-handel eller bankscenarium. 


### <a name="device-control-and-device-data-interactions"></a>Enhedskontrol og enheden data interaktioner

Tilsluttede enheder med særlige formål har et betydeligt antal mulige interaktion arealer og interaktion mønstre, der skal anses for at udgøre en ramme til sikring af digital adgang til disse enheder. Udtrykket "digital adgang" bruges her til at skelne fra alle handlinger, der udføres via direct-enhed interaktion hvor adgang sikkerhed leveres gennem fysisk adgangskontrol. For eksempel at sætte enheden i et lokale med en lås på døren. Mens fysisk adgang ikke kan nægtes, ved hjælp af software og hardware, kan der træffes foranstaltninger for at forhindre fysisk adgang fra førende system interferens. 
 
Når vi udforsker interaktion mønstrene, ser vi nærmere på "enhedskontrol" og "device data" med det samme niveau af opmærksomhed under trussel modellering. "Enhedskontrol" kan klassificeres som alle oplysninger, der leveres til en enhed af enhver part med formålet at ændre eller har indflydelse på funktionsmåden mod dens tilstand eller tilstanden for sit miljø. "Enhedsdata" kan klassificeres som alle oplysninger, der udsender en enhed til en anden part om dens tilstand og dets miljø observerede tilstand. 

## <a name="threat-modeling-the-azure-iot-reference-architecture"></a>Trussel modeling Azure IoT reference-arkitektur

Microsoft bruger de rammer, der er beskrevet ovenfor, til at trussel modellering for Azure IoT. I afsnittet nedenfor bruge vi derfor det konkrete eksempel på Azure IoT referencearkitektur til at demonstrere, hvordan du synes om modellering for IoT trussel og at imødegå de risici, der er identificeret. I dette tilfælde har vi identificeret fire hovedområder i fokus:

-   Enheder og datakilder,
-   Transport af data,
-   Enheden og behandling af hændelse, og
-   Præsentation

![Organisationsmodel til Azure IoT trussel](media/iot-security-architecture/iot-security-architecture-fig2.png) 

Nedenstående diagram indeholder en forenklet visning af Microsofts IoT arkitektur med et dataflowdiagram model, der bruges af værktøjet Microsoft Threat modellering:

![Trussel organisationsmodel til Azure IoT med MS trussel Modeling værktøj](media/iot-security-architecture/iot-security-architecture-fig3.png)

Det er vigtigt at bemærke, at arkitekturen adskiller funktionerne til enheden og gateway. Dette gør det muligt at udnytte gatewayenheder, der er mere sikker: de er i stand til at kommunikere med skyen gateway ved hjælp af sikre protokoller, som typisk kræver større behandling af spild, at en oprindelig enhed - f.eks termostat - kan give af sig selv. I zonen Azure tjenester antager vi, at sky-Gateway er repræsenteret af tjenesten Azure IoT Hub.

### <a name="device-and-data-sourcesdata-transport"></a>Transport af enheden og data kilder/data

Dette afsnit udforsker den arkitektur, der er beskrevet ovenfor gennem linsen af trussel modellering og giver et overblik over hvordan vi diskuterer nogle af de iboende bekymringer. Vi fokuserer på kerneelementer i en trussel-model:

- Processer (dem under vores kontrol og eksterne elementer)
- Kommunikation (også kaldet dataflow)
- Storage (også kaldet datalagre)

#### <a name="processes"></a>Processer

I hver af de kategorier, der er skitseret i Azure IoT-arkitekturen, vi forsøger at afhjælpe en række forskellige trusler på tværs af de forskellige faser, som dataene/oplysningerne findes i: proces, kommunikation og opbevaring. Nedenfor giver vi en oversigt over de mest almindelige er for kategorien "proces", efterfulgt af en oversigt over, hvordan disse kunne være bedst afhjulpet: 

**Spoofing (forfalskning) (S)**: en hacker kan udtrække kryptografiske nøglemateriale fra en enhed, enten på software eller hardware, og efterfølgende adgang til systemet med en anden fysisk eller virtuel enhed under identiteten af enheden nøglematerialet er taget fra. En god illustration er fjernbetjeninger, der kan slå et TV, og som er populære prankster funktioner.

**Nægtelse af Service (D)**: en enhed kan gengives af funktion eller kommunikation via forstyrrer radiofrekvenser eller opskæring ledninger. Eksempelvis rapporterer et kamera til overvågning, der havde bevidst udskåret strøm eller netværk forbindelsen ikke data overhovedet.

**Indgreb (T)**: en hacker kan helt eller delvis erstatte softwaren kører på enheden, så potentielt erstattede softwaren at udnytte identiteten for enheden, hvis nøglematerialet eller kryptografiske faciliteter holde vigtigste materialer er tilgængelige for programmet ulovlig. For eksempel kan en hacker udnytte udpakkede nøglematerialet for at opfange og undertrykke data fra enheden på kommunikationsstien og erstatte det med falske data, der er godkendt med stjålne nøglematerialet.

**Offentliggørelse af oplysninger (I)**: Hvis enheden kører software, der er manipuleret, manipulated softwaren blev lækker data for uautoriserede personer. For eksempel kan en hacker udnytte udpakkede nøglematerialet for at indsætte sig selv i kommunikationsstien mellem enheden og en controller eller felt gateway eller sky gateway til siphon fra oplysninger.

**Udvidelse af rettigheder (E)**: en enhed, der understøtter en bestemt funktion, kan blive tvunget til at gøre noget andet. For eksempel kan en ventil, der er programmeret til at åbne halvvejs blive narret for at åbne hele vejen.

| **Komponent** | **Trussel** | **Afhjælpning**                                                                                                                                                | **Risiko**                                                                                                                                                                                                    | **Gennemførelse**                                                                                                                                                                                                                                                                                                                                     |
|---------------|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Enhed        | S          | Tildeler enheden identitet og godkende enheden                                                                                                | Udskiftning af enheden eller en del af enheden med en anden enhed. Hvordan kan vi vide, vi taler med den rigtige enhed?                                                                                           | Godkender enheden, ved hjælp af Transport Layer Security (TLS) eller IPSec. Infrastrukturen skal understøtte ved hjælp af forhåndsdelt nøgle (PSK) på enheder, som ikke kan håndtere fuld asymmetrisk kryptografi. Udnytte Azure AD [OAuth](http://www.rfc-editor.org/in-notes/internet-drafts/draft-ietf-ace-oauth-authz-01.txt)                             |
|               | TRID       | Vedrøre tamperproof mekanismer enheden for eksempel ved at gøre det meget svært at umuligt at få nøgler og andre kryptografisk materiale fra enheden. | Risikoen er, hvis en person manipulation enhed (fysisk interferens). Hvordan er vi sikker på, at enheden ikke ændret.                                                                                 | Den mest effektive forebyggelse er en pålidelig platform module (TPM) funktion, der giver mulighed for opbevaring af nøgler i særlige på chip-kredsløb, hvor tasterne kan ikke læses, men kan kun bruges til kryptografiske operationer, der bruger nøglen, men aldrig videregive nøglen. Kryptering af hukommelse på enheden. Nøgleadministration til enheden. Signering af koden. |
|               | E          | Med adgangskontrol på enheden. Godkendelsesskema.                                                                                                    | Hvis enheden giver mulighed for individuelle handlinger der skal udføres på grundlag af kommandoer fra en ekstern kilde, eller endda skadet sensorer, tillader det angreb til at udføre handlinger ikke er tilgængelig. | Har godkendelsesskema for enheden                                                                                                                                                                                                                                                                                                             |
| Feltet Gateway | S          | Godkendelse af felt-gateway til Gateway sky (cert baseret, PSK, krav, der er baseret,..)                                                                           | Hvis en person kan efterligne felt Gateway, kan det præsentere sig selv som en enhed.                                                                                                                               | TLS RSA/PSK, IPSe, [RFC 4279](https://tools.ietf.org/html/rfc4279). Alle de samme nøgle opbevaring og attest vedrører enheder generelt – bedste-fald er bruge TPM. Udvidelse af 6LowPAN for IPSec understøtter trådløs Sensor netværk (WSN).                                                                                                              |
|               | TRID       | Beskytte gatewayen felt mod forfalskning (TPM)?                                                                                                            | Spoofingangreb, narre sky gateway tænker taler til feltet gateway kan medføre afsløring af oplysninger og data manipulation                                                             | Hukommelse kryptering, TPM'S, godkendelse.                                                                                                                                                                                                                                                                                                              |
|               | E          | Access control mekanisme til feltet Gateway                                                                                                                    |                                                                                                                                                                                                             |                                                                                                                                                                                                                                                                                                                                                        |

Her er nogle eksempler på trusler i denne kategori:

Spoofing: Kan en hacker pakke kryptografiske nøglemateriale fra en enhed, enten software eller hardware, og efterfølgende adgang til systemet med en anden fysisk eller virtuel enhed under identiteten af enheden nøglematerialet er taget fra.

**Denial of Service**: en enhed kan gengives af funktion eller kommunikation via forstyrrer radiofrekvenser eller opskæring ledninger. Eksempelvis rapporterer et kamera til overvågning, der havde bevidst udskåret strøm eller netværk forbindelsen ikke data overhovedet.

**Tampering**: en hacker kan helt eller delvis erstatte softwaren kører på enheden, så potentielt erstattede softwaren at udnytte identiteten for enheden, hvis nøglematerialet eller kryptografiske faciliteter holde vigtigste materialer er tilgængelige for programmet ulovlig.

**Tampering**: et kamera til overvågning, der er vist et synlige spektrum billede af en tom gangene kunne tage sigte på et fotografi af sådanne gangene. Kunne rapportering røg eller brand sensor nogen holder en lysere nedenunder. I begge tilfælde skal enheden kan være teknisk fuldt pålidelig mod systemet, men rapporterer det manipulerede oplysninger.

**Tampering**: en hacker kan udnytte udpakkede nøglematerialet for at opfange og undertrykke data fra enheden på kommunikationsstien og erstatte det med falske data, der er godkendt med stjålne nøglematerialet.

**Tampering**: en hacker kan helt eller delvist erstatte softwaren kører på enheden, så potentielt erstattede softwaren at udnytte identiteten for enheden, hvis nøglematerialet eller kryptografiske faciliteter holde vigtigste materialer er tilgængelige for programmet ulovlig.
   
**Offentliggørelse af oplysninger**: Hvis enheden kører software, der er manipuleret, manipulated softwaren blev lækker data for uautoriserede personer.

**Offentliggørelse af oplysninger**: en hacker kan udnytte udpakkede nøglematerialet for at indsætte sig selv i kommunikationsstien mellem enheden og en controller eller et felt gateway eller sky gateway til siphon fra oplysninger.

**Denial of Service**: enheden kan deaktiveret eller aktiveret i en tilstand, hvor kommunikation er ikke mulig (som er tilsigtet i mange industrielle maskiner).

**Tampering**: enheden kan konfigureres for at køre i tilstanden ukendt til control system (uden for kendte kalibreringsparametre) og dermed levere data, der kan mistolkes

**Udvidelse af rettigheder**: en enhed, der understøtter en bestemt funktion, kan blive tvunget til at gøre noget andet. For eksempel kan en ventil, der er programmeret til at åbne halvvejs blive narret for at åbne hele vejen.

**Denial of Service**: enheden kan omdannes til en tilstand, hvor kommunikation ikke er mulig.

**Tampering**: enheden kan konfigureres for at køre i tilstanden ukendt til control system (uden for kendte kalibreringsparametre) og dermed levere data, der kan mistolkes.
 
**Tampering-spoofing/afvisning**: Hvis ikke sikret (hvilket er sjældent tilfældet med forbrugeren fjernbetjeninger) en hacker kan ændre status for en enhed anonymt. En god illustration er fjernbetjeninger, der kan slå et TV, og som er populære prankster funktioner.

#### <a name="communication"></a>Kommunikation

Trusler omkring kommunikationsstien mellem enheder, enheder og felt gateways og gateway for enheden og sky. Tabellen nedenfor indeholder nogle retningslinjer omkring åbne stik på enhed/VPN:

| **Komponent**               | **Trussel** | **Afhjælpning**                                      | **Risiko**                                                                                                      | **Gennemførelse**                                                                                                                                                                                                                                                                                                                                                               |
|-----------------------------|------------|-----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Enheden IoT Hub              | TID        | (D) TLS (PSK/RSA) til at kryptere trafik             | Aflytning eller forstyrre kommunikationen mellem enheden og den lokale gateway                             | Sikkerhed på protokolniveau. Vi skal finde ud af, hvordan du beskytter dem med brugerdefinerede protokoller. I de fleste tilfælde foregår kommunikationen fra enheden til hubben IoT (enheden opretter forbindelsen).                                                                                                                                                                 |
| Enhed til enhed               | TID        | (D) TLS (PSK/RSA) til at kryptere trafik.            | Læsning af data i transit mellem enheder. Manipulation af data. Overbelastning af enheden med nye forbindelser | Sikkerhed på protokolniveau (MQTT/AMQP/HTTP/CoAP. Vi skal finde ud af, hvordan du beskytter dem med brugerdefinerede protokoller. Afhjælpningen af DoS-trussel er at peer-enheder via en sky eller felt gateway og har kun act som klienter over netværket. Den peering kan resultere i en direkte forbindelse mellem to peers, efter at have været formidlet af gatewayen |
| Ekstern enhed enhed      | TID        | Stærk samparring af ekstern enhed til enheden | Afluring forbindelsen til enheden. Forstyrre kommunikationen med enheden                     | Sikker parring den eksterne enhed til LE NFC/Bluetooth-enheden. Styring af panelet drift af enheden (fysisk)                                                                                                                                                                                                                                                  |
| Feltet Gateway sky Gateway | TID        | TLS (PSK/RSA) til at kryptere trafik.               | Aflytning eller forstyrre kommunikationen mellem enheden og den lokale gateway                             | Sikkerhed på protokolniveau (MQTT/AMQP/HTTP/CoAP). Vi skal finde ud af, hvordan du beskytter dem med brugerdefinerede protokoller.                                                                                                                                                                                                                                                       |
| Enheden sky Gateway        | TID        | TLS (PSK/RSA) til at kryptere trafik.               | Aflytning eller forstyrre kommunikationen mellem enheden og den lokale gateway                             | Sikkerhed på protokolniveau (MQTT/AMQP/HTTP/CoAP). Vi skal finde ud af, hvordan du beskytter dem med brugerdefinerede protokoller.                                                                                                                                                                                                                                                       |

Her er nogle eksempler på trusler i denne kategori:

**Denial of Service**: begrænsede enheder er generelt DoS truet når de aktivt lytter efter indgående forbindelser eller uopfordrede datagrammer på et netværk, da en hacker kan mange forbindelser parallelt og ikke behandle dem eller behandle dem meget langsomt, eller enheden kan være overfyldt med uønsket trafik. I begge tilfælde skal kan enheden effektivt gengives ikke fungerer på netværket.

**Spoofing, afsløring af oplysninger**: begrænset enheder og enheder til særlige formål har ofte sikkerhed for-alle faciliteter som adgangskode eller PIN-beskyttelse, eller de er helt afhængige af tillid til netværket, hvilket betyder, at de vil give adgang til oplysninger, når en enhed er på samme netværk, og netværket er ofte kun beskyttet af en delt nøgle. Det betyder, at når den delte hemmelighed til enheden eller netværksplaceringen er videregives, det er muligt at styre enheden, eller overhold data, der udledes fra enheden.  

**Spoofing**: en hacker kan opfange eller delvis tilsidesætte udsendelsen og efterligner igangsætter (man i midten)

**Tampering**: en hacker kan opfange eller delvis tilsidesætte udsendelsen og sende falske oplysninger 

**Offentliggørelse af oplysninger:** en hacker kan mobiltelefonkommandoer på en udsendelse og indhente oplysninger uden tilladelse **Denial of Service:** en hacker kan fast broadcast signalet og afvise distribution af oplysninger

#### <a name="storage"></a>Storage

Alle enheds- og gateway er en form for oplagring (midlertidig for MSMQ-data, os billedlagring).

| **Komponent**                            | **Trussel** | **Afhjælpning**                       | **Risiko**                                                                                                                                                                                                                                                                                                                | **Gennemførelse**                                                                                                                                                     |
|------------------------------------------|------------|--------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Storage Device                           | TRID       | Opbevaring kryptering, signering logfilerne | Læser data fra opbevaring (PII data), manipulation af fjernmåling data. Manipulation af i kø eller cachelagrede kommando kontroldata. Manipulation af konfiguration eller firmware opdateringspakker kan mens cachelagret eller i kø lokalt føre til OS og/eller komponenter, der bliver skadet                                         | Kryptering, meddelelsesgodkendelseskoden (MAC) eller digital signatur. Hvor muligt, effektiv adgangskontrol gennem ressourceadgang styre lister (ACL'er) eller tilladelser. |
| Enhedens Operativsystem billede                          | TRID       |                                      | Manipulation af OS / udskiftning af OS-komponenter                                                                                                                                                                                                                                                                         | Skrivebeskyttet OS-partitionen, signeret afbildning af Operativsystemet og kryptering                                                                                                                    |
| Feltet Gateway-storage (MSMQ-data) | TRID       | Opbevaring kryptering, signering logfilerne | Læser data fra lageret (PII data), manipulation af data for fjernmåling, manipulation med i kø eller cachelagrede kommando kontroldata. Manipulation af konfiguration eller firmware update-pakker (bestemt til enheder eller felt gateway) kan mens cachelagret eller i kø lokalt føre til OS og/eller komponenter, der bliver skadet | BitLocker                                                                                                                                                              |
| Feltet Gateway OS billede                   | TRID       |                                      | Manipulation af OS / udskiftning af OS-komponenter                                                                                                                                                                                                                                                                          | Skrivebeskyttet OS-partitionen, signeret afbildning af Operativsystemet og kryptering                                                                                                                    |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Enheden og hændelse behandling/sky gateway zone

En sky-gateway er system, der gør det muligt for eksterne kommunikation fra og til enheder eller felt gateways fra flere forskellige steder på tværs af offentlige netværk plads, normalt mod en skybaseret kontrol og data analysesystem, en sammenslutning af sådanne systemer. I nogle tilfælde kan kan en sky gateway umiddelbart lette adgangen til særlige formål enheder fra terminaler som tabletter eller telefoner. I den kontekst, der er beskrevet her, "sky" er beregnet til at henvise til et dedikeret edb-system, der ikke er bundet til det samme sted som den tilsluttede enheder eller felt-gateways, og hvor operationelle foranstaltninger forhindre målrettet fysisk adgang, men er ikke nødvendigvis til en "offentlig sky"-infrastruktur.  En sky-gateway kan potentielt være knyttet til et netværk virtualisering overlejring til at isolere sky-gateway og alle dens tilsluttede enheder eller felt gateways fra andre netværkstrafik. Gatewayen sky sig selv er hverken en kontrolordning til enheden eller en behandling eller oplag for enhedsdata. disse faciliteter grænseflade med sky gateway. Sky gateway zone omfatter sky gatewayen selve sammen med alle felt gateways og enheder, der direkte eller indirekte er knyttet til den.

Sky gateway er overvejende brugerdefineret bygget stykke software, der kører som en tjeneste med udsatte slutpunkter, felt-gateway og enheder opretter forbindelse. Det skal således være designet med fokus på sikkerhed. Følg [SDL](http://www.microsoft.com/sdl) proces for design og udvikling af denne tjeneste. 

#### <a name="services-zone"></a>Services zone

Et kontrolsystem (eller controller) er en softwareløsning, der kommunikerer med en enhed eller et felt-gateway eller sky gateway for kontrollen med en eller flere enheder og/eller til at indsamle og/eller lagre og/eller analysere enhedsdata for præsentationen eller efterfølgende kontrolformål. Kontrolsystemer er de eneste enheder omfattet af denne diskussion, der straks kan lette interaktion med andre. Undtagelsen er mellemliggende fysiske styreflader på enheder, som en parameter, der tillader en person at slukke for enheden eller ændre andre egenskaber, og hvor der er ingen funktionel tilsvarende, der kan få adgang til digitalt. 

Mellemliggende fysiske styreflader er dem, hvor alle slags vedrørende logik begrænser funktion af den fysiske kontrol overflade, så en tilsvarende funktion kan initieres udefra eller input er i konflikt med eksterne input kan undgås – sådanne intermediated styreflader begrebsmæssigt er knyttet til en lokal kontrolsystem, der udnytter den samme underliggende funktionalitet som andre fjernbetjening system, der kan vedhæftes enheden parallelt. Øverste trusler mod den cloud computing kan læses på [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/research/top-threats/) side.


## <a name="additional-resources"></a>Yderligere ressourcer

Se følgende artikler for at få flere oplysninger:

- [SDL trussel modelværktøjet](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
- [Microsoft Azure IoT referencearkitektur](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
 
