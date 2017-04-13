<properties 
    pageTitle="Administrer Azure søgning med Powershell-scripts | Microsoft Azure | Hostet skyen search-tjenesten" 
    description="Administrere din Azure søgetjenesten med PowerShell-scripts. Oprette eller opdatere en Azure Search-tjenesten og administrere Azure Søg administrator taster" 
    services="search" 
    documentationCenter="" 
    authors="seansaleh" 
    manager="mblythe" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="search" 
    ms.devlang="na" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="powershell" 
    ms.date="08/15/2016" 
    ms.author="seasa"/>

# <a name="manage-your-azure-search-service-with-powershell"></a>Administrere din Azure søgetjenesten med PowerShell
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [REST-API](search-get-started-management-api.md)

Dette emne beskrives PowerShell-kommandoer til at udføre mange af opgaverne til dokumentstyring for Azure Søg tjenester. Vi vil gennemgå oprettelse af en søgetjeneste, skalering og administration af dens API nøgler.
Disse kommandoer parallel management mulighederne i [Azure Søg Management REST-API](http://msdn.microsoft.com/library/dn832684.aspx).

## <a name="prerequisites"></a>Forudsætninger
 
- Du skal have Azure PowerShell 1.0 eller større. Flere oplysninger under [installere og konfigurere Azure PowerShell](../powershell-install-configure.md).
- Du skal være logget på abonnementet Azure i PowerShell, som beskrevet nedenfor.

Først skal du logge på Azure med denne kommando:

    Login-AzureRmAccount

Angiv mailadressen til din Azure-konto og adgangskoden i dialogboksen Microsoft Azure login.

Alternativt kan du [logon uden interaktion med en vigtigste tjeneste](../resource-group-authenticate-service-principal.md).

Hvis du har flere Azure abonnementer, skal du angive dit Azure-abonnement. Hvis du vil se en liste over dine aktuelle abonnementer, du Kør denne kommando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Hvis du vil angive abonnementet, skal du køre følgende kommando. I følgende eksempel på abonnementets navn er `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Kommandoer til at hjælpe dig med at komme i gang

    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search" -Force

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1
    
    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
    
    # View your resource
    $resource
    
    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key
    
    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
        
    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource
    
    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource
    
## <a name="next-steps"></a>Næste trin
    
Nu, hvor tjenesten er oprettet, kan du tage de næste trin: opbygge et [indeks](search-what-is-an-index.md), [forespørgsel et indeks](search-query-overview.md), og til sidst oprette og administrere dine egne søgeprogram, der bruger Azure søgning.

- [Oprette en Azure søgeindekset i portalen Azure](search-create-index-portal.md)

- [Forespørgsel en Azure søgeindekset ved hjælp af Search Explorer i portalen Azure](search-explorer.md)

- [Konfiguration af et indekseringsprogram at indlæse data fra andre tjenester](search-indexer-overview.md)

- [Sådan bruges Azure søgning i .NET](search-howto-dotnet-sdk.md)

- [Analysere dine Azure Søg trafik](search-traffic-analytics.md)
