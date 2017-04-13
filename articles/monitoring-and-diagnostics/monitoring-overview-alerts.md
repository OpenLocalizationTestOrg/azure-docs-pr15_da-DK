<properties
    pageTitle="Oversigt over beskeder i Microsoft Azure | Microsoft Azure"
    description="Beskeder gør det muligt at overvåge Azure ressource målepunkter, hændelser eller logfiler og få besked, når en betingelse, du angiver er opfyldt."
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
    ms.date="09/24/2016"
    ms.author="robb"/>

# <a name="overview-of-alerts-in-microsoft-azure"></a>Oversigt over beskeder i Microsoft Azure


I denne artikel beskriver, hvilke beskeder er, deres fordele, og hvordan du kommer i gang med at bruge dem.  

## <a name="what-are-alerts"></a>Hvad er beskeder?
Beskeder er en metode til at overvåge Azure ressource målepunkter, begivenheder eller logfiler og derefter blive besked, når en betingelse, du angiver er opfyldt.

Du kan modtage beskeder, der er baseret på:

- **Metriske værdier**: udløser denne besked, når værdien af en bestemt metrik krydser en grænse, som du tildeler i den ønskede retning. Det vil sige det udløser begge når betingelsen opfyldes først og derefter bagefter når betingelse, der ikke længere er opfyldt.
- **Aktivitet loghændelser**: denne besked kan udløse på hver begivenhed, eller kun, når et bestemt antal hændelser indtræffer.

## <a name="alerts-in-different-azure-services"></a>Beskeder i forskellige Azure-tjenester

Beskeder er tilgængelige på tværs af forskellige tjenesterne, herunder:

- **Programmet indsigt**: Aktiverer web test og metrisk beskeder. Se [konfigurere beskeder i programmet indsigt](../application-insights/app-insights-alerts.md) og [overvåge tilgængelighed og tilgængeligheden af et websted](../application-insights/app-insights-monitor-web-app-availability.md).
- **Log Analytics (handlinger Management Suite)**: Aktiverer routing af diagnosticeringslogfiler til Log analyser. Handlinger Management Suite kan metrisk, log og andre beskeder om typer. Se [beskeder i Log Analytics](../log-analytics/log-analytics-alerts.md)kan finde flere oplysninger.  
- **Azure skærm**: gør det muligt for beskeder, der er baseret på både metriske værdier og aktivitet log begivenheder. Azure skærm omfatter [Azure skærm REST-API](https://msdn.microsoft.com/library/dn931943.aspx).  Du kan finde flere oplysninger [ved hjælp af portalen Azure, PowerShell, eller kommandolinjen til at oprette beskeder](insights-alerts-portal.md).

## <a name="alert-actions"></a>Handlinger for beskeder
Du kan konfigurere en besked for at gøre følgende:

- Send besked via mail til tjenesteadministratoren til medadministratorer eller til yderligere mailadresser, som du angiver.
- Ringe til en webhook, som gør det muligt at åbne yderligere automatiske handlinger.

 ![Beskeder forklaring](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)


## <a name="next-steps"></a>Næste trin

Få oplysninger om regler for påmindelser og konfiguration af dem ved hjælp af:

- [Azure-portalen](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [Kommandolinjen (CLI)](insights-alerts-command-line-interface.md)
- [Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
