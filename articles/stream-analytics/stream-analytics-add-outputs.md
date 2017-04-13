<properties 
    pageTitle="Sådan konfigureres data skriver for Stream Analytics sager | Microsoft Azure" 
    description="Konfigurere output til Stream Analytics job | lærer sti målgruppe."
    keywords="data output, flytning af data"
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

# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Sådan konfigureres data skriver for Stream Analytics sager

Azure Stream Analytics-job kan være forbundet til en eller flere data output, som definerer en forbindelse til en eksisterende data sink. Efterhånden som Stream Analytics tingene behandler og transformerer indgående data, skrives en strøm af data output begivenheder til din sag output.

Stream Analytics-data, der lagrer kan bruges til at datakilde realtid dashboards eller påmindelser, udløser data bevægelse arbejdsprocesser, eller blot arkivere data til batchbehandling senere. Stream Analytics har førsteklasses integration med flere Azure services, som er beskrevet i detaljer her.

Sådan føjes en output til Stream Analytics tingene:

1. I portalen Azure klassisk, skal du klikke på **output** , og klik derefter på **Tilføj Output** i Stream Analytics tingene.

    ![Tilføje output](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  

    Klik på feltet **output** i Stream Analytics tingene på portalen Azure.

    ![Azure Porta tilføje output](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. Angiv typen output:

    ![Vælg datatypen for bevægelse](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  

    ![Azure portal vælge bevægelse datatype](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. Angiv et navn for denne output i boksen **Output Alias** . Dette navn kan bruges i dit arbejde forespørgsel senere til at referere til output.  
    
    Udfyld resten af de nødvendige forbindelsesegenskaber at oprette forbindelse til din output.  Disse felter varierer efter outputtype og er defineret i detaljer her.  

    ![Tilføje data output egenskaber](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)  

4. Afhængigt af outputtypen skal du angive, hvordan dataene er serialiseret eller formateret. Bestemte serialisering indstillingerne for hver outputtype er beskrevet her.

    Udfyld resten af de nødvendige forbindelsesegenskaber til at oprette forbindelse til datakilden. Disse felter varierer afhængigt af input og kilde indholdstype og er defineret i detaljer [her](stream-analytics-create-a-job.md).  

    ![Tilføje data output til begivenhed hub](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)  

    ![Azure portalen data output til begivenhed hub](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)  

> [Azure.Note] En hvilken som helst output element, der er føjet til jobbet, skal være før jobbet startes og begivenheder starte der flyder. Eksempelvis hvis du bruger Blob-lager som output, kan jobbet ikke oprette en lagerplads konto automatisk. Det skal være oprettet af brugeren, før ASA jobbet startes.

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
