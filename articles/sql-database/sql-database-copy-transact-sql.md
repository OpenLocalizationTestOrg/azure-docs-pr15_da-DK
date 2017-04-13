<properties 
    pageTitle="Kopiere en Azure SQL-database ved hjælp af Transact-SQL | Microsoft Azure" 
    description="Oprette kopi af en ved hjælp af Transact-SQL Azure SQL-database" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/19/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Kopiere en ved hjælp af Transact-SQL Azure SQL-database


> [AZURE.SELECTOR]
- [Oversigt](sql-database-copy.md)
- [Azure-portalen](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)


Denne følgende trin viser, hvordan du kopierer en SQL-database med Transact-SQL til den samme server eller en anden server. Kopieringen af databasen bruger sætningen [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) .

Du skal bruge følgende for at fuldføre trinnene i denne artikel:

- Et Azure-abonnement. Hvis du skal bruge et Azure abonnement bare Klik på **Gratis prøveversion** øverst på denne side, og derefter vende tilbage til denne artikel er færdig.
- Azure SQL-Database. Hvis du ikke har en SQL-database, kan du oprette én følge trinnene i denne artikel: [oprette din første Azure SQL-Database](sql-database-get-started.md).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Hvis du ikke har SSMS, eller hvis funktioner, der er beskrevet i denne artikel ikke er tilgængelige, [hente den nyeste version](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="copy-your-sql-database"></a>Kopiere SQL-database

Log på master databasen ved hjælp af de vigtigste login serverniveau eller logon, der har oprettet den database, du vil kopiere. Logon, der ikke er serverniveau hovedstolen skal være medlem af rollen dbmanager for at kopiere databaser. Se [administrere logon](sql-database-manage-logins.md)kan finde flere oplysninger om logon og oprette forbindelse til serveren.

Begynd at kopiere kildedatabasen med sætningen [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . Udføre denne sætning starter databasen kopiere proces. Da kopiering af en database er en asynkron proces, returnerer sætningen CREATE DATABASE, før databasen er færdig med at kopiere.


### <a name="copy-a-sql-database-to-the-same-server"></a>Kopiere en SQL-database til den samme server

Log på master databasen ved hjælp af de vigtigste login serverniveau eller logon, der har oprettet den database, du vil kopiere. Logon, der ikke er serverniveau hovedstolen skal være medlem af rollen dbmanager for at kopiere databaser.

Database2 denne kommandoen Kopier Database1 til en ny database med navnet på den samme server. Afhængigt af størrelsen på databasen tage kopieringen lidt tid at fuldføre.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopiere en SQL-database til en anden server

Log på master databasen på destinationsserveren,, den Azure SQL-databaseserver, hvor den nye database der skal oprettes. Brug et logon, der har samme navn og din adgangskode som databaseejer (DBO) i kildedatabasen på kildeserveren Azure SQL-Database. Logge på destinationsserveren skal også være medlem af rollen dbmanager eller være serverniveau vigtigste logon.

Denne kommando Kopier Database1 på server1 - til en ny database, der hedder Database2 på server2. Afhængigt af størrelsen på databasen tage kopieringen lidt tid at fuldføre.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## <a name="monitor-the-progress-of-the-copy-operation"></a>Overvåge forløbet af kopieringen

Overvåge kopieringen ved at forespørge visningerne sys.databases og sys.dm_database_copies. Mens kopiering er i gang, er kolonnen state_desc i visningen sys.databases til den nye database indstillet til KOPIERING.


- Hvis kopiering mislykkes, skal er kolonnen state_desc i visningen sys.databases til den nye database indstillet til MISTÆNKELIGE. I dette tilfælde skal udføre sætningen DROP for den nye database, og prøv igen senere.
- Hvis kopiering lykkes, er kolonnen state_desc i visningen sys.databases til den nye database angivet til ONLINE. I dette tilfælde kopiering er fuldført, og den nye database er en almindelig database, som kan ændres uafhængigt af kildedatabasen.

> [AZURE.NOTE] – Hvis du vil annullere, kopiere, mens det er i gang, skal du udføre sætningen [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) på den nye database. Du kan også annullerer udføre sætningen DROP DATABASE på kildedatabasen også kopieringen.


## <a name="resolve-logins-after-the-copy-operation-completes"></a>Løse logon, når kopieringen er fuldført

Når den nye database er online på destinationsserveren, kan du bruge sætningen [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) til at tilknytte brugere fra den nye database til logon på destinationsserveren igen. Du kan løse tabte brugere ved at se [Fejlfinding i forbindelse med tabte brugere](https://msdn.microsoft.com/library/ms175475.aspx). Se også [Sådan administreres Azure SQL Databasesikkerhed efter nedbrud](sql-database-geo-replication-security-config.md).

Alle brugere i den nye database Vedligehold de tilladelser, som de havde i kildedatabasen. Den bruger, der startede kopien af databasen bliver databaseejeren af den nye database og er tildelt et nyt sikkerheds-id (SID). Efter kopiering lykkes og før andre brugere er tilknyttet, kan kun logon, der startede den kopierer databaseejeren (DBO), logge på den nye database.


## <a name="next-steps"></a>Næste trin

- Se [kopiere en Azure SQL-database](sql-database-copy.md) til en oversigt over kopierer en Azure SQL-Database.
- Se [kopiere Azure SQL-database ved hjælp af portalen Azure](sql-database-copy-portal.md) til at kopiere en database ved hjælp af portalen Azure.
- Se [kopi Azure SQL-database ved hjælp af PowerShell](sql-database-copy-powershell.md) til at kopiere en database ved hjælp af PowerShell.
- Se, [hvordan du administrerer Azure SQL Databasesikkerhed efter nedbrud](sql-database-geo-replication-security-config.md) til at få mere at vide om administration af brugere og -logon, når du kopierer en database til en anden logiske server.



## <a name="additional-resources"></a>Yderligere ressourcer

- [Administrere logon](sql-database-manage-logins.md)
- [Oprette forbindelse til SQL-Database med SQL Server Management Studio og udføre en stikprøve T-SQL-forespørgsel](sql-database-connect-query-ssms.md)
- [Eksportere databasen til en BACPAC](sql-database-export.md)
- [Oversigt over Business løbende](sql-database-business-continuity.md)
- [SQL-Database dokumentation](https://azure.microsoft.com/documentation/services/sql-database/)


