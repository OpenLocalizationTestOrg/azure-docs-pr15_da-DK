<properties
    pageTitle="Tilføje en ny Azure stak lejerkonto i Azure Active Directory | Microsoft Azure"
    description="Når du anvender Microsoft Azure stak Konceptet, skal du oprette mindst én lejer brugerkonto, så du kan udforske portalen lejer."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Tilføje en ny Azure stak lejerkonto i Azure Active Directory

Efter [installation af Azure stak Konceptet](azure-stack-run-powershell-script.md)skal du en lejer brugerkonto, så du kan udforske portalen lejer og teste dit tilbud og planer. Du kan oprette en lejerkonto ved [hjælp af portalen Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) eller ved [hjælp af PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Oprette en Azure stak lejerkonto ved hjælp af portalen Azure

Du skal have en Azure abonnement bruge Azure-portalen.

1. Log på [Azure](http://manage.windowsazure.com).

2.  I Microsoft Azure venstre navigationslinje skal du klikke på **Active Directory**.

3.  Klik på den mappe, du vil bruge til Azure stak på listen mappe, eller Opret en ny.

4.  Klik på **brugere**på siden denne mappe.

5.  Klik på **Tilføj bruger**.

6.  Vælg **ny bruger i organisationen**i guiden **Tilføj bruger** på listen **Type af bruger** .

7.  Skriv et navn for brugeren, i feltet **brugernavn** .

8.  I den **@** skal du vælge den relevante post.

9.  Klik på pilen Næste.

10.  Skriv et **Fornavn**, **Efternavn**og **viste navn**på siden **brugerprofil** i guiden.

11. Vælg **brugeren**på listen **rolle** .

12. Klik på pilen Næste.

13. Klik på **Opret**på siden **Hent midlertidige adgangskode** .

14. Kopiere den **nye adgangskode**.

15. Log på Microsoft Azure med den nye konto. Ændre adgangskoden når du bliver bedt om.

16. Log på `https://portal.azurestack.local` med den nye konto til at se portalen lejer.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Oprette en Azure stak lejerkonto ved hjælp af PowerShell

Hvis du ikke har et Azure-abonnement, kan du ikke bruge portalen Azure til at tilføje en lejer brugerkonto. I dette tilfælde kan du bruge Azure Active Directory-modulet til Windows PowerShell i stedet.

> [AZURE.NOTE] Hvis du bruger Microsoft Account (Live ID) til at udrulle Azure stak konceptet, kan du bruge AAD PowerShell til at oprette lejerkonto. 

1.  Installere [Microsoft Online Services Logonassistent for IT-fagfolk RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).

2.  Installer [Azure Active Directory-modulet til Windows PowerShell (64-bit version)](http://go.microsoft.com/fwlink/p/?linkid=236297) , og åbne den.

3.  Kør følgende cmdletter:




    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack PoC
   
            $msolcred = get-credential
    
    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".
    
            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId
    
    ```

4.  Log på Microsoft Azure med den nye konto. Ændre adgangskoden når du bliver bedt om.

5.  Log på `https://portal.azurestack.local` med den nye konto til at se portalen lejer.



