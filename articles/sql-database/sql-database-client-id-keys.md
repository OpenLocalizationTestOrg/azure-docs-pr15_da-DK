<properties
   pageTitle="Få de påkrævede værdier til godkendelse af et program tilladelse til at få adgang til SQL-Database fra kode | Microsoft Azure"
   description="Oprette en tjeneste hovedstolen for at få adgang til SQL-Database fra kode."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Få de påkrævede værdier til godkendelse af et program tilladelse til at få adgang til SQL-Database fra kode

Hvis du vil oprette og administrere SQL-Database fra kode skal du registrere din app i Azure Active Directory (AAD) domæne i abonnementet, hvor dine Azure ressourcer er blevet oprettet.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Oprette en tjeneste vigtigste få adgang til ressourcer fra et program

Du skal have den nyeste [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) installeret og kører. Du kan finde detaljerede oplysninger, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

Følgende PowerShell-script opretter programmet Active Directory (AD) og service hovedstolen, vi har brug for at godkende vores C#-app. Scriptet udskriver værdier, der skal bruges i den foregående C#-eksempel. Du kan finde detaljerede oplysninger, [Bruge Azure PowerShell til at oprette en sikkerhedskonto til at få adgang til ressourcer tjeneste](../resource-group-authenticate-service-principal.md).

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Se også

- [Oprette en SQL-database med C#](sql-database-get-started-csharp.md)
- [Oprette forbindelse til SQL-Database ved hjælp af Azure Active Directory-godkendelse](sql-database-aad-authentication.md)


