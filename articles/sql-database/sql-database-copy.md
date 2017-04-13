<properties
    pageTitle="Kopiere en Azure SQL-database | Microsoft Azure"
    description="Oprette en kopi af en Azure SQL-database"
    services="sql-database"
    documentationCenter=""
    authors="anosov1960"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/24/2016"
    ms.author="sstein; sashan"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="copy-an-azure-sql-database"></a>Kopiere en Azure SQL-Database

> [AZURE.SELECTOR]
- [Oversigt](sql-database-copy.md)
- [Azure-portalen](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Du kan bruge den Azure [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md) til at oprette en kopi af din SQL-database. Kopien af databasen bruger den samme teknologi som funktionen geografisk gentagelse. Men i modsætning til geografisk gentagelse den ophører gentagelse kæden som når forhåndsudfyldningen fasen er fuldført. Kopiér databasen er derfor et øjebliksbillede af kildedatabasen på tidspunktet for din anmodning kopi.  
Du kan oprette databasekopi på den samme server eller en anden server. Service niveau og ydeevne niveauet (priser lag) for kopien af databasen er den samme som kildedatabasen som standard. Når du bruger API, kan du vælge et andet ydeevneniveau i den samme webtjenesten (edition). Når kopien er fuldført, bliver kopien et fuldt funktionelt uafhængige-database. På dette tidspunkt, kan du opgradere eller nedgradere til en hvilken som helst edition. Logon, brugere og tilladelser kan administreres uafhængigt af hinanden.  

Når du kopierer en database til den samme logiske server, kan de samme logon bruges i begge databaser. Den sikkerhedskonto du kan bruge til at kopiere databasen bliver databaseejeren (DBO) på den nye database. Alle brugere, tilladelserne og deres sikkerheds-id'er (sikkerheds-id'er) kopieres til kopien af databasen.  

Når du kopierer en database til en anden logiske server, bliver den sikkerhedskonto på den nye server ejeren af databasen på den nye database. Hvis du bruger [indeholdt databasebrugere](sql-database-manage-logins.md) til sikrer dataadgang både primære og sekundære databaser altid har de samme legitimationsoplysninger som bruger, så når kopi er fuldført du straks kan åbne den med de samme legitimationsoplysninger. Hvis du bruger [Azure Active Directory](../active-directory/active-directory-whatis.md), kan du helt eliminere behovet for administration af legitimationsoplysninger i feltet Kopier. Men når du kopierer databasen til en ny server, adgang til det login baseret Generelt fungerer ikke fordi logon, der ikke findes på den nye server. Se, [hvordan du administrerer Azure SQL Databasesikkerhed efter nedbrud](sql-database-geo-replication-security-config.md) til at få mere at vide om administration af logon, når du kopierer en database til en anden logiske server. 

Hvis du vil kopiere en SQL-database, skal bruge du følgende:

- Et Azure-abonnement. Hvis du skal bruge et Azure abonnement bare Klik på **Gratis prøveversion** øverst på denne side, og derefter vende tilbage til denne artikel er færdig.
- En SQL-database til at kopiere. Hvis du ikke har en SQL-database, kan du oprette én følge trinnene i denne artikel: [oprette din første Azure SQL-Database](sql-database-get-started.md).

## <a name="next-steps"></a>Næste trin

- Se [kopiere Azure SQL-database ved hjælp af portalen Azure](sql-database-copy-portal.md) til at kopiere en database ved hjælp af portalen Azure.
- Se [kopi Azure SQL-database ved hjælp af PowerShell](sql-database-copy-powershell.md) til at kopiere en database ved hjælp af PowerShell.
- Se [kopi Azure SQL-database ved hjælp af T-SQL](sql-database-copy-transact-sql.md) til at kopiere en database ved hjælp af Transact-SQL.
- Se, [hvordan du administrerer Azure SQL Databasesikkerhed efter nedbrud](sql-database-geo-replication-security-config.md) til at få mere at vide om administration af brugere og -logon, når du kopierer en database til en anden logiske server.



## <a name="additional-resources"></a>Yderligere ressourcer

- [Administrere logon](sql-database-manage-logins.md)
- [Oprette forbindelse til SQL-Database med SQL Server Management Studio og udføre en stikprøve T-SQL-forespørgsel](sql-database-connect-query-ssms.md)
- [Eksportere databasen til en BACPAC](sql-database-export.md)
- [Oversigt over Business løbende](sql-database-business-continuity.md)
- [SQL-Database dokumentation](https://azure.microsoft.com/documentation/services/sql-database/)
