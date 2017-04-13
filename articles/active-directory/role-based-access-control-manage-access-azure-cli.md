<properties
    pageTitle="Administrere rollebaseret adgangskontrol (RBAC) med Azure CLI | Microsoft Azure"
    description="Lær at administrere rollebaseret adgangskontrol (RBAC) med Azure kommandolinjen ved listen over roller og rolle handlinger og ved at tildele roller til abonnement og programmet områder."
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

# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Administrere rollebaseret adgangskontrol med Azure kommandolinjen

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST-API](role-based-access-control-manage-access-rest.md)

Du kan bruge rollebaseret adgangskontrol (RBAC) i portalen Azure og Azure ressourcestyring API til at administrere adgang til dit abonnement og ressourcer på et detaljerede niveau. Med denne funktion kan du give adgang til Active Directory-brugere, grupper eller principper for tjenesten ved at tildele dem nogle roller i et særlig omfang.

Før du kan bruge kommandolinjen Azure (CLI) til at administrere RBAC, skal du have følgende:

- Azure CLI version 0.8.8 eller nyere. For at installere den nyeste version og knytte den til dit Azure abonnement skal du se [installere og konfigurere Azure CLI](../xplat-cli-install.md).
- Azure ressourcestyring i Azure CLI. Gå til at [bruge Azure CLI med ressourcestyring](../xplat-cli-azure-resource-manager.md) få mere at vide.

## <a name="list-roles"></a>Liste over roller

### <a name="list-all-available-roles"></a>Liste over alle tilgængelige roller
Du kan få vist alle tilgængelige roller, kan du bruge:

        azure role list

