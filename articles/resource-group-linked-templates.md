<properties
   pageTitle="Sammenkædet skabeloner med ressourcestyring | Microsoft Azure"
   description="Beskriver, hvordan bruge sammenkædede skabeloner i en Azure ressourcestyring skabelon til at oprette en modul skabelon løsning. Viser, hvordan du overfører parameterværdier skal du angive en parameterfil og dynamisk oprettede URL-adresser."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/02/2016"
   ms.author="tomfitz"/>

# <a name="using-linked-templates-with-azure-resource-manager"></a>Brug af sammenkædede skabeloner med Azure ressourcestyring

Fra i én Azure ressourcestyring skabelon, kan du sammenkæde med en anden skabelon, som gør det muligt at opdele din installation i et sæt rettet, formål-specifikke skabeloner. Som i decomposing et program i flere kodeklasser, giver opdelingstræ fordele med hensyn til test, genbrug og læsbarhed.  

Du kan overføre parametre fra en primære skabelon til en sammenkædet skabelon, og disse parametre direkte kan knyttes til parametre eller variabler, der vises af skabelonen opkald. Den sammenkædede skabelon kan også sende en output variabel tilbage til skabelonen for datakilden, så en tovejs data exchange mellem skabeloner.

## <a name="linking-to-a-template"></a>Oprette en kæde til en skabelon

Du opretter en kæde mellem to skabeloner ved at tilføje en installation ressource i den primære skabelon, der peger på den sammenkædede skabelon. Du kan angive egenskaben **templateLink** til den sammenkædede skabelon URI. Du kan angive parameterværdier for den sammenkædede skabelon enten ved at angive værdier direkte i din skabelon eller ved at sammenkæde med en parameterfil. I følgende eksempel bruges egenskaben **parametre** til at angive en parameterværdi direkte.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

Tjenesten ressourceleder, der skal kunne få adgang til den sammenkædede skabelon. Du kan ikke angive en lokal fil eller en fil, der er kun tilgængelig på dit lokale netværk til den sammenkædede skabelon. Du kan kun angive en URI-værdi, der indeholder enten **http** eller **https**. En mulighed er at placere din sammenkædede skabelon med en konto med lagerplads, og brug URI for det pågældende element, som vist i følgende eksempel.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

Selvom den sammenkædede skabelon skal være eksternt tilgængelige, behøver det ikke være alment tilgængelig for offentligheden. Du kan tilføje din skabelon til en privat lagerplads-konto, der er tilgængeligt til kontoejeren lagerplads. Derefter skal oprette du en delt adgangstoken til signatur (SAS) for at aktivere adgang under installationen. Du kan tilføje denne SAS token på URI-adressen for den sammenkædede skabelon. Se [Implementer ressourcer til Ressourcestyring skabeloner og Azure PowerShell](resource-group-template-deploy.md) eller [Implementer ressourcer til Ressourcestyring skabeloner og Azure CLI](resource-group-template-deploy-cli.md)trin til konfiguration af en skabelon med en konto med lagerplads, og der opstår et SAS token. 

I følgende eksempel viser en overordnet skabelon, der er knyttet til en anden skabelon. Den sammenkædede skabelon åbnes med et SAS token, der overføres i som en parameter.

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "incremental",
              "templateLink": {
                "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
                "contentVersion": "1.0.0.0"
              }
            }
        }
    ],

Selvom Tokenet er gået i som en sikker streng, logføres URI af den sammenkædede skabelon, herunder token SAS i handlingerne installation for ressourcegruppen. For at begrænse visning, skal du angive en udløbsdato for tokenet.

## <a name="linking-to-a-parameter-file"></a>Oprette en kæde til en parameterfil

Det næste eksempel bruger egenskaben **parametersLink** til at sammenkæde med en parameterfil.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

URI værdien for den sammenkædede parameterfil være ikke en lokal fil, og skal indeholde **http** eller **https**. Parameterfilen kan også være begrænset til adgang via et SAS token.

## <a name="using-variables-to-link-templates"></a>Ved hjælp af variabler kædes skabeloner

