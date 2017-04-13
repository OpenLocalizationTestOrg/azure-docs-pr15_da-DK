<properties
    pageTitle="Give brugertilladelser til bestemte øvelse politikker | Microsoft Azure"
    description="Lær at give brugertilladelser til bestemte øvelse politikker i DevTest øvelser baseret på hver enkelt brugers behov"
    services="devtest-lab,virtual-machines,visual-studio-online"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="tarcher"/>

# <a name="grant-user-permissions-to-specific-lab-policies"></a>Give brugertilladelser til bestemte øvelse politikker

## <a name="overview"></a>Oversigt

I denne artikel illustrerer, hvordan du kan bruge PowerShell til at tildele brugertilladelser til en bestemt øvelse politik. På denne måde kan tilladelser anvendes baseret på hver enkelt brugers behov. Du vil muligvis give mulighed for at ændre indstillingerne for politik VM, men ikke omkostninger politikkerne for en bestemt bruger.

## <a name="policies-as-resources"></a>Politikker som ressourcer

Som beskrevet i artiklen [Azure rollebaseret adgangskontrol](../active-directory/role-based-access-control-configure.md) , aktiverer RBAC detaljerede access administration af ressourcer til Azure. Ved hjælp af RBAC, kan du udskille opgaver i teamet DevOps og give kun mængden adgang til brugere, at de skal udføre deres arbejde.

En politik er en ressourcetype, der gør det muligt for handlingen RBAC **Microsoft.DevTestLab/labs/policySets/policies/**i DevTest øvelser. Hver øvelse politik er en ressource i ressourcetype politik og kan tildeles som et område til en RBAC rolle.

For eksempel for at give brugere læse-og skriveadgang tilladelse til at politikken **Tilladt VM størrelser** , du vil oprette en brugerdefineret rolle, der fungerer med **Microsoft.DevTestLab/labs/policySets/policies/** * handling, og Tildel derefter de relevante brugere til denne brugerdefinerede rolle i omfanget af * *Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Hvis du vil vide mere om brugerdefinerede roller i RBAC, kan du se det [brugerdefinerede roller adgangskontrol](../active-directory/role-based-access-control-custom-roles.md).

##<a name="creating-a-lab-custom-role-using-powershell"></a>Oprette en øvelse brugerdefineret rolle ved hjælp af PowerShell
For at komme i gang, skal du læse følgende artikel, som forklarer, hvordan du installerer og konfigurerer Azure PowerShell-cmdlet'er: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Når du har konfigureret Azure PowerShell-cmdletter, kan du udføre følgende opgaver:

- Liste over alle handlinger/handlingerne for en ressource-udbyder
- Liste over handlinger i en bestemt rolle:
- Oprette en brugerdefineret rolle

Følgende PowerShell-script viser eksempler på, hvordan du udfører disse opgaver:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##<a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Når du tildeler tilladelser til en bruger til en bestemt regel ved hjælp af brugerdefinerede roller
Når du har angivet din brugerdefinerede roller, kan du tildele dem til brugere. Hvis du vil tildele en brugerdefineret rolle til en bruger, skal du først hente **ObjectId** , der repræsenterer pågældende bruger. Brug cmdlet'en **Get-AzureRmADUser** gør.

I eksemplet nedenfor er **ObjectId** for brugeren, *SomeUser* 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Når du har **ObjectId** for brugeren og navnet på en brugerdefineret, kan du tildele rollen til brugeren med **Ny AzureRmRoleAssignment** cmdlet:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

I det forrige eksempel bruges politikken **AllowedVmSizesInLab** . Du kan bruge en af følgende politikker:

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Næste trin

Du har en gang tildelt brugertilladelser til bestemte øvelse politikker her er nogle næste trin at overveje:

- [Sikker adgang til en øvelse](devtest-lab-add-devtest-user.md).

- [Indstille øvelse politikker](devtest-lab-set-lab-policy.md).

- [Opret en øvelse skabelon](devtest-lab-create-template.md).

- [Opret brugerdefinerede elementer til din FOS](devtest-lab-artifact-author.md).

- [Tilføj en VM med elementer til en øvelse](devtest-lab-add-vm-with-artifacts.md).
