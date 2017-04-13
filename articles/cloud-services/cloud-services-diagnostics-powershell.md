<properties
    pageTitle="Aktivere diagnosticering i Azure Cloud Services ved hjælp af PowerShell | Microsoft Azure"
    description="Lær, hvordan du aktiverer diagnosticering for ved hjælp af PowerShell-skytjenester"
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Aktivere diagnosticering i Azure Cloud Services ved hjælp af PowerShell

Du kan indsamle diagnosticering data som programmet logfiler ydeevne tæller osv fra en skybaseret tjeneste, der bruger filtypenavnet Azure diagnosticering. I denne artikel beskrives, hvordan du aktiverer filtypenavnet Azure diagnosticering til en skybaseret tjeneste, ved hjælp af PowerShell.  Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) til forudsætninger, der kræves for denne artikel.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Aktiver diagnosticering udvidelse som en del af installation af en skybaseret tjeneste

Denne metode af god for fortløbende Integrationstype scenarier, hvor filtypenavnet diagnosticering kan aktiveres som en del af installation af skytjenesten. Når du opretter en ny skybaseret tjeneste-installation, kan du aktivere filtypenavnet diagnosticering ved overførsel af parameteren *ExtensionConfiguration* til [Ny AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) cmdlet. Parameteren *ExtensionConfiguration* kræver en matrix med diagnosticering konfigurationer, der kan oprettes ved hjælp af [Ny AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx) cmdlet.

I følgende eksempel viser, hvordan du kan aktivere diagnostics til en skybaseret tjeneste med en WebRole og WorkerRole hver har en anden diagnosticering konfiguration.

    $service_name = "MyService"
    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

Hvis konfigurationsfil diagnosticering angiver et StorageAccount element med et kontonavn-lager, bruge lagerplads konto automatisk i ny AzureServiceDiagnosticsExtensionConfig cmdlet. Dette skal fungere, skal kontoen lagerplads være i samme abonnement som Skytjenesten installeres.

Fra Azure SDK 2.6 og fremad lokalnummer konfigurationsfiler genereres af MSBuild publicere omfatter Måloutput kontonavn lager baseret på den diagnosticering konfiguration streng, der er angivet i filen til konfiguration af service (.cscfg). Scriptet nedenfor viser, hvordan du analysere lokalnummer konfigurationsfiler fra Måloutput Publicer og konfigurere diagnosticering udvidelse til hver rolle, når du installerer skytjenesten.

    $service_name = "MyService"
    $service_package = "C:\build\output\CloudService.cspkg"
    $service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

    #Find the Extensions path based on service configuration file
    $extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

    $diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
    $diagnosticsConfigurations = @()
    foreach ($extPath in $diagnosticsExtensions)
    {
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
        {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
        }
    }
    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations

Visual Studio Online bruger en lignende fremgangsmåde til automatiseret deploymnts af Cloud Services med filtypenavnet diagnosticering. Du kan se [Publicer AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) til en komplet eksempel.

Hvis ingen StorageAccount ikke blev angivet i diagnosticering konfigurationen, skal du overføre i parameteren StorageAccountName til cmdlet. Hvis parameteren StorageAccountName er angivet, bruge kontoen lagerplads, der er angivet i parameteren og ikke dét, der er angivet i filen diagnosticering konfiguration altid i cmdlet.

Hvis kontoen diagnosticering lagerplads er i et andet abonnement fra Skytjenesten, skal du overføre eksplicit i parametrene StorageAccountName og StorageAccountKey til cmdlet. Parameteren StorageAccountKey er ikke er nødvendigt ved diagnosticering lagerplads konto er i samme-abonnement, som cmdlet kan automatisk forespørgsel og angive den nøgleværdi, når du aktiverer filtypenavnet diagnosticering. Men hvis diagnosticering lagerplads konto er i et andet abonnement, derefter cmdlet muligvis ikke kunne hente tasten automatisk og skal du angive eksplicit tasten gennem parameteren StorageAccountKey.

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key


## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Aktiver diagnosticering udvidelse på en eksisterende skytjeneste

Du kan bruge cmdlet'en [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) til at aktivere eller opdatere diagnosticering konfiguration på en skybaseret tjeneste, der allerede kører.


    $service_name = "MyService"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name


## <a name="get-current-diagnostics-extension-configuration"></a>Få aktuelle diagnosticering lokalnummer konfiguration
Brug cmdlet'en [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) til at få den aktuelle diagnosticering konfiguration for en skybaseret tjeneste.

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## <a name="remove-diagnostics-extension"></a>Fjerne diagnosticering filtypenavn
Du kan bruge cmdlet'en [Fjern AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx) til for at deaktivere diagnosticering på en skybaseret tjeneste.

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Hvis du har aktiveret filtypenavnet diagnosticering ved hjælp af *Sæt AzureServiceDiagnosticsExtension* eller *Ny AzureServiceDiagnosticsExtensionConfig* uden parameteren *rolle* kan du fjerne filtypenavnet ved hjælp af *Fjern AzureServiceDiagnosticsExtension* uden parameteren *rolle* . Hvis parameteren *rolle* blev brugt, når du aktiverer filtypenavnet skal derefter den også bruges når du fjerner filtypenavnet.

Sådan fjernes filtypenavnet diagnosticering fra hver enkelt rolle:

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## <a name="next-steps"></a>Næste trin

- Du kan finde ekstra vejledning om brug af Azure diagnosticerings- og andre teknikker til fejlfinding af problemer med at [Aktivere diagnosticering i Azure Cloud Services og virtuelle computere](cloud-services-dotnet-diagnostics.md).
- [Diagnosticering konfiguration skema](https://msdn.microsoft.com/library/azure/dn782207.aspx) beskrives de forskellige XML-konfigurationer indstillinger for filtypenavnet diagnosticering.
- Hvis du vil se, hvordan du aktiverer filtypenavnet diagnosticering til virtuelle maskiner, under [oprette en Windows virtuel maskine overvågning og diagnosticering ved hjælp af Azure ressourcestyring skabelon](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)  
