<properties
    pageTitle="Oprette Service Bus ressourcer ved hjælp af Azure ressourcestyring skabeloner | Microsoft Azure"
    description="Brug Azure ressourcestyring skabeloner til at automatisere oprettelse af Service Bus ressourcer"
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
    ms.author="sethm"/>

# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Oprette Service Bus ressourcer ved hjælp af Azure ressourcestyring skabeloner

I denne artikel beskrives, hvordan du opretter og installerer Service Bus og begivenhed Hubs ressourcer ved hjælp af Azure ressourcestyring skabeloner, PowerShell og provideren Service Bus ressource.

Azure ressourcestyring skabeloner hjælpe dig med at definere ressourcerne, der skal installeres til en løsning, og til at angive parametre og variabler, der gør det muligt at inputværdier til forskellige miljøer. Skabelonen består af JSON og udtryk, som du kan bruge til at oprette værdier til din installation. Du kan finde detaljerede oplysninger om at skrive Azure ressourcestyring skabeloner og en beskrivelse af Skabelonformatet [redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md). 

>[AZURE.NOTE] Eksemplerne i denne artikel viser, hvordan du bruger Azure ressourcestyring til at oprette en Service Bus navneområde og SMS enhed (kø). Finde andre skabelon eksempler på [skabelongalleriet Azure Hurtig start][] , og Søg efter "Service Bus."

## <a name="service-bus-and-event-hubs-resource-manager-templates"></a>Tjenesten Bus og begivenhed Hubs ressourcestyring skabeloner

Disse Service Bus og begivenhed Hubs Azure ressourcestyring skabeloner er tilgængelige for hentning og installation. Klik på linkene nedenfor for at få oplysninger om hver af dem, med links til skabeloner på GitHub: 

- [Oprette et Service Bus navneområde](service-bus-resource-manager-namespace.md)
- [Oprette et Service Bus navneområde med kø](service-bus-resource-manager-namespace-queue.md)
- [Oprette et Service Bus navneområde med emne og abonnement](service-bus-resource-manager-namespace-topic.md)
- [Oprette et Service Bus navneområde med køen og godkendelse regel](service-bus-resource-manager-namespace-auth-rule.md)
- [Oprette en begivenhed Hubs navneområde med en begivenhed Hub og forbrugere gruppe](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)

## <a name="deploy-with-powershell"></a>Installere med PowerShell

Følgende procedure beskriver, hvordan du bruge PowerShell til at installere en Azure ressourcestyring skabelon, der opretter et **Standard** niveau Service Bus navneområde og en kø i pågældende navneområdet. I dette eksempel er baseret på skabelonen [Opret et Service Bus navneområde med kø](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) . Omtrentlig arbejdsprocessen er som følger:

1. Installere PowerShell.
2. Oprette skabelonen og (valgfrit) en parameterfil.
2. Log på din Azure-konto i PowerShell.
3. Oprette en ny ressourcegruppe, hvis der ikke findes.
4. Test installationen.
5. Hvis du vil, kan du angive tilstanden installation.
6. Installer skabelonen.

Du kan finde detaljerede oplysninger om installation af Azure ressourcestyring skabeloner, [Implementer ressourcer med Azure ressourcestyring skabeloner][].

### <a name="install-powershell"></a>Installere PowerShell

Installer Azure PowerShell ved at følge vejledningen i, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

### <a name="create-a-template"></a>Oprette en skabelon

Klone, eller kopiere skabelonen [201-servicebus-Opret-kø](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) fra GitHub:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
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
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-optional"></a>Oprette en parametre-fil (valgfrit)

For at bruge en valgfrie parametre fil skal du kopiere [201-servicebus-Opret-kø](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) filen. Erstat værdien af `serviceBusNamespaceName` med navnet på den tjeneste Bus navneområde, du vil oprette i denne installation, og Erstat værdien af `serviceBusQueueName` med navnet på den kø, du vil oprette. 

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

Du kan få mere at vide under emnet [parameterfil](../resource-group-template-deploy.md#parameter-file) .

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Log på Azure og angive Azure-abonnement

Kør følgende kommando fra en PowerShell-prompt:

```
Login-AzureRmAccount
```

Du bliver bedt om at logge på din Azure-konto. Når du logger på, at køre følgende kommando for at få vist dine tilgængelige abonnementer.

```
Get-AzureRMSubscription
```

Denne kommando returnerer en liste over tilgængelige Azure abonnementer. Vælg et abonnement til den aktuelle session ved at køre følgende kommando. Erstatte `<YourSubscriptionId>` med GUID for det Azure abonnement, du vil bruge.

```
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Angive ressourcegruppen

Hvis du ikke har en eksisterende ressourcegruppe, kan du oprette en ny ressourcegruppe med kommandoen **Ny AzureRmResourceGroup** . Angiv navnet på den ressourcegruppe og placering, du vil bruge. Eksempel:

```
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Hvis det lykkes, vises en oversigt over den nye ressourcegruppe.

```
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Test installationen

Valider installationen ved at køre den `Test-AzureRmResourceGroupDeployment` cmdlet. Når du tester installationen, du give parametre, præcis, som du ville gøre, når du udfører installationen.

```
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Oprette installationen

Hvis du vil oprette den nye installation, skal du køre den `New-AzureRmResourceGroupDeployment` kommando, og Angiv de nødvendige parametre, når du bliver bedt om. Parametrene omfatter et navn til din installation, navnet på din ressourcegruppe, og den sti eller URL-adressen til skabelonfilen. Hvis parameteren **tilstand** ikke er angives, bruges af standardværdien for **tilvækst** . Få mere at vide under [tilvækst og fuldført installationer](../resource-group-template-deploy.md#incremental-and-complete-deployments).

Følgende kommando beder dig om de tre parametre i vinduet PowerShell:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Brug følgende kommando til at angive en parametre fil i stedet.

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Du kan også bruge indbygget parametre, når du kører Cmdletten installation. Kommandoen er som følger:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

For at køre en [komplet](../resource-group-template-deploy.md#incremental-and-complete-deployments) implementering skal du indstille parameteren **tilstand** til **færdigt**:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

### <a name="verify-the-deployment"></a>Kontrollere installationen

Hvis ressourcerne, der er installeret, vises en oversigt over installationen i vinduet PowerShell:

```
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>Næste trin

Du har nu set grundlæggende arbejdsproces og kommandoer til implementering af en skabelon til Azure ressourcestyring. Besøg følgende links for at få mere detaljerede oplysninger:

- [Oversigt over Azure ressourcestyring][]
- [Implementere ressourcer med Azure ressourcestyring skabeloner][]
- [Redigering af skabeloner](../resource-group-authoring-templates.md)


[Oversigt over Azure ressourcestyring]: ../resource-group-overview.md
[Implementere ressourcer med Azure ressourcestyring skabeloner]: ../resource-group-template-deploy.md
[Azure skabelongalleriet i Hurtig start]: https://azure.microsoft.com/documentation/templates/?term=service+bus