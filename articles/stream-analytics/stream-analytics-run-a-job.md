<properties 
    pageTitle="Sådan startes streaming sager i Stream Analytics | Microsoft Azure" 
    description="Hvordan kører en streaming sag i Azure Stream Analytics | lærer sti målgruppe."
    keywords="streaming job"
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

# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>Sådan køres en streaming sag i Azure Stream Analytics

Når et job input, forespørgsel og output har alle angivet du kan starte Stream Analytics-job.

Sådan starter du tingene:

1.  Klik på **Start** i bunden af siden i portalen Azure klassisk dashboard job.

    ![Starte job knappen](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  

    Klik på **Start** øverst på siden sag i Azure-portalen.

    ![Azure portalen startjob knappen](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  

2.  Angiv en **Start Output** værdi at fastslå, hvornår dette job begynde med at producere output. Standardindstillingen for job, der tidligere ikke er blevet startet er **Job starttidspunkt**, hvilket betyder, at jobbet straks starte behandling af data. Du kan også angive et **brugerdefineret** tidspunkt i fortiden (for forbrug historiske data) eller senere for (at forsinke behandling indtil et tidspunkt i fremtiden). I de tilfælde når en sag er blevet tidligere startes og stoppes, er indstillingen **Stoppet sidst** tilgængelig for at fortsætte jobbet fra output sidst og undgå at miste data.  

    ![Start streaming job tid](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  

    ![Azure portalen Start streaming job tid](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  

3.  Bekræfte dit valg. Jobstatus ændres til *Start* og flyttes kort for at *køre* , når jobbet er startet. Du kan overvåge forløbet af handlingen **Start** i **Meddelelse Hub**:

    ![streaming job fremdrift](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  

    ![Azure-portalen streaming job fremdrift](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
