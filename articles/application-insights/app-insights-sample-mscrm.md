<properties 
    pageTitle="Gennemgang: Overvåge Microsoft Dynamics CRM med programmet indsigt" 
    description="Få telemetri fra Microsoft Dynamics CRM Online ved hjælp af programmet indsigt. Gennemgang af konfigurationen, hentning af data, visualisering og Eksportér." 
    services="application-insights" 
    documentationCenter=""
    authors="mazharmicrosoft" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="awills"/>
 
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Gennemgang: Aktivering Telemetri til Microsoft Dynamics CRM Online ved hjælp af programmet indsigt

I denne artikel beskrives, hvordan at få telemetridata fra [Microsoft Dynamics CRM Online](https://www.dynamics.com/) ved hjælp af [Visual Studio programmet indsigt](https://azure.microsoft.com/services/application-insights/). Vi gennemgår fuldført processen med at tilføje programmet indsigt script til dit program indsamle data og datavisualisering.

>[AZURE.NOTE] [Gennemse løsningen eksempel](https://dynamicsandappinsights.codeplex.com/).

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Føje programmet indsigt til nye eller eksisterende CRM Online forekomst 

Hvis du vil overvåge dit program, kan du tilføje et program indsigt SDK i dit program. SDK sender telemetri til [programmet indsigt portal](https://portal.azure.com), hvor du kan bruge vores effektive analyser og værktøjer til diagnosticering eller eksportere dataene til-lager.

### <a name="create-an-application-insights-resource-in-azure"></a>Oprette en ressource programmet indsigt i Azure

1. Få [en konto i Microsoft Azure](http://azure.com/pricing). 
2. Log på [Azure-portalen](https://portal.azure.com) , og Tilføj en ny programmet indsigt ressource. Dette er, hvor dine data, behandles og vises.

    ![Klik på +, Developer Services, programmet indsigt.](./media/app-insights-sample-mscrm/01.png)

    Vælg ASP.NET som programtypen.

3. Åbn fanen Hurtig Start, og Åbn scriptet kode.

    ![](./media/app-insights-sample-mscrm/03.png)

**Hold siden kode åben** , mens du gør næste trin i en anden browser-vindue. Du skal have koden snart. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Oprette en JavaScript webressource i Microsoft Dynamics CRM

1. Åbn CRM Online forekomst og log på med administratorrettigheder.
2. Åbn Microsoft Dynamics CRM indstillinger og derefter tilpasninger, tilpasse systemet

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. Oprette en JavaScript-ressource.

    ![](./media/app-insights-sample-mscrm/07.png)

    Give den et navn, Vælg **Script (JScript)** , og Åbn tekstredigeringsprogrammet.

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. Kopiere koden til fra programmet indsigt. Sørg for at ignorere script-koder under kopieringen. Se under skærmbillede:

    ![](./media/app-insights-sample-mscrm/09.png)

    Koden indeholder tasten instrumentation, der identificerer dine programmet indsigt ressource.

5. Gem og Publicer.

    ![](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>Instrument formularer

1. Åbn formularen firma i Microsoft CRM Online

    ![](./media/app-insights-sample-mscrm/11.png)

2. Åbn formularen egenskaber

    ![](./media/app-insights-sample-mscrm/12.png)

3. Føje JavaScript webressourcen, du har oprettet

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. Gem og Publicer tilpasningerne formular.


## <a name="metrics-captured"></a>Målepunkter hentes

Du har nu konfigureret telemetri capture til formularen. Når den bruges, sendes data til dit program indsigt ressource.

Her er nogle eksempler på de data, du får vist.

#### <a name="application-health"></a>Programmet tilstand

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Browser undtagelser:

![](./media/app-insights-sample-mscrm/17.png)

Klik på diagrammet for at få flere detaljer:

![](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Brugen

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Browsere

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Geolokalitet

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Indvendig sideanmodning for visning

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Eksempel på kode

[Gennemse eksempelkoden](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI

Du kan gøre endnu bedre analyse, hvis du [eksportere dataene til Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Eksempel Microsoft Dynamics CRM-løsning

[Her er implementeret i Microsoft Dynamics CRM eksempel løsningen] (https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Lær mere

* [Hvad er programmet indsigt?](app-insights-overview.md)
* [Programmet indsigt til websider](app-insights-javascript.md)
* [Flere eksempler og gennemgange](app-insights-code-samples.md)

 
