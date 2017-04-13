<properties 
    pageTitle="Brug af Analytics - værktøjet effektiv søgning i programmet indsigt | Microsoft Azure" 
    description="Brug af analyser, effektive diagnosticering søgeværktøjet af programmet indsigt. " 
    services="application-insights" 
    documentationCenter=""
    authors="danhadari" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/21/2016" 
    ms.author="awills"/>


# <a name="using-analytics-in-application-insights"></a>Brug af Analytics i programmet indsigt


[Analytics](app-insights-analytics.md) er effektive søgefunktionen af [Programmet indsigt](app-insights-overview.md). Disse sider beskriver forespørgselssprog analyser.

* **[Se indledende videoen](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Test drev analyser af vores simuleret data](https://analytics.applicationinsights.io/demo)** , hvis din app ikke sende data til programmet indsigt endnu.

## <a name="open-analytics"></a>Åbn Analytics

Klik på Analytics fra din app privat ressource i programmet indsigt.

![Åbn portal.azure.com, Åbn dit program indsigt ressource, og klik på Analytics.](./media/app-insights-analytics-using/001.png)

Indbygget selvstudiet giver dig nogle ideer til, hvad du kan gøre.

Der er en [mere omfattende rundvisning her](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Forespørgsel din telemetri

### <a name="write-a-query"></a>Skrive en forespørgsel

![Skema visning](./media/app-insights-analytics-using/150.png)

Starte med navnene på en af de tabeller, der er anført på venstre (eller de [område](app-insights-analytics-reference.md#range-operator) eller [union](app-insights-analytics-reference.md#union-operator) operatorer). Brug `|` til at oprette en rørledning af [operatorer](app-insights-analytics-reference.md#queries-and-operators). IntelliSense beder dig om med operatorerne og nogle af de Udtrykselementer, du kan bruge.

Se [Oversigt over Analytics sprog](app-insights-analytics-tour.md) og [language reference](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Køre en forespørgsel

![Køre en forespørgsel](./media/app-insights-analytics-using/130.png)

1. Du kan bruge enkelt linjeskift i en forespørgsel.
2. Placere markøren i eller i slutningen af den forespørgsel, du vil køre.
3. Klik på Udfør for at køre forespørgslen.
4. Sæt ikke tomme linjer i forespørgslen. Du kan holde flere adskilte forespørgsler i en under forespørgselsfanen ved at adskille dem med tomme linjer. Kun ene med markøren kører.

### <a name="save-a-query"></a>Gemme en forespørgsel

![Gemme en forespørgsel](./media/app-insights-analytics-using/140.png)

1. Gemme den aktuelle forespørgselsfil.
2. Åbn en gemt forespørgsel-fil.
3. Oprette en ny forespørgselsfil.


## <a name="see-the-details"></a>Få vist detaljer

Udvide en hvilken som helst række i resultaterne for at se den komplette liste over egenskaber. Du kan yderligere udvide en egenskab, der er en strukturerede værdi – for eksempel, brugerdefinerede mål eller listen over i en undtagelse stablen.

![Udvide en række](./media/app-insights-analytics-using/070.png)

 

## <a name="arrange-the-results"></a>Arranger resultaterne

Du kan sortere, filtrere, indstillinger og gruppere resultaterne returneres fra din forespørgsel.

> [AZURE.NOTE] Sortering, gruppering og filtrering i browseren, ikke køre forespørgslen igen. De kun omarrangere de resultater, der blev returneret af din seneste forespørgsel. 
> 
> For at udføre disse opgaver i serveren, før resultaterne returneres, skal du skrive din forespørgsel med [sortering](app-insights-analytics-reference.md#sort-operator), [opsummere](app-insights-analytics-reference.md#summarize-operator) og [hvor](app-insights-analytics-reference.md#where-operator) operatorer.

Vælg de kolonner, du gerne vil se, træk kolonneoverskrifter for at omarrangere dem og ændre størrelse på kolonner ved at trække i kanterne.

![Arrangere kolonner](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Sortere og filtrere elementer

Sortere dine resultater ved at klikke på afsnit i en kolonne. Klik igen for at sortere anden vej, og klik på en tredje tid til at vende tilbage til den oprindelige rækkefølge, der returneres af din forespørgsel.

Brug filterikonet til at indsnævre søgningen.

![Sortere og filtrere kolonner](./media/app-insights-analytics-using/040.png)



### <a name="group-items"></a>Gruppeelementer

For at sortere efter mere end én kolonne, skal du bruge gruppering. Først aktivere den, og træk derefter kolonneoverskrifter på plads over tabellen.

![Gruppe](./media/app-insights-analytics-using/060.png)



### <a name="missing-some-results"></a>Mangler nogle resultater?

Er der en grænse på omkring 10 k rækker på siden resultater, der returneres fra portalen. En advarsel vises, hvis du går hen over grænsen. Hvis det sker, viser sortere dine resultater i tabellen ikke altid dig alle de faktiske første eller sidste resultater. 

Det er god praksis at undgå at ramme grænsen. Bruge operatorer som f.eks.:

* [hvor tidsstempel > ago(3d)](app-insights-analytics-reference.md#where-operator)
* [top 100 efter tidsstempel](app-insights-analytics-reference.md#top-operator) 
* [tage 100](app-insights-analytics-reference.md#take-operator)
* [opsummere](app-insights-analytics-reference.md#summarize-operator) 



## <a name="diagrams"></a>Diagrammer

Vælg typen diagram, du vil have:

![Vælg en diagramtype](./media/app-insights-analytics-using/230.png)

Hvis du har flere kolonner med den rigtige type, kan du vælge x og y-akser og en kolonne med dimensioner opdele resultaterne efter.

Resultaterne vises først som en tabel, og du vælger manuelt diagrammet som standard. Men du kan bruge den [gengive direktiv](app-insights-analytics-reference.md#render-directive) i slutningen af en forespørgsel til at vælge et diagram.

## <a name="pin-to-dashboard"></a>Fastgøre til dashboard

Kan du fastgøre et diagram eller tabel til en af dine [delte dashboards](app-insights-dashboards.md) - bare klikke på pinkoden. (Du kan være nødvendigt at [opgradere din app er priser pakke](app-insights-pricing.md) til at slå denne funktion.) 

![Klik på PIN-koden](./media/app-insights-analytics-using/pin-01.png)

Det betyder, at når du placerer sammen at hjælpe dig med at overvåge ydeevnen eller brugen af dine webtjenester, kan du medtage helt komplekse analyser sammen med anden metrik. 

Hvis der er fire eller færre kolonner, kan du fastgøre en tabel til dashboard. Kun de øverste syv rækker vises.


#### <a name="dashboard-refresh"></a>Dashboard-opdatering

Fastgjort til dashboard diagrammet opdateres automatisk, ved at køre forespørgslen cirka hver halv time.

#### <a name="automatic-simplifications"></a>Automatisk forenklinger

I nogle tilfælde anvendes visse forenklinger til et diagram, når du fastgøre den til et dashboard.

Når du Fastgør et diagram, der viser en masse separate placeringer (typisk et liggende søjlediagram), mindre på forhånd placeringer grupperes automatisk i en enkelt placering "andre". For eksempel denne forespørgsel:

    requests | summarize count_search = count() by client_CountryOrRegion

ser sådan ud i Analytics:


![Diagram med lang hale](./media/app-insights-analytics-using/pin-07.png)

men når du Fastgør den til et dashboard, ser den således ud:


![Diagram med begrænset placeringer](./media/app-insights-analytics-using/pin-08.png)




## <a name="export-to-excel"></a>Eksporter til Excel

Når du har kørt en forespørgsel, kan du hente en .csv-fil. Klik på **Eksporter til Excel**.

## <a name="export-to-power-bi"></a>Eksportere til Power BI

Placer markøren i en forespørgsel, og vælg **Eksportér til Power BI**.

![Eksportere fra Analytics til Power BI](./media/app-insights-analytics-using/240.png)

Du kører forespørgslen i Power BI. Du kan indstille den til at opdatere en tidsplan.

Med Power BI, kan du oprette dashboards, der samler data fra en lang række kilder.


[Lær mere om Eksportér til Power BI](app-insights-export-power-bi.md)



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

