<properties
    pageTitle="SQL-Database Selvstudium: Introduktion til sikkerhed"
    description="Lær at oprette brugerkonti til at få adgang til og til at administrere en database."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/17/2016"
    ms.author="carlrab"/>

# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>SQL-Database Selvstudium: oprette SQL-database brugerkonti til at få adgang til og administrere en database


> [AZURE.SELECTOR]
- [Hent Introduktion selvstudium](sql-database-get-started-security.md)
- [Give adgang](sql-database-manage-logins.md)

I dette selvstudium lærer du at bruge SQL Server Management Studio (SSMS) til at:

- Log på SQL-Database ved hjælp af et serverniveau vigtigste logon.
- Oprette en brugerkonto i SQL-Database.
- Give [db_owner tilladelser](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0)for en SQL-Database.
- Oprette forbindelse til en SQL-database til en brugerkonto, der ikke er en serverniveau sikkerhedskonto.

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## <a name="next-steps"></a>Næste trin
Nu, hvor du har afsluttet selvstudiet SQL-Database og en brugerkonto har oprettet og tildelt brugeren tilladelser som databaseejer konto, er du klar til at lære mere om [SQL-Database sikkerhed](sql-database-manage-logins.md).


