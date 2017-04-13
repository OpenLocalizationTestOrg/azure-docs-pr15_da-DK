<properties
    pageTitle="Analysere Azure diagnosticeringslogfiler ved hjælp af Log Analytics | Microsoft Azure"
    description="Log Analytics kan læse loggene fra Azure-tjenester, skriver Azure diagnosticeringslogfiler for at blob storage i JSON-format."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Analysere Azure diagnosticeringslogfiler ved hjælp af Log Analytics

Log Analytics kan indsamle logfilerne for følgende Azure tjenester, der skriver [Azure diagnosticeringslogfiler](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) til blob-lager i JSON-format:

+ Automatisering (Preview)
+ Vigtige samling (Preview)
+ Application Gateway (Preview)
+ Sikkerhedsgruppe netværk (Preview)

De følgende afsnit fører dig igennem ved hjælp af PowerShell til at:

+ Konfigurere Log analyser for at indsamle logge fra lagerplads for hver ressource  
+ Aktivere løsningen Log Analytics til Azure-tjenesten

Før Log Analytics kan indsamle data til disse ressourcer, skal være aktiveret Azure diagnosticering. Brug den `Set-AzureRmDiagnosticSetting` til at aktivere logføring.

Se følgende artikler kan finde flere oplysninger om, hvordan du aktivere logføring af diagnostik:

+ [Vigtige samling](../key-vault/key-vault-logging.md)
+ [Application Gateway](../application-gateway/application-gateway-diagnostics.md)
+ [Netværk sikkerhedsgruppe](../virtual-network/virtual-network-nsg-manage-log.md)

Denne dokumentation indeholder også oplysninger på:

+ Fejlfinding i forbindelse med indsamling af data
+ Stoppe dataindsamling

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Konfigurere Log analyser for at indsamle Azure diagnosticeringslogfiler

Indsamling af logfiler for disse tjenester og aktivering af løsningen til visualisering af logfiler udføres ved hjælp af PowerShell-scripts.

I eksemplet nedenfor kan logge på alle understøttede ressourcer

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


