<properties
    pageTitle="Registrere Azure tjenestetilstand ved hjælp af Azure overvåge aktivitetslogfiler | Microsoft Azure"
    description="Find ud af, når Azure fik ydeevne er forringet eller tjeneste afbrydelser. "
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Registrer Azure tjenestetilstand ved hjælp af Azure overvåge aktivitet i logfiler

Azure publicizes hver gang der er en tjenesten blev afbrudt eller ydeevne er forringet. Du kan gennemse disse hændelser i Azure-portalen, og du kan også bruge [REST-API](https://msdn.microsoft.com/library/azure/dn931927.aspx) eller [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) have programmeringsmæssig adgang det samlede sæt af begivenheder.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>Gennemse loggene tjenestetilstand for dit abonnement

1. Log på [Azure-portalen](https://portal.azure.com/).

2. På **Startside** skal du se et felt, kaldet **tjenestetilstand**. Klik på den.

    ![Startside](./media/insights-service-health/Insights_Home.png)

3. Du kan se en liste over alle områder i Azure. Klik på en hvilken som helst område for at få vist den aktivitetslog forespørgsel, der viser tjenestehændelser, der har påvirket et af dine abonnementer i de seneste 24 timer.

    ![Aktivitet Log abonnement tjenestetilstand](./media/insights-service-health/AzureActivityLogServiceHealth3.png)

4. Du kan se detaljerne for en enkelt hændelse ved at klikke på denne begivenhed i tabellen.

5. Ændre **Timespan** for at få vist en længere tidsramme.

## <a name="next-steps"></a>Næste trin

* [Overvåge tilgængelighed og tilgængeligheden af enhver webside](../application-insights/app-insights-monitor-web-app-availability.md) med programmet viden, så du kan finde ud af, om din side er nede.
