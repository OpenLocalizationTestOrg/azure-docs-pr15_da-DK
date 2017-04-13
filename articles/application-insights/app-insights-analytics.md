<properties 
    pageTitle="Analytics - værktøjet effektiv søgning i programmet indsigt | Microsoft Azure" 
    description="Oversigt over Analytics, effektive diagnosticering søgeværktøjet af programmet indsigt. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/26/2016" 
    ms.author="awills"/>


# <a name="analytics-in-application-insights"></a>Analytics i programmet indsigt


[Analytics](app-insights-analytics.md) er effektive søgefunktionen af [Programmet indsigt](app-insights-overview.md). Disse sider beskriver Analytics forespørgsel lanquage. 

* **[Se indledende videoen](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Test drev analyser af vores simuleret data](https://analytics.applicationinsights.io/demo)** , hvis din app ikke sende data til programmet indsigt endnu.

## <a name="queries-in-analytics"></a>Forespørgsler i Analytics
 
En typisk forespørgsel er *en kildetabel, efterfulgt af en række *operatorer* adskilt af* `|`. 

For eksempel Lad os finde ud af, hvilken tid på dagen borgeres Hyderabad Prøv vores online. Og mens vi er der, Lad os se, hvilke resultatet koder vender tilbage til deres HTTP-anmodninger. 

```AIQL

    requests      // Table of events that log HTTP requests.
  	| where timestamp > ago(7d) and client_City == "Hyderabad"
  	| summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
  	| extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Vi tælle entydige Klienternes IP-adresser, gruppere dem efter timen på dagen over de seneste 7 dage. 

Lad os få vist resultaterne med liggende søjlediagram præsentationen, hvis du vælger at stable resultater fra forskellige Svarkoder:

![Vælg liggende søjlediagram, x og y-akser og derefter segmentering](./media/app-insights-analytics/020.png)

Ser ud til vores app er mest populære på frokosttid og Integrer og klokkeslæt i Hyderabad. (Og vi skal undersøge 500 koder.)


Der er også effektive statistiske handlinger:

![](./media/app-insights-analytics/025.png)


Sproget, der har mange flot funktioner:

* [Filtrere](app-insights-analytics-reference.md#where-operator) din rå app telemetri ved eventuelle felter, herunder dine brugerdefinerede egenskaber og målepunkter.
* [Deltage i](app-insights-analytics-reference.md#join-operator) flere tabeller – correlate anmodninger med sidevisninger, afhængighed opkald, undtagelser og log sporer.
* Effektiv statistiske [sammenlægninger](app-insights-analytics-reference.md#aggregations).
* Lige så effektive som SQL, men meget nemmere for komplekse forespørgsler: i stedet for indlejre sætninger, du pipe data fra én grundlæggende handling til næste.
* Øjeblikkelig og effektive visualiseringer.







## <a name="connect-to-your-application-insights-data"></a>Oprette forbindelse til dit program indsigt data


Åbne Analytics fra din app [Oversigt blade](app-insights-dashboards.md) i programmet indsigt: 

![Åbn portal.azure.com, Åbn dit program indsigt ressource, og klik på Analytics.](./media/app-insights-analytics/001.png)


## <a name="limits"></a>Begrænsninger

Resultater af en forespørgsel er begrænset til lige over en uge tidligere data på nuværende tidspunkt.



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


## <a name="next-steps"></a>Næste trin


* Vi anbefaler, at du starter med [sprog rundvisning](app-insights-analytics-tour.md).