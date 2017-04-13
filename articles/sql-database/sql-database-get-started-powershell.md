<properties
    pageTitle="Ny konfiguration af SQL-Database med PowerShell | Microsoft Azure"
    description="Få mere at vide nu til at oprette en SQL-database med PowerShell. Almindelige database konfigurationsopgaver kan administreres via PowerShell-cmdlet'er."
    keywords="oprette sql-database database konfiguration"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/19/2016"
    ms.author="sstein"/>

# <a name="create-a-sql-database-and-perform-common-database-setup-tasks-with-powershell-cmdlets"></a>Oprette en SQL-database og udføre almindelige opgaver til opsætning af databasen med PowerShell-cmdlet'er


> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Lær at oprette en SQL-database ved hjælp af PowerShell-cmdlet'er. (Se [oprette en ny gruppe elastiske database med PowerShell](sql-database-elastic-pool-create-powershell.md)til at oprette elastiske databaser,).


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="database-setup-create-a-resource-group-server-and-firewall-rule"></a>Konfiguration af database: oprette en ressourcegruppe, server og firewallregel

Når du har adgang til at køre cmdletter mod abonnementet markerede Azure, er næste trin oprettelse af ressourcegruppen, der indeholder den server, hvor databasen skal oprettes. Du kan redigere den næste kommando til at bruge uanset gyldig placering, du vælger. Køre **(Get-AzureRmLocation | Hvor-objekt {$_. Udbydere - eq "Microsoft.Sql"}). Placering** til at få en liste over gyldige placeringer.

Kør følgende kommando for at oprette en ressourcegruppe:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### <a name="create-a-server"></a>Oprette en server

SQL-databaser oprettes i Azure SQL Database-servere. Køre [ny-AzureRmSqlServer] (https://msdn.microsoft.com/library/azure/mt603715(v=azure.300\).aspx) til at oprette en server. Navnet på din server skal være entydige for alle servere, der Azure SQL-Database. Hvis servernavnet er allerede taget, får du en fejl. Også værd at bemærke er, at denne kommando kan tage flere minutter at gennemføre. Du kan redigere den kommando til at bruge en hvilken som helst gyldig placering, du vælger, men du skal bruge den samme placering, du har brugt til ressourcegruppen, der er oprettet i ovenstående trin.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

Når du kører denne kommando, bliver du bedt om dit brugernavn og din adgangskode. Ikke angive dine Azure legitimationsoplysninger. I stedet angive det brugernavn og adgangskode for at oprette som serveradministratoren. Scriptet i bunden af denne artikel viser, hvordan du kan indstille server-legitimationsoplysninger i kode.

Detaljer om server vises, når serveren er blevet oprettet.

### <a name="configure-a-server-firewall-rule-to-allow-access-to-the-server"></a>Konfigurere en server firewallregel for at give adgang til serveren

For at få adgang til serveren, skal du oprette en regel. Køre [ny-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx) kommandoen, erstatte start- og IP-adresser med gyldige værdier for din computer.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Regeldetaljer firewall vises, når reglen er blevet oprettet.

Tillade andre Azure tjenester til at få adgang til serveren, tilføje en firewallregel og angive både StartIpAddress og EndIpAddress til 0.0.0.0. Denne regel tillader Azure trafik fra en hvilken som helst Azure-abonnement for at få adgang til serveren.

Du kan finde yderligere oplysninger finder [Azure SQL Database-firewallen](sql-database-firewall-configure.md).


## <a name="create-a-sql-database"></a>Oprette en SQL-database

Nu har du en ressourcegruppe, en server, og en firewallregel, der er konfigureret, så du kan få adgang til serveren.

Følgende ny-AzureRmSqlDatabase (https://msdn.microsoft.com/library/azure/mt619339(v=azure.300\).aspx) kommandoen opretter en (tom) SQL-database Standard service lag, med en S1 ydeevneniveau:


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Database oplysninger vises, når databasen er blevet oprettet.

## <a name="create-a-sql-database-powershell-script"></a>Oprette en SQL-database PowerShell-script

Følgende PowerShell-script opretter en SQL-database og alle dens afhængige ressourcer. Erstat alle `{variables}` med værdier, der er specifikke for dit abonnement og ressourcer (fjerne **{}** , når du indstiller dit værdier).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## <a name="next-steps"></a>Næste trin
Når du opretter en SQL-database og udføre grundlæggende database konfigurationsopgaver, er du klar til følgende:

- [Administrere SQL-Database med PowerShell](sql-database-manage-powershell.md)
- [Oprette forbindelse til SQL-Database med SQL Server Management Studio og udføre en stikprøve T-SQL-forespørgsel](sql-database-connect-query-ssms.md)


## <a name="additional-resources"></a>Yderligere ressourcer

- [Azure SQL Database-cmdletter] (https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
- [Azure SQL-Database](https://azure.microsoft.com/documentation/services/sql-database/)
