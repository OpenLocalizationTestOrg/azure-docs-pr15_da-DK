<properties
    pageTitle="Azure SQL-Database serverniveau og database niveau firewallregler ved hjælp af T-SQL | Microsoft Azure"
    description="Lær, hvordan du konfigurerer firewallen for IP-adresser, få adgang til Azure SQL-databaser."
    services="sql-database"
    documentationCenter=""
    authors="BYHAM"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="08/30/2016"
    ms.author="rickbyh"/>


# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Konfigurere ved hjælp af T-SQL Azure SQL-Database serverniveau og database niveau firewallregler


> [AZURE.SELECTOR]
- [Oversigt](sql-database-firewall-configure.md)
- [Azure-portalen](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST-API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database bruger firewallregler til at tillade forbindelser til servere og databaser. Du kan definere serverniveau og database niveau firewall-indstillinger for slidemaster eller en brugerdatabase i serverens Azure SQL-Database til at tillade adgang til databasen.

> [AZURE.IMPORTANT] Hvis du vil tillade programmer fra Azure for at oprette forbindelse til din-databaseserver, skal være aktiveret Azure forbindelser. Du kan finde flere oplysninger om firewallregler og aktivering forbindelser fra Azure [Azure SQL Database-firewallen](sql-database-firewall-configure.md). Hvis du laver forbindelser inden for Azure skyen kanten, skal du muligvis åbne nogle ekstra TCP-porte. Finde flere oplysninger i **version 12 af SQL-Database: uden for vs i** sektion af [porte ud over 1433 til ADO.NET 4.5 og version 12 SQL-Database](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="server-level-firewall-rules"></a>Serverniveau firewallregler

Kun serverniveau vigtigste logon eller Azure Active Directory-administrator kan oprette en firewallregel for serverniveau ved hjælp af Transact-SQL.

1. Start et forespørgselsvindue og oprette forbindelse til den virtuelle master database ved hjælp af SQL Server Management Studio.
2. Serverniveau firewallregler kan markeret, oprettes, opdateres eller slettes fra i forespørgselsvinduet.
3. For at oprette eller opdatere serverniveau firewallregler skal udføre på `sp_set_firewall_rule` lagret procedure. I følgende eksempel gør det muligt for et område af IP-adresser på Contoso-serveren.<br/>Start ved at se, hvilke regler allerede findes.

        SELECT * FROM sys.firewall_rules ORDER BY name;

    Føj en firewallregel.

        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

    Slette en firewallregel for serverniveau, udføre sp_delete_firewall_rule lagrede procedure. I følgende eksempel sletter den regel, der hedder ContosoFirewallRule.
 
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 Se [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) og [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)kan finde flere oplysninger om disse lagrede procedurer.

## <a name="database-level-firewall-rules"></a>Database niveau firewallregler

Kun databasebruger med tilladelsen **KONTROLELEMENT** på databasen (såsom databaseejeren) kan oprette en database på brugerniveau firewallregel.

1. Start et forespørgselsvindue via portalen klassisk eller SQL Server Management Studio, når du har oprettet en serverniveau firewall til din IP-adresse.
2. Oprette forbindelse til databasen, du vil oprette en database på brugerniveau firewallregel.

    Hvis du vil oprette en ny eller opdatere en eksisterende database niveau firewallregel ved at udføre de `sp_set_database_firewall_rule` lagret procedure. I følgende eksempel oprettes en ny firewallregel med navnet ContosoFirewallRule.
 
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
    Hvis du vil slette en eksisterende database niveau firewallregel, skal du udføre de `sp_delete_database_firewall_rule` lagret procedure. I følgende eksempel sletter den regel, der hedder ContosoFirewallRule.
`
   EKSEKVERBAR sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Se [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) og [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx)kan finde flere oplysninger om disse lagrede procedurer.

## <a name="next-steps"></a>Næste trin

For hvordan til artikler om oprettelse af serverniveau firewallregler ved hjælp af andre metoder, se: 

- [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af portalen Azure](sql-database-configure-firewall-settings.md)
- [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af REST-API](sql-database-configure-firewall-settings-rest.md)

Se [oprette en SQL-database i minutter ved hjælp af portalen Azure](sql-database-get-started.md)selvstudium til oprettelse af en database.
Du kan finde hjælp til at oprette forbindelse til en Azure SQL-database fra Åbn kilde eller tredjepartsprogrammer, [klienten Hurtig start kodeeksempler til SQL-Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
For at forstå, hvordan du navigerer til databaser, skal du se [administrere database access og logon-sikkerhed](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Yderligere ressourcer

- [Sikring af din database](sql-database-security.md)
- [Sikkerhedscenter til SQL Server-Database Engine og Azure SQL-Database](https://msdn.microsoft.com/library/bb510589)
