<properties
    pageTitle="Oprette et Service Bus navneområde med emne og abonnement ved hjælp af en skabelon til Azure ressourcestyring | Microsoft Azure"
    description="Oprette et Service Bus navneområde med emne og ved hjælp af Azure ressourcestyring skabelon-abonnement"
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
    ms.date="10/14/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Oprette et Service Bus navneområde med emne og abonnement ved hjælp af en skabelon til Azure ressourcestyring

I denne artikel viser, hvordan du bruger en skabelon til Azure Ressourcestyring, opretter et Service Bus navneområde med et emne og abonnement. Du skal lære hvordan til at definere hvilke ressourcer er installeret, og hvordan du kan definere parametre, der er angivet, når installationen er udført. Du kan bruge denne skabelon til dine egne installationer eller tilpasse den for at opfylde dine behov

Du kan finde flere oplysninger om at oprette skabeloner, skal du se [redigering Azure ressourcestyring skabeloner][].

Du kan finde skabelonen fuldført skabelonen [Service Bus navneområde med emne og abonnement][] .

>[AZURE.NOTE] Følgende Azure ressourcestyring skabeloner er tilgængelige for hentning og installation.
>
>-    [Oprette et Service Bus navneområde med køen og godkendelse regel](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Oprette et Service Bus navneområde med kø](service-bus-resource-manager-namespace-queue.md)
>-    [Oprette et Service Bus navneområde](service-bus-resource-manager-namespace.md)
>-    [Oprette en begivenhed Hubs navneområde med en begivenhed Hub og forbrugere gruppe](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>
>For at søge efter de seneste skabeloner, skal du besøge skabelongalleriet [Azure Hurtig start][] , og Søg efter "Service Bus."

## <a name="what-will-you-deploy"></a>Hvad vil du installere?

Med denne skabelon, vil du installere et Service Bus navneområde med emne og abonnement.

Giver en en-til-mange form for kommunikation, i et *Udgiv/Abonner* mønster [Service Bus emner og abonnementer](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) .

For at køre installationen automatisk, skal du klikke på knappen følgende:

[![Installere på Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parametre

Med Azure ressource Manager skal du definere parametre for værdier, du vil angive, hvornår skabelonen, der er installeret. Skabelonen indeholder afsnittet `Parameters` , der indeholder alle parameterværdierne. Du skal angive en parameter for de værdier, der varierer baseret på det projekt, du anvender eller baseret på det miljø, du installerer til. Ikke definere parametre for værdier, der forbliver altid det samme. Hver parameterværdi bruges i skabelonen til at definere de ressourcer, der er installeret.

Skabelonen definerer følgende parametre.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Navnet på Service Bus navneområdet til at oprette.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Navnet på det emne, der er oprettet i navneområdet Service Bus.

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Navnet på det abonnement, der er oprettet i navneområdet Service Bus.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

Tjenesten Bus API-version af skabelonen.

```
"serviceBusApiVersion": {
"type": "string"
}
```
## <a name="resources-to-deploy"></a>Ressourcer til at udrulle

Opretter et standard Service Bus navneområde af typen **meddelelser**med emne og abonnement.

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Kommandoer til at køre Installation

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Næste trin

Nu, hvor du har oprettet og installeret ressourcer ved hjælp af Azure Ressourcestyring, se, hvordan du administrere disse ressourcer ved at få vist følgende artikler:

- [Administrere Service Bus med PowerShell](service-bus-powershell-how-to-provision.md)
- [Administrere Service Bus ressourcer med tjenesten Bus Stifinder](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Redigering Azure ressourcestyring skabeloner]: ../resource-group-authoring-templates.md
  [Azure Hurtig start skabeloner]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Service Bus navneområde med emne og abonnement]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
