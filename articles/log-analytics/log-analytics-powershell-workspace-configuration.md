<properties
    pageTitle="Bruge PowerShell til at oprette og konfigurere et Log Analytics arbejdsområde | Microsoft Azure"
    description="Logge Analytics bruger data fra servere i dit lokalt eller skybaseret infrastruktur. Du kan indsamle maskine data fra Azure lagerplads, når genereret Azure diagnosticering."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="powershell"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="richrund"/>

# <a name="manage-log-analytics-using-powershell"></a>Administrere Log analyser ved hjælp af PowerShell

Du kan bruge [Log Analytics PowerShell-cmdlet'er] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) til at udføre forskellige funktioner i Log Analytics fra en kommandolinje eller som en del af et script.  Eksempler på de opgaver, du kan udføre med PowerShell omfatter:

+ Oprette et arbejdsområde
+ Tilføje eller fjerne en løsning
+ Importere og eksportere gemte søgninger
+ Oprette en computergruppe
+ Aktivere samling af IIS-logfilerne fra computere med Windows-agent er installeret
+ Indsamle tællere i ydeevne fra Linux og Windows-computere
+ Indsamle hændelser fra syslog på Linux-computere 
+ Indsamle hændelser fra Windows-hændelseslogge
+ Indsamle brugerdefinerede hændelseslogfiler
+ Føje log analytics agent til en Azure virtuelt
+ Konfigurere log analytics indeksere data indsamlet ved hjælp af Azure diagnosticering


Denne artikel indeholder to kodeeksempler, der illustrerer nogle af de funktioner, som du kan udføre fra PowerShell.  Du kan referere til [Log Analytics PowerShell-cmdlet reference] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) til andre funktioner.

> [AZURE.NOTE] Log Analytics hedder tidligere funktionsdygtige viden, som er grunden til, at det er det navn, der bruges i cmdlet'erne.

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil bruge PowerShell med arbejdsområdet Log Analytics, skal du have:

+ Et Azure-abonnement og 
+ Arbejdsområdet Azure Log Analytics knyttet til abonnementet Azure.

Hvis du har oprettet et OMS arbejdsområde, men det endnu ikke er knyttet den til en Azure-abonnement kan du oprette linket:

+ På portalen Azure
+ På portalen OMS eller 
+ Brug af Get-AzureRmOperationalInsightsLinkTargets og ny AzureRmOperationalInsightsWorkspace cmdlet'er.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Oprette og konfigurere et Log Analytics-arbejdsområde

Følgende script eksempel viser, hvordan du:

1.  Oprette et arbejdsområde
2.  Liste over de tilgængelige løsninger
3.  Føj løsninger til arbejdsområdet
4.  Importér gemt søgninger
5.  Eksportér gemt søgninger
6.  Oprette en computergruppe
7.  Aktivere samling af IIS-logfilerne fra computere med Windows-agent er installeret
8.  Indsaml logisk Disk performance tællere fra Linux-computere (% bruges Inodes Gratis megabyte; % Bruges mellemrum. Disken overførsler/sec; Disken/sec; Disken skriver/sec)
9.  Indsamle syslog begivenheder fra Linux-computere
10. Indsamle fejl og advarsel hændelser fra programmets hændelseslog fra Windows-computere
11. Indsaml hukommelse til rådighed ydeevne tæller fra Windows-computere
12. Indsamling af en brugerdefineret logfil 


```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Konfiguration af Log Analytics indeksere Azure diagnosticering 

For uafhængig overvågning af Azure ressourcer, skal ressourcerne, der have Azure diagnosticering aktiveret og konfigureret til at skrive til en lagerplads konto. Log Analytics kan derefter konfigureres til at indsamle logge fra kontoen lagerplads. Ressourcer, som du har brug for at gøre den foregående konfiguration for omfatter:

+ Klassisk skytjenester (internettet og arbejder roller)
+ Service-strukturen klynger
+ Netværk sikkerhedsgrupper
+ Primære vaults og 
+ Program-gateways

Du kan også bruge PowerShell til at konfigurere et Log Analytics-arbejdsområde i ét Azure abonnement til at indsamle logge fra forskellige Azure-abonnementer.

Følgende eksempel viser Sådan:

1.  Få vist eksisterende lagerplads konti og placeringer, som Log Analytics indekserer data fra
2.  Oprette en konfiguration til at læse fra en lagerplads konto
3.  Opdatere nyoprettede konfigurationen indeksere data fra flere steder
4.  Slette den nyoprettede konfiguration

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## <a name="next-steps"></a>Næste trin

- [Gennemse logfilen Analytics PowerShell-cmdlet'er](http://msdn.microsoft.com/library/mt188224.aspx) til yderligere oplysninger om at bruge PowerShell til konfiguration af Log analyser.

