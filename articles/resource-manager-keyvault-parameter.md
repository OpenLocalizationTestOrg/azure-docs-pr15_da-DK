<properties
   pageTitle="Tasten samling hemmeligt med ressourcestyring skabelon | Microsoft Azure"
   description="Viser, hvordan du overfører et hemmeligt fra en vigtige samling som en parameter under installationen."
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="pass-secure-values-during-deployment"></a>Overføre sikker værdier under installationen

Når du vil overføre en sikker værdi (som en adgangskode) som en parameter under installationen, kan du gemme den pågældende værdi som et hemmeligt i en [Azure nøgle samling](./key-vault/key-vault-whatis.md) og henviser til værdien i andre ressourcestyring skabeloner. Du kan medtage kun en reference til hemmeligheden i skabelonen så hemmeligheden er aldrig afsløret, og du behøver ikke at manuelt at angive værdien for hemmeligheden, hver gang du installerer ressourcerne. Du angiver, hvilke brugere eller principper for tjenesten kan få adgang til hemmeligheden.  

## <a name="deploy-a-key-vault-and-secret"></a>Installere en samling af vigtige legitimationsoplysninger og hemmeligt

Hvis du vil oprette vigtige samling, der skal refereres til fra andre ressourcestyring skabeloner, skal du angive egenskaben **enabledForTemplateDeployment** til **Sand**, og du skal give adgang til den bruger eller tjeneste hovedstolen, der udføres den installation, der henviser til hemmeligheden.

For at få mere for at vide om installation af en samling af vigtige legitimationsoplysninger og hemmeligt, se [tasten samling skemaet](resource-manager-template-keyvault.md) og [nøgle samling hemmeligt skemaet](resource-manager-template-keyvault-secret.md).

## <a name="reference-a-secret-with-static-id"></a>Henvise til et hemmeligt med statisk-id

Du referere til hemmeligt fra i en fil, parametre som overfører værdier til din skabelon. Du referere til Hemmeligheden bag ved at overføre ressource-id'et for den vigtige samling af legitimationsoplysninger og navnet på hemmeligt. Hemmeligheden bag vigtige samling skal allerede findes i dette eksempel, og du bruger en statisk værdi for den ressource-id.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

En hel parameterfil kan se ud:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

Den parameter, der accepterer hemmeligheden skal være en **securestring**. I følgende eksempel vises de relevante afsnit i en skabelon, der installerer en SQL server, der kræver en administratoradgangskode.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## <a name="reference-a-secret-with-dynamic-id"></a>Henvise til et hemmeligt med dynamisk-id

Afsnittet tidligere viste Sådan overfører et statisk ressource-id til vigtige samling hemmeligheden. Men i nogle scenarier skal du henvise til en vigtige samling hemmeligt, der varierer baseret på den aktuelle installation. Det er tilfældet, kan du ikke svært-kode ressource-id i parameterfilen. Desværre kan ikke kan du dynamisk generere ressource-id i parameterfilen fordi skabelon udtryk ikke er tilladt i parameterfilen.

For at generere dynamisk ressource-id for en vigtige samling hemmeligt, skal du flytte den ressource, der skal hemmeligheden til en indlejret skabelon. Du kan tilføje skabelonen indlejrede og overfører i en parameter, der indeholder dynamisk genererede ressource-id i skabelonen mastersider.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## <a name="next-steps"></a>Næste trin

- Generelle oplysninger om vigtige vaults, kan du se [Introduktion til Azure nøgle samling](./key-vault/key-vault-get-started.md).
- Du kan finde oplysninger om at bruge en vigtige samling med en virtuel maskine [overvejelser om sikkerheden for Azure Resource Manager](best-practices-resource-manager-security.md).
- Se [tasten samling eksempler](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)fuldført eksempler på refererer til vigtige hemmeligheder.

