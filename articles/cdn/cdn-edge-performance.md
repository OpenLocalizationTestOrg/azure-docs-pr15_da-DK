<properties
    pageTitle="Analysere ydeevne i Azure CDN | Microsoft Azure"
    description="Analysere ydeevne i Microsoft Azure CDN node. Kant ydeevne Analytics indeholder detaljerede oplysninger trafik og båndbredde brugen til CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analysere kant node ydeevne i Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Oversigt
Kant ydeevne analytics indeholder detaljerede oplysninger trafik og båndbredde brugen til CDN. Disse oplysninger kan derefter bruges til at generere cirkulerer statistik, som gør det muligt at få indsigt i, hvordan dine aktiver er ved at blive cachelagret og leveret til dine kunder. Dette giver dig mulighed at danne en strategi om, hvordan du kan optimere leveringen af dit indhold og for at finde ud af, hvad problemer skal behandles til bedre udnyttelse af CDN også. Som et resultat, ikke kun du kan forbedre ydeevnen for levering af data, men du vil også kunne reducere din CDN omkostninger.

> [AZURE.NOTE] Alle rapporter Brug UTC/GMT notation, når du angiver en dato/klokkeslæt.

## <a name="reports-and-log-collection"></a>Rapporter og log af websteder

CDN aktivitetsdata indsamles af modulet kant ydeevne Analytics, før den kan generere rapporter på den. Denne samling proces opstår, når en dag og det dækker den aktivitet, der er foretaget under den forrige dag. Det betyder, at en rapport statistik repræsenterer en stikprøve af dagens statistik på det tidspunkt, det blev behandlet, og Send ikke nødvendigvis indeholde det komplette sæt af data for den aktuelle dag. Den primære funktion i disse rapporter er at vurdere ydeevne. De der ikke bør benyttes til fakturering formål eller nøjagtige numeriske statistik.

> [AZURE.NOTE] Rækkedata hvorfra kant ydeevne analytisk fejlrapporter findes i mindst 90 dage.

## <a name="dashboard"></a>Dashboard

Dashboardet kant ydeevne Analytics registrerer aktuelle og historiske CDN trafik via et diagram og statistik. Brug dette dashboard til at registrere seneste og langsigtede tendenser på ydeevnen for CDN trafik til din konto.

Dette dashboard består af:

* Et interaktivt diagram, der gør det muligt for visualisering af vigtige mål og tendenser.
* En tidslinje, der indeholder en følelse af langtidsaftale mønstre til vigtige mål og tendenser.
* Vigtige mål og statistiske oplysninger om, hvordan forbedrer vores CDN netværk websted trafik målt efter overordnede ydeevne, brugen og effektivitet.

### <a name="accessing-the-edge-performance-dashboard"></a>Få adgang til dashboardet kant ydeevne

1. Klik på knappen **Administrer** fra bladet CDN profil.

    ![Knappen Administrer CDN profil blade](./media/cdn-edge-performance/cdn-manage-btn.png)

    Portalen CDN management åbnes.

2. Peg på fanen **analyser** og derefter holde markøren over den **Kant ydeevne og Analytics** pop op.  Klik på **Dashboard**.

    Dashboardet kant node analytics vises.

### <a name="chart"></a>Diagram

Dashboard indeholder et diagram, der registrerer en metrikværdi over det tidsrum, der er valgt på tidslinjen, der vises direkte derunder.  En tidslinje, grafer op til den sidste to år efter CDN aktivitet vises direkte under diagrammet.

#### <a name="using-the-chart"></a>Brug af diagrammet

* Som standard, der skal afbildes cache effektivitet sats for de seneste 30 dage.
* Dette diagram er dannet ud fra data, der udskrives sætvis dagligt.
* Pege på en dag i kurvediagrammet vil angive en dato og værdien af metrikken på den pågældende dato.
* Klik på Fremhæv weekender for at slå en overlejring af light grå lodrette søjler, der repræsenterer weekender over i diagrammet. Denne type overlejring er nyttig til at identificere mønstre for netværkstrafik over weekender.
* Klik på Vis ét år siden for at slå en overlejring af det forrige år aktivitet over den samme tidsperiode over i diagrammet. Denne type sammenligning giver indblik i langsigtede CDN brugsmønstre. Det øverste højre hjørne af diagrammet indeholder en forklaring, der angiver farvekode for hver linjegraf.

