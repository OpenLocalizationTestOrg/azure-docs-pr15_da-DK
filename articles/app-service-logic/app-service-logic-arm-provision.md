<properties 
    pageTitle="Oprette en logik App ved hjælp af Azure ressourcestyring skabeloner i Azure App Service | Microsoft Azure" 
    description="Bruge en skabelon til Azure ressourcestyring til at installere en tom logik App til at definere arbejdsprocesser." 
    services="logic-apps" 
    documentationCenter="" 
    authors="MSFTMan" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/25/2016" 
    ms.author="deonhe"/>

# <a name="create-a-logic-app-using-a-template"></a>Oprette en logik App ved hjælp af en skabelon

Bruge en skabelon til Azure ressourcestyring til at oprette en tom logik app, der kan bruges til at definere arbejdsprocesser. Du kan angive, hvilke ressourcer er installeret, og hvordan du kan definere parametre, der er angivet, når installationen er udført. Du kan bruge denne skabelon til dine egne installationer eller tilpasse den for at opfylde dine behov.

Du kan finde flere oplysninger om egenskaberne logik app [Logik App arbejdsproces Management API](https://msdn.microsoft.com/library/azure/mt643788.aspx). 

Du kan finde eksempler på definitionen selve [forfatter logik App definitioner](app-service-logic-author-definitions.md). 

Se [Redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md), du kan finde flere oplysninger om oprettelse af skabeloner.

Du kan finde skabelonen fuldført [logik App skabelon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Hvad du vil installere

Med denne skabelon kan installere du en logik app.

Vælg følgende knap for at køre installationen automatisk skal:  

[![Installere på Azure](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>Parametre

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }
    
## <a name="resources-to-deploy"></a>Ressourcer til at udrulle

### <a name="logic-app"></a>Logik app

Opretter appen logik.

Skabelonerne bruger en parameterværdi for logik app-navn. Det angiver placeringen af appen logik til samme placering, som ressourcegruppen. 

Denne bestemt definition kører én gang i timen og Pinger den placering, der er angivet i parameteren **testUri** . 

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## <a name="commands-to-run-deployment"></a>Kommandoer til at køre Installation

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 
