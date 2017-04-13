<properties
    pageTitle="Oprette et Service Bus navneområde med emnet abonnement, og reglen ved hjælp af en skabelon til Azure ressourcestyring | Microsoft Azure"
    description="Oprette et Service Bus navneområde med emne, abonnement og regel ved hjælp af Azure ressourcestyring skabelon"
    services="service-bus"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Oprette et Service Bus navneområde med emne, abonnement og regel ved hjælp af en skabelon til Azure ressourcestyring

I denne artikel viser, hvordan du bruger en skabelon til Azure Ressourcestyring, opretter et Service Bus navneområde med et emne, abonnement og regel (filter). Du Lær, hvordan du definerer hvilke ressourcer er installeret, og hvordan du kan definere parametre, der er angivet, når installationen er udført. Du kan bruge denne skabelon til dine egne installationer eller tilpasse den for at opfylde dine behov

Du kan finde flere oplysninger om oprettelse af skabeloner, [redigering Azure ressourcestyring skabeloner][].

Du kan finde flere oplysninger om praksis og mønstre på Azure ressourcer navngivningskonventioner under [Azure regler for navngivning af ressourcer][].

Du kan finde skabelonen fuldført skabelonen [Service Bus navneområde med emne, abonnement, og regel][] .

>[AZURE.NOTE] Følgende Azure ressourcestyring skabeloner er tilgængelige for hentning og installation.
>
>-    [Oprette et Service Bus navneområde med køen og godkendelse regel](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Oprette et Service Bus navneområde med kø](service-bus-resource-manager-namespace-queue.md)
>-    [Oprette et Service Bus navneområde](service-bus-resource-manager-namespace.md)
>-    [Oprette et Service Bus navneområde med emne og abonnement](service-bus-resource-manager-namespace-topic.md)
>
>For at søge efter de seneste skabeloner, skal du besøge skabelongalleriet [Azure Hurtig start][] , og Søg efter Service Bus.

## <a name="what-will-you-deploy"></a>Hvad vil du installere?

Med denne skabelon kan installere du en Service Bus navneområde med emne, abonnement og regel (filter).

Giver en en-til-mange form for kommunikation, i et *Udgiv/Abonner* mønster [Service Bus emner og abonnementer](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) . Når du bruger emner og abonnementer Kommuniker komponenterne i et distribueret program ikke direkte med hinanden, i stedet for exchange de meddelelser via emne, der fungerer som mellemliggende. Et abonnement til et emne ligner en virtuel kø, der modtager kopier af meddelelser, der blev sendt til emnet. Et filter på abonnement kan du angive, hvilke meddelelser der sendes til et emne skal vises i et bestemt emne abonnement.

## <a name="what-are-rules-filters"></a>Hvad er regler (filtre)?

I mange situationer, skal meddelelser, der har specifikke egenskaber behandles på forskellige måder. Hvis du vil aktivere, kan du konfigurere abonnementer for at finde meddelelser, der har behov egenskaber og derefter udføre bestemte ændringer af disse egenskaber. Mens Service Bus abonnementer se alle meddelelser, som sendes til emnet, kan du kun kopiere et undersæt af disse meddelelser til køen virtuelle abonnement. Dette opnås ved hjælp af abonnement filtre. Du kan få mere at vide om rules(filters), se [Service Bus køer, emner og abonnementer][].

For at køre installationen automatisk, skal du klikke på knappen følgende:

[![Installere på Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametre

Med Azure Resource Manager skal du definere parametre for værdier, du vil angive, hvornår skabelonen, der er installeret. Skabelonen indeholder afsnittet `Parameters` , der indeholder alle parameterværdier. Du skal angive en parameter for de værdier, der varierer baseret på det projekt, du anvender eller baseret på det miljø, du installerer til. Ikke definere parametre for værdier, der altid forbliver de samme. Hver parameterværdi bruges i skabelonen til at definere de ressourcer, der er installeret.

Skabelonen definerer følgende parametre:

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
### <a name="servicebusrulename"></a>serviceBusRuleName

Navnet på den rule(filter), der er oprettet i navneområdet Service Bus.

```
   "serviceBusRuleName": {
   "type": "string",
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

Opretter et standard Service Bus navneområde af typen **meddelelser**med emne og abonnement og regler.

```
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
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
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Kommandoer til at køre Installation

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Næste trin

Nu, hvor du har oprettet og installeret ressourcer ved hjælp af Azure Ressourcestyring, se, hvordan du administrere disse ressourcer ved at få vist følgende artikler:

- [Administrere Azure Service Bus med Automation Azure](service-bus-automation-manage.md)
- [Administrere Service Bus med PowerShell](service-bus-powershell-how-to-provision.md)
- [Administrere Service Bus ressourcer med tjenesten Bus Stifinder](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Redigering Azure ressourcestyring skabeloner]: ../resource-group-authoring-templates.md
  [Azure Hurtig start skabeloner]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Azure ressourcer navngivningskonventioner]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
  [Service Bus navneområde med emne, abonnement og regel]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
  [Service Bus køer, emner og abonnementer]:service-bus-queues-topics-subscriptions.md
  