#### <a name="updating-the-chart"></a>Opdatere diagrammet

* Tidsinterval: Udføre én af følgende:
    * Vælg det ønskede område på tidslinjen. Diagrammet opdateres med data, der svarer til den valgte tidsperiode.
    * Dobbeltklik på diagrammet for at få vist alle tilgængelige historiske data op til maksimalt to år.
* Metrisk: Klik på det diagram, der vises ud for den ønskede metrikværdi. Diagrammet og tidslinjen opdateres med data til den tilsvarende metrikværdi.


### <a name="key-metrics-and-statistics"></a>Vigtige mål og statistik

#### <a name="efficiency-metrics"></a>Effektivitet målepunkter

Formålet med disse mål er at se, om cachen effektivitet kan forbedres. De vigtigste fordele udledt fra cachen effektivitet er:

* Reduktion af belastning på origin serveren, der kan medføre, at:
    * Bedre ydeevne i web-serveren.
    * Reduceret funktionsdygtige omkostninger.
* Forbedret data levering hardwareacceleration, da flere anmodninger skal håndteres direkte fra CDN.

Felt | Beskrivelse
------|------------
Cache effektivitet | Angiver procentdelen af data, der overføres, der blev fungeret fra cachen. Denne metriske målinger, når en cachelagret version af det ønskede indhold blev leveres direkte fra CDN (edge-servere) til anmodere (f.eks., webbrowser)
Tryk på rente | Angiver procentdelen af anmodninger, der blev fungeret fra cachen. Denne metriske målinger, når en cachelagret version af det ønskede indhold blev leveres direkte fra CDN (edge-servere) til anmodere (f.eks., webbrowser).
% af Remote byte - Config ingen Cache | Angiver procentdelen af trafik, der blev fungeret fra origin servere til CDN (edge-servere), der ikke cachelagres som et resultat af funktionen Spring cachelagring (HTTP regler Engine).
% af Remote byte - udløbet Cache | Angiver procentdelen af trafik, der blev fungeret fra origin servere til CDN (edge-servere) som et resultat af forældede indhold fornyelse.

#### <a name="usage-metrics"></a>Brugen målepunkter

Formålet med disse mål er at tilvejebringe indsigt i de følgende omkostninger klippes målinger:

* Minimere funktionsdygtige omkostninger gennem CDN.
* Reducere CDN udgifter via cache effektivitet og komprimering.

> [AZURE.NOTE] Trafik lydstyrken tal repræsenterer trafik, der blev brugt i beregninger af forholdstal og procenttal og kan kun viser en del af den samlede trafik for store mængder kunder.

Felt | Beskrivelse
------|------------
Em byte ud | Angiver det gennemsnitlige antal byte, som overføres til hver enkelt anmodning om served fra CDN (edge-servere) til anmoder (f.eks., webbrowser).
Ingen Cache Config Byte rente | Angiver procentdelen af trafik leveres fra CDN (edge-servere) til anmoder (f.eks., webbrowser), der ikke cachelagres på grund af funktionen Spring Cache.
Komprimeret Byte rente | Angiver procentdelen af trafik, der sendes fra CDN (edge-servere) til anmodere (f.eks., webbrowser) i et komprimeret format.
Byte ud | Angiver mængden data i byte, som blev leveret fra CDN (edge-servere) til anmoder (f.eks., webbrowser).  
Byte i | Angiver mængden data i byte sendt fra anmodere (f.eks., webbrowser) til CDN (edge-servere).
Byte Remote | Angiver mængden data i byte, der sendes fra CDN og kunde origin servere til CDN (edge-servere).

#### <a name="performance-metrics"></a>Ydeevnen målepunkter

Formålet med disse mål er at spore overordnede CDN ydeevne til din trafik.

Felt | Beskrivelse
------|------------
Overførselshastighed | Angiver den gennemsnitlige hastighed, hvormed indhold er overført fra CDN til en anmoder.
Varighed | Angiver den gennemsnitlige tid i millisekunder, det tog til at holde et aktiv for en anmoder (f.eks., webbrowser).
Komprimeret anmodning rente | Angiver procentdelen af søgeresultater, som blev leveret fra CDN (edge-servere) til anmoder (f.eks., webbrowser) i et komprimeret format.
4xx fejl rente | Angiver procentdelen af søgeresultater, der genereres en 4xx statuskode.
5XX fejl rente | Angiver procentdelen af søgeresultater, der genereres en 5xx statuskode.
Besøg | Angiver antallet af anmodninger om CDN indhold.

