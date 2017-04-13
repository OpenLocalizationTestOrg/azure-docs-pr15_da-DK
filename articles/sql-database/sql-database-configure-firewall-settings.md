<properties
    pageTitle="Konfigurere en SQL-Database serverniveau firewallregel | Microsoft Azure"
    description="Lær, hvordan du konfigurerer firewallen for IP-adresser, få adgang til Azure SQL server."
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
    ms.author="rickbyh;carlrab"/>


# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>Konfigurere en regel for Azure SQL-Database serverniveau firewall ved hjælp af portalen Azure


> [AZURE.SELECTOR]
- [Oversigt](sql-database-firewall-configure.md)
- [Azure-portalen](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST-API](sql-database-configure-firewall-settings-rest.md)

Azure SQL server bruger firewallregler til at tillade forbindelser til servere og databaser. Du kan definere serverniveau og database niveau firewall-indstillinger for slidemaster eller en brugerdatabase i din Azure SQL server logiske server til at tillade adgang til databasen. Dette emne beskrives serverniveau firewallregler.

> [AZURE.IMPORTANT] Hvis du vil tillade programmer fra Azure til at oprette forbindelse til din Azure SQL-server, skal være aktiveret Azure forbindelser. For at forstå, hvordan firewallen regler arbejde, skal du se [hvordan du konfigurerer en Azure SQL server firewall \- oversigt](sql-database-firewall-configure.md). Hvis du laver forbindelser inden for Azure skyen kanten, skal du muligvis åbne nogle ekstra TCP-porte. Finde flere oplysninger i **version 12 af SQL-Database: uden for vs i** sektion af [porte ud over 1433 til ADO.NET 4.5 og version 12 SQL-Database](sql-database-develop-direct-route-ports-adonet-v12.md)

**Anbefaling:** Brug serverniveau firewallregler for administratorer og, når du har mange databaser, der har samme access krav, og du ikke vil bruge tid på at konfigurere hver database individuelt. Microsoft anbefaler at bruge databasen niveau firewallregler, når det er muligt, at forbedre sikkerheden og gøre databasen mere portable.

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Administrere eksisterende serverniveau firewallregler via Azure-portalen

Gentag trinnene for at administrere serverniveau firewallregler.

- Hvis du vil tilføje den aktuelle computer, skal du klikke på Tilføj klient IP-adresse.
- Hvis du vil tilføje yderligere IP-adresser, du skriver i regelnavn, starte IP-adresse og slutningen IP-adresse.
- Klik på et af felterne i reglen for at ændre en eksisterende regel, og Rediger.
- Slette en eksisterende regel, holde markøren over reglen, indtil der vises på X i slutningen af rækken. Klik på X for at fjerne reglen.

Klik på **Gem** for at gemme ændringerne.

## <a name="next-steps"></a>Næste trin

For en sådan artikel om, hvordan du bruger Transact-SQL til at oprette firewallregler serverniveau og database niveau, skal du se [konfigurere Azure SQL-Database serverniveau og database niveau firewallregler ved hjælp af T-SQL](sql-database-configure-firewall-settings-tsql.md). 

For hvordan til artikler om oprettelse af serverniveau firewallregler ved hjælp af andre metoder, se: 

- [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af PowerShell](sql-database-configure-firewall-settings-powershell.md)
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

 
