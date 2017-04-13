<properties
   pageTitle="Ressourcestyring skabelon til rolletildelinger | Microsoft Azure"
   description="Viser ressourcestyring skemaet til implementering af en rolletildeling gennem en skabelon."
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

# <a name="role-assignments-template-schema"></a>Rolle tildelinger skabelon skema

Tildeler en bruger, gruppe eller tjeneste hovedstolen til en rolle på et angivet område.

## <a name="resource-format"></a>Ressource-format

For at oprette en rolletildeling, skal du tilføje følgende skema i afsnittet ressourcer af din skabelon.
    
    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>Værdier

I følgende tabel beskrives de værdier, du vil angive i skemaet.

| Navn | Påkrævet | Beskrivelse |
| ---- | -------- | ----------- |
| type | Ja    | Ressourcetype til at oprette.<br /><br /> For ressourcegruppe:<br />**Microsoft.Authorization/roleAssignments**<br /><br />For ressource:<br />**{provider-navneområdet} / {ressourcetype} / udbydere/roleAssignments** |
| apiVersion |Ja | API versionen skal bruges til at oprette ressourcen.<br /><br /> Brug **2015-07-01**. | 
| Navn | Ja | Et globalt entydigt id for den nye rolletildeling. |
| dependsOn | Nej | En fil med semikolonseparerede matrix af en ressource navne eller ressource entydige identifikatorer.<br /><br />Samling af ressourcer, der afhænger af denne rolletildeling. Hvis tildeler en rolle, der er fastsat til en ressource, og som ressource er implementeret i den samme skabelon, kan du medtage ressourcenavnet i dette element for at sikre, at ressourcen, der er installeret først. |
| Egenskaber | Ja | Objektet egenskaber, der identificerer den rolledefinition, hovedstolen og omfang. |

### <a name="properties-object"></a>Egenskaber for objekt

| Navn | Påkrævet | Beskrivelse |
| ---- | -------- | ----------- |
| roleDefinitionId | Ja |  Id for en eksisterende rolledefinition skal bruges i rolletildelingen.<br /><br /> Brug følgende format:<br /> **/ subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId | Ja | Globalt entydigt id for en eksisterende hovedstolen. Denne værdi kort id i mappen og kan pege på en bruger, service hovedstolen eller sikkerhedsgruppe. |
| omfang | Nej | Det område, hvor denne rolletildeling gælder for.<br /><br />Ressourcegrupper, skal du bruge:<br />**/subscriptions/ {abonnement-id} /resourceGroups/ {ressource-gruppens navn}**  <br /><br />Bruge ressourcer:<br />**/ subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |  |


## <a name="how-to-use-the-role-assignment-resource"></a>Sådan bruger du rolle tildeling ressource

Du føjer en rolletildeling til din skabelon, når du har brug at tilføje en bruger, gruppe eller tjeneste, der er primære til en rolle under installation. Nedarves rolletildelinger fra højere omfang, så hvis du allerede har tilføjet en sikkerhedskonto til en rolle på niveauet for abonnement, ikke behøver du at tildele den til den ressourcegruppe eller ressource.

Der findes mange id-værdier, skal du angive, når du arbejder med rolletildelinger. Du kan hente værdierne via PowerShell eller Azure CLI.

### <a name="powershell"></a>PowerShell

Navnet på rolletildeling kræver et globalt entydigt id. Du kan oprette et nyt id for **navn** med:

    $name = [System.Guid]::NewGuid().toString()

Du kan hente et id for rolledefinition med:

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Du kan hente id for hovedstolen hos en af følgende kommandoer.

Navngivet **revisorer**for en gruppe:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Navngivet **exampleperson**for en bruger:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Til en tjeneste hedder hovedstolen **exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>Azure CLI

Du kan hente et id for rolledefinition med:

    azure role show Reader --json | jq .[].Id -r

Du kan hente id for hovedstolen hos en af følgende kommandoer.

Navngivet **revisorer**for en gruppe:

    azure ad group show --search Auditors --json | jq .[].objectId -r

Navngivet **exampleperson**for en bruger:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

Til en tjeneste hedder hovedstolen **exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>Eksempler

Følgende skabelon modtager et id for en rolle og et id for en bruger, gruppe eller tjeneste hovedstolen. Tildeling af rollen på ressourcegruppeniveau.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



Den næste skabelon opretter en lagerplads konto og tildeler læserrollen til kontoen lagerplads. Id'erne for to grupper og læserrollen, der er medtaget i skabelonen til at forenkle installation. Disse værdier kan hentes fra det øjeblik gennem i script og givet som parametre.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>Hurtig start skabeloner

Følgende skabeloner, der viser, hvordan du bruger rolle tildeling ressourcen:

- [Tildel indbyggede rolle til ressourcegruppe](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Tildel indbyggede rolle til eksisterende VM](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Tildel indbyggede rolle til flere eksisterende FOS](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>Næste trin

- Du kan finde oplysninger om strukturen skabelon [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md).
- Du kan finde flere oplysninger om rollebaseret adgangskontrol, [Azure Active Directory rollebaseret adgangskontrol](./active-directory/role-based-access-control-configure.md).
