<properties
    pageTitle="Oprette en regel til Service Bus godkendelse ved hjælp af en skabelon til Azure ressourcestyring | Microsoft Azure"
    description="Oprette en regel til godkendelse af Service Bus for navneområde og kø ved hjælp af Azure ressourcestyring skabelon"
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

# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Oprette en regel til godkendelse af Service Bus for navneområde og kø ved hjælp af en skabelon til Azure ressourcestyring

I denne artikel viser, hvordan du bruger en skabelon til Azure Ressourcestyring, opretter en [regel for godkendelse](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) for en Service Bus navneområde og kø. Du skal lære hvordan til at definere hvilke ressourcer er installeret, og hvordan du kan definere parametre, der er angivet, når installationen er udført. Du kan bruge denne skabelon til dine egne installationer eller tilpasse den for at opfylde dine behov.

Du kan finde flere oplysninger om at oprette skabeloner, skal du se [redigering Azure ressourcestyring skabeloner][].

Se [Service Bus auth regelskabelon][] på GitHub for skabelonen fuldført.

>[AZURE.NOTE] Følgende Azure ressourcestyring skabeloner er tilgængelige for hentning og installation.
>
>-    [Oprette en begivenhed Hubs navneområde med en begivenhed Hub og forbrugere gruppe](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Oprette et Service Bus navneområde med kø](service-bus-resource-manager-namespace-queue.md)
>-    [Oprette et Service Bus navneområde med emne og abonnement](service-bus-resource-manager-namespace-topic.md)
>-    [Oprette et Service Bus navneområde](service-bus-resource-manager-namespace.md)
>
>For at søge efter de seneste skabeloner, skal du besøge skabelongalleriet [Azure Hurtig start][] , og Søg efter "Service Bus."

## <a name="what-will-you-deploy"></a>Hvad vil du installere?

Med denne skabelon, vil du installere en Service Bus godkendelse regel for en navneområde og SMS objekt (i dette tilfælde en kø).

Denne skabelon bruger [Delt Access signatur (SAS)](service-bus-sas-overview.md) til godkendelse. SAS gør det muligt for programmer til at godkende Service Bus ved hjælp af en hurtigtast, der er konfigureret på navneområdet eller på SMS objektet (kø eller emne), som er knyttet specifikke rettigheder. Derefter kan du bruge denne tast til at oprette et SAS token, kunder kan også bruge til at godkende Service Bus.

For at køre installationen automatisk, skal du klikke på knappen følgende:

[![Installere på Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

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

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName 

Navnet på godkendelsesreglen for til navneområdet.

```
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

Navnet på køen i navneområdet Service Bus.

```
"serviceBusQueueName": {
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

Opretter et standard Service Bus navneområde af typen **meddelelser**og en Service Bus godkendelse regel for navneområde og enhed.

```
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

## <a name="commands-to-run-deployment"></a>Kommandoer til at køre Installation

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Næste trin

Nu, hvor du har oprettet og installeret ressourcer ved hjælp af Azure Ressourcestyring, se, hvordan du administrere disse ressourcer ved at få vist følgende artikler:

- [Administrere Service Bus med PowerShell](service-bus-powershell-how-to-provision.md)
- [Administrere Service Bus ressourcer med tjenesten Bus Stifinder](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)
- [Service Bus godkendelse og autorisation](service-bus-authentication-and-authorization.md)

  [Redigering Azure ressourcestyring skabeloner]: ../resource-group-authoring-templates.md
  [Azure Hurtig start skabeloner]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Service Bus auth regelskabelon]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