De tidligere eksempler viste faste værdier for URL-adresse til skabelon kæderne. Denne metode fungerer muligvis for en enkelt skabelon, men det fungerer ikke, og når du arbejder med en stor mængde modul skabeloner. I stedet kan du oprette en statisk variabel, der gemmer en grundlæggende URL-adresse til den primære skabelon og derefter dynamisk oprette URL-adresser for de sammenkædede skabeloner fra den grundlæggende URL-adresse. Fordelen ved denne fremgangsmåde er du nemt kan flytte eller deler sig skabelonen, fordi du kun vil ændre den statiske variabel i den primære skabelon. Skabelonen primære overfører de korrekte URI'er i hele skabelonen delt.

I følgende eksempel viser, hvordan du bruger en grundlæggende URL-adresse til at oprette to URL-adresser til sammenkædede skabeloner (**sharedTemplateUrl** og **vmTemplate**). 

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

Du kan også bruge [deployment()](resource-group-template-functions.md#deployment) til at få grundlæggende URL-adressen til den aktuelle skabelon og bruge den til at hente URL-adressen til andre skabeloner på samme placering. Denne metode er nyttigt, hvis din skabelonplacering ændres (måske på grund af versionsstyring), eller du vil undgå hårdt kodningssprog URL-adresser i skabelonfilen. 

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## <a name="conditionally-linking-to-templates"></a>Betinget sammenkædning til skabeloner

Du kan sammenkæde med forskellige skabeloner ved overførsel af en parameterværdi, der bruges til at oprette URI af den sammenkædede skabelon. Denne metode er praktisk, når du har brug at angive under installationen, sammenkædet skabelon, du vil bruge. For eksempel kan du angive en skabelon skal bruges til en eksisterende lagerplads konto og en anden skabelon skal bruges til en ny konto lagerplads.

I følgende eksempel viser en parameter for et lager kontonavn og en parameter for at angive, om kontoen lagerplads er nyt eller eksisterende.

    "parameters": {
        "storageAccountName": {
            "type": "String"
        },
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },

Du opretter en variabel til skabelonen URI, der indeholder værdien af den nye eller eksisterende parameter.

    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/exampleuser/templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },

Du kan angive den pågældende variabel værdi for installation ressourcen.

    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "StorageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    }
                }
            }
        }
    ],

URI oversættes til en skabelon med navnet **existingStorageAccount.json** eller **newStorageAccount.json**. Oprette skabeloner til disse URI'er.

I følgende eksempel viser skabelonen **existingStorageAccount.json** .

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

Det næste eksempel viser skabelonen **newStorageAccount.json** . Bemærk, at som den eksisterende lager Kontoskabelon objektet lagerplads konto returneres i outputtet. Skabelonen master fungerer med enten sammenkædede skabelon.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('StorageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {
          }
        }
      ],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('StorageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

## <a name="complete-example"></a>Eksempel på fuldført

Følgende eksempel skabeloner viser en forenklet opsætning af sammenkædede skabeloner til at illustrere flere af begreberne i denne artikel. Det antages, at skabelonerne er blevet føjet til samme beholder med en konto med lagerplads med offentlig adgang slået fra. Den sammenkædede skabelon overfører en værdi tilbage til den primære skabelon i sektionen **skriver** .

Filen **parent.json** består af:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "linkedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
              "contentVersion": "1.0.0.0"
            }
          }
        }
      ],
      "outputs": {
        "result": {
          "type": "object",
          "value": "[reference('linkedTemplate').outputs.result]"
        }
      }
    }

Filen **helloworld.json** består af:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {
        "result": {
            "value": "Hello World",
            "type" : "string"
        }
      }
    }
    
I PowerShell, du får et token for objektbeholderen og installere skabelonerne med:

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

I Azure CLI, du får et token for objektbeholderen og anvende skabeloner med følgende kode. I øjeblikket, skal du angive et navn til installationen, når du bruger en skabelon URI, der indeholder et SAS token.  

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

Du bliver bedt om at angive SAS token som en parameter. Skal du skrive token med **?**.

## <a name="next-steps"></a>Næste trin
- For at få mere for at vide om at definere installation rækkefølgen for dine ressourcer, se [definere afhængigheder i Azure ressourcestyring skabeloner](resource-group-define-dependencies.md)
- For at lære at definere én ressource, men oprette mange forekomster af det, skal du se [oprette flere forekomster af ressourcer i Azure ressourcestyring.](resource-group-create-multiple.md)