For nogle ressourcer er det muligt at udføre konfigurationen af foregående fra Azure-portalen med de trin, der er beskrevet i [Oversigt over Azure diagnosticeringslogfiler](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Konfigurere Log analyser for at indsamle Azure diagnosticeringslogfiler

Vi har fået et PowerShell-script-modul, der eksporterer to cmdlet'er til at hjælpe med at konfigurere Log Analytics:

1. `Add-AzureDiagnosticsToLogAnalyticsUI`beder dig om input og er i stand til at konfigurere enkle konfigurationer
2. `Add-AzureDiagnosticsToLogAnalytics`tager ressourcer til at overvåge som input og derefter konfigurerer Log Analytics  

### <a name="pre-requisites"></a>Forudsætninger

1. Azure PowerShell med version 1.0.8 eller nyere af cmdlet'erne funktionsdygtige indsigt.
  - [Hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md)
  - Kontrollere din version af cmdletter:`Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. Logføring af diagnostik er konfigureret til den ønskede til at overvåge Azure ressource. Brug `Set-AzureRmDiagnosticSetting` eller referere til [Brug Log Analytics til at indsamle data fra Azure lagerplads konti](log-analytics-azure-storage.md) til, hvordan du aktiverer diagnosticering.
3. Et [Log Analytics](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS) -arbejdsområde  
4. AzureDiagnosticsAndLogAnalytics PowerShell-modulet
  - Hente modulet [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) fra PowerShell-galleri

### <a name="option-1-run-the-interactive-configuration-scripts"></a>Mulighed 1: Køre interaktive konfigurationsscripts

Åbne PowerShell, og kør:

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

Du er vist en liste over tilgængelige valg og får en meddelelse om at foretage dine valg.
Du bliver bedt om at foretage valg for hver af følgende:

+ Ressourcetyper (Azure Services) til at indsamle logge fra
+ Ressource forekomster til at indsamle logge fra
+ Log Analytics arbejdsområde til indsamling af data

Når du kører dette script, skal du se poster i Log Analytics om 30 minutter, efter at der skrives nye diagnosticering data til lager. Hvis poster ikke er tilgængelige, når denne gang refererer til afsnittet fejlfinding nedenfor.

### <a name="option-2-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>Mulighed 2: Oprette en liste over ressourcer og overføre dem til Cmdletten konfiguration

Du kan oprette en liste over ressourcer, der er Azure diagnosticering aktiveret og derefter sende ressourcerne til Cmdletten konfiguration.

Du kan se yderligere oplysninger om cmdlet ved at køre `Get-Help Add-AzureDiagnosticsToLogAnalytics`.


Til at finde flere oplysninger på OMS [Log Analytics PowerShell-cmdletter](https://msdn.microsoft.com/library/mt188224.aspx)

>[AZURE.NOTE] Hvis ressource og arbejdsområdet er i forskellige Azure-abonnementer, skifte mellem dem efter behov ved hjælp af`Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
Når du kører dette script, skal du se poster i Log Analytics om 30 minutter, efter at der skrives nye diagnosticering data til lager. Hvis poster ikke er tilgængelige, når denne gang refererer til afsnittet fejlfinding nedenfor.  

>[AZURE.NOTE] Konfigurationen er ikke synlig i portalen Azure. Du kan kontrollere konfiguration ved hjælp af den `Get-AzureRmOperationalInsightsStorageInsight` cmdlet.  


## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Forhindrer Log Analytics i indsamle Azure diagnosticeringslogfiler

Hvis du vil slette Log Analytics-konfiguration for en ressource, skal du bruge den `Remove-AzureRmOperationalInsightsStorageInsight` cmdlet.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Fejlfinding i forbindelse med konfiguration for Azure diagnosticeringslogfiler

*Problem*

Følgende fejl vises, når du konfigurerer en ressource, der logger klassisk lagerplads:

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*Opløsning*

Log på API til den klassiske implementeringsmodel med`Add-AzureAccount`

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Fejlfinding i forbindelse med indsamling af data til Azure diagnosticeringslogfiler

Hvis du ikke kan se data for din Azure ressource i Log Analytics, kan du bruge følgende fejlfindingstrin:

+ Bekræft dataene, der flyder til kontoen lagerplads
+ Kontrollere logfilen Analytics-løsning for tjenesten er aktiveret
+ Kontrollér, at Log Analytics er konfigureret til at læse fra lagerplads
+ Opdatere kontonøgle lagerplads

### <a name="verify-data-is-flowing-to-the-storage-account"></a>Bekræfte data der flyder til kontoen lagerplads

Du kan se, hvis dataene der flyder til kontoen lager, med et værktøj, der giver mulighed for gennemsyn Azure-lager (for eksempel Visual Studio) eller ved hjælp af PowerShell.

Ressourcen for at finde den lagerplads konto er konfigureret til at logge for at bruge følgende PowerShell:

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

Objektbeholderen til lagring af bruges af Azure diagnosticering har et navn med et format fra:  

`insights-logs-<Category>`

Se [Brug lagerplads cmdlet'er til at kontrollere en objektbeholder til seneste data](../storage/storage-powershell-guide-full.md) til at få mere at vide om at få vist indholdet af kontoen lagerplads.

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>Kontrollere logfilen Analytics-løsning for tjenesten er aktiveret

Hvis du bruger `Add-AzureDiagnosticsToLogAnalyticsUI`, den korrekte Log Analytics-løsning er automatisk aktiveret for dig.

For at kontrollere, om en løsning er aktiveret, skal du køre følgende PowerShell:

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

Hvis løsningen ikke er aktiveret, kan du aktivere den ved hjælp af følgende PowerShell:

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

Brug de følgende PowerShell (denne variabel er tilgængelig, når du har importeret modulet) til at finde navnet på løsningen skal aktiveres for hver ressourcetype:

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Kontrollér, at Log Analytics er konfigureret til at læse fra lagerplads

Hvis du tilføjer yderligere Azure ressourcer, skal du aktivere diagnosticering logføring for dem, og konfigurere Log Analytics til dem.
Sådan kontrollerer du, hvilke ressourcer og lagerplads konti er Log Analytics konfigureret til at indsamle logge for brug følgende PowerShell:

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>Opdatere tasten lagerplads

Hvis du ændrer nøglen for kontoen lagerplads, skal Log Analytics konfigurationen også opdateres med den nye nøgle.
Du kan opdatere Log Analytics-konfiguration med en ny nøgle ved hjælp af følgende PowerShell:

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

Hvis du vil finde lagerplads indsigt navnet skal bruge den `Get-AzureRmOperationalInsightsStorageInsight` cmdlet, som vist i tidligere eksempler.

## <a name="next-steps"></a>Næste trin

- [Brug blob-lager til IIS og tabellagring for begivenheder](log-analytics-azure-storage-iis-table.md) til at læse logfiler til Azure tjenester, Skriv diagnostics til tabellagring eller IIS-logfiler, der er skrevet til blob storage.
- [Aktivere løsninger](log-analytics-add-solutions.md) for at give indblik i dataene.
- [Brug søgeforespørgsler](log-analytics-log-searches.md) til at analysere dataene.