#### <a name="secure-traffic-metrics"></a>Sikker trafik målepunkter

Formålet med disse mål er at registrere CDN ydeevnen til HTTPS-trafikken.

Felt | Beskrivelse
------|------------
Beskyt Cache effektivitet | Angiver procentdelen af data, der overføres til HTTPS-anmodninger, der blev fungeret fra cachen. Denne metriske målinger, når en cachelagret version af det ønskede indhold blev leveres direkte fra CDN (edge-servere) anmodere (f.eks., webbrowser) over HTTPS.
Secure overførselshastighed | Angiver den gennemsnitlige hastighed, hvormed indhold er overført fra CDN (edge-servere) til anmodere (f.eks.,-webserverne) over HTTPS.
Gennemsnitlig varighed sikre | Angiver den gennemsnitlige tid i millisekunder, det tog til at holde et aktiv for en anmoder (f.eks., webbrowser) over HTTPS.
Secure søgeresultater | Angiver antallet af HTTPS anmodninger om CDN indhold.
Secure byte ud | Angiver mængden HTTPS-trafikken i byte, som blev leveret fra CDN (edge-servere) til anmoder (f.eks., webbrowser).

## <a name="reports"></a>Rapporter

Hver rapport i dette modul indeholder et diagram og statistik for brug af båndbredde og trafik for forskellige typer målepunkter (f.eks., HTTP status-koder, cachen status-koder, anmode om URL-adresse osv.). Disse oplysninger kan bruges til at bevæger sig længere ind hvordan indhold der vises til dine kunder og til at finjustere CDN funktionsmåde for at forbedre ydeevnen for levering af data.

### <a name="accessing-the-edge-performance-reports"></a>Få adgang til rapporterne kant ydeevne

1. Klik på knappen **Administrer** fra bladet CDN profil.

    ![Knappen Administrer CDN profil blade](./media/cdn-edge-performance/cdn-manage-btn.png)

    Portalen CDN management åbnes.

2. Peg på fanen **analyser** og derefter holde markøren over den **Kant ydeevne og Analytics** pop op.  Klik på **http-stort objekt**.

    Skærmbilledet kant node analytics-rapporter vises.

