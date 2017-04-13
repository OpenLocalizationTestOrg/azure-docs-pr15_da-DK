<properties
    pageTitle="Oprette en elastiske database puljen med C# | Microsoft Azure"
    description="Bruge C# database udviklingsteknikker til at oprette en SVG elastiske database pulje i Azure SQL-Database, så du kan dele ressourcer på tværs af mange databaser."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>

# <a name="create-an-elastic-database-pool-with-cx23"></a>Oprette en elastiske database puljen med C & #x 23;

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


I denne artikel beskrives, hvordan du bruger C# til at oprette en SQL Azure elastiske database puljen til [Azure SQL Database-bibliotek til .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Hvis du vil oprette en enkeltstående SQL-database, skal du se [Brug C# til at oprette en SQL-database med SQL Database-bibliotek til .NET](sql-database-get-started-csharp.md).

Biblioteket Azure SQL-Database til .NET indeholder en [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md)-baseret API, der ombrydes [Ressourcestyring-baserede SQL Database REST-API](https://msdn.microsoft.com/library/azure/mt163571.aspx).

>[AZURE.NOTE] Mange nye funktioner i SQL-Database understøttes kun, når du bruger [Azure ressourcestyring implementeringsmodel](../azure-resource-manager/resource-group-overview.md), så du altid skal bruge nyeste **Azure SQL Database Management bibliotek til .NET ([dokumenter](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet pakke](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Indstillingen ældre [Klassisk implementeringsmodel baserede biblioteker](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) understøttes for at opnå bagudkompatibilitet kun, så vi anbefaler, at du bruger de nyere ressourcestyring baseret biblioteker.

For at fuldføre trinnene i denne artikel skal bruge du følgende:

- Et Azure-abonnement. Hvis du skal bruge et Azure abonnement skal du blot klikke på **Gratis konto** øverst på denne side, og derefter vende tilbage til denne artikel er færdig.
- Visual Studio. Finde en gratis kopi af Visual Studio, på siden [Visual Studio-overførsler](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="create-a-console-app-and-install-the-required-libraries"></a>Oprette en console app og installere nødvendige DLL-filer

1. Start Visual Studio.
2. Klik på **filer** > **nye** > **Project**.
3. Oprette et C#- **Konsollen programmet** , og kald den: *SqlElasticPoolConsoleApp*


For at oprette en SQL-database med C#, skal du indlæse de nødvendige management biblioteker (ved hjælp af [pakke manager-konsollen](http://docs.nuget.org/Consume/Package-Manager-Console)):

1. Klik på **værktøjer** > **NuGet Package Manager** > **Pakke Manager-konsollen**.
2. Skriv `Install-Package Microsoft.Azure.Management.Sql –Pre` at installere [Microsoft Azure SQL Management bibliotek](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).
3. Skriv `Install-Package Microsoft.Azure.Management.ResourceManager –Pre` at installere [Microsoft Azure ressourcestyring bibliotek](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Skriv `Install-Package Microsoft.Azure.Common.Authentication –Pre` at installere [Microsoft Azure almindelige Authentication Library](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 



> [AZURE.NOTE] Eksemplerne i denne artikel kan bruge en synkron form for hver API-anmodning og blokere indtil fuldførelse af RESTEN ringe på den underliggende tjeneste. Der er tilgængelige asynkrone metoder.


## <a name="create-a-sql-elastic-database-pool---c-example"></a>Oprette en SQL elastiske database pulje - C# eksempel

I følgende eksempel oprettes en ressourcegruppe, server, firewallregel, elastiske puljen, og opretter derefter en SQL-database i gruppen. Se, [oprette en tjeneste sikkerhedskonto til at få adgang til ressourcer](#create-a-service-principal-to-access-resources) for at få den `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` variabler.

Erstatte indholdet af **Program.cs** med følgende, og Opdater den `{variables}` med din app-værdier (du skal ikke medtage den `{}`).


```
using Microsoft.Azure;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.Azure.Management.Sql;
using Microsoft.Azure.Management.Sql.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace SqlElasticPoolConsoleApp
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

        static string _poolName = "{pool-name}";
        static string _poolEdition = "{Standard}";
        static int _poolDtus = {100};
        static int _databaseMinDtus = {10};
        static int _databaseMaxDtus = {100};

        static string _databaseName = "{elasticdbfromcsarticle}";



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

            Console.WriteLine("Elastic pool...");
            ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);
            Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);
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



        static ElasticPoolCreateOrUpdateResponse CreateOrUpdateElasticDatabasePool(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string poolName, string poolEdition, int poolDtus, int databaseMinDtus, int databaseMaxDtus)
        {
            // Retrieve the server that will host this elastic pool
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus,
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlMgmtClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, poolName, newPoolParameters);
            return newPoolResponse;
        }




        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string poolName)
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
                    ElasticPoolName = poolName
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
```





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

- [Administrere din puljen](sql-database-elastic-pool-manage-csharp.md)
- [Opret elastiske job](sql-database-elastic-jobs-overview.md): elastiske job gør det muligt at køre T-SQL-scripts mod en hvilken som helst antal databaser i en gruppe.
- [Skalering af med Azure SQL-Database](sql-database-elastic-scale-introduction.md): bruge elastiske Databaseværktøjer skalere ud.

## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure ressource Management API'er](https://msdn.microsoft.com/library/azure/dn948464.aspx)
