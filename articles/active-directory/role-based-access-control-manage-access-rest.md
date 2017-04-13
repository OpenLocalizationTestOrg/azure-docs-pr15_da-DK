<properties
    pageTitle="Administration af rollebaseret adgangskontrol med REST API'ER"
    description="Administration af rollebaseret adgangskontrol med REST API'ER"
    services="active-directory"
    documentationCenter="na"
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="multiple"
    ms.tgt_pltfrm="rest-api"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="managing-role-based-access-control-with-the-rest-api"></a>Administration af rollebaseret adgangskontrol med REST API'ER

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST-API](role-based-access-control-manage-access-rest.md)

Rollebaseret Access kontrolelement (RBAC) i Azure portalen og Azure ressourcestyring API hjælper dig med at administrere adgangen til dit abonnement og ressourcer på et detaljerede niveau. Med denne funktion kan du give adgang til Active Directory-brugere, grupper eller principper for tjenesten ved at tildele dem nogle roller i et særlig omfang.

## <a name="list-all-role-assignments"></a>Liste over alle rolletildelinger

Oversigt over alle rolletildelinger på de angivne område og subscopes.

Til listen rolletildelinger, du skal have adgang til `Microsoft.Authorization/roleAssignments/read` handlingen i området. Alle de indbyggede roller er tildelt adgang til denne handling. Du kan finde flere oplysninger om rolletildelinger og administration af adgang til Azure ressourcer [Azure Role-Based adgangskontrol](role-based-access-control-configure.md).

### <a name="request"></a>Anmode om

Du kan bruge metoden **få** med den følgende URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

Foretage de følgende erstatninger til at tilpasse din anmodning inden for URI:

1. Erstat *{omfang}* med det område, hvor du vil listen rolletildelinger. Følgende eksempler viser, hvordan du kan angive omfanget for forskellige niveauer:

  - Abonnement: /subscriptions/ {abonnement-id}  
  - Ressourcegruppe: /subscriptions/ {abonnement-id} / resourceGroups/myresourcegroup1  
  - Ressource: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Erstat *{api-version}* med 2015-07-01.

3. Erstat *{filteret}* med den betingelse, du vil anvende for at filtrere listen rolle tildeling:

  - Listen rolletildelinger for kun det angivne område, herunder ikke rolletildelinger på subscopes:`atScope()`    
  - Listen rolletildelinger for en bestemt bruger, gruppe eller -programmer:`principalId%20eq%20'{objectId of user, group, or service principal}'`  
  - Liste over rolletildelinger for en bestemt bruger, herunder dem nedarvet fra grupper |`assignedTo('{objectId of user}')`

### <a name="response"></a>Svar

Statuskode: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Få oplysninger om en rolletildeling

Henter oplysninger om en enkelt rolletildeling, der er angivet af rolle opgave-id.

Hvis du vil have oplysninger om en rolletildeling, skal du have adgang til `Microsoft.Authorization/roleAssignments/read` handling. Alle de indbyggede roller er tildelt adgang til denne handling. Du kan finde flere oplysninger om rolletildelinger og administration af adgang til Azure ressourcer [Azure Role-Based adgangskontrol](role-based-access-control-configure.md).

### <a name="request"></a>Anmode om

Du kan bruge metoden **få** med den følgende URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Foretage de følgende erstatninger til at tilpasse din anmodning inden for URI:

1. Erstat *{omfang}* med det område, hvor du vil listen rolletildelinger. Følgende eksempler viser, hvordan du kan angive omfanget for forskellige niveauer:

  - Abonnement: /subscriptions/ {abonnement-id}  
  - Ressourcegruppe: /subscriptions/ {abonnement-id} / resourceGroups/myresourcegroup1  
  - Ressource: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Erstat *{rolle-opgave-id}* med GUID-id for rolletildelingen.

3. Erstat *{api-version}* med 2015-07-01.

### <a name="response"></a>Svar

Statuskode: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Oprette en rolletildeling

Oprette en rolletildeling på det angivne område for den angivne hovedstolen give den angivne rolle.

