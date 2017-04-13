<properties
    pageTitle="Oprette en begivenhed Hubs navneområde med begivenhed Hub og forbrugere gruppe ved hjælp af en skabelon til Azure ressourcestyring | Microsoft Azure"
    description="Oprette en begivenhed Hubs navneområde med begivenhed Hub og forbrugere gruppe ved hjælp af Azure ressourcestyring skabelon"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Oprette en begivenhed Hubs navneområde med begivenhed Hub og forbrugere gruppe ved hjælp af en skabelon til Azure ressourcestyring

Denne artikel beskrives, hvordan du bruger en skabelon til Azure Ressourcestyring, opretter en begivenhed Hubs navneområde med en begivenhed Hub og en forbruger gruppe. Du skal lære hvordan til at definere hvilke ressourcer er installeret, og hvordan du kan definere parametre, der er angivet, når installationen er udført. Du kan bruge denne skabelon til dine egne installationer eller tilpasse den for at opfylde dine behov

Du kan finde flere oplysninger om at oprette skabeloner, skal du se [redigering Azure ressourcestyring skabeloner][].

Se [begivenhed Hub og forbrugere Gruppeskabelon][] på GitHub for skabelonen fuldført.

>[AZURE.NOTE]
>For at søge efter de seneste skabeloner, skal du besøge skabelongalleriet [Azure Hurtig start][] , og Søg efter begivenhed Hubs.

## <a name="what-will-you-deploy"></a>Hvad vil du installere?

Med denne skabelon, vil du installere en begivenhed Hubs navneområde med en begivenhed Hub og en forbruger gruppe.

[Begivenhed Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) er en begivenhed, behandling af tjeneste, der bruges til at give begivenheden og telemetri vandindtrængen til Azure massive skaleres lav ventetid og høj pålidelighed.

For at køre installationen automatisk, skal du klikke på knappen følgende:

[![Installere på Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Parametre

Med Azure ressource Manager skal du definere parametre for værdier, du vil angive, hvornår skabelonen, der er installeret. Skabelonen indeholder afsnittet `Parameters` , der indeholder alle parameterværdierne. Du skal angive en parameter for de værdier, der varierer baseret på det projekt, du anvender eller baseret på det miljø, du installerer til. Ikke definere parametre for værdier, der forbliver altid det samme. Hver parameterværdi bruges i skabelonen til at definere de ressourcer, der er installeret.

Skabelonen definerer følgende parametre.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Navnet på begivenhed Hubs navneområdet til at oprette.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

Navnet på den begivenhed Hub oprettet i Hubs navneområde.

```
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

Navnet på gruppen consumer oprettet for begivenhed hubben.

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

API-version af skabelonen.

```
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Ressourcer til at udrulle

Opretter et navneområde af typen **EventHubs**, med en begivenhed Hub og en forbruger gruppe.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Kommandoer til at køre Installation

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[Redigering Azure ressourcestyring skabeloner]: ../resource-group-authoring-templates.md
[Azure Hurtig start skabeloner]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Begivenhed Hub og forbrugere Gruppeskabelon]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
