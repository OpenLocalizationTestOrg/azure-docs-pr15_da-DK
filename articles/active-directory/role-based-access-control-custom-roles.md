<properties
    pageTitle="Brugerdefinerede roller i Azure RBAC | Microsoft Azure"
    description="Lær at definere brugerdefinerede roller med Azure Role-Based adgangskontrol for mere præcis identitet management i abonnementet Azure."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="kgremban"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="07/25/2016"
    ms.author="kgremban"/>


# <a name="custom-roles-in-azure-rbac"></a>Brugerdefinerede roller i Azure RBAC


Oprette en brugerdefineret rolle i Azure Role-Based Access kontrolelement (RBAC), hvis ingen af de indbyggede roller opfylder dine behov bestemt adgang. Brugerdefinerede roller kan oprettes med [Azure PowerShell](role-based-access-control-manage-access-powershell.md), [Azure kommandolinjen](role-based-access-control-manage-access-azure-cli.md) (CLI) og [REST-API](role-based-access-control-manage-access-rest.md). Ligesom indbyggede roller, kan brugerdefinerede roller tildeles til brugere, grupper og programmer på abonnement, ressourcegruppe og ressource områder. Brugerdefinerede roller er gemt i en Azure AD-lejer og kan deles på tværs af alle abonnementer, bruger lejeren som Azure AD-mappe til subsciption.

Følgende er et eksempel på en brugerdefineret rolle til overvågning og genstarte virtuelle maskiner:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Handlinger
Egenskaben **Handlinger** for en brugerdefineret rolle angiver de Azure handlinger, som giver rollen adgang. Det er en samling af handlingen strenge, som identificerer der kan sikres handlinger af Azure ressource udbydere. Handlingen strenge, der indeholder jokertegn (\*) give adgang til alle handlinger, der svarer til strengen handling. For eksempel:

-   `*/read`giver adgang til at læse handlinger for alle ressourcetyper af alle Azure ressource udbydere.
-   `Microsoft.Network/*/read`giver adgang til at læse handlinger for alle ressourcetyper Microsoft.Network ressource hos udbyderen af Azure.
-   `Microsoft.Compute/virtualMachines/*`giver adgang til alle handlinger på virtuelle maskiner og dets underordnede ressourcetyper.
-   `Microsoft.Web/sites/restart/Action`giver adgang til genstart websteder.

Brug `Get-AzureRmProviderOperation` (i PowerShell) eller `azure provider operations show` (i Azure CLI) til listen datahandlinger af Azure ressource udbydere. Du kan også bruge disse kommandoer til at kontrollere, at en handling streng er gyldig, og at udvide jokertegn handlingen strenge.

```
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Skærmbillede af PowerShell - Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT handling OperationName](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Azure CLI skærmbillede - azure udbyder handlinger Vis "Microsoft.Compute/virtualMachines/\*/action" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Bruge egenskaben **NotActions** , hvis sæt af handlinger, som du vil tillade defineres nemmere ved at udelukke begrænsede handlinger. Adgang til det af en brugerdefineret rolle er beregnet ved at trække **NotActions** handlinger fra handlingerne **Handlinger** .

> [AZURE.NOTE] Hvis en bruger er tildelt en rolle, der ikke indeholder en handling i **NotActions**, og er tildelt en anden rolle, der giver adgang til den samme handling, kan brugeren at udføre denne handling. **NotActions** er ikke en regel for Afvis – det er blot en nem måde at oprette et sæt tilladte handlinger, når bestemte handlinger skal udelades.

## <a name="assignablescopes"></a>AssignableScopes
Egenskaben **AssignableScopes** for den brugerdefinerede rolle angiver de områder (abonnementer, grupper eller ressourcer), hvor den brugerdefinerede rolle er tilgængelige for tildeling. Du kan gøre den brugerdefinerede rolle tilgængelig for tildeling i abonnementer eller grupper, der kræver det og ikke rod brugeroplevelsen for resten af abonnementer eller grupper.

Eksempler på gyldige kan tildeles områder omfatter:

-   "/ abonnementer/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/ abonnementer/e91d47c4-76f3-4271-a796-21b4ecfe3624" - gør rollen tilgængelig for tildeling i to abonnementer.
-   "/ abonnementer/c276fc76-9cd4-44c9-99a7-4fd71546436e" - gør rollen tilgængelig for opgaven i et enkelt abonnement.
-  "/ abonnementer/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/netværk" - gør rollen tilgængelig for tildeling kun i gruppen netværk ressource.

> [AZURE.NOTE] Du skal bruge mindst et abonnement, ressourcegruppe eller ressource-ID.

## <a name="custom-roles-access-control"></a>Brugerdefinerede roller adgangskontrol
Egenskaben **AssignableScopes** for den brugerdefinerede rolle styrer også, hvem der kan få vist, redigere og slette rollen.

- Hvem kan oprette en brugerdefineret rolle?
    Ejere (og bruger Access administratorer) af abonnementer, grupper og ressourcer, der kan oprette brugerdefinerede roller til brug i disse områder.
    Den bruger, der opretter rollen, skal være i stand til at udføre `Microsoft.Authorization/roleDefinition/write` handling på alle **AssignableScopes** af rollen.

- Hvem der kan redigere en brugerdefineret rolle?
    Ejere (og bruger Access administratorer) af abonnementer, grupper og ressourcer, der kan redigere brugerdefinerede roller i disse områder. Brugere har brug for at kunne udføre de `Microsoft.Authorization/roleDefinition/write` handlingen på alle **AssignableScopes** i en brugerdefineret rolle.

- Hvem kan få vist brugerdefinerede roller?
    Alle indbyggede roller i Azure RBAC Tillad visning af roller, der er tilgængelige for tildeling. Brugere, der kan udføre på `Microsoft.Authorization/roleDefinition/read` handling på et omfang kan få vist de RBAC roller, der er tilgængelige for på dette område.

## <a name="see-also"></a>Se også
- [Rolle baseret adgangskontrol](role-based-access-control-configure.md): Introduktion til RBAC i portalen Azure.
- Lær at administrere adgang med:
    - [PowerShell](role-based-access-control-manage-access-powershell.md)
    - [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
    - [REST-API](role-based-access-control-manage-access-rest.md)
- [Indbyggede roller](role-based-access-built-in-roles.md): Få mere at vide om de roller, der leveres som standard i RBAC.
