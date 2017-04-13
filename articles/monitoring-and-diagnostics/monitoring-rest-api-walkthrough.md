<properties
    pageTitle="Azure overvågning RESTEN API gennemgang | Microsoft Azure"
    description="Om at godkende anmodninger om til og bruge Azure overvågning REST-API."
    authors="mcollier, rboucher"
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
    ms.date="09/27/2016"
    ms.author="mcollier"/>

# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure overvågning RESTEN API gennemgang
I denne artikel beskrives, hvordan til at udføre godkendelse, så din kode kan bruge [Microsoft Azure skærm RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

Azure skærm API gør det muligt at hente de tilgængelige standard metriske definitioner (typen af metrik såsom CPU-tid, anmodninger, osv.), granularitet og metriske værdier fra et program. Når hentet, kan gemmes data i et separat datalager som Azure SQL-Database, DocumentDB eller Azure Data sø. Derfra kan kun udføres yderligere analyser efter behov.

Ud over at arbejde med forskellige metriske datapunkter, som i denne artikel beskrives, gør API'EN skærm det muligt at få vist regler for påmindelser, Vis aktivitetslogfiler og meget mere. Se [Microsoft Azure skærm RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn931943.aspx)til en komplet liste over tilgængelige handlinger.

## <a name="authenticating-azure-monitor-requests"></a>Godkendelse af anmodninger om Azure skærm

Det første trin er at godkende anmodningen.

Alle de opgaver, der køres i forhold til Azure skærm API Brug Azure ressourcestyring godkendelse modellen. Derfor skal alle anmodninger godkendes med Azure Active Directory (Azure AD). En metode til at godkende klientprogrammet er at oprette en Azure AD service hovedstolen og hente tokenet godkendelse (JWT). Følgende eksempelscript viser oprettelse af en Azure AD-tjeneste vigtigste via PowerShell. Se i dokumentationen til [Brug af Azure PowerShell for at oprette en sikkerhedskonto til at få adgang til ressourcer tjeneste](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password—powershell)for en mere detaljeret gennemgang. Det er også muligt at [oprette en tjeneste princippet via Azure-portalen](../resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Hvis du vil forespørge Azure skærm API, Brug klientprogrammet tidligere oprettet service hovedstolen til at godkende. I følgende eksempel PowerShell-script viser én tilgang hjælp af [Active Directory Authentication Library](../active-directory/active-directory-authentication-libraries.md) (ADAL) for at få JWT godkendelse token. JWT tokenet sendes som en del af en HTTP-godkendelse parameter i anmodninger til Azure skærm REST-API.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)
 
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values 
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Når trinnet godkendelse installationen er fuldført, kan forespørgsler udføres på Azure skærm REST-API. Der er to nyttige forespørgsler:

1. Liste over de metriske definitioner for en ressource

2. Hente metriske værdier


## <a name="retrieve-metric-definitions"></a>Hente metriske definitioner
>[AZURE.NOTE] For at hente metriske definitionerne ved hjælp af Azure skærm REST-API skal du bruge "2016-03-01" som API-version.

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
I en Azure logik App, vil metriske definitionerne vises svarende til følgende skærmbillede:

![Alt "JSON visning af metriske definition svar".](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

Du kan finde flere oplysninger i dokumentationen [listen metriske definitionerne for en ressource i Azure skærm REST-API](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Hente metriske værdier
Når de tilgængelige metriske definitioner er bekendt, er du derefter hente de relaterede metriske værdier. Bruge den metrikværdi navn 'værdi' (ikke ' localizedValue') til filtrering anmodninger (for eksempel hente 'CpuTime' og 'Anmoder om' metriske datapunkterne). Hvis ingen filtre, der ikke er angivet, returneres standard metrisk.

>[AZURE.NOTE] For at hente metriske værdier ved hjælp af Azure skærm REST-API skal du bruge "2016-06-01" som API-version.

**Metode**: få

**Request URI**: https://management.azure.com/subscriptions/_{abonnement-id}_/resourceGroups/_{ressource-gruppens navn}_/providers/_{ressource-udbyder-navneområdet}_/_{ressourcetype}_/_{ressourcenavn}_/providers/microsoft.insights/metrics?$filter=_{filteret}_og api-version =_{apiVersion}_

For eksempel for at hente RunsSucceeded metriske datapunkterne for det angivne tidsinterval og et klokkeslæt korn, 1 time, ville anmodningen være på følgende måde:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

Resultatet vil se ud som eksempel følger skærmbillede:

![Alt "JSON svar med gennemsnitlige svartid metrikværdi"](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

Hvis du vil hente flere data eller sammenlægning punkter, Tilføj metriske definition navne og sammenlægning typer til filteret, som det fremgår i følgende eksempel:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>Brug ARMClient
Et alternativ til at bruge PowerShell (som vist ovenfor), er at bruge [ARMClient](https://github.com/projectkudu/ARMClient) på din Windows-computer. ARMClient håndterer Azure AD-godkendelse (og resulterende JWT token) automatisk. Følgende trin oprettes en disposition for brug af ARMClient til hentning af metriske data:

1. Installer [Chocolatey](https://chocolatey.org/) og [ARMClient](https://github.com/projectkudu/ARMClient).

2. Skriv _armclient.exe login_i et terminal vindue. Dette beder dig om at logge på Azure.

3. Skriv _armclient få [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01_

4. Skriv _armclient få [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-06-01_


![Alt "Ved hjælp af ARMClient til at arbejde med Azure overvågnings REST-API"](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)


## <a name="retrieve-the-resource-id"></a>Hente ressource-ID
Brug af REST-API virkelig hjælper med at forstå de tilgængelige metriske definitioner, granularitet og relaterede værdier. Disse oplysninger er nyttigt, når du bruger [Azure Management bibliotek](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Til den foregående kode er ressource-ID til at bruge den fulde sti til den ønskede Azure ressource. Hvis du vil forespørge mod en Azure-WebApp, bliver ressource-ID for eksempel:

*/subscriptions/{Subscription-id}/resourceGroups/{Resource-Group-Name}/Providers/Microsoft.Web/Sites/{site-Name}/*

Følgende liste indeholder nogle få eksempler på ressource-ID formater for forskellige Azure ressourcer:

* **IoT Hub** - /subscriptions/_{abonnement-id}_/resourceGroups/_{ressource-gruppens navn}_/providers/Microsoft.Devices/IotHubs/_{iot-hub-navn}_

* **Elastiske SQL puljen** - /subscriptions/_{abonnement-id}_/resourceGroups/_{ressource-gruppens navn}_/providers/Microsoft.Sql/servers/_{puljen db}_/elasticpools/_{sql-puljen-navn}_

* **SQL-Database (version 12)** - /subscriptions/_{abonnement-id}_/resourceGroups/_{ressource-gruppens navn}_/providers/Microsoft.Sql/servers/_{servernavn}_/databases/_{Databasenavn}_

* **Service Bus** - /subscriptions/_{abonnement-id}_/resourceGroups/_{ressource-gruppens navn}_/providers/Microsoft.ServiceBus/_{navneområdet}_/_{servicebus-name}_

* **VM skala sæt** - /subscriptions/_{abonnement-id}_/resourceGroups/_{ressource-gruppens navn}_/providers/Microsoft.Compute/virtualMachineScaleSets/_{vm-name}_

* **FOS** - /subscriptions/_{abonnement-id}_/resourceGroups/_{ressource-gruppens navn}_/providers/Microsoft.Compute/virtualMachines/_{vm-name}_

* **Begivenhed Hubs** - /subscriptions/_{abonnement-id}_/resourceGroups/_{ressource-gruppens navn}_/providers/Microsoft.EventHub/namespaces/_{eventhub-navneområdet}_


Der findes alternative metoder til at hente den ressource-ID, herunder brug af Azure ressource Explorer, få vist den ønskede ressource i portalen Azure og via PowerShell eller Azure CLI.

### <a name="azure-resource-explorer"></a>Azure ressource Explorer
For at finde den ressource-ID for en ønskede ressource, er en praktisk metode til at bruge værktøjet [Azure ressource Explorer](https://resources.azure.com) . Gå til den ønskede ressource og derefter se på det ID, der vises, som i følgende skærmbillede:

![Alt "Azure ressource Stifinder"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure-portalen
Ressource-ID kan også fås fra Azure-portalen. Gå til den ønskede ressource for at gøre det, og vælg derefter Egenskaber. Ressource-ID vises i bladet egenskaber, som det fremgår følgende skærmbillede:

![Alt "ressource-ID vises i bladet egenskaber i portalen Azure"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
Ressource-ID kan hentes ved hjælp af Azure PowerShell-cmdletter. For eksempel for at få ressource-ID for en Azure-WebApp skal udføre Cmdletten Get-AzureRmWebApp som i følgende skærmbillede:

![Alt "ressource-ID hentet via PowerShell"](./media\monitoring-rest-api-walkthrough\resourceid_powershell.png)

### <a name="azure-cli"></a>Azure CLI
For at hente den ved hjælp af Azure CLI ressource-ID, skal du udføre kommandoen 'azure webapp Vis', der angiver den '--json' alt, som vist på følgende skærmbillede:

![Alt "ressource-ID hentet via PowerShell"](./media\monitoring-rest-api-walkthrough\resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>Hente aktivitet logdata
Ud over at arbejde med metriske definitioner og relaterede værdier er det også muligt at hente yderligere interessante viden, der er relateret til Azure ressourcer. Det er muligt at forespørgselsdata [aktivitetslog](https://msdn.microsoft.com/library/azure/dn931934.aspx) som et eksempel. I følgende eksempel demonstrerer brugen af Azure skærm REST-API til forespørgsel aktivitet logdata i et bestemt datointerval for et Azure-abonnement:

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>Næste trin
* Gennemgå [Oversigt over overvågning](monitoring-overview.md).
* Få vist de [understøttede målepunkter med Azure skærm](monitoring-supported-metrics.md).
* Gennemse [Microsoft Azure skærm RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Gennemse [Azure Management bibliotek](https://msdn.microsoft.com/library/azure/mt417623.aspx).
