<properties
    pageTitle="Overvåge og administrere en elastiske database puljen med C# | Microsoft Azure"
    description="Bruge C# database udviklingsteknikker til at administrere en Azure SQL-Database elastiske database puljen."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-cx23"></a>Overvåge og administrere en elastiske database puljen med C & #x 23; 

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Få mere at vide, hvordan du administrerer en [elastiske database puljen](sql-database-elastic-pool.md) , hvis du bruger C & #x 23;. 

>[AZURE.NOTE] Mange nye funktioner i SQL-Database understøttes kun, når du bruger [Azure ressourcestyring implementeringsmodel](../azure-resource-manager/resource-group-overview.md), så du altid skal bruge nyeste **Azure SQL Database Management bibliotek til .NET ([dokumenter](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet pakke](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. De ældre [Klassisk installation model-baserede biblioteker](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) understøttes for at opnå bagudkompatibilitet kun, så vi anbefaler, at du bruger de nyere ressourcestyring baseret biblioteker.

For at fuldføre trinnene i denne artikel skal bruge du følgende elementer:

- En elastiske puljen (den gruppe, du vil administrere). Hvis du vil oprette en gruppe, skal du se [oprette en elastiske database puljen med C#](sql-database-elastic-pool-create-csharp.md).
- Visual Studio. Finde en gratis kopi af Visual Studio, på siden [Visual Studio-overførsler](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="move-a-database-into-an-elastic-pool"></a>Flytte en database til en elastiske puljen

Du kan flytte en separat database ind eller ud på en gruppe.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Liste over databaser i en elastiske puljen

For at hente alle databaser i en gruppe skal du ringe til metoden [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>Ændre indstillinger for ydelse af en samling

Hent eksisterende egenskaberne puljen. Rediger værdierne og udfører metoden CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>Ventetid på elastiske puljen handlinger

- Ændre min eDTUs per database eller Maks eDTUs per database typisk fuldfører på fem minutter eller mindre.
- Tid til at ændre puljestørrelse (eDTUs), afhænger af den kombinerede størrelse for alle databaser i puljen. Ændringer i gennemsnit 90 minutter eller mindre per 100 GB. For eksempel, hvis den samlede mængde plads af alle databaser i puljen er 200 GB, derefter den forventede ventetid til ændring af puljen eDTU per puljen er 3 timer eller mindre.




## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL fejlkoder for SQL-Database-klientprogrammer: Webdatabase forbindelsesfejl og andre problemer](sql-database-develop-error-messages.md).
- [SQL-Database](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure ressource Management API'er](https://msdn.microsoft.com/library/azure/dn948464.aspx)
- [Oprette en ny gruppe elastiske database med C#](sql-database-elastic-pool-create-csharp.md)
- [Hvornår skal en elastiske database puljen bruges?](sql-database-elastic-pool-guidance.md)
- Se [Skalering ud med Azure SQL-Database](sql-database-elastic-scale-introduction.md): bruge elastiske Databaseværktøjer skala ud, skal du flytte data, forespørgsel, eller Opret transaktioner.

