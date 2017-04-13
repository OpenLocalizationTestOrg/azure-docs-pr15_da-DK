<properties
    pageTitle="Bruge PowerShell til at aktivere Azure diagnosticering i en virtuel maskine, der kører Windows | Microsoft Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    description="Lær at bruge PowerShell til at aktivere Azure diagnosticering i en virtuel maskine, der kører Windows"
    authors="sbtron"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>


# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Bruge PowerShell til at aktivere Azure diagnosticering i en virtuel maskine, der kører Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure diagnosticering er muligheden for i Azure, der gør det muligt for indsamling af diagnosticering data på en udløst programmet. Du kan bruge filtypenavnet diagnosticering til at indsamle diagnosticering data som programmet logfiler eller tællere i ydeevne fra en Azure virtuel maskine (VM), der kører Windows. I denne artikel beskrives, hvordan du bruger Windows PowerShell til at aktivere filtypenavnet diagnosticering til en VM. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) til forudsætninger, der kræves for denne artikel.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Aktivere filtypenavnet diagnosticering, hvis du bruger implementeringsmodel ressourcestyring

Du kan aktivere filtypenavnet diagnosticering, mens du opretter en Windows-VM gennem implementeringsmodel Azure ressourcestyring ved at føje lokalnummer konfigurationen til skabelonen ressourcestyring. Se [oprette en Windows virtuel maskine overvågning og diagnosticering ved hjælp af skabelonen Azure ressourcestyring](virtual-machines-windows-extensions-diagnostics-template.md).

Du kan bruge [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) PowerShell-cmdlet for at aktivere filtypenavnet diagnosticering på en eksisterende VM, der blev oprettet via implementeringsmodel Ressourcestyring, som vist nedenfor.


    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* er stien til den fil, der indeholder diagnosticering konfigurationen i XML, som beskrevet i [eksempel](#sample-diagnostics-configuration) nedenfor.  

Hvis konfigurationsfil diagnosticering angiver et **StorageAccount** element med et kontonavn-lager, angive filtypenavnet diagnosticering til at sende diagnosticering data til lagerplads konto automatisk i scriptet *Sæt AzureRMVMDiagnosticsExtension* . Dette skal fungere, skal kontoen lagerplads være i samme abonnement som VM.

Hvis ingen **StorageAccount** ikke blev angivet i diagnosticering konfigurationen, skal du overføre i parameteren *StorageAccountName* til cmdlet. Hvis parameteren *StorageAccountName* er angivet, derefter anvender cmdlet altid lagerplads kontoen, der er angivet i parameteren og ikke det, der er angivet i filen diagnosticering konfiguration.

Hvis kontoen diagnosticering lagerplads er i et andet abonnement fra VM, skal du overføre eksplicit i parametrene *StorageAccountName* og *StorageAccountKey* til cmdlet. Parameteren *StorageAccountKey* er ikke er nødvendigt ved diagnosticering lagerplads konto er i samme-abonnement, som cmdlet kan automatisk forespørgsel og angive den nøgleværdi, når du aktiverer filtypenavnet diagnosticering. Men hvis diagnosticering lagerplads konto er i et andet abonnement, derefter cmdlet muligvis ikke kunne hente tasten automatisk og skal du angive eksplicit tasten gennem parameteren *StorageAccountKey* .  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Når filtypenavnet diagnosticering er aktiveret på en VM, kan du få de aktuelle indstillinger ved hjælp af cmdlet'en [Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx) til.

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Cmdlet returnerer *PublicSettings*, som indeholder XML-konfigurationen i et Base64-kodet format. Hvis du vil læse XML, skal du afkode den.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

[Fjern AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx) cmdlet kan bruges til at fjerne filtypenavnet diagnosticering fra VM.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Aktivere filtypenavnet diagnosticering, hvis du bruger den klassiske implementeringsmodel

Du kan bruge cmdlet'en [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) til at aktivere filtypenavnet diagnosticering på en VM, som du opretter gennem modellen Klassisk installation. I følgende eksempel viser, hvordan du opretter en ny VM gennem klassisk installation modellen med filtypenavnet diagnosticering aktiveret.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

For at aktivere filtypenavnet diagnosticering på en eksisterende VM, der blev oprettet ved hjælp af klassisk implementeringsmodel skal du først bruge cmdlet'en [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) til at få VM konfigurationen. Derefter opdatere konfigurationen af VM for at medtage filtypenavnet diagnosticering ved hjælp af cmdlet'en [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) til. Til sidst skal anvende den opdaterede konfiguration til VM ved hjælp af [Opdatering AzureVM](https://msdn.microsoft.com/library/mt589121.aspx).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Eksempel diagnosticering konfiguration

Følgende XML kan bruges til diagnosticering offentlige konfigurationen med de ovenstående scripts. Denne eksempel konfiguration overføre forskellige tællere i ydeevne til diagnosticering lagerplads kontoen, sammen med fejl fra det program, sikkerhed og systemkanaler i Windows-hændelseslogge og eventuelle fejl fra loggene diagnosticering infrastruktur.

Konfigurationen skal opdateres for at medtage følgende:

- Attributten *resourceID* i elementet **målepunkter** skal opdateres med ressource-ID'ET for VM.
    - Ressource-ID kan være opbygget ved hjælp af følgende mønster: "/ abonnementer / {*abonnement-ID for abonnementet med VM*} /resourceGroups/ {*resourcegroup navnet på VM*} / providers/Microsoft.Compute/virtualMachines/ {*VM navnet*}".
    - Eksempelvis hvis abonnement-ID for det abonnement, hvor VM kører er **11111111-1111-1111-1111-111111111111**, ressource gruppenavn til ressourcegruppen er **MyResourceGroup**, og navnet på VM er **MyWindowsVM**, ville derefter værdien for *resourceID* være:

        ```
        <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
        ```

    - Yderligere oplysninger om, hvordan målepunkter der genereres baseret på den ydeevne tællere og målepunkter konfiguration, skal du se [Azure diagnosticering målepunkter tabel i lagerplads](virtual-machines-windows-extensions-diagnostics-template.md#wadmetrics-tables-in-storage).

- Elementet **StorageAccount** skal opdateres med navnet på kontoen diagnosticering lagerplads.

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Næste trin
- Du kan finde ekstra vejledning om brug af muligheden for Azure diagnosticering og andre teknikker til fejlfinding af problemer med at [Aktivere diagnosticering i Azure Cloud Services og virtuelle computere](../cloud-services/cloud-services-dotnet-diagnostics.md).
- [Diagnosticering konfigurationer skema](https://msdn.microsoft.com/library/azure/mt634524.aspx) beskrives de forskellige XML-konfigurationer indstillinger for filtypenavnet diagnosticering.
