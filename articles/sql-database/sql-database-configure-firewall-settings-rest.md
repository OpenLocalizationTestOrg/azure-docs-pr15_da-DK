<properties
    pageTitle="Azure SQL-Database serverniveau firewallregler ved hjælp af REST-API | Microsoft Azure"
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


#  <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af REST-API


> [AZURE.SELECTOR]
- [Oversigt](sql-database-firewall-configure.md)
- [Azure-portalen](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST-API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database bruger firewallregler til at tillade forbindelser til servere og databaser. Du kan definere serverniveau og database niveau firewall-indstillinger for slidemaster eller en brugerdatabase i serverens Azure SQL-Database til at tillade adgang til databasen.

> [AZURE.IMPORTANT] Hvis du vil tillade programmer fra Azure for at oprette forbindelse til din-databaseserver, skal være aktiveret Azure forbindelser. Du kan finde flere oplysninger om firewallregler og aktivering forbindelser fra Azure [Azure SQL Database-firewallen](sql-database-firewall-configure.md). Hvis du laver forbindelser inden for Azure skyen kanten, skal du muligvis åbne nogle ekstra TCP-porte. Finde flere oplysninger i **version 12 af SQL-Database: uden for vs i** sektion af [porte ud over 1433 til ADO.NET 4.5 og version 12 SQL-Database](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="manage-server-level-firewall-rules-through-rest-api"></a>Administrere serverniveau firewallregler gennem REST-API
1. Administrere firewallregler gennem REST-API skal godkendes. Du kan finde oplysninger, [Developer's guide til godkendelse med Azure ressourcestyring API](../resource-manager-api-authentication.md).
2. Serverniveau regler kan oprettes, opdaterede eller slettes ved hjælp af REST-API

    Hvis du vil oprette eller opdatere en serverniveau firewallregel, du Udfør metoden læg ved hjælp af følgende:
 
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
    
    Anmodningsteksten

        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 
 

    Hvis du vil fjerne en eksisterende serverniveau firewallregel, skal du udføre metoden DELETE ved hjælp af følgende:
     
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>Administrere firewallregler ved hjælp af REST-API

* [Oprette eller opdatere firewallregel](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Slette firewallregel](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Få firewallregel](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Liste over alle firewallregler](https://msdn.microsoft.com/library/azure/mt604478.aspx)
 
## <a name="next-steps"></a>Næste trin

For en sådan artikel om, hvordan du bruger Transact-SQL til at oprette firewallregler serverniveau og database niveau, skal du se [konfigurere Azure SQL-Database serverniveau og database niveau firewallregler ved hjælp af T-SQL](sql-database-configure-firewall-settings-tsql.md). 

For hvordan til artikler om oprettelse af serverniveau firewallregler ved hjælp af andre metoder, se: 

- [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af portalen Azure](sql-database-configure-firewall-settings.md)
- [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af PowerShell](sql-database-configure-firewall-settings-powershell.md)

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

 
