<properties 
    pageTitle="Installere en WebApp, der er knyttet til en GitHub lager" 
    description="Bruge en skabelon til Azure ressourcestyring til at installere en WebApp, der indeholder et projekt fra en GitHub lager." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2016" 
    ms.author="cephalin"/>

# <a name="deploy-a-web-app-linked-to-a-github-repository"></a>Installere en WebApp, der er knyttet til et GitHub lager

I dette emne lærer du, hvordan du opretter en skabelon til Azure Ressourcestyring, installerer en WebApp, der er knyttet til et projekt i et GitHub-lager. Du skal lære hvordan til at definere hvilke ressourcer er installeret, og hvordan du kan definere parametre, der er angivet, når installationen er udført. Du kan bruge denne skabelon til dine egne installationer eller tilpasse den for at opfylde dine behov.

Se [Redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md), du kan finde flere oplysninger om oprettelse af skabeloner.

Se [Web App, der er knyttet til GitHub skabelon](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json)skabelonen fuldført.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="what-you-will-deploy"></a>Hvad du vil installere

Med denne skabelon, vil du installere en WebApp, der indeholder kode fra et projekt i GitHub.

For at køre installationen automatisk, skal du klikke på knappen følgende:

[![Installere på Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## <a name="parameters"></a>Parametre

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### <a name="repourl"></a>repoURL

URL-adressen til GitHub lager, der indeholder projektet, der skal installeres. Denne parameter indeholder en standardværdi, men denne værdi er kun beregnet til viser, hvordan du giver dig URL-adressen til lager. Du kan bruge denne værdi, når du tester skabelonen, men skal du angive URL-adressen dit eget lager, når du arbejder med skabelonen.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### <a name="branch"></a>gren

Forgrening af lager skal bruges, når du installerer programmet. Standardværdien er mastersider, men du kan angive navnet på en hvilken som helst gren i lageret, du vil installere.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## <a name="resources-to-deploy"></a>Ressourcer til at udrulle

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="web-app"></a>WebApp

Opretter den web-app, der er knyttet til projektet i GitHub. 

Du angiver navnet på online via parameteren **siteName** og placeringen af online via parameteren **siteLocation** . Skabelonen definerer i elementet **dependsOn** WebApp som afhængig af tjenesten vært plan. Fordi det er afhængig af den hosting plan, oprettes WebApp ikke, før den hosting plan er afsluttet, der oprettes. Elementet **dependsOn** bruges kun til at angive installation rækkefølge. Hvis du ikke markerer online som afhænger af den hosting plan, Azure ressource Mananger forsøger at oprette begge ressourcer på samme tid, og du kan modtage en fejl, hvis online oprettes før den hosting plan.

WebApp, har også en underordnet ressource, der er defineret i **ressourcer** nedenfor. Denne underordnede ressource definerer versionsstyring for det projekt, der er installeret med WebApp. I denne skabelon, er versionsstyring sammenkædet med et bestemt GitHub-lager. GitHub lager defineres med koden **"RepoUrl": "https://github.com/davidebbo-test/Mvc52Application.git"** du muligvis hårde kode lager URL-adressen, når du vil oprette en skabelon, der installerer flere gange et enkelt projekt og medfører det mindste antal parametre.
I stedet for hårde-kodning lager URL-adressen, kan du tilføje en parameter for lager URL-adressen og bruge den pågældende værdi for egenskaben **RepoUrl** .

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Kommandoer til at køre Installation

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 
