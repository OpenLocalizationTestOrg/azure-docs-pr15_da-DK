<properties
    pageTitle="Visualisere og foretage fejlfinding af Stream Analytics job | Microsoft Azure"
    description="Lær at visualisere en Stream Analytics job pipeline til selvbetjening fejlfinding ved hjælp af funktionen diagnosticering diagram."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Visualisere og foretage fejlfinding af Stream Analytics-job

I Stream Analytics som med andre skybaseret teknologier fejlfinding i forbindelse med nogle gange skal bruges til at undersøge hvorfor et job ikke medfører den forventede afgang (eller noget output til forbedrede). Med dette huske giver Stream Analytics mulighed for at visualisere en streaming sag. Dette er også praktiske som et modellering værktøj og har side fordelen for disse der kræver dokumentation for deres arbejde.

Input er synlige samt den forespørgsel, der udføres og derefter alle de output konfigureret i panelet visualisering. Problemer med forbindelse eller konfiguration kan blive kan gøres mere synlige, og det kan også være praktisk at se en visuel repræsentation af konfigurationen.

## <a name="using-the-diagnosis-diagram-tool"></a>Ved hjælp af værktøjet diagnosticering diagram

For at få adgang til denne visualizer, skal du blot klikke på knappen "Diagnosticering diagram" i bladet "Indstillinger" af det på sag, Stream analyser.

![stream-Analytics-troubleshoot-Visualization-Diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Alle input og output er farvekodet for at angive aktuelt tilstand af den pågældende komponent, som vist nedenfor.

![stream-Analytics-troubleshoot-Visualization-input-Map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Når brugeren ønsker at se på mellemliggende forespørgselstrin at forstå data flow mønstre i et job, giver værktøjet visualisering en visning af opdeling af forespørgslen i dens komponent trin og flow sekvens. At klikke på forespørgselstrin viser den tilsvarende sektion i en forespørgsel, der redigerer ruden som vist. 

![stream-Analytics-troubleshoot-Visualization-Intermediate-Steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)




## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
