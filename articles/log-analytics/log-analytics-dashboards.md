<properties
    pageTitle="Oprette et brugerdefineret dashboard i Log Analytics | Microsoft Azure"
    description="Denne vejledning hjælper dig med at forstå, hvordan Log Analytics Dashboards kan visualisere alle dine gemte log søgninger, hvorved du får en enkelt lens til at få vist dit miljø."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="create-a-custom-dashboard-in-log-analytics"></a>Oprette et brugerdefineret dashboard i Log Analytics

Denne vejledning hjælper dig med at forstå, hvordan Log Analytics dashboards kan visualisere alle dine gemte log søgninger med, hvorved du får en enkelt lens til at få vist dit miljø.

![Eksempeldashboard](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

De brugerdefinerede dashboards, som du opretter i portalen OMS findes også i OMS Mobile-App. Se de følgende sider kan finde flere oplysninger om apps.

- [OMS-mobilappen fra Microsoft Store](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
- [OMS-mobilappen fra Apple iTunes](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![Mobile dashboard](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Hvordan opretter jeg min dashboard?

For at starte skal du gå til siden OMS oversigt. Du får vist feltet **Mit Dashboard** i venstre side. Klik på den for at analysere ned i dit dashboard.

![Oversigt](./media/log-analytics-dashboards/oms-dashboards-overview.png)


## <a name="adding-a-tile"></a>Tilføje et felt

I dashboards, er felter udstyret med dine gemt logfil søgninger. OMS leveres med mange foretaget allerede gemt logfil søgninger, så du kan begynde at det samme. Brug følgende trin, der skitserer, hvordan du kan begynde.

I mit Dashboard visningen, skal du blot klikke på **Tilpas** til at angive Tilpas tilstand.

![Grafisk](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 Panelet, der åbnes i højre side af siden viser alle dit arbejdsområde gemt logfil søgninger. Hold markøren over en gemt søgning for at visualisere en gemt logfil søgning som et felt, og klik derefter på symbolet for **plus** .

![Tilføje felter 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

Når du klikker på symbolet for **plus** , vises et nyt felt i visningen Mine Dashboard.

![Tilføje felter 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)


## <a name="edit-a-tile"></a>Redigere et felt

I mit Dashboard visningen, skal du blot klikke på **Tilpas** til at angive Tilpas tilstand. Klik på feltet, du vil redigere. Højre panel ændringer til at redigere, og der vises en markering af indstillinger:

![Redigere felt](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![Redigere felt](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Feltet visualiseringer#
Der findes tre typer felt visualiseringer at vælge mellem:

|diagramtype|Hvad gør den|
|---|---|
|![Liggende søjlediagram](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png)|Viser en tidslinje for søgningen gemt logfil resultater som et søjlediagram eller en liste over resultater efter et felt, afhængigt af, hvis søgningen log aggregerer resultater efter et felt eller ej.
|![metrisk](./media/log-analytics-dashboards/oms-dashboards-metric.png)|Viser dine samlede log Søg resultatet besøg som et tal i et felt. Metriske felter gør det muligt at angive en grænse, der fremhæver feltet når grænsen nås.|
|![linje](./media/log-analytics-dashboards/oms-dashboards-line.png)|Viser en tidslinje for gemt logfil Søg resultatet søgeresultater med værdier, som et kurvediagram.|

### <a name="threshold"></a>Grænseværdi
Du kan oprette en grænse på et felt ved hjælp af metriske Visualiseringen. Vælg på at oprette en grænseværdi i feltet. Vælg, om du vil fremhæve feltet, når værdien er over eller under det valgte grænseværdi, og angiv derefter værdien grænseværdi for.

## <a name="organizing-the-dashboard"></a>Organisere dashboard
Hvis du vil organisere dit dashboard, gå til visningen Mine Dashboard, og klik på Tilpas **Tilpas** til at angive tilstand. Klik og træk det felt, du vil flytte, og flytte den til det ønskede dit felt for at være.

![Organisere dit Dashboard](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Fjerne et felt
Hvis du vil fjerne et felt, gå til visningen Mine Dashboard, og klik på Tilpas **Tilpas** til at angive tilstand. Vælg det felt, du vil fjerne, og vælg **Fjern flisen**i højre panel.

![Fjerne et felt](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Næste trin

- Opret [beskeder](log-analytics-alerts.md) i Log Analytics til at generere beskeder og afhjælpning problemer.
