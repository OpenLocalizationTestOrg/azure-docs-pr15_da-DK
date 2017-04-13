<properties
    pageTitle="Trafik Manager - trafik routing metoder | Microsoft Azure"
    description="I denne artikel hjælper dig med at forstå de forskellige trafik routing metoder, der bruges af trafik Manager"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="traffic-manager-traffic-routing-methods"></a>Trafik Manager trafik-routing metoder

Azure trafik Manager understøtter tre trafik-routing metoder til at bestemme, hvordan til at dirigere netværkstrafik til forskellige service slutpunkterne. Trafik Manager gælder metoden trafik-routing for hver DNS-forespørgsel den modtager. Metoden trafik-routing bestemmer, hvilke slutpunkt returneres i svaret DNS.

Fra Azure ressourcestyring support til trafik Manager bruger en anden terminologi end den klassiske implementeringsmodel. Følgende tabel viser forskellene mellem ressourcestyring og klassisk ord:

| Ressourcestyring ord | Klassisk ord |
|-----------------------|--------------|
| Trafik-routing metode | Justering af belastning metode |
| Prioritet metode | Failover metode |
| Vægtet metode | Round robin-metode |
| Ydeevnen metode | Ydeevnen metode |

Baseret på kundefeedback, ændret vi terminologi for at forbedre klarhed og reducere almindelige misforståelser. Der er ingen forskel i funktioner.

Der er tilgængelige i trafik Manager tre trafik routing metoder:

- **Prioritet:** Vælg 'Priority', når du vil bruge en primær tjenesteslutpunkt for al trafik, og giv sikkerhedskopier i tilfælde af primært eller sikkerhedskopiering slutpunkterne er ikke tilgængelig.
- **Vægtet:** Vælg 'vægtet' når du vil distribuere trafik på tværs af et sæt af slutpunkter, enten jævnt eller efter tykkelser, som du kan definere.
- **Ydeevne:** Vælg 'Ydeevne', når du har slutpunkter i forskellige geografiske steder, og du vil slutbrugere mulighed for at bruge "nærmeste" slutpunktet med hensyn til de laveste netværksventetid.

