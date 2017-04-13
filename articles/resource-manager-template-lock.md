<properties
   pageTitle="Ressourcestyring skabelon til ressourcelåse | Microsoft Azure"
   description="Viser ressourcestyring skemaet til implementering af ressourcelåse gennem en skabelon."
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
   ms.date="10/03/2016"
   ms.author="tomfitz"/>

# <a name="resource-locks-template-schema"></a>Ressource låse skabelon skema

Opretter en lås på en ressource og dets underordnede ressourcer.

## <a name="schema-format"></a>Skema format

For at oprette en lås, skal du tilføje følgende skema i afsnittet ressourcer af din skabelon.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## <a name="values"></a>Værdier

I følgende tabel beskrives de værdier, du vil angive i skemaet.

| Navn | Påkrævet | Beskrivelse |
| ---- | -------- | ----------- |
| type | Ja | Ressourcetype til at oprette.<br /><br />For ressourcer:<br />**{navneområdet} / {Indtast} / udbydere/låse**<br /><br/>For ressourcegrupper:<br />**Microsoft.Authorization/locks** |
| apiVersion | Ja | API versionen skal bruges til at oprette ressourcen.<br /><br />Brug:<br />**2015-01-01**<br /><br /> |
| Navn | Ja | En værdi, der angiver både ressourcen låse og et navn til låsen. Kan bestå af op til 64 tegn og må ikke indeholde <>, % &,?, eller nogen styre tegn.<br /><br />For ressourcer:<br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />For ressourcegrupper:<br />**{lockname}** |
| dependsOn | Nej | En kommasepareret liste over en ressource navne eller ressource entydige identifikatorer.<br /><br />Samling af ressourcer, der afhænger af denne Lås. Hvis du låse ressourcen er installeret i den samme skabelon, kan du medtage pågældende ressourcenavn i dette element for at sikre, at ressourcen, der er installeret først. | 
| Egenskaber | Ja | Et objekt, der identificerer typen Lås og noter om låsen.<br /><br />Se [Egenskaber for objekt](#properties-object). |  

### <a name="properties-object"></a>Egenskaber for objekt

| Navn | Påkrævet | Beskrivelse |
| ---- | -------- | ----------- |
| niveau   | Ja | Typen af Lås anvende i området.<br /><br />**CannotDelete** - brugere kan redigere ressource, men ikke slette den.<br />**Skrivebeskyttet** - brugere kan læse fra en ressource, men de kan ikke slette den eller udføre handlinger på den. |
| noter   | Nej | Beskrivelse af låsen. Kan bestå af op til 512 tegn. |


## <a name="how-to-use-the-lock-resource"></a>Sådan bruger du låse ressourcen

Du kan tilføje denne ressource til din skabelon til at forhindre, at bestemte handlinger på en ressource. Låsen gælder for alle brugere og grupper.

Hvis du vil oprette eller slette management låse, skal du have adgang til **Microsoft.Authorization/** * eller * *Microsoft.Authorization/locks/* ** Handlinger. Af de indbyggede roller, kun **ejer** og **bruger Access Administrator ** tildeles disse handlinger. Du kan finde oplysninger om rollebaseret adgangskontrol [Azure rollebaseret adgangskontrol](./active-directory/role-based-access-control-configure.md).

Låsen er anvendt på den angivne ressource og de underordnede ressourcer.

Du kan fjerne en lås med PowerShell-kommandoen **Fjern AzureRmResourceLock** eller [sletning](https://msdn.microsoft.com/library/azure/mt204562.aspx) af REST-API.

## <a name="examples"></a>Eksempler

I følgende eksempel anvender en kan ikke slette Lås til en WebApp.

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
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

Det næste eksempel gælder en kan ikke slette Lås for ressourcegruppen.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## <a name="next-steps"></a>Næste trin

- Du kan finde oplysninger om strukturen skabelon [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md).
- Se [låse ressourcer med Azure ressourcestyring](resource-group-lock-resources.md)kan finde flere oplysninger om låse.
