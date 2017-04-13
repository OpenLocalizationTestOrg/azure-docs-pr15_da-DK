<properties 
    pageTitle="Forstå Stream Analytics Job overvågning | Microsoft Azure" 
    description="Forstå Stream Analytics Job overvågning" 
    keywords="forespørgsel skærm"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Forstå Stream Analytics job overvågning og hvordan du kan overvåge forespørgsler

## <a name="introduction-the-monitor-page"></a>Introduktion: Siden skærm

Portalen Azure Management og Azure Portal overflade KPI'er målepunkter, der kan bruges til at overvåge og foretage fejlfinding af din forespørgsel og job ydeevnen. 

Klik på fanen **skærm** i et igangværende Stream Analytics job at se denne statistik i portalen Azure Management. Der er en forsinkelse af højst 1 minut i ydeevne målene vises på siden skærm.  

  ![Overvågning job Dashboard](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

Gå til Stream Analytics jobbet du er interesseret i at se omfanget af og få vist sektionen **overvågning** i Azure-portalen.  

  ![Portal overvågning af Azure forsendelse job Dashboard](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)  

Første gang der oprettes et Stream Analytics-job i et område, skal du konfigurere diagnosticering for det pågældende område. Klik et vilkårligt sted i sektionen **overvågning** og bladet **diagnosticering** , for at gøre dette, vises. Her kan du aktivere diagnosticering og angive en lagerplads konto til overvågning af data.  

  ![Azure-portalen konfigurere-forespørgsel diagnosticering](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Målepunkter, der er tilgængelige for Stream analyser


| Metrisk | Definition |
|--------|-------------|
| SØ % udnyttelse | Anvendelsen af den Streaming enhed tildelt et job fra fanen skala på sag. Skal denne indikator når 80%, eller herover, der er høj sandsynlighed, behandling af hændelse kan udskydes eller standses gør fremskridt. |
| Indtast begivenheder | Mængde data, der modtages af kørslen Stream Analytics i antallet af begivenheder. Dette kan bruges til at validere, som hændelser, der sendes til inputkilden. |
| Output begivenheder | Mængde af data, der sendes af Stream Analytics jobbet til output destination, i antallet af begivenheder. |
| Ud over ordre-begivenheder | Antallet af hændelser, der modtages af rækkefølge, der enten sænket eller givet et justerede tidsstempel, baseret på politikken begivenhed rækkefølge. Dette kan blive påvirket af konfigurationen af indstillingen ud for rækkefølge Tolerance vindue. |
| Datakonverteringsfejl | Antallet af datakonverteringsfejl påløbet ved et Stream Analytics-job. |
| Kørselsfejl | Antal fejl, der sker under udførelse af et Stream Analytics-job. |
| Sen Input begivenheder | Antallet af hændelser, der er modtaget sent fra den kilde, som er enten sluppet eller deres tidsstempel er blevet ændret, baseret på begivenhed ordning politik konfigurationen af indstillingen sen modtagelse af Tolerance vindue. |

## <a name="customizing-monitoring-in-the-azure-management-portal"></a>Tilpasse overvågnings i portalen Azure Management ##

Op til 6 målepunkter kan vises i et diagram.

For at skifte mellem at få vist relative værdier (sidste værdi kun for hver metrisk) og absolutte værdier (Y-aksen vises), skal du vælge relativ eller absolut øverst i diagrammet.

  ![Forespørgsel skærm Relative absolut](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)  

Målepunkter kan ses i skærm diagrammet i sammenlægninger til 1 time, 12 timer, 24 timer eller 7 dage.

Hvis du vil ændre tidsintervallet Vælg målepunkter diagram vises 1 hour, 24 timer eller 7 dage øverst i diagrammet.

  ![Forespørgsel skærm tidsskalaen](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)  

Du kan oprette regler, der kan give dig besked via mail i tilfælde af jobbet krydser en defineret grænseværdi. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Tilpasse overvågning på portalen Azure ##

Du kan justere diagramtypen, målepunkter, der vises, og tilpasse tidspunktet for område i indstillingerne for redigere diagrammet. Se Få mere at vide, [hvordan du tilpasse overvågning](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Azure portalen forespørgsel skærm tidsskalaen](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  

## <a name="job-status"></a>Jobstatus

Status for Stream Analytics job kan ses på klassisk Azure-portalen, hvor du kan se en liste over job. Du kan se en liste over job ved at klikke på ikonet Stream Analytics i portalen Azure klassisk.

| Status | Definition |
|--------|------------|
| Oprettet | Et job er blevet oprettet, men er ikke startet. |
| Starte | En bruger, der er klikket på Start jobbet, og jobbet starter |
| Kører | Jobbet er allokeret, behandler input eller venter på at behandle input. Hvis jobbet viser tilstanden kører uden at frembringe output, er det sandsynligvis, tidsramme databehandling er stort eller logik forespørgsel er komplicerede. En anden mulig grund kan være, at der i øjeblikket ikke alle data, der sendes til jobbet. |
| Stoppe | En bruger, der er klikket på Stop jobbet, og jobbet stopper. |
| Ikke længere | Jobbet er stoppet. |
| Nedsat | Denne tilstand Angiver, at et Stream Analytics-job opfatter midlertidige fejl (for ex. Input/outputområder fejl, behandling fejl, Konverteringsfejl osv.). Jobbet kører stadig, men der er mange fejl, der genereres. Dette job skal kundens opmærksomhed, og kan ses af kunden loggene handlinger for fejlene. |
| Mislykkedes | Dette angiver, at jobbet mislykkes på grund af fejl, og behandlingen er stoppet. Kunden har brug at undersøge loggene handlinger for at foretage fejlfinding af fejlene. |
| Slette | Dette angiver, at jobbet er slettet. |

## <a name="diagnosis"></a>Diagnosticering

Dashboardet job indeholder oplysninger om hvor du har brug at søge efter diagnosticering, det vil sige input, output og/eller handlingerne log på portalen Azure Management. Du kan klikke på linket for at gå den korrekte placering at kigge på diagnosticering.

  ![Forespørgsel skærm fejl](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)  

At klikke på input- eller ressourcen indeholder detaljerede diagnostiske oplysninger. Dette er opdateret med de seneste diagnosticering oplysninger, mens jobbet kører.

  ![Forespørgsel diagnosticering](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)  

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
