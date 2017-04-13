<properties
    pageTitle="Konfigurere beskeder for forespørgsler i Stream Analytics | Microsoft Azure"
    description="Forstå Stream Analytics advarer"
    keywords="konfigurere beskeder"
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


# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Konfigurere beskeder for Azure Stream Analytics-job

## <a name="introduction-monitor-page"></a>Introduktion: Siden skærm

Du kan konfigurere beskeder til at udløse en besked, når en metrikværdi når en betingelse, du angiver.

For eksempel "Hvis Output hændelser i de sidste 15 minutter er < 100, sende e-mail-meddelelser til e-mail-id: xyz@company.com”.

Regler kan konfigureres på målepunkter via portalen eller kan være konfigureret [fra et program](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) over logfilerne for handlingerne data.

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>Konfigurere beskeder via portalen Azure klassisk

Der er to måder at konfigurere beskeder i portalen Azure klassisk:  

1.  Fanen **skærm** i Stream Analytics tingene  
2.  Handlinger Log in Management services  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>Konfigurere besked via fanen skærm på sag i portalen

1.  Vælg metrikken under fanen skærm, og klik på knappen **Tilføj regel** i bunden af dashboardet og konfigurere regler.  

    ![Dashboard](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  

2.  Definer navn og beskrivelse af den vigtige besked  

    ![Opret regel](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  

3.  Angiv tærsklerne, beskeder om evaluering vindue og handlinger for den vigtige besked  

    ![Definere betingelser](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>Konfigurere beskeder via loggene handlinger

1.  Gå til fanen **beskeder** i Management Services i [Azure klassisk Portal](https://manage.windowsazure.com).  
2.  Klik på **Tilføj regel**  

    ![Kriterier](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  

3.  Definer navn og beskrivelse af beskeden. Vælg 'Stream Analytics' som tjenestetype, og Jobnavnet som navnet på tjenesten.  

    ![Definere besked](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>Konfigurere beskeder i portalen Azure ##

Find den Stream Analytics-sag, du er interesseret i advarer på portalen Azure, og klik på sektionen **overvågning** .  Klik på kommandoen **Tilføj påmindelse** i bladet **metrisk** , der åbnes.

  ![Opsætning af Azure portal](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

Du kan navngive beskeder om reglen, og vælg en beskrivelse, der vises i mailen.

Når du vælger målepunkter skal du vælge en betingelse og en grænseværdi værdien for metrikværdien.

  ![Azure portalen Vælg metrisk](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

Du kan finde flere oplysninger om konfiguration af beskeder på portalen Azure [Modtag påmindelser](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