I følgende eksempel vises på listen over *alle tilgængelige roller*.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![RBAC Azure kommandolinjen - azure rolle liste - skærmbillede](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### <a name="list-actions-of-a-role"></a>Listehandlinger af en rolle
Du kan få vist handlingerne for en rolle, du brug:

    azure role show "<role name>"

I følgende eksempel vises handlingerne af rollerne *bidragyder* og *Virtuelt bidragyder* .

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![RBAC Azure kommandolinjen - azure rolle Vis - skærmbillede](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##  <a name="list-access"></a>Liste over access
### <a name="list-role-assignments-effective-on-a-resource-group"></a>Listen rolletildelinger træder i kraft på en ressourcegruppe
Du kan få vist de rolletildelinger, der findes i en ressourcegruppe, bruge:

    azure role assignment list --resource-group <resource group name>

I følgende eksempel viser rolletildelinger i gruppen *pharma-salg-projecforcast* .

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![RBAC Azure kommandolinjen - azure rolle tildelingslisten efter gruppe skærmbillede](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### <a name="list-role-assignments-for-a-user"></a>Listen rolletildelinger for en bruger
Du kan få vist rolletildelinger for en bestemt bruger og de opgaver, der er tildelt til en brugers grupper, du brug:

    azure role assignment list --signInName <user email>

Du kan også se rolletildelinger, der nedarves fra grupper ved at ændre kommandoen:

    azure role assignment list --expandPrincipalGroups --signInName <user email>

I følgende eksempel vises de rolletildelinger, der er tildelt den *sameert@aaddemo.com* bruger. Dette omfatter roller, der er tildelt direkte til brugeren og roller, der nedarves fra grupper.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![RBAC Azure kommandolinjen - azure rolle tildelingsliste af bruger - skærmbillede](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##  <a name="grant-access"></a>Give adgang
Hvis du vil give adgang, når du har identificeret den rolle, du vil tildele, skal du bruge:

    azure role assignment create

### <a name="assign-a-role-to-group-at-the-subscription-scope"></a>Tildel en rolle til gruppe i området abonnement
Hvis du vil tildele en rolle til en gruppe i området abonnement, skal du bruge:

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

I følgende eksempel tildeles rollen *læser* til *Christine Koch Team* i området *abonnement* .


![RBAC Azure kommandolinjen - azure rolletildeling oprette ved gruppe skærmbillede](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Tildel en rolle til et program i området abonnement
Hvis du vil tildele en rolle til et program i området abonnement, skal du bruge:

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

I følgende eksempel giver rollen *bidragyder* et *Azure AD* -program på det markerede abonnement.

 ![RBAC Azure kommandolinjen - azure rolletildeling oprette af program](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Tildel en rolle til en bruger i området ressource gruppe
Hvis du vil tildele en rolle til en bruger i området ressource gruppe, skal du bruge:

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

I følgende eksempel giver rollen *Bidragyder Virtual Machine* til *samert@aaddemo.com* bruger i området *Pharma-salg-ProjectForcast* ressource gruppe.

![RBAC Azure kommandolinjen - azure rolletildeling oprette ved bruger skærmbillede](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Tildel en rolle til en gruppe i området ressource
Hvis du vil tildele en rolle til en gruppe i området ressource, skal du bruge:

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

I følgende eksempel tildeler rollen *Bidragyder Virtual Machine* til en *Azure AD* -gruppe på et *undernet*.

![RBAC Azure kommandolinjen - azure rolletildeling oprette ved gruppe skærmbillede](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##  <a name="remove-access"></a>Fjerne adgang
Hvis du vil fjerne en rolletildeling, skal du bruge:

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

I følgende eksempel fjerner rolletildeling *Virtuelt bidragyder* fra den *sammert@aaddemo.com* brugeren på *Pharma-salg-ProjectForcast* ressourcegruppe.
Eksemplet fjerner derefter rolletildelingen fra en gruppe på abonnementet.

![RBAC Azure kommandolinjen - azure rolle tildeling Slet - skærmbillede](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## <a name="create-a-custom-role"></a>Oprette en brugerdefineret rolle
Hvis du vil oprette en brugerdefineret rolle, skal du bruge:

    azure role create --inputfile <file path>

I følgende eksempel oprettes en brugerdefineret rolle kaldet *Virtuelt Operator*. Den brugerdefinerede rolle giver adgang til alle Læs handlinger af *Microsoft.Compute*, *Microsoft.Storage*og *Microsoft.Network* ressource udbydere og giver adgang til at starte, genstart, og overvåge virtuelle computere. Den brugerdefinerede rolle kan bruges i to abonnementer. I dette eksempel bruges en JSON-fil som input.

![JSON - brugerdefineret rolledefinition - skærmbillede](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![RBAC Azure kommandolinjen - azure rolle oprette - skærmbillede](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## <a name="modify-a-custom-role"></a>Redigere en brugerdefineret rolle

Hvis du vil ændre en brugerdefineret rolle, skal du først bruge den `azure role show` kommando for at hente rolledefinition. Foretag de ønskede ændringer, til den rolle definitionsfil. Til sidst skal du bruge `azure role set` at gemme den ændrede rolledefinition.

    azure role set --inputfile <file path>

I følgende eksempel adderes handlingen *Microsoft.Insights/diagnosticSettings/* **Handlinger**, og en Azure abonnement på **AssignableScopes** af rollen virtuelt Operator brugerdefineret.

![JSON - ændre brugerdefineret rolledefinition - skærmbillede](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![RBAC Azure kommandolinjen - azure rolle set - skærmbillede](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## <a name="delete-a-custom-role"></a>Slette en brugerdefineret rolle

Hvis du vil slette en brugerdefineret rolle, skal du først bruge den `azure role show` kommando for at bestemme rollen **ID** . Derefter skal du bruge den `azure role delete` kommandoen til at slette rollen ved at angive **ID**.

I følgende eksempel fjerner rollen *Virtuelt Operator* brugerdefineret.

![RBAC Azure kommandolinjen - azure rolle Slet - skærmbillede](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## <a name="list-custom-roles"></a>Liste over brugerdefinerede roller

Du kan få vist de roller, der er tilgængelige for på et område, du bruger den `azure role list` kommandoen.

I følgende eksempel viser en liste over alle rolle, der er tilgængelige for tildeling i det markerede abonnement.

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![RBAC Azure kommandolinjen - azure rolle liste - skærmbillede](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

I eksemplet nedenfor rollen *Virtuelt Operator* brugerdefinerede er ikke tilgængelig i *Production4* abonnement fordi dette abonnement, der ikke er i **AssignableScopes** af rollen.

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![RBAC Azure kommandolinjen - azure rolle liste til brugerdefinerede roller - skærmbillede](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## <a name="rbac-topics"></a>RBAC emner
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
