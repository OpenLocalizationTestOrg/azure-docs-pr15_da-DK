<properties
   pageTitle="Hvad er Log Analytics? | Microsoft Azure"
   description="Log Analytics er en tjeneste i handlinger Management pakke (OMS), der hjælper dig med at indsamle og analysere funktionsdygtige data, der genereres af ressourcer i din skyen og lokale miljø.  Denne artikel indeholder en kort oversigt over de forskellige komponenter i Log analyser og links til detaljerede indhold."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />

# <a name="what-is-log-analytics"></a>Hvad er Log Analytics?
Log Analytics er en tjeneste i [Handlinger Management Suite \(OMS\) ](../operations-management-suite/operations-management-suite-overview.md) , der hjælper med at indsamle og analysere data, der genereres af ressourcer i din skyen og lokale miljøer. Det giver dig realtid indsigt ved hjælp af integreret søgning og brugerdefinerede dashboards til at analysere let millioner af poster på tværs af alle dine arbejdsmængder og servere uanset deres fysiske placering.


## <a name="log-analytics-components"></a>Log Analytics komponenter
På center Log Analytics er det OMS lager som hostes i Azure skyen.  Data indsamles til lageret fra tilknyttede kilder ved konfiguration af datakilder og tilføje løsninger til dit abonnement.  Datakilder og løsninger opretter hver forskellige posttyper, som har deres egne sæt af egenskaber, men kan stadig analysere sammen i forespørgsler til lageret.  Dette kan du bruge de samme værktøjer og metoder til at arbejde med forskellige typer data, der indsamles fra forskellige kilder.


![OMS lager](media/log-analytics-overview/overview.png)


Tilknyttede kilder er computerne og andre ressourcer, der opretter data, der indsamles via Log analyser.  Dette kan omfatte supportmedarbejdere, der er installeret på [Windows](log-analytics-windows-agents.md) og [Linux](log-analytics-linux-agents.md) -computere, der har forbindelse direkte eller supportmedarbejdere i en [forbindelse System Center Operations Manager management gruppe](log-analytics-om-agents.md).  Log Analytics kan også Indsaml data fra [Azure-lager](log-analytics-azure-storage.md).

[Datakilder](log-analytics-data-sources.md) er de forskellige typer data, der indsamles fra hver forbundne kilde.  Dette omfatter begivenheder og [ydelsesdata](log-analytics-data-sources-performance-counters.md) fra [Windows](log-analytics-data-sources-windows-events.md) og Linux supportmedarbejdere ud over kilder som [IIS-logfiler](log-analytics-data-sources-iis-logs.md)og [brugerdefineret tekst hændelseslogge](log-analytics-data-sources-custom-logs.md).  Du konfigurerer hver datakilde, du vil indsamle, og konfigurationen leveres automatisk til hver forbundne kilde.


## <a name="analyzing-log-analytics-data"></a>Analyse af Log Analytics-data
De fleste af din interaktion med Log Analytics bliver via portalen OMS som kører i enhver browser og giver dig adgang til indstillinger for søgekonfiguration og flere værktøjer for at analysere og handle på indsamlede data.  Du kan udnytte [log søgninger](log-analytics-log-searches.md) , hvor du kan oprette forespørgsler til at analysere indsamlede data, [dashboards](log-analytics-dashboards.md) , som du kan tilpasse med grafiske visninger af dine mest værdifulde søgninger og [løsninger](log-analytics-add-solutions.md) , som understøtter flere funktioner og analyseværktøjer fra portalen.

![OMS portal](media/log-analytics-overview/portal.png)


Log Analytics indeholder en forespørgselssyntaksen for at hente og konsolidere data i lageret hurtigt.  Du kan oprette og gemme [Log søgninger](log-analytics-log-searches.md) direkte analyse af data i portalen OMS eller har log søgninger køres automatisk for at oprette en besked, hvis resultatet af forespørgslen angiver en vigtige betingelse.

![Log søgning](media/log-analytics-overview/log-search.png)

Hvis du vil give en hurtig grafisk oversigt over tilstanden for dit overordnede miljø, kan du tilføje visualiseringer for gemt logfil søgninger til dit [dashboard](log-analytics-dashboards.md).   

![Dashboard](media/log-analytics-overview/dashboard.png)

For at analysere data uden for Log Analytics, kan du eksportere dataene fra OMS lager til funktioner som [Power BI](log-analytics-powerbi.md) eller Excel.  Du kan også udnytte [Log Søg API](log-analytics-log-search-api.md) til at oprette tilpassede løsninger, der udnytte Log Analytics-data eller integrere med andre systemer.

## <a name="solutions"></a>Løsninger
Løsninger føje funktioner til Log analyser.  De primært kører i skyen og give analyse af data, der indsamles i OMS-lager. De kan også angive nye posttyper til indsamles, der kan analysere med Log søgninger eller via yderligere brugergrænseflade, der leveres af løsningen i dashboardet OMS.  

![Ændre registrering løsning](media/log-analytics-overview/change-tracking.png)


Løsninger, der er tilgængelige for en række funktioner, og du kan nemt gennemse tilgængelige løsninger og [føje dem til arbejdsområdet OMS](log-analytics-add-solutions.md) fra løsningsgalleriet.  Mange installeres automatisk og begynde at arbejde med det samme, mens andre kræver muligvis nogle konfiguration.

![Løsningsgalleriet](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-architecture"></a>Log Analytics arkitektur
Log Analytics installation krav er minimale, da de centrale komponenter er hostet i Azure skyen.  Dette omfatter lager ud over de tjenester, der kan bruges til at koordinere og analysere indsamlede data.  Portalen kan åbnes fra enhver browser, så der er ingen krav til klientsoftware.

Du skal installere supportmedarbejdere på [Windows](log-analytics-windows-agents.md) og [Linux](log-analytics-linux-agents.md) -computere, men der er ingen yderligere agent, der kræves til computere, der er allerede medlem af en [forbindelse SCOM administration af gruppe](log-analytics-om-agents.md).  SCOM supportmedarbejdere fortsætter med at kommunikere med management-servere, der videresender deres data til Log analyser.  Nogle løsninger via kræver supportmedarbejdere at kommunikere direkte med Log analyser.  I dokumentationen til hver løsning vil angive dens krav til kommunikation.

Når du [tilmelde dig Log Analytics](log-analytics-get-started.md), du vil oprette en OMS arbejdsområde.  Du kan betragte arbejdsområdet som et entydigt OMS miljø med sin egen datalager, datakilder og løsninger. Du kan oprette flere arbejdsområder i dit abonnement til at understøtte flere miljøer som fremstilling og teste.

![Log Analytics arkitektur](media/log-analytics-overview/architecture.png)


## <a name="next-steps"></a>Næste trin

- [Tilmelde dig en gratis Log Analytics-konto](log-analytics-get-started.md) til at teste i dit eget miljø.
- Få vist de forskellige [Datakilder](log-analytics-data-sources.md) der er tilgængelige til at indsamle data til OMS lageret.
- [Gennemse de tilgængelige løsninger i løsningsgalleriet](log-analytics-add-solutions.md) at føje funktioner til Log analyser.
