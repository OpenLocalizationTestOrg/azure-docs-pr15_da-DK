<properties 
    pageTitle="Programmet oversigten i programmet indsigt | Microsoft Azure" 
    description="En visuel præsentation af afhængigheder mellem app komponenter mærket med KPI'er og beskeder." 
    services="application-insights" 
    documentationCenter=""
    authors="SoubhagyaDash" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/15/2016" 
    ms.author="awills"/>
 
# <a name="application-map-in-application-insights"></a>Programmet oversigten i programmet indsigt

I [Visual Studio programmet viden](app-insights-overview.md)er programmet tilknytning et visuelt layout af afhængighed relationerne mellem din programkomponenter. Hver komponent vises KPI'er som indlæsning, ydeevne, fejl og beskeder, kan hjælpe dig med at opdage alle komponenter, der forårsager et problem med ydeevnen eller fejl. Du kan klikke gennem fra en hvilken som helst komponent til mere detaljerede diagnosticering, fra programmet indsigt og, hvis din app bruger Azure services - Azure diagnosticering, såsom SQL Database Advisor anbefalinger.

Som andre diagrammer, kan du fastgøre en programmet tilknytning til Azure dashboard, hvor det er fuldt funktionelle. 

## <a name="open-the-application-map"></a>Åbn program-kortet

Åbne kortet fra bladet oversigt for dit program:

![Åbn app kort](./media/app-insights-app-map/01.png)

![App-kort](./media/app-insights-app-map/02.png)

Kortet vises:

* Tilgængelighed test
* Klient side komponent (overvåges ved hjælp af JavaScript SDK)
* Server side-komponent
* Afhængigheder klient- og komponenter

Du kan udvide og skjule afhængighed link grupper:

![Skjul](./media/app-insights-app-map/03.png)
 
Hvis du har et stort antal afhængigheder af én type (SQL, HTTP osv.), kan de vises grupperet. 


![grupperede afhængigheder](./media/app-insights-app-map/03-2.png)
 
 
## <a name="spot-problems"></a>Finde frem til problemer

Hver node har relevante indikatorer, som de indlæsning, ydeevne og manglende satser for den pågældende komponent. 

Advarsel om ikoner fremhæve eventuelle problemer. En orange advarsel betyder, at der er fejl i anmodninger om sidevisninger eller afhængighed opkald. Rød betyder, at en manglende sats over 5%.


![manglende ikoner](./media/app-insights-app-map/04.png)

 
Aktiv advarer også Vis op: 


![aktive beskeder](./media/app-insights-app-map/05.png)
 
Hvis du bruger SQL Azure, der er et ikon, der viser, hvornår der er anbefalinger til, hvordan du kan forbedre ydeevnen. 


![Azure anbefaling](./media/app-insights-app-map/06.png)

Klik på et ikon for at få flere oplysninger:


![Azure anbefaling](./media/app-insights-app-map/07.png)
 
 
## <a name="diagnostic-click-through"></a>Diagnosticering Klik gennem

Hver af noderne på kortet tilbyder målrettede Klik gennem til diagnosticering. Indstillingerne varierer afhængigt af typen noden.

![serverindstillinger](./media/app-insights-app-map/09.png)

 
For komponenter, der er hostet i Azure, omfatter indstillingerne direkte links til dem.


## <a name="filters-and-time-range"></a>Filtre og tidsinterval

Som standard opsummerer kortet alle de data, der er tilgængelige for det valgte tidsinterval. Men du kan filtrere den for at medtage bestemt operation navne eller afhængigheder.

* Handlingsnavn: Dette omfatter både sidevisninger og anmodning om servertyper side. Med denne indstilling viser kortet KPI-Indikatoren på noden server/client side for de valgte operationer. Det viser de afhængigheder, kaldet i forbindelse med disse bestemte handlinger.
* Afhængighed base navn: Dette omfatter AJAX browser side afhængigheder og server side afhængigheder. Hvis du rapporterer brugerdefineret afhængighed telemetri med TrackDependency API, vises de også her. Du kan vælge afhængigheder vise på kortet. Vær opmærksom på, på nuværende tidspunkt dette ikke filtrere server side anmodninger eller klient side sidevisninger.


![Angiv filtre](./media/app-insights-app-map/11.png)

 
 
## <a name="save-filters"></a>Gemme filtre

For at gemme de filtre, du har anvendt skal du fastgøre den filtrerede visning på et [dashboard](app-insights-dashboards.md).


![Fastgøre til dashboard](./media/app-insights-app-map/12.png)
 


## <a name="feedback"></a>Feedback

Skal du [give feedback via indstillingen portalen feedback](app-insights-get-dev-support.md).


![Billede af MapLink-1](./media/app-insights-app-map/13.png)


