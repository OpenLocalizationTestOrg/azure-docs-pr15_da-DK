<properties
    pageTitle="Brug på tværs af platforme Command Line Interface (CLI) til at oprette beskeder for Azure services | Microsoft Azure"
    description="Brug kommandoen linje grænsefladen til at oprette Azure påmindelser, som kan udløse beskeder eller automatisering, når de angivne betingelser er opfyldt."
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
    ms.date="10/24/2016"
    ms.author="robb"/>

# <a name="use-the-cross-platform-command-line-interface-cli-to-create-alerts-for-azure-services"></a>Brug på tværs af platforme Command Line Interface (CLI) til at oprette beskeder for Azure tjenester

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Oversigt

I denne artikel beskrives, hvordan du konfigurerer Azure advarsler ved hjælp af kommandoen linje Interface (CLI).

>[AZURE.NOTE] Azure skærm er det nye navn til Hvad hedder "Azure indsigt" indtil Sept 25 2016. Navneområder og dermed kommandoerne nedenfor indeholder stadig "viden".

Du kan modtage en besked, der er baseret på overvågning måleredskaber for eller begivenheder i dine Azure tjenester.

- **Metriske værdier** - beskeder om udløsere, når værdien af en bestemt metrik krydser en grænse, du tildeler i den ønskede retning. Det vil sige det udløser begge når betingelsen opfyldes først og derefter bagefter når betingelse, der ikke længere er opfyldt.    
- **Aktivitet log begivenheder** - kan udløse en besked på *hver* begivenhed eller, kun, når et bestemt antal hændelser indtræffer.

Du kan konfigurere en besked for at gøre følgende, når det udløser:

- Send besked via mail til administratoren af tjenesten og flere administratorer
- sende mail til flere mails, du angiver.
- ringe til en webhook
- starte udførelse af en Azure runbook (kun fra Azure-portalen på nuværende tidspunkt)

Du kan konfigurere og få at vide om brug af regler for påmindelser

- [Azure-portalen](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [kommandolinjen (CLI)](insights-alerts-command-line-interface.md)
- [Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Du kan altid få hjælp til kommandoer ved at skrive en kommando- og læg - hjælp til sidst. Eksempel:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Oprette regler for påmindelser ved hjælp af CLI

1. Udføre forudsætninger og logge på Azure. Se [eksempler på Azure skærm CLI](insights-cli-samples.md). Kort sagt, installere CLI og køre disse kommandoer. De kommer du logget på, skal du vise det abonnement, du bruger, og forberede dig til at køre Azure skærm kommandoer.


    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2.  Brug de følgende formular **azure indsigt beskeder regel liste** for at få vist eksisterende regler på en ressourcegruppe, *[parametre] &lt;resourceGroup&gt; *

    ```console
    azure insights alerts rule list myresourcegroupname

    ```
3. Hvis du vil oprette en regel, skal du først har flere vigtige dele af oplysninger.
    - **Ressource-ID** for ressourcen, du vil indstille en besked om
    - **Metriske definitioner** tilgængelige for denne ressource

    En metode til at få ressource-ID er at bruge Azure-portalen. Hvis du allerede har oprettet ressourcen, vælge den på portalen. Vælg derefter *Egenskaber* i bladet næste under afsnittet *Indstillinger* . *Ressource-ID* er et felt i den næste blade. En anden måde er at bruge [Azure ressource Explorer](https://resources.azure.com/).

    Et eksempel ressource-id til en WebApp er

    ```console
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    For at få en liste over de tilgængelige målepunkter og enheder på disse målepunkter til det forrige eksempel ressource skal du bruge kommandoen følgende CLI:  

    ```console
    azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
    ```

    _PT1M_ er Granulariteten af de tilgængelige mål (1-minutters intervaller). Brug af forskellige granulariteter giver dig forskellige metriske indstillinger.


4. For at oprette en metrikværdi-baserede reglen skal du bruge en kommando i formularen følgende:

    **Azure indsigt beskeder regel metrisk angive** *[parametre] &lt;regelnavn&gt; &lt;placering&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;grænseværdi for&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt; *

    I følgende eksempel angiver en påmindelse for en ressource til websted. Beskeder om udløsere, når den konsekvent modtager al trafik til 5 minutter og igen når der modtages nogen trafik til 5 minutter.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```

5. Hvis du vil oprette webhook eller sende en besked udløses, skal du først oprette mail og/eller webhooks. Oprette reglen med det samme bagefter. Du kan ikke knytte webhook eller mails med allerede har oprettet regler, der anvender CLI.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```


6. Brug formularen til at oprette en besked, der udløses af en bestemt betingelse aktivitet log:

    **indsigt beskeder regel logge sæt** *[parametre] &lt;regelnavn&gt; &lt;placering&gt; &lt;resourceGroup&gt; &lt;operationName&gt; *

    For eksempel

    ```console
    azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
    ```

    OperationName svarer til en hændelsestype for et opslagsord aktivitet log. Som eksempler kan nævnes *Microsoft.Compute/virtualMachines/delete* og *microsoft.insights/diagnosticSettings/write*.

    Du kan bruge PowerShell-kommandoen [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) til at få en liste over mulige operationNames. Alternativt kan du bruge Azure portal til at forespørge på aktivitet loggen og finde bestemte tidligere handlinger, som du vil oprette en besked for. De handlinger, der vises i visningen grafisk log af det fulde navn. Søg i JSON for posten og trække OperationName værdien.   

7. Du kan kontrollere, at dine beskeder er oprettet korrekt, ved at se på en enkelt regel.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```

8. Hvis du vil slette regler, skal du bruge en kommando i formularen:

    **indsigt beskeder regel slette** [parametre] &lt;resourceGroup&gt; &lt;regelnavn&gt;

    Disse kommandoer slette de regler, der tidligere blev oprettet i denne artikel.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```



## <a name="next-steps"></a>Næste trin

* [Få en oversigt over Azure overvågning](monitoring-overview.md) herunder typerne oplysninger kan du indsamle og overvåge.
* Lær mere om [konfiguration af webhooks i beskeder](insights-webhooks-alerts.md).
* Lær mere om [Azure automatisering Runbooks](..\automation\automation-starting-a-runbook.md).
* Få en [Oversigt over ved at indsamle logge til diagnosticering](monitoring-overview-of-diagnostic-logs.md) til at indsamle detaljerede hyppigt målepunkter på din tjeneste.
* Få en [Oversigt over målepunkter af websteder](insights-how-to-customize-monitoring.md) at sikre, at din tjeneste er tilgængelig og svarede.
