<properties 
    pageTitle="PowerShell-script til at oprette en programmet indsigt ressource" 
    description="Automatisere oprettelse af programmet indsigt ressourcer." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2016" 
    ms.author="awills"/>

#  <a name="powershell-script-to-create-an-application-insights-resource"></a>PowerShell-script til at oprette en programmet indsigt ressource

*Programmet indsigt er i Vis udskrift.*

Når du vil overvåge et nyt program- eller en ny version af et program – med [Visual Studio programmet viden](https://azure.microsoft.com/services/application-insights/), skal konfigurere du en ny ressource i Microsoft Azure. Denne ressource er hvor telemetridata fra din app er analysere og vises. 

Du kan automatisere oprettelse af en ny ressource ved hjælp af PowerShell.

Eksempelvis hvis du udvikler en app på en mobilenhed, er det sandsynligvis, når som helst, vil der være flere udgivne versioner af din app i brug af dine kunder. Du vil ikke få telemetri resultater fra forskellige versioner blandet. Så får du build processen til at oprette en ny ressource for hver build.

## <a name="script-to-create-an-application-insights-resource"></a>Script til at oprette en programmet indsigt ressource

Se de relevante cmdlet specifikationer for:

* [Ny AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [Ny AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)


*PowerShell-Script*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 
# - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource

$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} `
  -ResourceType "Microsoft.Insights/Components" `
  -Location "Central US" `
  -PropertyObject @{"Type"="ASP.NET"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Hvad skal jeg gøre med iKey

Hver ressource er identificeret med dens instrumentation nøgle (iKey). IKey er output fra ressource oprettelse af script. Scriptet build kan give iKey i programmet indsigt SDK integreret i din app.

Der er to måder at gøre iKey tilgængeligt for SDK:
  
* I [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Eller [initialisering](app-insights-api-custom-events-metrics.md)kode: 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## <a name="see-also"></a>Se også

* [Oprette programmet indsigt og web test ressourcer fra skabeloner](app-insights-powershell.md)
* [Konfigurere overvågning af Azure diagnosticering med PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Angive beskeder ved hjælp af PowerShell](app-insights-powershell-alerts.md)

 