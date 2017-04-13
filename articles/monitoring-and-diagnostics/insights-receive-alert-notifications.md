<properties
    pageTitle="Modtage påmindelser for Azure services | Microsoft Azure"
    description="Få besked, når regler for påmindelser betingelser er opfyldt."
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
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="receive-alert-notifications"></a>Modtage påmindelser

Du kan modtage en besked, der er baseret på overvågning måleredskaber for eller begivenheder i dine Azure tjenester.

En besked om regel for en metrikværdi, når værdien af en bestemt metrik krydser en grænse, der er tildelt, reglen aktiveres og kan sende en meddelelse. En regel kan sende beskeder til en besked om hændelser, på *hver* begivenhed eller, kun, når et bestemt antal begivenheder sker.

Når du opretter en besked om, kan du vælge indstillinger til at sende besked via mail til administratoren af tjenesten og medadministratorer eller til en anden administrator, som du kan angive. En mail sendes, når reglen bliver aktiv, og når en besked om betingelse er løst.

Du kan bruge [REST-API](https://msdn.microsoft.com/library/azure/dn931945.aspx) eller [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) til at konfigurere og få at vide om regler for påmindelser fra et program.

## <a name="create-an-alert-rule"></a>Oprette en regel for beskeder

1. Klik på **Gennemse**, og klik derefter en ressource, du er interesseret i overvågning på [portalen](https://portal.azure.com/).

2. Klik på feltet **regler for påmindelser** i objektiv til **Handlinger** .

3. Klik på kommandoen **Tilføj påmindelse** .

    ![Tilføje besked](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. Du kan navngive beskeder om reglen, og vælg en beskrivelse, der vises i mailen.

5. Når du vælger **målepunkter** skal du vælge en betingelse og en grænseværdi værdi for metrikværdien. Dette er tidsrum, der bruger Azure på skærmen og afbildning beskeder om aktivitet.

    ![Betingelse og en grænseværdi](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. Du kan også vælge **begivenheder**og får en meddelelse, når en bestemt begivenhed sker.

    ![Begivenheder](./media/insights-receive-alert-notifications/Insights_Events.png)

7. Til sidst skal kan du vælge at sende mailmeddelelse til ansvarlig administratorer.

Når du klikker på **Gem**, inden for et par minutter der besked når den metrikværdi, du vælger overstiger grænsen.

## <a name="managing-your-alert-rules"></a>Administration af beskeder om regler

Når du har oprettet en besked om, kan du få vist et eksempel på beskeden grænseværdi sammenlignet metrisk fra den forrige dag.

![Begivenheder](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


Du kan naturligvis redigere denne reglen, og **deaktivere** eller **aktivere** det Hvis du vil midlertidigt ikke længere vil modtage beskeder om den.

## <a name="next-steps"></a>Næste trin

* [Konfigurer webhooks på dine beskeder](insights-webhooks-alerts.md) til at distribuere meddelelser til forskellige kanaler
* [Overvåge tjenesten målepunkter](insights-how-to-customize-monitoring.md) at sikre, at din tjeneste er tilgængelig og svarede.
* [Aktivere overvågning og diagnosticering](insights-how-to-use-diagnostics.md) til at indsamle detaljerede hyppigt målepunkter på din tjeneste.
* [Overvåge tilgængelighed og tilgængeligheden af enhver webside](../application-insights/app-insights-monitor-web-app-availability.md) med programmet viden, så du kan finde ud af, om din side er nede.
* [Overvåg programmet ydeevne](../application-insights/app-insights-azure-web-apps.md) , hvis du ønsker at forstå præcis hvordan din kode klarer sig i skyen.
* [Se begivenheder og overvågningslogge for](insights-debugging-with-events.md) at lære alt det, der er sket i din tjeneste.
* [Registrer tjenestetilstand](insights-service-health.md) til at finde ud af, når Azure fik ydeevne er forringet eller tjeneste afbrydelser.
