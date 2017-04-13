<properties
   pageTitle="Ressourcestyring skabelon til et hemmeligt i en vigtige samling | Microsoft Azure"
   description="Viser ressourcestyring skemaet til implementering af vigtige samling hemmeligheder gennem en skabelon."
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-secret-template-schema"></a>Vigtige samling hemmeligt skabelon skema

Opretter et hemmeligt, der er gemt i en vigtige samling af legitimationsoplysninger. Denne ressourcetype er ofte installeres som en underordnet ressource af [vigtige samling](resource-manager-template-keyvault.md).

## <a name="schema-format"></a>Skema format

For at oprette en vigtige samling hemmeligt skal du tilføje følgende skema til din skabelon. Hemmeligheden bag kan defineres som enten en underordnet ressource af en samling af vigtige legitimationsoplysninger eller på øverste niveau ressource. Du kan angive den som en underordnet ressource, når den vigtige samling af legitimationsoplysninger er implementeret i den samme skabelon. Du skal definere hemmeligheden som en ressource på øverste niveau, når den vigtige samling af legitimationsoplysninger ikke er implementeret i den samme skabelon, eller når du har brug at oprette flere hemmeligheder ved løkker på ressourcetypen. 

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## <a name="values"></a>Værdier

I følgende tabel beskrives de værdier, du vil angive i skemaet.

| Navn | Værdi |
| ---- | ---- | 
| type | Optæl<br />Påkrævet<br />**hemmeligheder** (når installeres som en underordnet ressource af vigtige samling) eller<br /> **Microsoft.KeyVault/vaults/secrets** (når installeres som en ressource på øverste niveau)<br /><br />Ressourcetype til at oprette. |
| apiVersion | Optæl<br />Påkrævet<br />**2015-06-01** eller **2014-12-19-preview**<br /><br />API versionen skal bruges til at oprette ressourcen. | 
| Navn | Streng<br />Påkrævet<br />Et enkelt ord, når de distribueres som en underordnet ressource af en samling af vigtige legitimationsoplysninger eller i formatet **{nøgle-samling-navn} / {hemmeligt name}** når installeres som en på øverste niveau ressource skal føjes til en eksisterende vigtige samling.<br /><br />Navnet på hemmeligt til at oprette. |
| Egenskaber | Objekt<br />Påkrævet<br />[Egenskaber for objekt](#properties)<br /><br />Et objekt, der angiver værdien af hemmeligt til at oprette. |
| dependsOn | Matrix<br />Valgfri<br />En kommasepareret liste over en ressource navne eller ressource entydige identifikatorer.<br /><br />Samling af ressourcer, der afhænger af dette link. Hvis den vigtige samling af legitimationsoplysninger for hemmeligheden er installeret i den samme skabelon, kan du medtage navnet på den vigtige samling af legitimationsoplysninger i dette element for at sikre, at den er installeret først. |

<a id="properties" />
### <a name="properties-object"></a>Egenskaber for objekt

| Navn | Værdi |
| ---- | ---- | 
| værdi | Streng<br />Påkrævet<br /><br />Hemmeligt værdien til at gemme i den vigtige samling af legitimationsoplysninger. Når der passerer i en værdi for denne egenskab, kan du bruge en parameter af typen **securestring**.  |

    
## <a name="examples"></a>Eksempler

Det første eksempel installerer et hemmeligt som en underordnet ressource af en vigtige samling af legitimationsoplysninger.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

Det andet eksempel installerer hemmeligheden som et websted på øverste ressource, der er gemt i en eksisterende vigtige samling.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## <a name="next-steps"></a>Næste trin

- Generelle oplysninger om vigtige vaults, kan du se [Introduktion til Azure nøgle samling](./key-vault/key-vault-get-started.md).
- Et eksempel på refererer til en vigtige samling hemmeligt, når du installerer skabeloner, kan du se [overføre sikker værdier under installationen](resource-manager-keyvault-parameter.md).


