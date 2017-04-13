<properties
    pageTitle="Overvejelser til Azure trafik Manager | Microsoft Azure"
    description="Forstå ydeevne på trafik Manager, og hvordan du teste ydeevnen af dit websted, når du bruger trafik Manager"
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

# <a name="performance-considerations-for-traffic-manager"></a>Overvejelser til trafik Manager

Denne siden beskrives, overvejelser ved hjælp af trafik Manager. Forestil dig følgende:

Du har forekomster af dit websted i WestUS og EastAsia områder. En af forekomsterne går ned tilstandskontrol for trafik manager efterprøvning af af. Programmet trafik ført til sund område. Denne failover forventes, men ydeevnen kan være et problem, der er baseret på forsinkelse på trafikken nu rejse til en anden region.

## <a name="how-traffic-manager-works"></a>Hvordan fungerer trafik Manager

Den eneste indflydelse på ydeevnen, som kan have trafik Manager på dit websted er den indledende DNS-opslag. En DNS-anmodning om navnet på din profil, trafik Manager håndteres af Microsofts DNS rod-server, der er vært zonen trafficmanager.net. Trafik Manager udfylder og opdaterer jævnligt på Microsofts DNS-rodservere baseret på politikken trafik Manager og efterprøvning af af resultaterne. Forespørgsler uden DNS sendes så selv under den indledende DNS-opslag til trafik Manager.

Trafik Manager består af flere komponenter: DNS-navnet servere, en API-tjeneste, lagerplads lag og et slutpunkt, overvågning tjeneste. Hvis en trafik Manager servicekomponent mislykkes, skal har det ingen virkning på DNS-navn, der er knyttet til din profil trafik Manager. Posterne i Microsofts DNS-servere forbliver uændret. Dog slutpunkt overvågning og opdatering af DNS-ikke sker. Derfor er trafik Manager ikke muligt at opdatere DNS til at pege på webstedet failover, når webstedet primære går ned.

DNS-navneoversættelse er hurtig, og resultaterne cachelagres. Hastigheden for den indledende DNS-opslag, afhænger af DNS-serverne klienten bruger til navneoversættelse. Typisk en klient, der kan udføre en DNS-opslag i ~ 50 ms. Resultaterne af opslaget cachelagres for varigheden af DNS-Time-to-live (TTL). Standard TTL til trafik Manager er 300 sekunder.

Trafik flyder ikke via trafik Manager. Når DNS-opslag er fuldført, er kunden en IP-adresse til en forekomst af dit websted. Klienten opretter forbindelse direkte til den pågældende adresse og går ikke gennem trafik Manager. Politikken trafik Manager skal du vælge har ingen indflydelse på DNS-ydeevne. En ydeevne routing-metoden kan negativt påvirke programmet oplevelsen. Eksempelvis hvis din politik omdirigerer trafik fra Nordamerika til en forekomst, der er hostet i Asien, kan netværksventetid for disse sessioner være problemer med ydeevnen.

## <a name="measuring-traffic-manager-performance"></a>Måle trafik Manager ydelse

Der er flere websteder, du kan bruge til at forstå den ydeevne og funktionalitet i en trafik Manager profil. Mange af disse websteder er gratis, men du skal muligvis begrænsninger. Nogle websteder tilbyder udvidet overvågning og rapportering for et gebyr.

Værktøjer på disse websteder måling DNS latenstider og Vis den løst IP-adresser for klient placeringer over hele verden. De fleste af disse værktøjer skal ikke cachelagres DNS resultaterne. Derfor viser værktøjerne fuld DNS opslaget hver gang en test udføres. Når du tester fra din egen klienten på computeren, opleve du kun fulde DNS-opslag ydeevne én gang i løbet TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Eksempel værktøjer til at måle DNS-ydeevne

- [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS indeholder mange værktøjer til ydeevnen. Værktøjet DNS sammenligning kan du se hvor lang tid det tager for at løse dine DNS-navn, og hvordan, der sammenlignes med andre DNS-udbydere.

- [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    En af de mest enkle værktøjer er WebSitePulse. Angiv URL-adressen for at få vist DNS-opløsning tid, første Byte, sidste Byte og andre præstationstal. Du kan vælge mellem tre forskellige test placeringer. I dette eksempel skal se du, første kørsel viser, at DNS-opslag tager 0.204 sec.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Fordi resultaterne cachelagres, den anden test for samme trafik Manager slutpunktet DNS-opslag tager 0.002 sec.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

- [CA App syntetiske skærm](https://asm.ca.com/en/checkit.php)

    Tidligere kendt som værktøjet Watchmouse Kontrollér websted, dette websted viser dig DNS-opløsning tid fra flere geografiske områder samtidigt. Angiv URL-adressen for at få vist DNS-opløsning tid, blev oprettet forbindelse og hastigheden fra flere geografiske placeringer. Brug denne test til at se, hvilke hostet service returneres til forskellige steder over hele verden.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

- [Pingdom](http://tools.pingdom.com/)

    Dette værktøj kan præstationstal for hvert element i en webside. Fanen side analyse viser procentdelen af tid på DNS-opslag.

- [Hvad er mine DNS?](http://www.whatsmydns.net/)

    Dette websted indeholder en DNS-opslag fra 20 forskellige steder og viser resultaterne på et kort.

- [Grave brugergrænsefladen internettet](http://www.digwebinterface.com)

    Dette websted viser mere detaljerede oplysninger om DNS-herunder CNAMEs og en poster. Kontrollér, at du Kontrollér 'Farvelæg output' og 'Statistik' under indstillinger, og vælge "Alle" under navneservere.

## <a name="next-steps"></a>Næste trin

[Om trafik Manager trafik routing metoder](traffic-manager-routing-methods.md)

[Test dine trafik Manager-indstillinger](traffic-manager-testing-settings.md)

[Handlinger på trafik Manager (REST-API Reference)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure-cmdletter trafik Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769)
