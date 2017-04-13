<properties
    pageTitle="Prøv SQL-Database: Brug C# til at oprette en SQL-database | Microsoft Azure"
    description="Prøv SQL-Database til udvikling af SQL og C# apps, og oprette en Azure SQL-Database med C# ved hjælp af biblioteket SQL-Database til .NET."
    keywords="Prøv sql, sql c#"   
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="csharp"
   ms.workload="data-management"
   ms.date="10/04/2016"
   ms.author="sstein"/>

# <a name="try-sql-database-use-c-to-create-a-sql-database-with-the-sql-database-library-for-net"></a>Prøv SQL-Database: Brug C# til at oprette en SQL-database med biblioteket SQL-Database til .NET


> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Lær, hvordan du bruger C# til at oprette en Azure SQL-database til [Microsoft Azure SQL Management bibliotek til .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). I denne artikel beskrives, hvordan du opretter en enkelt database med SQL- og C#. For at oprette elastiske database grupper skal du se [oprette en elastiske database puljen](sql-database-elastic-pool-create-csharp.md).

Biblioteket Azure SQL Database administration til .NET indeholder en [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md)-baseret API, der ombrydes [Ressourcestyring-baserede SQL Database REST-API](https://msdn.microsoft.com/library/azure/mt163571.aspx).

>[AZURE.NOTE] Mange nye funktioner i SQL-Database understøttes kun, når du bruger [Azure ressourcestyring implementeringsmodel](../azure-resource-manager/resource-group-overview.md), så du altid skal bruge nyeste **Azure SQL Database Management bibliotek til .NET ([dokumenter](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet pakke](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Indstillingen ældre [Klassisk implementeringsmodel baserede biblioteker](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) understøttes for at opnå bagudkompatibilitet kun, så vi anbefaler, at du bruger de nyere ressourcestyring baseret biblioteker.

For at fuldføre trinnene i denne artikel skal bruge du følgende:

- Et Azure-abonnement. Hvis du skal bruge et Azure abonnement skal du blot klikke på **Gratis konto** øverst på denne side, og derefter vende tilbage til denne artikel er færdig.
- Visual Studio. Finde en gratis kopi af Visual Studio, på siden [Visual Studio-overførsler](https://www.visualstudio.com/downloads/download-visual-studio-vs) .

>[AZURE.NOTE] I denne artikel opretter en ny, tom SQL-database. Ændre metoden *CreateOrUpdateDatabase(...)* i følgende eksempel på at kopiere databaser, skalere databaser skal du oprette en database i en gruppe, osv. Du kan finde flere oplysninger [DatabaseCreateMode](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreatemode.aspx) og [DatabaseProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databaseproperties.aspx) klasser.



## <a name="create-a-console-app-and-install-the-required-libraries"></a>Oprette en console app og installere nødvendige DLL-filer

1. Start Visual Studio.
2. Klik på **filer** > **nye** > **Project**.
3. Oprette et C#- **Konsollen programmet** , og kald den: *SqlDbConsoleApp*


For at oprette en SQL-database med C#, skal du indlæse de nødvendige management biblioteker (ved hjælp af [pakke manager-konsollen](http://docs.nuget.org/Consume/Package-Manager-Console)):

1. Klik på **værktøjer** > **NuGet Package Manager** > **Pakke Manager-konsollen**.
2. Skriv `Install-Package Microsoft.Azure.Management.Sql –Pre` at installere den nyeste [Microsoft Azure SQL Management bibliotek](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).
3. Skriv `Install-Package Microsoft.Azure.Management.ResourceManager –Pre` at installere [Microsoft Azure ressourcestyring bibliotek](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Skriv `Install-Package Microsoft.Azure.Common.Authentication –Pre` at installere [Microsoft Azure almindelige Authentication Library](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 



> [AZURE.NOTE] Eksemplerne i denne artikel kan bruge en synkron form for hver API-anmodning og blokere indtil fuldførelse af RESTEN ringe på den underliggende tjeneste. Der er tilgængelige asynkrone metoder.


## <a name="create-a-sql-database-server-firewall-rule-and-sql-database---c-example"></a>Oprette en SQL-databaseserver, firewallregel og SQL database - C# eksempel

I følgende eksempel oprettes en ressourcegruppe, server, firewallregel og en SQL-database. Se, [oprette en tjeneste sikkerhedskonto til at få adgang til ressourcer](#create-a-service-principal-to-access-resources) for at få den `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` variabler.

Erstatte indholdet af **Program.cs** med følgende, og Opdater den `{variables}` med din app-værdier (du skal ikke medtage den `{}`).


    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbConsoleApp
    {
    class Program
       {
        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _databaseName = "{dbfromcsarticle}";
        static string _databaseEdition = DatabaseEditions.Basic;
        static string _databasePerfLevel = ""; // "S0", "S1", and so on here for other tiers


        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _databaseEdition, _databasePerfLevel);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string databaseEdition, string databasePerfLevel)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
      }
    }





## <a name="create-a-service-principal-to-access-resources"></a>Oprette en tjeneste vigtigste få adgang til ressourcer

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



## <a name="next-steps"></a>Næste trin
Nu, hvor du har prøvet SQL-Database og konfigurere en database med C#, du er klar til, i følgende artikler:

- [Oprette forbindelse til SQL-Database med SQL Server Management Studio og udføre en stikprøve T-SQL-forespørgsel](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database](https://azure.microsoft.com/documentation/services/sql-database/)
- [Database klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.management.sql.models.database.aspx)




<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png
