<properties
   pageTitle="Ressourcestyring skabelon til at sammenkæde ressourcer | Microsoft Azure"
   description="Viser ressourcestyring skemaet til implementering af links mellem relaterede ressourcer til en skabelon."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="resource-links-template-schema"></a>Ressource links skabelon skema

Opretter en kæde mellem to ressourcer. Linket er anvendt en ressource, kendt som kilde ressourcen. Den anden ressource i feltet link kaldes target ressourcen.

## <a name="schema-format"></a>Skema format

For at oprette et link, skal du tilføje følgende skema i afsnittet ressourcer af din skabelon.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## <a name="values"></a>Værdier

I følgende tabel beskrives de værdier, du vil angive i skemaet.

| Navn | Værdi |
| ---- | ---- |
| type | Optæl<br />Påkrævet<br />**{navneområdet} / {Indtast} / udbydere/links**<br /><br />Ressourcetype til at oprette. {Navneområdet} og {typen} værdierne refererer til den udbyder navneområde og ressource type på ressourcen, kilde. |
| apiVersion | Optæl<br />Påkrævet<br />**2015-01-01**<br /><br />API versionen skal bruges til at oprette ressourcen. |  
| Navn | Streng<br />Påkrævet<br />**{resouce}/Microsoft.Resources/{linkname}**<br /> op til 64 tegn og må ikke indeholde <>, % &,?, eller nogen styre tegn.<br /><br />En værdi, der angiver både navnet på kilde ressource og et navn til linket. |
| dependsOn | Matrix<br />Valgfri<br />En kommasepareret liste over en ressource navne eller ressource entydige identifikatorer.<br /><br />Samling af ressourcer, der afhænger af dette link. Hvis de ressourcer, du opretter et link er installeret i den samme skabelon, skal du medtage disse ressourcenavne i dette element for at sikre, at de er implementeret først. | 
| Egenskaber | Objekt<br />Påkrævet<br />[Egenskaber for objekt](#properties)<br /><br />Et objekt, der identificerer ressourcen oprette et link til, og noter om linket. |  

<a id="properties" />
### <a name="properties-object"></a>Egenskaber for objekt

| Navn | Værdi |
| ------- | ---- |
| targetId | Streng<br />Påkrævet<br />**{ressource-id}**<br /><br />Id for destinationsprogram ressourcen kædes sammen med. |
| noter | Streng<br />Valgfri<br />op til 512 tegn<br /><br />Beskrivelse af låsen. |


## <a name="how-to-use-the-link-resource"></a>Sådan bruger du linket ressourcen

Du kan anvende en kæde mellem to ressourcer, når ressourcerne, der har en afhængighed, der fortsat efter installation. For eksempel en app kan oprette forbindelse til en database i en anden ressourcegruppe. Du kan definere afhængigheden ved at oprette et link fra app til databasen. Links gør det muligt at dokumentere relationen mellem to ressourcer. Du eller en anden i organisationen kan senere forespørge en ressource til links til at se, hvordan ressourcen, der fungerer med andre ressourcer.

Alle sammenkædede ressourcer skal tilhøre samme abonnement. Hver ressource kan sammenkædes med 50 andre ressourcer. Hvis nogen af de sammenkædede ressourcer er slettet eller flyttet, skal ejeren af link oprydning resterende linket.

Du kan arbejde med links gennem RESTEN ved at se [Sammenkædede ressourcer](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Brug følgende Azure PowerShell-kommando til at se alle kæder i dit abonnement. Du kan angive andre parametre for at begrænse resultaterne.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>Eksempler

I følgende eksempel anvender en skrivebeskyttet Lås til en WebApp.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Hurtig start skabeloner

Følgende skabeloner i Hurtig start implementere ressourcer med et link.

- [Giv besked til kø med logik app](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [Besked om at slæk med logik app](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [Klargøre en API-app med en eksisterende gateway](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [Klargøre en API-app med en ny gateway](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [Oprette en logik App samt API-app ved hjælp af en skabelon](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [Logik app, der sender en SMS-besked, når en påmindelse udløses](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## <a name="next-steps"></a>Næste trin

- Du kan finde oplysninger om strukturen skabelon [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md).
