<properties
    pageTitle="Ved hjælp af blob-lager til IIS og tabel lagerplads på begivenheder | Microsoft Azure"
    description="Log Analytics kan læse loggene til Azure-tjenester, skriver diagnosticering til table storage eller IIS-logfiler, der er skrevet til blob storage."
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
    ms.date="10/25/2016"
    ms.author="banders"/>


# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>Brug af blob-lager til IIS og tabellagring om begivenheder

Log Analytics kan læse logfilerne for følgende tjenester, der skriver diagnosticering til table storage eller IIS-logfiler, der er skrevet til blob storage:

+ Tjenesten strukturen klynger (Preview)
+ Virtuelle maskiner
+ Web/arbejder roller

Før Log Analytics kan indsamle data til disse ressourcer, skal være aktiveret Azure diagnosticering.

Når diagnosticering er aktiveret, kan du bruge portalen Azure eller PowerShell konfigurere Log Analytics at indsamle logge.

Azure diagnosticering er en Azure udvidelse, der gør det muligt at indsamle diagnosticering data fra en kollega rolle, web rolle eller virtuelt kører i Azure. Data, der er gemt i en Azure-lager-konto og kan derefter indsamles af Log Analytics.

Log Analytics at indsamle disse Azure diagnosticering logfiler, være logge på følgende placeringer:

| Logtype | Ressourcetype | Placering |
| -------- | ------------- | -------- |
| IIS-logfilerne | Virtuelle maskiner <br> Web roller <br> Arbejder roller | wad-iis-logfiles (Blob Storage) |
| Syslog | Virtuelle maskiner | LinuxsyslogVer2v0 (tabellen lagerplads) |
| Tjenesten strukturen funktionsdygtige begivenheder | Tjenesten strukturen noder | WADServiceFabricSystemEventTable |
| Tjenesten strukturen pålidelig Agent begivenheder | Tjenesten strukturen noder | WADServiceFabricReliableActorEventTable |
| Strukturen pålidelig Service tjenestehændelser | Tjenesten strukturen noder | WADServiceFabricReliableServiceEventTable |
| Windows-hændelseslogge | Tjenesten strukturen noder <br> Virtuelle maskiner <br> Web roller <br> Arbejder roller | WADWindowsEventLogsTable (Table Storage) |
| Windows ETW logfiler | Tjenesten strukturen noder <br> Virtuelle maskiner <br> Web roller <br> Arbejder roller | WADETWEventTable (Table Storage) |

>[AZURE.NOTE] IIS-logfilerne fra Azure websteder understøttes ikke i øjeblikket.

Virtuelle maskiner har du mulighed for at installere [Log Analytics agent](log-analytics-azure-vm-extension.md) til din virtuelle maskine til at aktivere flere indsigt. Ud over at kunne analysere IIS-logfiler og -hændelseslogge, kan du udføre yderligere analyser, herunder konfiguration registrering, SQL vurdering og Opdater vurdering.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Aktivere Azure diagnosticering i et virtuelt for hændelseslog og IIS logge af websteder

Benyt følgende fremgangsmåde for at aktivere Azure diagnosticering i en virtuel maskine til hændelseslog og IIS log af websteder ved hjælp af portalen Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Aktivere Azure diagnosticering i et virtuelt ved hjælp af Azure portal

1. Installere VM Agent, når du opretter en virtuel maskine. Hvis den virtuelle maskine allerede findes, skal du kontrollere, VM Agent allerede er installeret.
    - Gå til den virtuelle maskine i Azure-portalen, Vælg **Valgfri konfiguration**, og klik derefter **diagnosticering** , og angiv **Status** til **til**.

    Når er afsluttet har hvor VM filtypenavnet Azure diagnosticering installeret og kører. Dette lokalnummer er ansvarlig for indsamling af dataene diagnosticering.

2. Aktivere overvågning og konfigurere hændelseslogføring på en eksisterende VM. Du kan aktivere diagnosticering på niveauet for VM. For at aktivere diagnosticering og derefter konfigurere hændelseslogføring skal du udføre følgende trin:
    1. Vælg VM.
    2. Klik på **overvågning**.
    3. Klik på **diagnosticering**.
    4. Angive **Status** til **til**.
    5. Markér hver logfil over diagnosticering, du vil indsamle.
    7. Klik på **OK**.

Du kan mere præcist ved hjælp af Azure PowerShell til at angive de hændelser, der er skrevet til Azure-lager. Referere til at [indsamle data ved hjælp af Azure diagnostics skrives til tabellagring eller IIS-logfiler, der er skrevet til blob](log-analytics-azure-storage-json.md).


## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Aktivere Azure diagnostics i en Web rolle for IIS-logfiler og begivenhed af websteder

Se [Hvordan til at aktivere diagnosticering i en skybaseret tjeneste](../cloud-services/cloud-services-dotnet-diagnostics.md) generelle trin til at aktivere Azure diagnosticering. Vejledningen nedenfor for bruge disse oplysninger og tilpasse det til brug sammen med Log analyser.

Med Azure diagnosticering aktiveret:

- IIS-logfilerne gemmes som standard med logdata overført med intervallet, scheduledTransferPeriod overførsel.
- Windows hændelseslogfiler overføres ikke som standard.

### <a name="to-enable-diagnostics"></a>Aktivere diagnosticering

Aktivere Windows hændelseslogfiler, eller ændre scheduledTransferPeriod, konfigurere Azure Diagnostics ved hjælp af XML-fil (diagnostics.wadcfg), som vist i [trin 4: oprette din Diagnostics konfigurationsfil og installere filtypenavnet](../cloud-services/cloud-services-dotnet-diagnostics.md)

Følgende eksempel konfigurationsfil indsamler IIS-logfiler og alle begivenheder fra loggene program- og:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Sørg for, at din ConfigurationSettings angiver en lagerplads-konto, som i følgende eksempel:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

**Kontonavn** og **AccountKey** værdier findes i portalen Azure i dashboardet lagerplads konto under administrere hurtigtaster. Protokol for forbindelsesstrengen skal være **https**.

Når den opdaterede diagnosticering konfiguration anvendes til skybaseret tjeneste, og den skriver diagnosticering til Azure-lager, er du klar til at konfigurere Log analyser.


## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Bruge Azure-portalen til at indsamle logge fra Azure-lager

Du kan bruge portalen Azure til at konfigurere Log Analytics til indsamling af logfiler for følgende Azure tjenester:

+ Tjenesten strukturen klynger
+ Virtuelle maskiner
+ Web/arbejder roller

Gå til arbejdsområdet Log Analytics i Azure-portalen og udføre følgende opgaver:

1. Klik på *lagerplads konti logfiler*
2. Klik på *Tilføj* opgave
3. Vælg den konto, lagerplads, der indeholder loggene diagnosticering
  - Denne konto kan være enten en klassisk lagerplads firma eller en Azure ressourcestyring lagerplads konto
4. Vælg den datatype, du vil indsamle logge for
  - Valgmulighederne, der er IIS-logfilerne Arrangementer Syslog (Linux). ETW logfiler; Strukturen tjenestehændelser
5. Værdien for kilde udfyldes automatisk baseret på datatypen og kan ikke ændres
6. Klik på OK for at gemme konfigurationen

Gentag trin 2-6 for ekstra lagerplads konti og datatyper, du vil Log Analytics at indsamle.

I ca. 30 minutter er du kunne se data fra kontoen lagerplads i Log analyser. Du vil kun se data, der er skrevet til lager, efter at konfigurationen er anvendt. Log Analytics læser ikke de eksisterende data fra kontoen lagerplads.

>[AZURE.NOTE] På portalen validerer ikke, at kilden findes i kontoen lagerplads, eller hvis nye data, der skrives.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Aktivere Azure diagnosticering i et virtuelt for hændelseslog og IIS logge af websteder ved hjælp af PowerShell

Følg trinnene i [Konfigurere Log Analytics indeksere Azure diagnosticering](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) for at bruge PowerShell til at læse fra Azure diagnosticering, der er skrevet til table storage.

Du kan mere præcist ved hjælp af Azure PowerShell til at angive de hændelser, der er skrevet til Azure-lager.
Få mere at vide under [Aktivering af Diagnostics i virtuelle Azure-computere](../virtual-machines-dotnet-diagnostics.md).

Du kan aktivere og opdatere Azure diagnosticering ved hjælp af følgende PowerShell-script.
Du kan også bruge dette script med en brugerdefineret logføring konfiguration.
Ændre scriptet for at angive lagerplads konto, navnet på tjenesten og virtuelt navn.
Scriptet bruger cmdletter for klassisk virtuelle computere.

Gennemgå følgende eksempel på script, kopiere den, redigere den efter behov, gemme stikprøvernes som en PowerShell-script-fil og derefter køre scriptet.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Næste trin

- [Brug JSON-filer i blob-lager](log-analytics-azure-storage-json.md) til at læse loggene fra Azure tjenester, Skriv diagnosticering til blob-lager i JSON-format.
- [Aktivere løsninger](log-analytics-add-solutions.md) for at give indblik i dataene.
- [Brug søgeforespørgsler](log-analytics-log-searches.md) til at analysere dataene.
