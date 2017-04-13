<properties 
    pageTitle="Klargøre en Redis Cache | Microsoft Azure" 
    description="Brug Azure ressourcestyring skabelon til at installere en Azure Redis Cache." 
    services="app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="web" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="sdanie"/>

# <a name="create-a-redis-cache-using-a-template"></a>Oprette en Redis Cache ved hjælp af en skabelon

I dette emne, du få mere at vide, hvordan du opretter en skabelon til Azure Ressourcestyring, installerer en Azure Redis Cache. Cachen kan bruges med en eksisterende konto lagerplads til at holde diagnosticering data. Du kan også lære hvordan til at definere hvilke ressourcer er installeret, og hvordan du kan definere parametre, der er angivet, når installationen er udført. Du kan bruge denne skabelon til dine egne installationer eller tilpasse den for at opfylde dine behov.

Indstillinger for diagnosticering deles på nuværende tidspunkt for alle cache i samme område til et abonnement. Opdatere en cache i området påvirker alle andre cache i området.

Se [Redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md), du kan finde flere oplysninger om oprettelse af skabeloner.

Du kan finde skabelonen fuldført [Redis Cache skabelon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

>[AZURE.NOTE] Ressourcestyring skabeloner til det nye [Premium niveau](cache-premium-tier-intro.md) er tilgængelige. 
>
>-    [Oprette en Premium Redis Cache med klynge](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
>-    [Oprette Premium Redis Cache med bevarelse af data](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
>-    [Oprette Premium Redis Cache med VNet og valgfrit klynge](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
>
>For at søge efter de seneste skabeloner, skal du se [Azure Hurtig start skabeloner](https://azure.microsoft.com/documentation/templates/) og søge efter `Redis Cache`.

## <a name="what-you-will-deploy"></a>Hvad du vil installere

I denne skabelon, vil du installere en Azure Redis Cache, som bruger en eksisterende konto lagerplads til diagnosticering data.

For at køre installationen automatisk, skal du klikke på knappen følgende:

[![Installere på Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametre

Med Azure ressource Manager skal du definere parametre for værdier, du vil angive, hvornår skabelonen, der er installeret. Skabelonen indeholder en sektion med navnet parametre, der indeholder alle parameterværdierne.
Du skal angive en parameter for de værdier, der varierer baseret på det projekt, du anvender eller baseret på det miljø, du installerer til. Ikke definere parametre for værdier, der altid forbliver de samme. Hver parameterværdi bruges i skabelonen til at definere de ressourcer, der er installeret. 


[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation

Placeringen af den Redis Cache. Bedste ydeevne ved at bruge den samme placering som app skal bruges sammen med cachen.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName

Navnet på den eksisterende lagerplads konto skal bruges til diagnosticering. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort

En boolesk værdi, der angiver, om du vil give adgang via ikke SSL-porte.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus

En værdi, der angiver, om diagnosticering er aktiveret. Brug til eller fra.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## <a name="resources-to-deploy"></a>Ressourcer til at udrulle

### <a name="redis-cache"></a>Redis Cache

Opretter Azure Redis cachen.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Kommandoer til at køre Installation

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)] 

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### <a name="azure-cli"></a>Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


