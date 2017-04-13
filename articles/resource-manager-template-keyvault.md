<properties
   pageTitle="Ressourcestyring skabelon til vigtige samling | Microsoft Azure"
   description="Viser ressourcestyring skemaet til implementering af vigtige vaults gennem en skabelon."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-template-schema"></a>Vigtige samling skabelon skema

Opretter en vigtige samling af legitimationsoplysninger.

## <a name="schema-format"></a>Skema format

For at oprette en vigtige samling, skal du tilføje følgende skema i afsnittet ressourcer af din skabelon.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>Værdier

I følgende tabel beskrives de værdier, du vil angive i skemaet.

| Navn | Værdi |
| ---- | ---- | 
| type | Optæl<br />Påkrævet<br />**Microsoft.KeyVault/vaults**<br /><br />Ressourcetype til at oprette. |
| apiVersion | Optæl<br />Påkrævet<br />**2015-06-01** eller **2014-12-19-preview**<br /><br />API versionen skal bruges til at oprette ressourcen. | 
| Navn | Streng<br />Påkrævet<br />Et navn, der er entydige på tværs af Azure.<br /><br />Navnet på den vigtige samling af legitimationsoplysninger til at oprette. Overvej at bruge funktionen [uniqueString](resource-group-template-functions.md#uniquestring) med din navnekonventionen til at oprette et entydigt navn, som vist i eksemplet nedenfor. |
| placering | Streng<br />Påkrævet<br />Et gyldigt område til vigtige vaults. For at bestemme gyldige områder, kan du se [understøttede områder](resource-manager-supported-services.md#supported-regions).<br /><br />Området, der skal være vært for den vigtige samling af legitimationsoplysninger. |
| Egenskaber | Objekt<br />Påkrævet<br />[Egenskaber for objekt](#properties)<br /><br />Et objekt, der angiver typen af vigtige samling af legitimationsoplysninger til at oprette. |
| ressourcer | Matrix<br />Valgfri<br />Tilladte værdier: [nøgle samling hemmeligt ressourcer](resource-manager-template-keyvault-secret.md)<br /><br />Underordnet ressourcer for den vigtige samling af legitimationsoplysninger. |

<a id="properties" />
### <a name="properties-object"></a>Egenskaber for objekt

| Navn | Værdi |
| ---- | ---- | 
| enabledForDeployment | Boolesk værdi<br />Valgfri<br />**Sand** eller **Falsk**<br /><br />Angiver, om samling af legitimationsoplysninger er aktiveret for virtuel computer eller tjeneste strukturen installation. |
| enabledForTemplateDeployment | Boolesk værdi<br />Valgfri<br />**Sand** eller **Falsk**<br /><br />Angiver, om samling af legitimationsoplysninger er aktiveret til brug i ressourcestyring skabelon installationer. Yderligere oplysninger, du [overfører sikker værdier under installationen](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption | Boolesk værdi<br />Valgfri<br />**Sand** eller **Falsk**<br /><br />Angiver, om samling af legitimationsoplysninger er aktiveret for lydstyrken kryptering. |
| tenantId | Streng<br />Påkrævet<br />**Globalt entydigt id**<br /><br />Lejer id for abonnementet. Du kan hente det med [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) PowerShell-cmdlet eller **azure-konto vises** Azure CLI kommandoen. |
| accessPolicies | Matrix<br />Påkrævet<br />[accessPolicies objekt](#accesspolicies)<br /><br />En matrix med op til 16 objekter, angiver tilladelser for den bruger eller tjeneste hovedstolen. |
| SKU | Objekt<br />Påkrævet<br />[SKU objekt](#sku)<br /><br />SKU for den vigtige samling af legitimationsoplysninger. |

<a id="accesspolicies" />
### <a name="propertiesaccesspolicies-object"></a>properties.accessPolicies objekt

| Navn | Værdi |
| ---- | ---- | 
| tenantId | Streng<br />Påkrævet<br />**Globalt entydigt id**<br /><br />Lejer id for den Azure Active Directory-lejer, der indeholder **objectId** i denne access-politik |
| objectId | Streng<br />Påkrævet<br />**Globalt entydigt id**<br /><br />Objekt-id'et af Azure Active Directory-bruger eller tjeneste hovedstolen, der har adgang til samling af legitimationsoplysninger. Du kan hente værdien fra [Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) eller [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) PowerShell-cmdlet'er eller kommandoerne **azure ad-bruger** eller **azure ad sp** Azure CLI. |
| tilladelser | Objekt<br />Påkrævet<br />[tilladelser objekt](#permissions)<br /><br />De tilladelser, der gives denne samling af legitimationsoplysninger til Active Directory-objektet. |

<a id="permissions" />
### <a name="propertiesaccesspoliciespermissions-object"></a>properties.accessPolicies.permissions objekt

| Navn | Værdi |
| ---- | ---- | 
| Taster | Matrix<br />Påkrævet<br />**alle**, **Sikkerhedskopiering**, **oprette**, **dekryptere**, **slette**, **kryptere**, **få**, **importere**, **liste**, **gendanne**, **Log**, **unwrapkey**, **opdatere**, **bekræfte**og **wrapkey**<br /><br />De tilladelser, der gives taster i denne samling af legitimationsoplysninger til dette Active Directory-objekt. Denne værdi skal være angivet som en matrix med en eller flere tilladte værdier. |
| hemmeligheder | Matrix<br />Påkrævet<br />**alle**, **slette**, **få**, **liste**, **angive**<br /><br />De tilladelser, der gives hemmeligheder i denne samling af legitimationsoplysninger til dette Active Directory-objekt. Denne værdi skal være angivet som en matrix med en eller flere tilladte værdier. |

<a id="sku" />
### <a name="propertiessku-object"></a>Properties.SKU objekt

| Navn | Værdi |
| ---- | ---- | 
| Navn | Optæl<br />Påkrævet<br />**standard**eller **premium** <br /><br />Webtjenesten af KeyVault til brug.  Standard understøtter hemmeligheder og software-beskyttede nøgler.  Premium tilføjer understøttelse af HSM beskyttet taster. |
| familie | Optæl<br />Påkrævet<br />**A** <br /><br />Sku familien, du vil bruge. |
 
    
## <a name="examples"></a>Eksempler

I følgende eksempel installerer en samling af vigtige legitimationsoplysninger og hemmeligt.

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

## <a name="quickstart-templates"></a>Hurtig start skabeloner

Skabelonen følgende Hurtig start installerer en vigtige samling af legitimationsoplysninger.

- [Oprette vigtige samling](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)


## <a name="next-steps"></a>Næste trin

- Generelle oplysninger om vigtige vaults, kan du se [Introduktion til Azure nøgle samling](./key-vault/key-vault-get-started.md).
- Et eksempel på refererer til en vigtige samling hemmeligt, når du installerer skabeloner, kan du se [overføre sikker værdier under installationen](resource-manager-keyvault-parameter.md).

