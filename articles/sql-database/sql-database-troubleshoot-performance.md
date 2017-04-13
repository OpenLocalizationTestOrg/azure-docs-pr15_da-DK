<properties
    pageTitle="SQL-Database ydeevne justering tip | Microsoft Azure"
    description="Tip til justering i Azure SQL-Database via evaluering og forbedring af ydeevnen."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="SQL-ydeevne justering, databasens ydeevne justering, sql-ydeevne justering tip, justering af ydeevnen af sql-database"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-database-performance-tuning-tips"></a>SQL-Database ydeevne justering tip
Du kan ændre det [webtjenesten](sql-database-service-tiers.md) i en enkelt database eller øge eDTUs i en gruppe, elastiske database på en hvilken som helst tidspunkt for at forbedre ydeevnen, men du overveje at identificere muligheder for at forbedre og optimere forespørgselsydelse først. Manglende indeks og dårligt optimerede forespørgsler er almindelige årsager til dårlig databasens ydeevne. I denne artikel indeholder en vejledning til ydeevne justering i SQL-Database.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="steps-to-evaluate-and-tune-database-performance"></a>Trin til at evaluere og finjustere databasens ydelse
1.  I [Azure-portalen](https://portal.azure.com), skal du klikke på **SQL-databaser**, Vælg databasen, og derefter bruge overvågnings-diagram til at søge efter ressourcer nærmer deres maksimum. DTU forbrug vises som standard. Klik på **Rediger** for at ændre tidsinterval og værdier, der vises.
2.  Bruge [Forespørgsel ydeevne indsigt](sql-database-query-performance.md) til at evaluere forespørgsler ved hjælp af DTUs, og derefter bruge [SQL Database Advisor](sql-database-advisor.md) til at få vist anbefalinger til oprettelse og slippe indeks, anvende parametre i forespørgsler og løse problemer med skemaet.
3.  Du kan bruge dynamiske management visninger (DMVs), udvidet begivenheder (Xevents) og den forespørgsel, der er gemt i SSMS til at få ydeevneparametre i realtid. Se [ydeevne vejledning emne](sql-database-performance-guidance.md) for detaljeret overvågning og justering af tip.


    > [AZURE.IMPORTANT] Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="steps-to-improve-database-performance-with-more-resources"></a>Trin til at forbedre databasens ydeevne med flere ressourcer
1.  For enkelt databaser kan du [ændre tjenesten niveauer](sql-database-scale-up.md) efter behov til at forbedre databaseydeevnen i.
2.  Overvej at bruge [elastiske database grupper](sql-database-elastic-pool-guidance.md) skalere ressourcer automatisk til flere databaser.