Hvis du vil oprette en rolletildeling, skal du have adgang til `Microsoft.Authorization/roleAssignments/write` handling. Af de indbyggede roller får kun *ejeren* og *Access Brugeradministrator* adgang til denne handling. Du kan finde flere oplysninger om rolletildelinger og administration af adgang til Azure ressourcer [Azure Role-Based adgangskontrol](role-based-access-control-configure.md).

### <a name="request"></a>Anmode om

Du kan bruge metoden **PLACERE** med den følgende URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Foretage de følgende erstatninger til at tilpasse din anmodning inden for URI:

1. Erstat *{omfang}* med det område, hvor du vil oprette rolletildelinger. Når du opretter en rolletildeling på et overordnet omfang, nedarver alle underordnede områder den samme rolletildeling. Følgende eksempler viser, hvordan du kan angive omfanget for forskellige niveauer:

  - Abonnement: /subscriptions/ {abonnement-id}  
  - Ressourcegruppe: /subscriptions/ {abonnement-id} / resourceGroups/myresourcegroup1   
  - Ressource: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Erstat *{rolle-opgave-id}* med et nyt GUID, som bliver GUID id for den nye rolletildeling.

3. Erstat *{api-version}* med 2015-07-01.

For anmodningsteksten, du levere værdierne i følgende format:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Elementnavn     | Påkrævet | Type   | Beskrivelse |
|------------------|----------|--------|-------------|
| roleDefinitionId | Ja      | Streng | Id for rollen. Formatet for id er:`{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId      | Ja      | Streng | objectId af Azure AD hovedstolen (brugeren, gruppen eller tjeneste hovedstolen), er tildelt rollen. |

### <a name="response"></a>Svar

Statuskode: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Slette en rolletildeling

Slette en rolletildeling på det angivne område.

Hvis du vil slette en rolletildeling, skal du have adgang til den `Microsoft.Authorization/roleAssignments/delete` handling. Af de indbyggede roller får kun *ejeren* og *Access Brugeradministrator* adgang til denne handling. Du kan finde flere oplysninger om rolletildelinger og administration af adgang til Azure ressourcer [Azure Role-Based adgangskontrol](role-based-access-control-configure.md).

### <a name="request"></a>Anmode om

Du kan bruge metoden **slette** med den følgende URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Foretage de følgende erstatninger til at tilpasse din anmodning inden for URI:

1. Erstat *{omfang}* med det område, hvor du vil oprette rolletildelinger. Følgende eksempler viser, hvordan du kan angive omfanget for forskellige niveauer:

  - Abonnement: /subscriptions/ {abonnement-id}  
  - Ressourcegruppe: /subscriptions/ {abonnement-id} / resourceGroups/myresourcegroup1  
  - Ressource: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Erstat *{rolle-opgave-id}* med rolle opgave-id GUID.

3. Erstat *{api-version}* med 2015-07-01.

### <a name="response"></a>Svar

Statuskode: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Liste over alle roller

Viser alle de roller, der er tilgængelige for tildeling på det angivne område.

Liste over roller, du skal have adgang til `Microsoft.Authorization/roleDefinitions/read` handlingen i området. Alle de indbyggede roller er tildelt adgang til denne handling. Du kan finde flere oplysninger om rolletildelinger og administration af adgang til Azure ressourcer [Azure Role-Based adgangskontrol](role-based-access-control-configure.md).

### <a name="request"></a>Anmode om

Du kan bruge metoden **få** med den følgende URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

Foretage de følgende erstatninger til at tilpasse din anmodning inden for URI:

1. Erstat *{omfang}* med det område, hvor du vil liste over rollerne. Følgende eksempler viser, hvordan du kan angive omfanget for forskellige niveauer:

  - Abonnement: /subscriptions/ {abonnement-id}  
  - Ressourcegruppe: /subscriptions/ {abonnement-id} / resourceGroups/myresourcegroup1  
  - Ressource /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Erstat *{api-version}* med 2015-07-01.

3. Erstat *{filteret}* med de betingelser, du vil anvende for at filtrere listen over roller:

  - Oversigt over roller, der er tilgængelige for tildeling på det angivne område og alle dens underordnede områder:`atScopeAndBelow()`
  - Søge efter en rolle ved hjælp af nøjagtige visningsnavnet: `roleName%20eq%20'{role-display-name}'`. Brug formularen URL-kodes af nøjagtige visningsnavnet af rollen. For eksempel`$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Svar