Alle trafik Manager profiler omfatte overvågning af slutpunkt tilstand og automatisk slutpunkt belastningsjustering. Du kan finde yderligere oplysninger finder [Trafik Manager slutpunkt overvågnings](traffic-manager-monitoring.md). En enkelt trafik Manager profil kan bruge kun én trafik routing metode. Når som helst, kan du vælge en anden trafik routing metode til din profil. Ændringerne anvendes inden for et minut, og ingen nedetid er påløbet. Trafik-routing metoder kan kombineres ved hjælp af indlejrede trafik Manager profiler. Indlejring gør det muligt for avancerede og fleksible trafik-routing konfigurationer, der imødekommer behovet hos større, kompleks programmer. Du kan finde yderligere oplysninger finder [indlejrede trafik Manager profiler](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Prioritet trafik-routing metode

Ofte vil en organisation at levere pålidelighed for dens tjenester ved at installere en eller flere ekstra tjenester, i tilfælde af deres primære tjeneste går ned. Metoden 'Priority' trafik-routing kan Azure kunder for let at implementere denne failover mønster.

![Azure trafik Manager 'Priority' trafik-routing metode][1]

Trafik Manager-profil indeholder en prioriteret liste over slutpunkter for tjenesten. Som standard sender trafik Manager al trafik til den primære (højeste prioritet) slutpunkt. Hvis den primære slutpunkt ikke er tilgængelig, dirigerer trafik Manager trafikken til det andet slutpunkt. Hvis begge de primære og sekundære slutpunkter ikke er tilgængelige, går trafikken til den tredje osv. Tilgængeligheden af slutpunktet er baseret på konfigurerede status (aktiveret eller deaktiveret) og igangværende slutpunkt overvågning.

### <a name="configuring-endpoints"></a>Konfigurerer slutpunkter

Med Azure ressource Manager skal du konfigurere slutpunkt prioriteten eksplicit ved hjælp af egenskaben 'priority' for hvert slutpunkt. Denne egenskab er en værdi mellem 1 og 1000. Lavere værdier repræsenterer en højere prioritet. Slutpunkter kan ikke dele prioritetsværdier. Angive egenskaben er valgfrit. Når udelades, bruges en prioritet, baseret på den første eller sidste ark rækkefølge som standard.

Slutpunkt prioriteten er konfigureret implicit med grænsefladen klassisk. Prioriteten er baseret på den rækkefølge, hvori slutpunkterne vises i definitionen af profil.

## <a name="weighted-traffic-routing-method"></a>Vægtet trafik-routing metode

Metoden 'Vægtet' trafik-routing gør det muligt at distribuere trafik jævnt eller bruge en foruddefineret vægtning.

![Azure trafik 'Vægtet' trafik-routing metoden Manager][2]

I den vægtede trafik-routing metode tildeler du en tykkelse til hvert slutpunkt i konfigurationen af trafik Manager profil. Tykkelsen er et heltal fra 1 til 1000. Denne parameter er valgfri. Hvis udelades, bruges trafik ledere en standard tykkelse '1'.

For hver DNS-forespørgsel, der er modtaget, vælger trafik Manager tilfældigt tilgængelige ark. Sandsynligheden for at vælge et slutpunkt er baseret på de vægt, der er tildelt til alle tilgængelige slutpunkter. Brug af den samme vægt på tværs af alle slutpunkter resultater i en lige trafik fordeling. Brug af højere eller lavere vægte på bestemte slutpunkter bevirker, at disse slutpunkter skal returneres mere eller mindre ofte i DNS-svar.

Metoden vægtet gør det muligt for visse scenarier med nyttige:

- Gradvist programmet opgraderingen: tildele en procentdel af trafik til at dirigere til et nyt slutpunkt, og Forøg gradvist trafikken over tid til 100%.
- Overførsel af programmet til Azure: oprette en profil med både Azure og eksterne slutpunkter. Justere tykkelse slutpunkter til foretrækker nye slutpunkterne.
- Skyen bursting sikrer ekstra kapacitet: udvide en lokal installation i skyen hurtigt ved at placere det bag en trafik Manager profil. Når du har brug for ekstra kapacitet i skyen, kan du tilføje eller aktivere flere slutpunkter og angive, hvilken del af trafikken går til hvert slutpunkt.

Den nye Azure portal understøtter konfigurationen af vægtet trafik routing. Tykkelser kan ikke konfigureres i portalen klassisk. Du kan også konfigurere tykkelser ved hjælp af Ressourcestyring og klassisk versioner af Azure PowerShell, CLI og REST API'er.

Det er vigtigt at forstå, at DNS-svar cachelagres klienter og rekursiv DNS-servere, der klienterne, der kan bruge til at fortolke DNS-navne. Denne cachelagring kan have indvirkning på vægtet trafik salgsdistributioner. Når antallet klienter og rekursiv DNS-servere er stor, fungerer trafik fordeling som forventet. Men når antallet af klienter eller rekursiv DNS-servere er lille, cachelagring kan markant vride trafik fordeling.

Almindelige brug tilfælde omfatter:

- Udvikling og test miljøer
- Program til program kommunikation
- Programmer med henblik på en smal bruger-base, der deler en fælles rekursiv DNS-infrastruktur (for eksempel medarbejdere på firma forbindelse via en proxy)

Effekterne DNS cachelagring er fælles for alle DNS-baseret trafik routing systemer, ikke kun Azure trafik Manager. I nogle tilfælde eksplicit rydde DNS-cachen muligvis give en løsning. I andre tilfælde kan sig en alternativ trafik-routing metode egner bedre.

## <a name="performance-traffic-routing-method"></a>Ydeevnen trafik-routing metode

Implementere slutpunkter i to eller flere placeringer over hele verden kan forbedre svartid mange programmer ved at dirigere trafik til den placering, der er tættest' for dig. Metoden 'Ydeevne' trafik-routing indeholder denne egenskab.

![Azure trafik Manager 'Ydeevne' trafik-routing metode][3]

'Nærmeste' slutpunktet er ikke nødvendigvis nærmeste målt efter geografiske afstand. I stedet bestemmer metoden 'Ydeevne' trafik-routing det nærmeste slutpunkt ved at måle netværksventetid. Trafik Manager vedligeholder en Internet ventetid tabel for at registrere den tid, frem og tilbage mellem IP-adresseområder og hver Azure datacenter.

Trafik Manager søger efter kilde IP-adressen for den indgående DNS-anmodning i tabellen Internet ventetid. Trafik chefen vælger tilgængelige ark i Azure datacenteret, der har den laveste ventetid for IP-adresseområdet og returnerer derefter pågældende slutpunkt i DNS-svaret.

Som beskrevet i [Sådan trafik Manager fungerer](traffic-manager-how-traffic-manager-works.md), modtager trafik Manager ikke DNS-forespørgsler direkte fra klienter. I stedet DNS-forespørgsler kommer fra rekursiv DNS-tjenesten, at klienterne, der er konfigureret til at bruge. Derfor IP-adresse bruges til at bestemme 'nærmeste' slutpunktet ikke er kundens IP-adresse, men det er IP-adressen til rekursiv DNS-tjenesten. I praksis er denne IP-adresse en god proxy for klienten.

Trafik Manager opdaterer jævnligt tabellen Internet ventetid for at tage højde for ændringer i den globale Internet- og nye Azure områder. Programmets ydeevne varierer dog baseret på realtid variationer i belastning på tværs af internettet. Ydeevnen trafik-routing overvåger ikke belastning på en given tjenesteslutpunkt. Men hvis et slutpunkt er tilgængelig, trafik Manager ikke findes i DNS-forespørgsel svar.

Peger på Bemærk:

- Hvis din profil indeholder flere slutpunkter i samme Azure region, derefter distribuerer trafik Manager trafik jævnt på tværs af de tilgængelige slutpunkter i det pågældende område. Hvis du foretrækker en anden trafik fordeling i et område, kan du bruge [indlejrede trafik Manager profiler](traffic-manager-nested-profiles.md).

- Hvis alle aktiveret slutpunkter i et bestemt Azure område er nedsat, fordeles trafik Manager trafik på tværs af alle andre tilgængelige slutpunkter i stedet for det næste nærmeste slutpunkt. Denne logik forhindrer en overlappende fejl i at opstå ved ikke overbelastning det næste nærmeste slutpunkt. Hvis du vil definere foretrukne failover rækkefølge, skal du bruge [indlejrede trafik Manager profiler](traffic-manager-nested-profiles.md).

- Når du bruger metoden ydeevne trafik routing med eksterne slutpunkter eller indlejret slutpunkter, skal du angive placeringen af disse slutpunkter. Vælg det Azure område, der er tættest på din installation. Disse placeringer er de værdier, der understøttes i Internet ventetid tabellen.

- Den algoritme, vælger slutpunktet er deterministisk. Gentagne DNS-forespørgsler fra den samme klient dirigeres til det samme slutpunkt. Typisk brug klienter forskellige rekursiv DNS-servere, når rejse. Klienten kan sendes til et andet endpoint. Routing kan også påvirkes af opdateringer til tabellen Internet ventetid. Derfor garanterer metoden ydeevne trafik-routing ikke en, en klient altid dirigeres til det samme slutpunkt.

- Når tabellen Internet ventetid ændres, kan du bemærke, at nogle klienter, dirigeres til en anden slutpunkt. Ændringen routing er mere nøjagtig baseret på aktuelle ventetid data. Disse opdateringer er vigtige for at bevare nøjagtigheden af trafik routing af ydeevne, mens internettet kontinuerligt udvikles.

## <a name="next-steps"></a>Næste trin

Lær, hvordan du udvikler programmer med høj tilgængelighed ved hjælp af [trafik Manager slutpunkt overvågning](traffic-manager-monitoring.md)

Lær, hvordan du [opretter en trafik Manager-profil](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png
