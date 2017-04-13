<properties 
    pageTitle="Sådan skrives forespørgsler i Stream Analytics | Microsoft Azure" 
    description="Skriv forespørgsler i Stream analyser og forespørgselsdata | lærer sti målgruppe."
    keywords="Sådan skrives forespørgsler, forespørgselsdata, skrive en forespørgsel, skrive forespørgsler"
    documentationCenter=""
    services="stream-analytics"
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

# <a name="how-to-write-queries-in-stream-analytics"></a>Sådan skrives forespørgsler i Stream Analytics

Oprettelsen af forespørgsler til stream behandling logik i Azure Stream Analytics er implementeret som en "stående forespørgsel", der er defineret, før et job starter og udført på data, som det har jobbet. Datatransformation udtrykkes i et forespørgselssprog, synes godt om SQL-, som er stort set et undersæt af T-SQL med nogle tilføjede sprog udvidelser som [Windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) bruges til at udtrykke tidsmæssig semantik.

## <a name="writing-queries"></a>Skriver forespørgsler: ##

1. Klik på **forespørgsel**i Stream Analytics tingene på portalen Azure Management.

    ![Vælg forespørgsel](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  

    Klik på **forespørgsel**i portalen Azure.

    ![Vælg forhåndsvisning af forespørgsel](./media/stream-analytics-write-queries/query-preview-portal.png)  

2.  Nyt job har en forespørgselsskabelon til at komme i gang. Forespørgselsskabelon udfører en "pass-through-" forespørgsel, der projekter alle felter fra input begivenheder til output.  

    - Hvis du har defineret mindst én input og output til dit arbejde, kan du erstatte pladsholderen "[YourOutputAlias]" og "[YourInputAlias]" felter med aliasser på input og output, du vil bruge først. Desuden kan du stadig redigere og teste din forespørgsel i portalen Azure klassisk uden at definere input og output på jobbet.
    - Hvis du vil udføre mere behandling end en simpel pass-through, kan du redigere definitionen af forespørgslen. For at komme i gang med redigering af forespørgsel skal du se nærmere på nogle almindelige forespørgsel mønstre er hentes [her](stream-analytics-stream-analytics-query-patterns.md).  
  
    ![Forespørgselsdata vindue](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>Hvis du vil validere forespørgselsdata virker: ##

Du kan kontrollere, at forespørgslen fungerer som forventet ved at køre det i browseren over en eller flere lokale JSON filer, der indeholder testdata. Dette ikke kan starte jobbet eller har de fakturering konsekvenser.

> [AZURE.NOTE] I browseren forespørgselstest er i øjeblikket ikke understøttet i portalen Azure.  

1.  Sørg for, at der er ingen fejl i forespørgslen (ellers knappen Test deaktiveres) og derefter klikke på knappen Test.  

    ![Forespørgselsdata Test](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  

2.  Du bliver bedt om at angive filer for hver af de materialer, der refereres til i forespørgslen. I dette eksempel, der er tilbage forespørgslen skabelon som-er, så dialogboksen bede om input med navnet "yourinputalias".  

    ![Test dataforespørgsel](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  

3.  Gå til en testfil. Mange eksempelfiler er tilgængelige under [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data) og du kan også hente eksempeldata fra dine egne data stream input via funktionen Sample Data under fanen oplysninger.  

    ![Forespørgsel Input](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  

4.  Når du lukker dialogboksen, forespørgslen køres over testdataene og du får vist resultaterne i bunden af siden forespørgsel.  

    ![Oversigt over forespørgsel](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
