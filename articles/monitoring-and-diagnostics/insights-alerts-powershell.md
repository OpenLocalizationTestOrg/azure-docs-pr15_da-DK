<properties
    pageTitle="Bruge PowerShell til at oprette påmindelser for Azure services | Microsoft Azure"
    description="Bruge PowerShell til at oprette Azure påmindelser, som kan udløse beskeder eller automatisering, når de angivne betingelser er opfyldt."
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

# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Bruge PowerShell til at oprette påmindelser for Azure tjenester

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Oversigt

I denne artikel beskrives, hvordan du konfigurerer Azure advarsler ved hjælp af PowerShell.  

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


Yderligere oplysninger finder du altid kan skrive ```get-help``` og derefter den PowerShell-kommando, du vil have hjælp til.

## <a name="create-alert-rules-in-powershell"></a>Oprette regler for påmindelser i PowerShell

1. Log på Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```

2. Få en liste over abonnementerne, der er tilgængelige. Kontrollér, at du arbejder med det rigtige abonnement. Hvis ikke, skal du angive den til den rigtige ved hjælp af output fra `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

3.  Brug følgende kommando for at få vist eksisterende regler for en ressourcegruppe:

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

4. Hvis du vil oprette en regel, skal du først har flere vigtige dele af oplysninger. 
    - **Ressource-ID** for ressourcen, du vil indstille en besked om
    - **Metriske definitioner** tilgængelige for denne ressource

    En metode til at få ressource-ID er at bruge Azure-portalen. Hvis du allerede har oprettet ressourcen, vælge den på portalen. Vælg derefter *Egenskaber* i bladet næste under afsnittet *Indstillinger* . RESSOURCE-ID er et felt i den næste blade. En anden måde er at bruge [Azure ressource Explorer](https://resources.azure.com/).

    Et eksempel ressource-id til en WebApp er

    ```
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    Du kan bruge `Get-AzureRmMetricDefinition` til at få vist listen over alle metriske definitioner for en bestemt ressource.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id>
    ```

    I følgende eksempel genererer en tabel med en metrikværdi navn og enheden for metrisk.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

    ```
    En komplet liste over tilgængelige indstillinger for Get-AzureRmMetricDefinition findes ved at køre Get-MetricDefinitions.


5. I følgende eksempel angiver en påmindelse for en ressource til websted. Beskeder om udløsere, når den konsekvent modtager al trafik til 5 minutter og igen når der modtages nogen trafik til 5 minutter.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```

6. Hvis du vil oprette webhook eller sende mail, når en påmindelse udløser, skal du først oprette mail og/eller webhooks. Derefter straks oprette reglen bagefter mærket - handlinger og som vist i følgende eksempel. Du kan ikke knytte webhook eller mails med allerede har oprettet regler via PowerShell.


    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```


7. For at oprette en besked, der udløser på en bestemt betingelse aktivitet log, skal du bruge kommandoerne i formularen følgende

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```

    OperationName - svarer til en type hændelse aktivitet log. Som eksempler kan nævnes *Microsoft.Compute/virtualMachines/delete* og *microsoft.insights/diagnosticSettings/write*.

    Du kan bruge PowerShell-kommandoen [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) til at få en liste over mulige operationNames. Alternativt kan du bruge Azure portal til at forespørge på aktivitet loggen og finde bestemte tidligere handlinger, som du vil oprette en besked for. De handlinger, der vises i visningen grafisk log af det fulde navn. Søg i JSON for posten og trække OperationName værdien.   

8. Kontrollér, at dine beskeder er oprettet korrekt, ved at se på de individuelle regler.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

9. Slette dine beskeder. Disse kommandoer slette de regler, der er oprettet tidligere i denne artikel.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Næste trin

* [Få en oversigt over Azure overvågning](monitoring-overview.md) herunder typerne oplysninger kan du indsamle og overvåge.
* Lær mere om [konfiguration af webhooks i beskeder](insights-webhooks-alerts.md).
* Lær mere om [Azure automatisering Runbooks](..\automation\automation-starting-a-runbook.md).
* Få en [Oversigt over ved at indsamle logge til diagnosticering](monitoring-overview-of-diagnostic-logs.md) til at indsamle detaljerede hyppigt målepunkter på din tjeneste.
* Få en [Oversigt over målepunkter af websteder](insights-how-to-customize-monitoring.md) at sikre, at din tjeneste er tilgængelig og svarede.
