<properties
    pageTitle="Bruge Azure portal til at oprette påmindelser for Azure services | Microsoft Azure"
    description="Bruge Azure-portalen til at oprette Azure beskeder, der kan udløse beskeder eller automatisering, når de angivne betingelser er opfyldt."
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
    ms.date="09/23/2016"
    ms.author="robb"/>

# <a name="use-azure-portal-to-create-alerts-for-azure-services"></a>Bruge Azure portal til at oprette påmindelser for Azure tjenester

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Oversigt

I denne artikel beskrives, hvordan du konfigurerer Azure beskeder ved hjælp af portalen Azure.   

Du kan modtage en besked, der er baseret på overvågning måleredskaber for eller begivenheder i dine Azure tjenester.

- **Metriske værdier** - beskeder om udløsere, når værdien af en bestemt metrik krydser en grænse, du tildeler i den ønskede retning. Det vil sige det udløser begge når betingelsen opfyldes først og derefter bagefter når betingelse, der ikke længere er opfyldt.    
- **Aktivitet log begivenheder** - kan udløse en besked på *hver* begivenhed eller, kun, når et bestemt antal hændelser indtræffer.


Du kan konfigurere en besked for at gøre følgende, når det udløser:

- Send besked via mail til administratoren af tjenesten og flere administratorer
- sende mail til flere mails, du angiver.
- ringe til en webhook
- starte udførelse af en Azure runbook (kun fra Azure-portalen)

Du kan konfigurere og få at vide om brug af regler for påmindelser

- [Azure-portalen](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [kommandolinjen (CLI)](insights-alerts-command-line-interface.md)
- [Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn931945.aspx)


## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Oprette en besked om på en metrikværdi ved hjælp af Azure portal

1. Finde den ressource, du er interesseret i at overvåge og markere den på [portalen](https://portal.azure.com/).

2. Vælg **beskeder** eller **beskeder om regler** under sektionen overvågning. Tekst og ikon kan variere en smule for forskellige ressourcer.  

    ![Overvågning](./media/insights-alerts-portal/AlertRulesButton.png)


3. Vælg kommandoen **Tilføj påmindelse** , og Udfyld felterne.

    ![Tilføje besked](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Navn** beskeden reglen, og vælg en **Beskrivelse**, som viser også i besked om mails.
5. Vælg **metrisk** , du vil overvåge, og derefter vælge en **betingelse** og en **grænseværdi** værdi for metrikværdien. Også valgte **periode** , metriske reglen skal være opfyldt, før de beskeder om udløsere. Så for eksempel, hvis du bruger perioden "PT5M", og din besked søger efter CPU over 80%, udløser beskeden når CPU'EN har været konsekvent ovenfor 80% i 5 minutter. Når den første udløser forekommer, aktiverer den igen, når CPU'EN forbliver under 80% i 5 minutter. CPU-mål opstår hver 1 minut.   

6. Markér **mail ejere...** , hvis du vil administratorer og medadministratorer at blive sendt mail, når påmindelsen udløses.

7. Hvis du vil yderligere e-mails, der skal modtage en besked, når påmindelsen udløses, skal du tilføje dem i feltet **ekstra Administrator email(s)** . Adskille flere mails med semikolon-*email@contoso.com;email2@contoso.com*

8. Du kan placere på en gyldig URI i feltet **Webhook** , hvis du vil have den kaldes, når påmindelsen udløses.

9. Hvis du bruger Azure automatisering, kan du vælge en Runbook skal køres, når påmindelsen udløses.

10. Vælg **OK** når færdig oprette beskeden.   

Beskeden er aktiv i et par minutter og udløser som beskrevet tidligere.

## <a name="managing-your-alerts"></a>Administrere dine beskeder

Når du har oprettet en besked, kan du vælge det og:

- Få vist et diagram, der viser i grænseværdi for metriske og de faktiske værdier fra den forrige dag.
- Redigere eller slette den.
- **Deaktivere** eller **aktivere** det Hvis du vil midlertidigt stoppe eller CV modtage meddelelser via for den pågældende påmindelse.



## <a name="next-steps"></a>Næste trin

* [Få en oversigt over Azure overvågning](monitoring-overview.md) herunder typerne oplysninger kan du indsamle og overvåge.
* Lær mere om [konfiguration af webhooks i beskeder](insights-webhooks-alerts.md).
* Lær mere om [Azure automatisering Runbooks](..\automation\automation-starting-a-runbook.md).
* Få en [Oversigt over logge til diagnosticering](monitoring-overview-of-diagnostic-logs.md) og indsamle detaljerede hyppigt målepunkter med din tjeneste.
* Få en [Oversigt over målepunkter af websteder](insights-how-to-customize-monitoring.md) at sikre, at din tjeneste er tilgængelig og svarede.
