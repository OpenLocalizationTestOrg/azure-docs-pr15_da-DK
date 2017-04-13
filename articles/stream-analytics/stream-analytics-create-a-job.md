<properties 
    pageTitle="Sådan oprettes et data analytics behandling job for Stream analyser | Microsoft Azure" 
    description="Oprette et data analytics behandling job til Stream Analytics | lærer sti målgruppe."
    keywords="analysebehandling data"
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

# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Sådan oprettes et data analytics behandling job for Stream analyser

På øverste niveau ressourcen i Azure Stream Analytics er et Stream Analytics-Job.  Den består af en eller flere input datakilder, en forespørgsel, udtrykke transformation af data og én eller flere output-mål, der skrives resultaterne i. Sammen gør disse det muligt at udføre data analyser behandling på at streaming data scenarier.

Start for at begynde at bruge Stream Analytics, ved at oprette et nyt Stream Analytics-job.  Bemærk, at denne handling ikke har ingen fakturering konsekvenserne indtil jobbet startes.

1.  Log på på online [Azure klassisk portal](http://manage.windowsazure.com) eller [Azure-portalen](https://portal.azure.com/).
2.  På portalen: **Klik på ny**, klik derefter på **Datatjenester** eller **Data Analytics** afhængigt af din portal og derefter klikke på **Azure Stream Analytics** eller **Stream analyser** og derefter **Hurtig oprettelse**.

    ![Guiden Analyser databehandling job](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  

    ![Oprette data analytics behandlingen af job](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  

3.  Angiv den ønskede konfiguration til Stream Analytics jobbet.
    - Angiv et navn til at identificere Stream Analytics jobbet i feltet **Jobnavn** . Når **Jobbet navn** er blevet godkendt, vises en grøn markering i feltet jobnavn. **Jobbet navn** kan kun indeholde alfanumeriske tegn og '-' tegn, og skal være mellem 3 og 63 tegn.
    - Brug **område** i Azure portal eller **placering** i portalen Azure til at angive den geografiske placering, hvor du vil køre jobbet.
    - Hvis ved hjælp af portalen Azure, vælge eller oprette et lager-konto, der skal bruges som **Internationale overvågning lagerplads konto**. Denne lagerplads konto bruges til at gemme overvågningsdata for alle Stream Analytics-job kører i dette område.
    - Hvis ved hjælp af portalen Azure, kan du angive en ny eller eksisterende **Ressourcegruppe** for at holde relaterede ressourcer for dit program.

4.  Klik på **Opret Stream Analytics Job**, når de nye Stream Analytics job indstillinger er konfigureret. Det kan tage et par minutter til Stream Analytics opgaven skal oprettes. Hvis du vil kontrollere status, kan du overvåge statussen i hubben beskeder.

    ![Data analytics behandling jobbet notfications hub](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  

    ![Azure portalen Data analytics behandlingen af job oprette Job](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  

5.  Den nye sag vises med statussen **oprettet**. Bemærk, at knappen **Start** er deaktiveret. Du skal konfigurere tingene input, forespørgsel og output, før du kan starte jobbet.

    ![Data analysebehandling sag sag Status](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  

    ![Azure portalen Data analytics behandling job jobstatus](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
