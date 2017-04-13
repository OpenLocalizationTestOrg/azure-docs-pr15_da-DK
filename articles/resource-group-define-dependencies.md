<properties
   pageTitle="Afhængigheder i ressourcestyring skabeloner | Microsoft Azure"
   description="Beskriver, hvordan du angive en ressource som afhænger af en anden ressource under installation for at sikre, at ressourcer er implementeret i den rigtige rækkefølge."
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
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="defining-dependencies-in-azure-resource-manager-templates"></a>Definition af afhængigheder i Azure ressourcestyring skabeloner

For en given ressource, kan der være andre ressourcer, der skal findes, før ressourcen, der er installeret. For eksempel skal en SQL server findes, før du forsøger at installere en SQL-database. Du kan definere denne relation ved at markere én ressource som afhænger af anden ressourcen. Typisk, du definerer en afhængighed med elementet **dependsOn** , men du kan også angive den via funktionen **reference** . 

Ressourcestyring evaluerer afhængigheder mellem ressourcer og installerer dem i deres afhængige rækkefølge. Når ressourcer, der ikke er afhængige af hinanden, installerer ressourcestyring dem parallelt.

## <a name="dependson"></a>dependsOn

I din skabelon elementet dependsOn giver dig mulighed at definere én ressource som en afhængig af en eller flere ressourcer. Værdien kan være en kommasepareret liste over ressourcenavne. 

I følgende eksempel viser et virtuelt skala sæt, der afhænger af belastningsjustering, virtuelt netværk og en løkke, som opretter flere lagerplads konti. Disse andre ressourcer vises ikke i følgende eksempel, men de har brug for til findes et andet sted i skabelonen.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

Angive elementet dependsOn navnet på indstillingen Gentag for at definere en afhængighed mellem en ressource og ressourcer, der er oprettet via en kopi løkke. Et eksempel, kan du se [oprette flere forekomster af ressourcer i Azure ressourcestyring](resource-group-create-multiple.md).

Mens du matematik kan bruge dependsOn til at tilknytte relationer mellem dine ressourcer, er det vigtigt at forstå, hvorfor du gør det. da det kan påvirke ydeevnen for din installation. For eksempel for at dokumentere, hvordan ressourcer, der er forbundet, er dependsOn ikke den rette tilgang. Du kan ikke forespørges, hvilke ressourcer er defineret i elementet dependsOn efter installation. Ved hjælp af dependsOn, påvirke du potentielt installation tid, fordi ressourceleder, der ikke implementerer i parallelle to ressourcer, der har en afhængighed. Dokument relationer mellem ressourcer, skal du i stedet bruge [ressource sammenkædning](resource-group-link-resources.md).

## <a name="child-resources"></a>Underordnet ressourcer

Egenskaben ressourcer kan du angive underordnede ressourcer, der er relateret til den ressource, der defineres. Underordnet ressourcer kan kun være defineret fem niveauer. Det er vigtigt at være opmærksom på, ikke er oprettet en implicit afhængighed mellem en underordnet ressource og den overordnede ressource. Hvis du har brug for underordnede ressourcen skal installeres efter den overordnede ressource, skal du udtrykkeligt angive afhængigheden med egenskaben dependsOn. 

Hver overordnede ressource accepterer kun visse ressourcetyper som underordnet ressourcer. Accepterede ressourcetyper er angivet i [skabelonen skema](https://github.com/Azure/azure-resource-manager-schemas) på den overordnede ressource. Navnet på underordnede ressourcetype indeholder navnet på den overordnede ressourcetype, som **Microsoft.Web/sites/config** og **Microsoft.Web/sites/extensions** er begge underordnede ressourcer af **Microsoft.Web/sites**.

I følgende eksempel viser en SQL server og SQL-database. Bemærk, at der er defineret en eksplicit afhængighed mellem SQL-database og SQL server, selvom databasen er underordnet i forhold til serveren.

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## <a name="reference-function"></a>funktionen reference

[Reference funktionen](resource-group-template-functions.md#reference) gør det muligt for et udtryk til at udlede dens værdi fra andre JSON navn og værdipar eller runtime ressourcer. Reference udtryk erklære implicit, en ressource afhænger af en anden. 

    reference('resourceName').propertyPath

Du kan bruge dette element eller elementet dependsOn til at angive afhængigheder, men du behøver ikke at bruge begge dele for den samme afhængige ressource. Når det er muligt, kan du bruge en implicit reference til at undgå ved et uheld at tilføje en unødvendig afhængighed.

Se [reference funktionen](resource-group-template-functions.md#reference)for at få mere for at vide.

## <a name="next-steps"></a>Næste trin

- Se [redigering skabeloner](resource-group-authoring-templates.md)for at få mere for at vide om oprettelse af Azure ressourcestyring skabeloner. 
- Du kan finde en liste over de tilgængelige funktioner i en skabelon, [skabelon funktioner](resource-group-template-functions.md).

