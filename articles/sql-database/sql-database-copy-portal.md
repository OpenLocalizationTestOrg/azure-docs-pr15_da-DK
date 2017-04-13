<properties
    pageTitle="Kopiere en Azure SQL-database ved hjælp af portalen Azure | Microsoft Azure"
    description="Oprette en kopi af en Azure SQL-database"
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



# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Kopiere en Azure SQL-Database ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Oversigt](sql-database-copy.md)
- [Azure-portalen](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Følgende trin viser, hvordan du kopierer en SQL-database ved hjælp af [Azure-portalen](https://portal.azure.com) til den samme server eller en anden server.

Hvis du vil kopiere en SQL-database, du skal bruge følgende elementer:

- Et Azure-abonnement. Hvis du skal bruge et Azure abonnement bare Klik på **Gratis prøveversion** øverst på denne side, og derefter vende tilbage til denne artikel er færdig.
- En SQL-database til at kopiere. Hvis du ikke har en SQL-database, kan du oprette én følge trinnene i denne artikel: [oprette din første Azure SQL-Database](sql-database-get-started.md).


## <a name="copy-your-sql-database"></a>Kopiere SQL-database

Åbn siden SQL-database til den database, du vil kopiere:

1.  Gå til [Azure-portalen](https://portal.azure.com).
2.  Klik på **flere tjenester** > **SQL-databaser**, og klik derefter på den ønskede database.
3.  Klik på **Kopiér**på siden SQL-database:

    ![SQL-Database](./media/sql-database-copy-portal/sql-database-copy.png)

1.  På siden **Kopiér** standardnavn en database. Skriv et andet navn, hvis du vil (alle databaser på en server skal have entydige navne).
2.  Vælg en **destinationsserver**. Target serveren er, hvor kopien af databasen er oprettet. Du kan kopiere databasen til den samme server eller en anden server. Du kan oprette en server eller vælge en eksisterende server på listen. 
3.  Når du har valgt **Target server**, **elastiske database puljen**og **priser niveau** aktiveres indstillinger. Hvis serveren er en samling, kan du kopiere databasen i den.
3.  Klik på **OK** for at starte kopieringsprocessen.

    ![SQL-Database](./media/sql-database-copy-portal/copy-page.png)


## <a name="monitor-the-progress-of-the-copy-operation"></a>Overvåge forløbet af kopieringen

- Når du starter kopien, skal du klikke på portalen meddelelsen få mere at vide.

    ![meddelelse om][3]
 
    ![skærm][4]


## <a name="verify-the-database-is-live-on-the-server"></a>Kontrollere databasen er direkte på serveren

- Klik på **flere tjenester** > **SQL-databaser** , og bekræft den nye database er **Online**.


## <a name="resolve-logins"></a>Løse logon

For at løse logon, når kopieringen er fuldført, skal du se [løse logon](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="next-steps"></a>Næste trin

- Se [kopiere en Azure SQL-database](sql-database-copy.md) til en oversigt over kopierer en Azure SQL-Database.
- Se [kopi Azure SQL-database ved hjælp af PowerShell](sql-database-copy-powershell.md) til at kopiere en database ved hjælp af PowerShell.
- Se [kopi Azure SQL-database ved hjælp af T-SQL](sql-database-copy-transact-sql.md) til at kopiere en database ved hjælp af Transact-SQL.
- Se, [hvordan du administrerer Azure SQL Databasesikkerhed efter nedbrud](sql-database-geo-replication-security-config.md) til at få mere at vide om administration af brugere og -logon, når du kopierer en database til en anden logiske server.



## <a name="additional-resources"></a>Yderligere ressourcer

- [Administrere logon](sql-database-manage-logins.md)
- [Oprette forbindelse til SQL-Database med SQL Server Management Studio og udføre en stikprøve T-SQL-forespørgsel](sql-database-connect-query-ssms.md)
- [Eksportere databasen til en BACPAC](sql-database-export.md)
- [Oversigt over Business løbende](sql-database-business-continuity.md)
- [SQL-Database dokumentation](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