Statuskode: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Få oplysninger om en rolle

Henter oplysninger om en enkelt rolle, der er angivet af rolle definition id. Hvis du vil have oplysninger om en enkelt rolle ved hjælp af dens visningsnavn, se [liste over alle roller](role-based-access-control-manage-access-rest.md#list-all-roles).

Hvis du vil have oplysninger om en rolle, skal du have adgang til `Microsoft.Authorization/roleDefinitions/read` handling. Alle de indbyggede roller er tildelt adgang til denne handling. Du kan finde flere oplysninger om rolletildelinger og administration af adgang til Azure ressourcer [Azure Role-Based adgangskontrol](role-based-access-control-configure.md).

### <a name="request"></a>Anmode om

Du kan bruge metoden **få** med den følgende URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Foretage de følgende erstatninger til at tilpasse din anmodning inden for URI:

1. Erstat *{omfang}* med det område, hvor du vil listen rolletildelinger. Følgende eksempler viser, hvordan du kan angive omfanget for forskellige niveauer:

  - Abonnement: /subscriptions/ {abonnement-id}  
  - Ressourcegruppe: /subscriptions/ {abonnement-id} / resourceGroups/myresourcegroup1  
  - Ressource: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Erstat *{rolle-definition-id}* med GUID-id for den rolledefinition.

3. Erstat *{api-version}* med 2015-07-01.

### <a name="response"></a>Svar

Statuskode: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Oprette en brugerdefineret rolle
Oprette en brugerdefineret rolle.

Hvis du vil oprette en brugerdefineret rolle, skal du have adgang til `Microsoft.Authorization/roleDefinitions/write` handling på alle de `AssignableScopes`. Af de indbyggede roller får kun *ejeren* og *Access Brugeradministrator* adgang til denne handling. Du kan finde flere oplysninger om rolletildelinger og administration af adgang til Azure ressourcer [Azure Role-Based adgangskontrol](role-based-access-control-configure.md).

### <a name="request"></a>Anmode om

Du kan bruge metoden **PLACERE** med den følgende URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Foretage de følgende erstatninger til at tilpasse din anmodning inden for URI:

1. Erstat *{omfang}* med den første *AssignableScope* af rollen brugerdefineret. Følgende eksempler viser, hvordan du kan angive omfanget for forskellige niveauer.

  - Abonnement: /subscriptions/ {abonnement-id}  
  - Ressourcegruppe: /subscriptions/ {abonnement-id} / resourceGroups/myresourcegroup1  
  - Ressource: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Erstat *{rolle-definition-id}* med et nyt GUID, som bliver GUID id for den nye brugerdefinerede rolle.

3. Erstat *{api-version}* med 2015-07-01.

For anmodningsteksten, du levere værdierne i følgende format:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Elementnavn | Påkrævet | Type | Beskrivelse |
|--------------|----------|------|-------------|
| Navn         | Ja | Streng   | GUID id for den brugerdefinerede rolle.    |
| properties.roleName               | Ja | Streng   | Få vist navnet på den brugerdefinerede rolle. Maksimal tekststørrelse 128 tegn.                        |
| Properties.Description            | Nej  | Streng   | Beskrivelse af den brugerdefinerede rolle. Maksimal tekststørrelse 1024 tegn.                                               |
| Properties.type                   | Ja | Streng   | Indstillet til "CustomRole."                                         |
| Properties.Permissions.Actions    | Ja | String] | En matrix med handling strenge, der angiver de handlinger, som brugerdefineret rollen har tildelt.             |
| properties.permissions.notActions | Nej  | String] | En matrix med handling strenge, der angiver handlinger til at udelukke mellem af rollen brugerdefinerede handlinger. |
| properties.assignableScopes       | Ja | String] | En matrix med områder, hvor den brugerdefinerede rolle kan bruges.   |

