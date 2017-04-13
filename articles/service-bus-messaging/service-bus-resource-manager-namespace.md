<properties
    pageTitle="Oprette et Service Bus navneområde ved hjælp af en ressourcestyring skabelon | Microsoft Azure"
    description="Brug Azure ressourcestyring skabelon til at oprette et Service Bus navneområde"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/04/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Oprette et Service Bus navneområde ved hjælp af en skabelon til Azure ressourcestyring

I denne artikel beskrives, hvordan du bruger en skabelon til Azure Ressourcestyring, opretter et Service Bus navneområde af typen **meddelelser** med en Standard/Basic SKU. I artiklen definerer også de parametre, der er angivet til udførelse af installationen. Du kan bruge denne skabelon til dine egne installationer eller tilpasse den for at opfylde dine behov.

Du kan finde flere oplysninger om at oprette skabeloner, skal du se [redigering Azure ressourcestyring skabeloner][].

Se [Service Bus navneområde skabelon][] på GitHub for skabelonen fuldført.

>[AZURE.NOTE] Følgende Azure ressourcestyring skabeloner er tilgængelige for hentning og installation. 
>
>-    [Oprette en begivenhed Hubs navneområde med en begivenhed Hub og forbrugere gruppe](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Oprette et Service Bus navneområde med kø](service-bus-resource-manager-namespace-queue.md)
>-    [Oprette et Service Bus navneområde med emne og abonnement](service-bus-resource-manager-namespace-topic.md)
>-    [Oprette et Service Bus navneområde med køen og godkendelse regel](service-bus-resource-manager-namespace-auth-rule.md)
>
>For at søge efter de seneste skabeloner, skal du besøge skabelongalleriet [Azure Hurtig start][] , og Søg efter Service Bus.

## <a name="what-will-you-deploy"></a>Hvad vil du installere?

Med denne skabelon, vil du installere et Service Bus navneområde med et [grundlæggende, Standard, eller førsteklasses](https://azure.microsoft.com/pricing/details/service-bus/) SKU.

For at køre installationen automatisk, skal du klikke på knappen følgende:

[![Installere på Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Parametre

Med Azure ressource Manager skal du definere parametre for værdier, du vil angive, hvornår skabelonen, der er installeret. Skabelonen indeholder afsnittet `Parameters` , der indeholder alle parameterværdierne. Du skal angive en parameter for de værdier, der varierer baseret på det projekt, du anvender eller baseret på det miljø, du installerer til. Ikke definere parametre for værdier, der forbliver altid det samme. Hver parameterværdi bruges i skabelonen til at definere de ressourcer, der er installeret.

Denne skabelon definerer følgende parametre.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Navnet på Service Bus navneområdet til at oprette.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebussku"></a>serviceBusSKU

Navnet på den tjeneste Bus [SKU](https://azure.microsoft.com/pricing/details/service-bus/) til at oprette.

```
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Basic", 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

Skabelonen definerer de værdier, der er tilladt for denne parameter (Basic, Standard eller Premium) og tildeler en standardværdi (Standard), hvis ingen værdi er angivet.

Du kan finde flere oplysninger om Service Bus priser, se [Service Bus priser og fakturering][].

### <a name="servicebusapiversion"></a>serviceBusApiVersion

Tjenesten Bus API-version af skabelonen.

```
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>Ressourcer til at udrulle

### <a name="service-bus-namespace"></a>Service Bus navneområde

Opretter et standard Service Bus navneområde af typen **meddelelser**.

```
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Kommandoer til at køre Installation

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Næste trin

Nu, hvor du har oprettet og installeret ressourcer ved hjælp af Azure Ressourcestyring, se, hvordan du administrere disse ressourcer ved at læse følgende artikler:

- [Administrere Service Bus med PowerShell](service-bus-powershell-how-to-provision.md)
- [Administrere Service Bus ressourcer med tjenesten Bus Stifinder](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Redigering Azure ressourcestyring skabeloner]: ../resource-group-authoring-templates.md
  [Service Bus navneområde skabelon]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
  [Azure Hurtig start skabeloner]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Service Bus priser og fakturering]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
