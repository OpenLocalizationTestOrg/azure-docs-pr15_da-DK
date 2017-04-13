<properties
    pageTitle="Oprette forbindelse til SQL-Database ved hjælp af .NET (C#) | Microsoft Azure"
    description="Brug eksempelkoden i denne hurtige starter på at oprette et moderne program med C# og sikkerhedskopien af en effektiv relationsdatabase i skyen med Azure SQL-Database."
    services="sql-database"
    documentationCenter=""
    authors="tobbox"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/16/2016"
    ms.author="tobiast"/>

# <a name="connect-to-sql-database-by-using-net-c"></a>Oprette forbindelse til SQL-Database ved hjælp af .NET (C#)

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 

## <a name="step-1--configure-development-environment"></a>Trin 1: Konfigurere udviklingsmiljø

[Konfigurere udviklingsmiljø til ADO.NET udvikling](https://msdn.microsoft.com/library/mt718321.aspx)

## <a name="step-2-create-a-sql-database"></a>Trin 2: Oprette en SQL-database

Se på [siden Introduktion](sql-database-get-started.md) til at se, hvordan du opretter en eksempeldatabasen.  Det er vigtigt du følge vejledningen for at oprette en **AdventureWorks databaseskabelon**. Eksemplerne nedenfor fungerer kun med **AdventureWorks skemaet**.  

## <a name="step-3--get-connection-string"></a>Trin 3: Få vist forbindelsesstreng

[AZURE.INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Trin 4: Køre eksempelkode

* [Bevis for at oprette forbindelse til SQL ved hjælp af ADO.NET konceptet](https://msdn.microsoft.com/library/mt718320.aspx)
* [Oprette forbindelse resiliently til SQL med ADO.NET.](https://msdn.microsoft.com/library/mt703195.aspx)

## <a name="next-steps"></a>Næste trin

* [Oprette en ASP.NET MVC app med auth og SQL DB og installere til Azure App-tjenesten]( ../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* Gennemgå [SQL Database udvikling oversigt](sql-database-develop-overview.md)
* Flere oplysninger om [Microsoft ADO.Net Driver til SQL Server](https://msdn.microsoft.com/library/mt657768.aspx)

## <a name="additional-resources"></a>Yderligere ressourcer 

* [Designs til flere lejer SaaS programmer med Azure SQL-Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Udforske alle [funktionerne i SQL-Database](https://azure.microsoft.com/services/sql-database/)