### <a name="response"></a>Svar

Statuskode: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Opdatere en brugerdefineret rolle

Redigere en brugerdefineret rolle.

Hvis du vil redigere en brugerdefineret rolle, skal du have adgang til `Microsoft.Authorization/roleDefinitions/write` handling på alle de `AssignableScopes`. Af de indbyggede roller får kun *ejeren* og *Access Brugeradministrator* adgang til denne handling. Du kan finde flere oplysninger om rolletildelinger og administration af adgang til Azure ressourcer [Azure Role-Based adgangskontrol](role-based-access-control-configure.md).

### <a name="request"></a>Anmode om

Du kan bruge metoden **PLACERE** med den følgende URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Foretage de følgende erstatninger til at tilpasse din anmodning inden for URI:

1. Erstat *{omfang}* med den første *AssignableScope* af rollen brugerdefineret. Følgende eksempler viser, hvordan du kan angive omfanget for forskellige niveauer:

  - Abonnement: /subscriptions/ {abonnement-id}  
  - Ressourcegruppe: /subscriptions/ {abonnement-id} / resourceGroups/myresourcegroup1  
  - Ressource: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Erstat *{rolle-definition-id}* med GUID-id for den brugerdefinerede rolle.

3. Erstat *{api-version}* med 2015-07-01.

For anmodningsteksten, du levere værdierne i følgende format:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Elementnavn | Påkrævet | Type | Beskrivelse |
|--------------|----------|------|-------------|
| Navn         | Ja      | Streng | GUID id for den brugerdefinerede rolle. |
| properties.roleName | Ja | Streng | Få vist navnet på den opdaterede brugerdefinerede rolle. |
| Properties.Description | Nej | Streng | Beskrivelse af den opdaterede brugerdefinerede rolle. |
| Properties.type | Ja | Streng | Indstillet til "CustomRole." |
| Properties.Permissions.Actions | Ja | String] | En matrix med handling strenge, der angiver de handlinger, som den opdaterede brugerdefinerede rolle giver adgang. |
| properties.permissions.notActions | Nej | String] | En matrix med handling strenge, der angiver handlingerne, der skal udelukkes fra de handlinger, som den opdaterede brugerdefinerede rolle giver. |
| properties.assignableScopes | Ja | String] | En matrix med områder, hvor den opdaterede brugerdefinerede rolle kan bruges. |

### <a name="response"></a>Svar

Statuskode: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Slette en brugerdefineret rolle

Slette en brugerdefineret rolle.

Hvis du vil slette en brugerdefineret rolle, skal du have adgang til `Microsoft.Authorization/roleDefinitions/delete` handling på alle de `AssignableScopes`. Af de indbyggede roller får kun *ejeren* og *Access Brugeradministrator* adgang til denne handling. Du kan finde flere oplysninger om rolletildelinger og administration af adgang til Azure ressourcer [Azure Role-Based adgangskontrol](role-based-access-control-configure.md).

### <a name="request"></a>Anmode om

Du kan bruge metoden **slette** med den følgende URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Foretage de følgende erstatninger til at tilpasse din anmodning inden for URI:

1. Erstat *{omfang}* med det område, hvor du vil slette den rolledefinition. Følgende eksempler viser, hvordan du kan angive omfanget for forskellige niveauer:

  - Abonnement: /subscriptions/ {abonnement-id}  
  - Ressourcegruppe: /subscriptions/ {abonnement-id} / resourceGroups/myresourcegroup1  
  - Ressource: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Erstat *{rolle-definition-id}* med GUID rolle definition id den brugerdefinerede rolle.

3. Erstat *{api-version}* med 2015-07-01.

### <a name="response"></a>Svar

Statuskode: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```


[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
