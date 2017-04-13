<properties
    pageTitle="Oprette en access-rapport Skift historik | Microsoft Azure"
    description="Opret en rapport, der viser alle ændringer i adgang til dine Azure abonnementer med rollebaseret adgangskontrol over de seneste 90 dage."
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
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="create-an-access-change-history-report"></a>Oprette en access-rapport Skift historik

Hver gang nogen giver eller tilbagekalder adgang til på dine abonnementer få ændringerne logget på Azure begivenheder. Du kan oprette adgang Skift historik rapporter for at se alle ændringer til de seneste 90 dage.

## <a name="create-a-report-with-azure-powershell"></a>Oprette en rapport med Azure PowerShell
For at oprette en access-rapport Skift oversigt i PowerShell, skal du bruge den `Get-AzureRMAuthorizationChangeLog` kommandoen. Flere oplysninger om denne cmdlet er tilgængelige i [Galleriet PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/1.0.6/Content/ResourceManagerStartup.ps1).

Når du ringer til denne kommando, kan du angive, hvilke egenskab for de opgaver, der skal vises, herunder følgende:

| Egenskaben | Beskrivelse |
| -------- | ----------- |
| **Handling** | Om access blev tildelt eller tilbagekaldt |
| **Opkalds** | Ejeren af ansvarlig for access ændringen |
| **Dato** | Dato og klokkeslæt, hvor access blev ændret |
| **DirectoryName** | Azure Active Directory-katalogtjeneste |
| **PrincipalName** | Navnet på den bruger, gruppe eller -programmer |
| **PrincipalType** | Om opgaven blev for en bruger, gruppe eller program |
| **RoleId** | GUID for den rolle, der er tildelt eller tilbagekaldt |
| **RoleName** | Den rolle, der er tildelt eller tilbagekaldt |
| **ScopeName** | Navnet på det abonnement, ressourcegruppe eller ressource |
| **ScopeType** | Om opgaven blev abonnement, ressourcegruppe eller ressource omfang |
| **SubscriptionId** | GUID for Azure-abonnement |
| **SubscriptionName** | Navnet på det Azure abonnement |

Dette eksempel viser kommandoen en alle ændringer i access i abonnementet for de seneste syv dage:

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - skærmbillede](./media/role-based-access-control-configure/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Oprette en rapport med Azure CLI
For at oprette en access-rapport Skift oversigt i Azure kommandolinjen (CLI), skal du bruge den `azure role assignment changelog list` kommandoen.

## <a name="export-to-a-spreadsheet"></a>Eksportere til et regneark
For at gemme rapporten, eller arbejde med dataene, skal du eksportere access ændringerne i en .csv-fil. Derefter kan du se rapporten i et regneark til gennemsyn.

![Changelog vises som regneark - skærmbillede](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## <a name="see-also"></a>Se også
- Komme i gang med [Azure Role-Based adgangskontrol](role-based-access-control-configure.md)
- Arbejde med [brugerdefinerede roller i Azure RBAC](role-based-access-control-custom-roles.md)
