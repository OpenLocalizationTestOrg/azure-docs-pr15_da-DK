<properties
    pageTitle="Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af PowerShell | Microsoft Azure"
    description="Lær, hvordan du konfigurerer firewallen for IP-adresser, få adgang til Azure SQL-databaser."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="sstein"/>


# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af PowerShell


> [AZURE.SELECTOR]
- [Oversigt](sql-database-firewall-configure.md)
- [Azure-portalen](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST-API](sql-database-configure-firewall-settings-rest.md)


Azure SQL-Database bruger firewallregler til at tillade forbindelser til servere og databaser. Du kan definere serverniveau og database niveau firewall-indstillinger for master databasen eller en brugerdatabase i dine SQL-databaseserver til at tillade adgang til databasen.

> [AZURE.IMPORTANT] Hvis du vil tillade programmer fra Azure for at oprette forbindelse til din-databaseserver, skal være aktiveret Azure forbindelser. Du kan finde flere oplysninger om firewallregler og aktivering forbindelser fra Azure [Azure SQL Database-firewallen](sql-database-firewall-configure.md). Hvis du laver forbindelser inden for Azure skyen kanten, skal du muligvis åbne nogle ekstra TCP-porte. Finde flere oplysninger i "version 12 af SQL-Database: uden for vs i" sektion af [porte ud over 1433 til ADO.NET 4.5 og version 12 SQL-Database](sql-database-develop-direct-route-ports-adonet-v12.md).


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>Oprette server firewallregler

Serverniveau firewall regler kan oprettes, opdateres, og slettes ved hjælp af Azure PowerShell.

For at oprette en ny firewallregel for serverniveau skal udføre på [ny-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx) cmdlet. I følgende eksempel gør det muligt for et område af IP-adresser på Contoso-serveren.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'       

Hvis du vil ændre en eksisterende serverniveau firewallregel skal udføre på [sæt-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx) cmdlet. I følgende eksempel ændres området af acceptable IP-adresser for den regel, der hedder ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

Hvis du vil slette en eksisterende serverniveau firewallregel, skal du udføre [Fjern-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx) cmdlet. I følgende eksempel sletter den regel, der hedder ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>Administrere firewallregler ved hjælp af PowerShell

Du kan også bruge PowerShell til at administrere firewallregler. Få mere at vide under følgende emner:

* [Ny AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx)
* Fjern-AzureRmSqlServerFirewallRule (https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx)
* [Sæt-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603586(v=azure.300\).aspx)


## <a name="next-steps"></a>Næste trin

Transact-SQL til at oprette serverniveau og database niveau firewallregler, se oplysninger om, hvordan du bruger [ved hjælp af T-SQL konfigurere Azure SQL-Database serverniveau og database niveau firewallregler](sql-database-configure-firewall-settings-tsql.md).

Finde oplysninger om, hvordan du opretter serverniveau firewallregler ved hjælp af andre metoder, i:

- [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af portalen Azure](sql-database-configure-firewall-settings.md)
- [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af REST-API](sql-database-configure-firewall-settings-rest.md)

Se [oprette en SQL-database i minutter ved hjælp af portalen Azure](sql-database-get-started.md)selvstudium til oprettelse af en database.
Du kan finde hjælp til at oprette forbindelse til en Azure SQL-database fra Åbn kilde eller tredjepartsprogrammer, [klienten Hurtig start kodeeksempler til SQL-Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
For at forstå, hvordan du navigerer til databaser, skal du se [administrere database access og logon-sikkerhed](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Yderligere ressourcer

- [Sikring af din database](sql-database-security.md)
- [Sikkerhedscenter til SQL Server-Database Engine og Azure SQL-Database](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->
