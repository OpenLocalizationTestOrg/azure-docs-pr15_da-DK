<properties
    pageTitle="Høj tæthed hoster på Azure App Service | Microsoft Azure"
    description="Høj tæthed hoster på Azure App Service"
    authors="btardif"
    manager="wpickett"
    editor=""
    services="app-service\web"
    documentationCenter=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"/>

# <a name="high-density-hosting-on-azure-app-service"></a>Høj tæthed hoster på Azure App Service

Når du bruger App Service, Afkoblet dit program fra den kapacitet, der er tildelt til den af to begreber:

- **Programmet:** Repræsenterer appen og dens runtime-konfiguration. For eksempel indeholder den versionen af .NET, der skal indlæses på kørselstidspunktet, indstillinger for osv.

- **App-serviceaftale:** Definerer karakteristika af kapacitet, tilgængelige funktionssæt og lokalitet af programmet. For eksempel være egenskaber store (fire kerner) maskine, fire forekomster, Premium-funktioner i af US.

En app er altid knyttet til en App Service-plan, men en App-serviceaftale kan give kapacitet til en eller flere apps.

Som et resultat, indeholder platformen fleksibilitet til at isolere en enkelt app eller har flere apps dele ressourcer ved at dele en App-serviceaftale.

Når flere apps deler en App-serviceaftale, kører en forekomst af denne app dog på hver forekomst af serviceaftale, som App.

## <a name="per-app-scaling"></a>Hver app skalering
*Per app skalering* er en funktion, der kan aktiveres på niveauet App Service plan og derefter bruges i programmet.

Hver app skaleres skalering en app uafhængigt af den App serviceaftale, der er vært den. Denne måde, en App-serviceaftale kan være konfigureret til at levere 10 forekomster, men kan angives en app til at skalere til kun 5 af dem.

Følgende Azure ressourcestyring skabelon opretter en App Service-plan, der er skaleret 10 forekomster og en app, som er konfigureret til at bruge per app skalering og skalere til kun 5 forekomster.

App-serviceaftale er angive egenskaben **enkelte websted skalering** til SAND ( `"perSiteScaling": true`). Appen er ved at **antal medarbejdere** , der bruges til 5 (`"properties": { "numberOfWorkers": "5" }`).

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "5" }
             } ]
         }]
    }


## <a name="recommended-configuration-for-high-density-hosting"></a>Anbefalet konfiguration for stor tæthed vært

Per app skalering er en funktion, der er aktiveret i både offentlige Azure områder og App-tjenesten miljøer. Den anbefalede strategi er dog bruge App Service miljøer til Udnyt deres avancerede funktioner og større grupper af kapacitet.  

Følg disse trin for at konfigurere høj tæthed, der er vært for dine apps:

1. Konfigurere App Service-miljø, og vælg en kollega pulje, der er dedikeret til høj tæthed hosting scenariet.

1. Oprette en enkelt App Service-plan, og tilpasse den for at bruge den tilgængelige kapacitet på arbejder puljen.

1. Angiv det enkelte websted skalering flag til sand på App-serviceaftale.

1. Nye websteder er oprettet og tildelt til denne App Service plan med egenskaben **numberOfWorkers** , der er angivet til **1**. Med denne konfiguration giver den højeste tætheden af mulige i denne arbejder puljen.

1. Antallet af medarbejdere kan konfigureres særskilt per websted for at give yderligere ressourcer efter behov. En høj Brug websted kan for eksempel indstillet **numberOfWorkers** til **3** for at have flere behandling kapacitet til app, mens lav Brug websteder vil angive **numberOfWorkers** til **1**.
