<properties
    pageTitle="Administrere rollebaseret adgangskontrol (RBAC) med Azure PowerShell | Microsoft Azure"
    description="Sådan administreres RBAC med Azure PowerShell, herunder listen over roller, tildele roller og slette rolletildelinger."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="07/22/2016"
    ms.author="kgremban"/>

# <a name="manage-role-based-access-control-with-azure-powershell"></a>Administrere rollebaseret adgangskontrol med Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST-API](role-based-access-control-manage-access-rest.md)


Du kan bruge rollebaseret adgangskontrol (RBAC) i portalen Azure og Azure ressource Management API til at administrere adgang til dit abonnement på et detaljerede niveau. Med denne funktion kan du give adgang til Active Directory-brugere, grupper eller principper for tjenesten ved at tildele dem nogle roller i et særlig omfang.

Før du kan bruge PowerShell til at administrere RBAC, skal du have følgende:

- Azure PowerShell version 0.8.8 eller nyere. For at installere den nyeste version og knytte den til abonnementet Azure, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

- Azure-cmdletter ressourcestyring. Installer [Azure ressourcestyring-cmdletter](https://msdn.microsoft.com/library/mt125356.aspx) i PowerShell.

## <a name="list-roles"></a>Liste over roller

### <a name="list-all-available-roles"></a>Liste over alle tilgængelige roller
Listen RBAC roller, der er tilgængelige for tildeling, og undersøge de handlinger, som de give adgang, bruge `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC PowerShell-Get AzureRmRoleDefinition - skærmbillede](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Listehandlinger af en rolle
Du kan få vist handlingerne for en bestemt rolle, bruge `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC PowerShell-Get AzureRmRoleDefinition for en bestemt rolle - skærmbillede](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Se, hvem der har adgang
Vist RBAC access-tildelinger ved at bruge `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Listen rolletildelinger på et bestemt område
Du kan se alle access-tildelinger for en bestemt abonnement, ressourcegruppe eller ressource. For eksempel bruge for at se alle de aktive tildelingerne for en ressourcegruppe skal `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell-Get AzureRmRoleAssignment for en ressourcegruppe - skærmbillede](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>Liste over roller, der er tildelt til en bruger
Du kan få vist alle de roller, der er tildelt til en bestemt bruger og de roller, der er tildelt til de grupper, som brugeren tilhører, bruge `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell-Get AzureRmRoleAssignment for en bruger - skærmbillede](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Listen klassisk tjenesteadministratoren og coadmin rolletildelinger
Liste over access tildelinger for klassisk abonnement administrator og coadministrators, brug:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Give adgang
### <a name="search-for-object-ids"></a>Søg efter objekt-id'er
Hvis du vil tildele en rolle, skal du identificere både objektet (brugeren, gruppen eller program) og omfanget.

Hvis du ikke kender abonnement-ID, kan du finde det i bladet **abonnementer** på Azure-portalen. Hvis du vil lære at forespørge efter abonnement-ID, skal du se [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) på MSDN.

For at få objekt-ID'ET for en Azure AD-gruppe skal du bruge:

    Get-AzureRmADGroup -SearchString <group name in quotes>

For at få objekt-ID'ET for en Azure AD-tjenesten hovedstolen eller et program skal du bruge:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Tildel en rolle til et program i området abonnement
Hvis du vil give adgang til et program i området abonnement, skal du bruge:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC PowerShell nye AzureRmRoleAssignment - skærmbillede](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Tildel en rolle til en bruger i området ressource gruppe
Hvis du vil give adgang til en bruger i området ressource gruppe, skal du bruge:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell nye AzureRmRoleAssignment - skærmbillede](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Tildel en rolle til en gruppe i området ressource
Hvis du vil give adgang til en gruppe i området ressource, skal du bruge:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell nye AzureRmRoleAssignment - skærmbillede](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Fjerne adgang
Hvis du vil fjerne adgangen for brugere, grupper og programmer, skal du bruge:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell-Fjern AzureRmRoleAssignment - skærmbillede](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Oprette en brugerdefineret rolle
Hvis du vil oprette en brugerdefineret rolle, skal du bruge den `New-AzureRmRoleDefinition` kommandoen.

Når du opretter en brugerdefineret rolle ved hjælp af PowerShell, skal du starte med en af de [indbyggede roller](role-based-access-built-in-roles.md). Redigere attributterne for at tilføje *Handlinger*, *notActions*eller *områder* , du vil, og derefter gemme ændringerne som en ny rolle.

I følgende eksempel starter med rollen *Bidragyder virtuelt* og bruger, til at oprette en brugerdefineret rolle kaldet *Virtuelt Operator*. Den nye rolle giver adgang til alle Læs handlinger af *Microsoft.Compute*, *Microsoft.Storage*og *Microsoft.Network* ressource udbydere og giver adgang til at starte, genstart, og overvåge virtuelle computere. Den brugerdefinerede rolle kan bruges i to abonnementer.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell-Get AzureRmRoleDefinition - skærmbillede](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## <a name="modify-a-custom-role"></a>Redigere en brugerdefineret rolle
Hvis du vil ændre en brugerdefineret rolle, skal du først bruge den `Get-AzureRmRoleDefinition` kommando for at hente den rolledefinition. Foretag de ønskede ændringer, til den rolledefinition. Til sidst skal du bruge den `Set-AzureRmRoleDefinition` kommando for at gemme den ændrede rolledefinition.

I følgende eksempel adderes den `Microsoft.Insights/diagnosticSettings/*` operator, der skal rollen *Virtuelt Operator* brugerdefineret.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell-sæt AzureRmRoleDefinition - skærmbillede](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

I følgende eksempel føjer et Azure-abonnement til kan tildeles områder af rollen *Virtuelt Operator* brugerdefineret.

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2"
Set-AzureRmRoleDefinition -Role $role)
```

![RBAC PowerShell-sæt AzureRmRoleDefinition - skærmbillede](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## <a name="delete-a-custom-role"></a>Slette en brugerdefineret rolle

Hvis du vil slette en brugerdefineret rolle, skal du bruge den `Remove-AzureRmRoleDefinition` kommandoen.

I følgende eksempel fjerner rollen *Virtuelt Operator* brugerdefineret.

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC PowerShell-Fjern AzureRmRoleDefinition - skærmbillede](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Liste over brugerdefinerede roller
Du kan få vist de roller, der er tilgængelige for på et område, du bruger den `Get-AzureRmRoleDefinition` kommandoen.

I følgende eksempel viser en liste over alle roller, der er tilgængelige for tildeling i det markerede abonnement.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC PowerShell-Get AzureRmRoleDefinition - skærmbillede](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

I eksemplet nedenfor rollen *Virtuelt Operator* brugerdefinerede er ikke tilgængelig i *Production4* abonnement fordi dette abonnement, der ikke er i **AssignableScopes** af rollen.

![RBAC PowerShell-Get AzureRmRoleDefinition - skærmbillede](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>Se også
- [Brug af Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md)
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
