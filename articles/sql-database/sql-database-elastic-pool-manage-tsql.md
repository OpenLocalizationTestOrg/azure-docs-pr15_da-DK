<properties 
    pageTitle="Oprette eller flytte en Azure SQL-database til en elastiske puljen ved hjælp af T-SQL | Microsoft Azure" 
    description="Brug T-SQL til at oprette en Azure SQL-database i en elastiske puljen. Eller brug T-SQL til at flytte datbase og grupper." 
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-transact-sql"></a>Overvåge og administrere en elastiske database puljen med Transact-SQL  

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Brug kommandoerne [Opret Database (Azure SQL-Database)](https://msdn.microsoft.com/library/dn268335.aspx) og [Ændre Database(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) til at oprette og flytte databaser til og fra elastiske grupper. Elastiske puljen skal findes, før du kan bruge disse kommandoer. Disse kommandoer påvirker kun databaser. Oprettelse af nye grupper og indstillingen af puljen egenskaber (såsom min og max eDTUs) kan ikke ændres med T-SQL-kommandoer.

## <a name="create-a-new-database-in-an-elastic-pool"></a>Oprette en ny database i en elastiske puljen
Bruge kommandoen Opret DATABASE med indstillingen SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in a pool named S3M100.

Alle databaser i en elastiske puljen nedarver webtjenesten af elastiske puljen (Basic, Standard, Premium). 


## <a name="move-a-database-between-elastic-pools"></a>Flytte en database mellem elastiske grupper
Bruge kommandoen JUSTER DATABASE med Rediger og angive SERVICE\_målsætning mulighed efter ELASTISKE\_RESSOURCEPULJEN; Angiv navnet til navnet på destinationen puljen.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to a pool named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Flytte en database til en elastiske puljen 
Bruge kommandoen JUSTER DATABASE med Rediger og angive SERVICE\_målsætning mulighed efter ELASTIC_POOL; Angiv navnet til navnet på destinationen puljen.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to a pool named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Flytte en database fra en elastiske puljen
Bruge kommandoen JUSTER DATABASE og angive SERVICE_OBJECTIVE til en af de ydeevneniveauer (S0, S1 osv.).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Liste over databaser i en elastiske puljen
Brug den [sys.database\_service \_målsætninger visning](https://msdn.microsoft.com/library/mt712619) til at få vist alle databaser i en elastiske puljen. Log på masteren database at forespørge på visningen.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-a-pool"></a>Hente data om brug af ressource til en gruppe

Brug den [sys.elastic\_puljen \_ressource \_statistik visning](https://msdn.microsoft.com/library/mt280062.aspx) at undersøge brugsstatistik ressource af en elastiske samling på en logisk server. Log på masteren database at forespørge på visningen.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-an-elastic-database"></a>Få ressourceforbrug til en elastiske database

Brug den [sys.dm\_ db\_ ressource\_statistik visning](https://msdn.microsoft.com/library/dn800981.aspx) eller [sys.resource \_statistik visning](https://msdn.microsoft.com/library/dn269979.aspx) at undersøge ressource brugsstatistik for en database i en elastiske puljen. Denne proces, der ligner forespørgsler Ressourceforbrug for en enkelt database.

## <a name="next-steps"></a>Næste trin

Når du har oprettet en elastiske database puljen, kan du administrere elastiske databaser i puljen ved at oprette elastiske job. Elastiske job lette kørende T-SQL-scripts mod en hvilken som helst antal databaser i puljen. Se [Oversigt over elastiske database](sql-database-elastic-jobs-overview.md)kan finde flere oplysninger. 

Se [Skalering ud med Azure SQL-Database](sql-database-elastic-scale-introduction.md): bruge elastiske Databaseværktøjer skala ud, skal du flytte data, forespørgsel, eller Opret transaktioner.