Rapport | Beskrivelse
-------|------------
Daglig oversigt | Kan du få vist daglige trafik tendenser over en bestemt tidsperiode. Hver søjle på denne kurve repræsenterer en bestemt dato. Størrelsen på linjen angiver det samlede antal søgeresultater, der er indtruffet på den pågældende dato.
Hver time oversigt | Kan du få vist hver time trafik tendenser over en bestemt tidsperiode. Hver søjle på denne kurve repræsenterer en enkelt time på en bestemt dato. Størrelsen på linjen angiver det samlede antal søgeresultater, der er indtruffet i denne time.
Protokoller | Viser fordelingen af trafik mellem HTTP og HTTPS protokollerne. Et krans diagram angiver procentdelen af søgeresultater, der er indtruffet for hver type af-protokollen.
HTTP-metoder | Gør det muligt at få et hurtigt indtryk af HTTP-metoder bliver brugt til at anmode om dine data. Typisk er de mest almindelige HTTP-anmodning om metoder få, afsnit og INDLÆG. Et krans diagram angiver procentdelen af søgeresultater, der er indtruffet for hver type HTTP-anmodning om metode.
URL-adresser | Indeholder et diagram, der viser de øverste 10 nødvendige URL-adresser. Der vises en søjle for hver URL-adresse. Højden på linjen angiver, hvor mange søgeresultater, som har genereret bestemt URL-adresse over tidsperioden dækket af rapporten. Statistik for øverste 100 anmodes om en URL-adresser vises umiddelbart under dette diagram.
CNAMEs | Indeholder et diagram, der viser den øverste 10 CNAMEs, der bruges til at anmode om aktiver via tid, der strækker sig over i en rapport. Statistik for øverste 100 anmodet om CNAMEs vises umiddelbart under dette diagram.
Baggrunden | Indeholder et diagram, der viser de øverste 10 CDN eller kunde origin servere, der blev anmodet om aktiver i en angivet periode. Statistik for øverste 100 anmodet om CDN- eller kundedata origin servere vises umiddelbart under dette diagram. Kunde origin servere er identificeret med det navn, der er defineret i indstillingen mappenavn.
Geografisk popper | Viser, hvor meget af din trafik dirigeres til en bestemt punkt-for-tilstedeværelse (POP). Den forkortelse på tre bogstaver repræsenterer en POP i vores CDN netværk.
Klienter | Indeholder et diagram, der viser de øverste 10 klienter, der anmodes om aktiver i en angivet periode. I forbindelse med denne rapport betragtes alle anmodninger, der stammer fra den samme IP-adresse skal være fra den samme klient. Statistik for de øverste 100 kunder vises umiddelbart under dette diagram. Denne rapport er nyttig, når download aktivitet mønstre for de øverste klienter.
Statusser, som cache | Giver en detaljeret opdeling af cache funktionsmåde, som kan afsløre fremgangsmåder til forbedring af overordnede slutbrugeroplevelsen. Da den hurtigste ydeevne stammer fra cachen, kan du optimere data levering hastigheder ved at minimere udgivelsescachen og udløbet i cachen.
INGEN detaljer | Indeholder et diagram, der viser de øverste 10 URL-adresser for aktiver, cachen indhold friskhed ikke er markeret i en angivet periode. Statistik for de øverste 100 URL-adresser for disse filtyper Aktiver vises umiddelbart under dette diagram.
CONFIG_NOCACHE detaljer | Indeholder et diagram, der viser de 10 største URL-adresser for aktiver, der ikke blev gemt i cachen på grund af kundens CDN konfiguration. Disse typer Aktiver fik vist direkte fra den oprindelige server. Statistik for de øverste 100 URL-adresser for disse filtyper Aktiver vises umiddelbart under dette diagram.
UNCACHEABLE detaljer | Indeholder et diagram, der viser de øverste 10 URL-adresser til aktiver, der ikke er cachelagret på grund af anmodning om sidehoved data. Statistik for de øverste 100 URL-adresser for disse filtyper Aktiver vises umiddelbart under dette diagram.
TCP_HIT detaljer | Indeholder et diagram, der viser de øverste 10 URL-adresser for aktiver, der leveres med det samme fra cachen. Statistik for de øverste 100 URL-adresser for disse filtyper Aktiver vises umiddelbart under dette diagram.
TCP_MISS detaljer | Indeholder et diagram, der viser de øverste 10 URL-adresser til aktiver, der har statussen cache TCP_MISS. Statistik for de øverste 100 URL-adresser for disse filtyper Aktiver vises umiddelbart under dette diagram.
TCP_EXPIRED_HIT detaljer | Indeholder et diagram, der viser de øverste 10 URL-adresser for forældede aktiver, der blev fungeret direkte fra POP. Statistik for de øverste 100 URL-adresser for disse filtyper Aktiver vises umiddelbart under dette diagram.
TCP_EXPIRED_MISS detaljer | Indeholder et diagram, der viser de øverste 10 URL-adresser for forældede aktiver som en ny version havde skal hentes fra den oprindelige server. Statistik for de øverste 100 URL-adresser for disse filtyper Aktiver vises umiddelbart under dette diagram.
TCP_CLIENT_REFRESH_MISS detaljer | Indeholder et liggende søjlediagram, der viser de 10 vigtigste URL-adresser for Aktiver blev hentet fra en server til origin på grund af en ikke-cache anmodning fra klienten på computeren. Statistik for de øverste 100 URL-adresser for disse filtyper anmodninger vises umiddelbart under dette diagram.
Anmodning om klienttyper | Angiver typen af forespørgsler, der er foretaget af HTTP-klienter (f.eks., browsere). Denne rapport indeholder et kranse-diagram, der indeholder en ide om, hvordan anmodninger der håndteres. Oplysninger om båndbredde og trafik for hver anmodningstype vises under diagrammet.
Brugeragent | Indeholder et søjlediagram, der viser de øverste 10 brugeragenter for at anmode om dit indhold gennem vores CDN. En brugeragent er typisk en webbrowser, medieafspiller eller en mobiltelefon browser. Statistik for de øverste 100 brugeragenter vises umiddelbart under dette diagram.
Henvisninger | Indeholder et søjlediagram, der viser de øverste 10 henvisninger til indhold, der er åbnet via vores CDN. Typisk er en referrer URL-adressen til den webside eller en ressource, der linker til dit indhold. Detaljerede oplysninger er angivet under diagrammet til top 100-henvisninger.
Komprimeringstyper af | Indeholder et kranse-diagram, der opdeler anmodede Aktiver ved om de er blevet komprimeret ved vores edge-servere. Procentdelen af komprimeret Aktiver er opdelt efter typen komprimering bruges. Detaljerede oplysninger er angivet under diagrammet for hver komprimeringstype og status.
Typer af filer | Indeholder et søjlediagram, der viser de øverste 10 filtyper, der har anmodet om via vores CDN for din konto. I forbindelse med denne rapport, en filtype er defineret af aktivets filtypenavn og Internet medietype (f.eks., .html \[tekst/html\], .htm \[tekst/html\], .aspx \[tekst/html\]osv.). Detaljerede oplysninger er angivet under diagrammet for de øverste 100 filtyper.
Entydige filer | Indeholder et diagram, der plottes det samlede antal entydige aktiver, der blev anmodet om en bestemt dag i en angivet periode.
Token Auth oversigt | Indeholder et cirkeldiagram, der giver en hurtig oversigt på om nødvendige Aktiver er beskyttet af Token-baseret godkendelse. Beskyttet Aktiver vises i diagrammet efter resultaterne af deres forsøgte godkendelse.
Token Auth nægte detaljer | Indeholder et søjlediagram, der gør det muligt at få vist de øverste 10 anmodninger, blev nægtet på grund af Token-baseret godkendelse.
HTTP-svar koder | Indeholder en opdeling af HTTP status-koder (f.eks. 200 OK, 403 forbudt, 404 ikke fundet, osv.), der blev leveret til kunderne HTTP ved vores edge-servere. Et cirkeldiagram, kan du hurtigt at vurdere, hvordan dine aktiver fik vist. Detaljerede statistiske dataene er angivet for hver svarkode under diagrammet.
404-fejl | Indeholder et søjlediagram, der gør det muligt at få vist de øverste 10 anmodninger, der udløste en 404 ikke fundet svarkode.
403 fejl | Indeholder et søjlediagram, der gør det muligt at få vist de øverste 10 anmodninger, der udløste en 403 forbudt svarkode. En 403 forbudt svarkode opstår, når en anmodning om afvises ved en kunde origin server eller en edge server på vores POP.
4xx fejl | Indeholder et søjlediagram, der gør det muligt at få vist de øverste 10 anmodninger, der udløste en svarkode i området 400. Undtaget fra denne rapport er 403 blev ikke fundet og 404 forbudt Svarkoder. En 4xx svarkode opstår typisk, når en anmodning om afvises som et resultat af en klientfejl i på computeren.
504 fejl | Indeholder et søjlediagram, der gør det muligt at få vist de øverste 10 anmodninger, der udløste en 504 Gateway Timeout svarkode. En 504 Gateway Timeout svarkode opstår, når der opstår en timeout, når en HTTP-proxy forsøger at kommunikere med en anden server. Hvis det er vores CDN en 504 Gateway Timeout svarkode typisk opstår, når en edge-server kan ikke oprette kommunikation med en kunde origin server.
502 fejl | Indeholder et søjlediagram, der gør det muligt at få vist de øverste 10 anmodninger, der udløste en 502 ugyldig Gateway svarkode. En 502 ugyldig Gateway svarkode opstår, når en HTTP-protokollen fejl opstår mellem en server og en HTTP-proxy. Hvis det er vores CDN en 502 ugyldig Gateway svarkode typisk opstår, når en kunde origin server returnerer et ugyldigt svar til en edge server. Et svar er ugyldige, hvis det ikke kan parses, eller hvis det er ufuldstændig.
5XX fejl | Indeholder et søjlediagram, der gør det muligt at få vist de øverste 10 anmodninger, der udløste en svarkode i området 500.  Undtaget fra denne rapport er 502 ugyldig Gateway og 504 Gateway Timeout Svarkoder.

## <a name="see-also"></a>Se også
* [Azure CDN oversigt](cdn-overview.md)
* [Realtid statistik i Microsoft Azure CDN](cdn-real-time-stats.md)
* [Tilsidesætte standardfunktionsmåde HTTP ved hjælp af regler databaseprogram](cdn-rules-engine.md)
* [Avancerede HTTP-rapporter](cdn-advanced-http-reports.md)
