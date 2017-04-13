<properties
    pageTitle="Oprette forbindelse til SQL-Database ved hjælp af PHP på Windows | Microsoft Azure"
    description="Viser et eksempel PHP program, der opretter forbindelse til Azure SQL-Database fra en Windows-klient og indeholder links til de nødvendige software-komponenter, der bruges af klienten."
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-php-on-windows"></a>Oprette forbindelse til SQL-Database ved hjælp af PHP i Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Dette emne viser, hvordan du kan oprette forbindelse til Azure SQL-Database fra en skrevet på PHP, der kører på Windows-klientprogrammet.

## <a name="step-1--configure-development-environment"></a>Trin 1: Konfigurere udviklingsmiljø

[Konfigurere udviklingsmiljø til PHP udvikling](https://msdn.microsoft.com/library/mt720663.aspx)

## <a name="step-2-create-a-sql-database"></a>Trin 2: Oprette en SQL-database

Se på [siden Introduktion](sql-database-get-started.md) til at se, hvordan du opretter en eksempeldatabasen.  Det er vigtigt du følge vejledningen for at oprette en **AdventureWorks databaseskabelon**. Eksemplerne nedenfor fungerer kun med **AdventureWorks skemaet**.


## <a name="step-3-get-connection-details"></a>Trin 3: Få oplysninger om forbindelse

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## <a name="step-4-run-sample-code"></a>Trin 4: Køre eksempelkode

* [Bevis for at oprette forbindelse til SQL ved hjælp af PHP konceptet](https://msdn.microsoft.com/library/mt720665.aspx)
* [Oprette forbindelse resiliently til SQL med PHP](https://msdn.microsoft.com/library/mt720667.aspx)


## <a name="next-steps"></a>Næste trin

* Gennemgå [SQL Database udvikling oversigt](sql-database-develop-overview.md)
* Flere oplysninger om [Microsoft PHP Driver til SQL Server](https://msdn.microsoft.com/library/dn865013.aspx)
* Du kan finde flere oplysninger om PHP installation og brug, [Få adgang til SQL Server-databaser med PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## <a name="additional-resources"></a>Yderligere ressourcer 

* [Designs til flere lejer SaaS programmer med Azure SQL-Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Udforske alle [funktionerne i SQL-Database](https://azure.microsoft.com/services/sql-database/)
