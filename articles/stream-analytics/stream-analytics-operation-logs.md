<properties 
    pageTitle="Fejlfinding ved hjælp af handlingen og registrerer tjenesten i Stream Analytics | Microsoft Azure" 
    description="Vejledning til brug Stream Analytics handlingen logfiler" 
    keywords="Tjenesten logfiler"
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

# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Fejlfinding af Stream Analytics job ved hjælp af og drift logfiler

Alle Azure tjenester leverer funktionsdygtige logføring af meddelelser til brugere til at registrere oplysninger, der er relateret til administration af handlinger. Disse oplysninger kan bruges til som vise jobstatus, jobbet status, i forbindelse med fejlfinding i Azure Stream analyser og fejlmeddelelser til at spore status for en sag i tidens løb fra start til behandling af til at få vist.

## <a name="find-operation-logs-in-the-azure-management-portal"></a>Finde handlingen logger på portalen Azure Management

Handlingen logfiler kan åbnes på to måder:  

- Dashboard for Stream Analytics-job  
- Management Services i portalen Azure klassisk  

## <a name="dashboard-of-the-stream-analytics-job"></a>Dashboard for Stream Analytics-job

Et link til tilsvarende logge af et Stream Analytics-job vises på fanen for det job Dashboard. Hvis du klikker på linket, indstiller filtrene på en måde, der vises for seneste logfiler for det pågældende bestemte job.

  ![Vælg Management Services logfiler](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Management Services

At gå manuelt til loggene handlingen for Stream analyser og andre tjenester i portalen Azure klassisk:

1.  Klik på **Management Services** i [Azure klassisk portal](https://manage.windowsazure.com).
2.  Du kan vælge **Stream Analytics** for **typen** af og navnet på sag for **Navnet på tjenesten**.  

  ![Vælg Stream Analytics](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Finde overvågningslogge i portalen Azure ##

Du kan finde funktionsdygtige logfiler til Stream Analytics tingene på portalen Azure, skal du klikke på **Gennemse** og derefter vælge **overvågningslogge**.

  ![Azure portal Vælg Stream Analytics](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Dette åbner en blade, der viser begivenheder fra de seneste 7 dage for alle ressourcer i dit abonnement.  Du kan filtrere for at se begivenheder i en Angiv type eller tidsramme ved at klikke på kommandoen **Filtrer** .

  ![Azure portal Vælg Stream Analytics](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Få oplysninger om logfiler

Du kan filtrere efter tidsinterval og Status for at få vist logfiler til dit arbejde.

Klik på knappen **Detaljer** i bunden af vinduet for at få vist flere detaljer om en markeret hændelse i portalen Azure Management. 

  ![Vælg detaljer](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

Klik på post i en logfil til at se de detaljerede hændelser indeni i portalen Azure.

  ![Azure portal Vælg detaljer](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

Derfra kan du åbne bladet **Detaljer** ved at klikke på begivenheden.

  ![Azure portal Vælg detaljer](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Fejlfinding af en mislykkede job

Klik på ikonet Søg, og indtast 'mislykkede' i Azure Management-portalen. Det giver et resultat af alle logfiler med fejl. 

  ![Fejlfinding af en mislykkede job](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

I portalen Azure, kan du filtrere efter niveau i meddelelsen for at få vist **kritisk** begivenheder.

  ![Azure portalen fejlfinding](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

Du kan vælge en af fejlene i og klikke på **Detaljer** for flere oplysninger om fejlen.  Nogle fejlmeddelelser indeholder også oplysninger om, hvordan du reducere problemet. 

  ![Oplysninger om handling](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

I tilfælde af, at du skal kontakte [Support](https://azure.microsoft.com/support/options/) eller overføre oplysninger til team via [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), skal du være opmærksom på handlingen, specifikt **Korrelations-ID'ET**. 

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
