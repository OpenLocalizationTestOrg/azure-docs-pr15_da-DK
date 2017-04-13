<properties
    pageTitle="Ved hjælp af PowerShell til konfiguration af programmet indsigt i en Azure | Microsoft Azure"
    description="Automatisere konfiguration af Azure diagnosticering til pipe til programmet indsigt."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/17/2015"
    ms.author="awills"/>

# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>Bruge PowerShell til at konfigurere programmet indsigt til en Azure-WebApp

[Microsoft Azure](https://azure.com) kan være [konfigureret til at sende Azure diagnosticering](app-insights-azure-diagnostics.md) til [Visual Studio programmet indsigt](app-insights-overview.md). Diagnosticering er relateret til Azure Cloud Services og Azure FOS. De supplement til telemetri, du sender fra App ved hjælp af programmet indsigt SDK. Som en del af automatisere processen med at oprette nye ressourcer i Azure, kan du konfigurere diagnosticering ved hjælp af PowerShell.

## <a name="azure-template"></a>Azure skabelon

Hvis WebApp er i Azure, og du opretter dine ressourcer ved hjælp af en skabelon til Azure Ressourcestyring, kan du konfigurere programmet indsigt ved at føje dette til noden ressourcer:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource`-et navn for programmet indsigt ressourcen
* `myWebAppName`-id'et for WebApp


## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Aktiver diagnosticering udvidelse som en del af installation af en skybaseret tjeneste

Den `New-AzureDeployment` cmdlet har en parameter `ExtensionConfiguration`, som tager en matrix med diagnosticering konfigurationer. Disse kan oprettes med den `New-AzureServiceDiagnosticsExtensionConfig` cmdlet. Eksempel:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Aktiver diagnosticering udvidelse på en eksisterende skytjeneste

Brug på en eksisterende tjeneste `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Få aktuelle diagnosticering lokalnummer konfiguration

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Fjerne diagnosticering filtypenavn

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Hvis du har aktiveret filtypenavnet diagnosticering ved hjælp af enten `Set-AzureServiceDiagnosticsExtension` eller `New-AzureServiceDiagnosticsExtensionConfig` uden parameteren rolle, kan du fjerne den lokalnummer ved hjælp af `Remove-AzureServiceDiagnosticsExtension` uden parameteren rolle. Hvis parameteren rolle blev brugt, når du aktiverer filtypenavnet skal derefter den også bruges når du fjerner filtypenavnet.

Sådan fjernes filtypenavnet diagnosticering fra hver enkelt rolle:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Se også

* [Overvåge Azure Cloud Services-apps med programmet indsigt](app-insights-cloudservices.md)
* [Sende Azure diagnosticering til programmet indsigt](app-insights-azure-diagnostics.md)
* [Automatisere konfiguration af beskeder](app-insights-powershell-alerts.md)

