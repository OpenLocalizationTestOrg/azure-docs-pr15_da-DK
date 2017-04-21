Database transaktion enhed (DTU) er måleenhed i SQL-Database, der repræsenterer den relative styrke i databaser, der er baseret på en reale måling: databasetransaktionen. Vi fandt et sæt handlinger, der er typisk til en online transaktion behandling af anmodning om (OLTP), og derefter målt hvor mange transaktioner blev fuldført sekundet under fuldt indlæst betingelser (det er den korte version, kan du læse gory oplysningerne i [Oversigt over Benchmark](../articles/sql-database/sql-database-benchmark-overview.md)). 

For eksempel indeholder en Premium P11 database med 1750 DTUs 350 x flere DTU beregne power end en grundlæggende database med 5 DTUs. 

![Introduktion til SQL-Database: enkelt database DTUs ved niveau og niveau.](./media/sql-database-understanding-dtus/single_db_dtus.png)

>[AZURE.NOTE] Hvis du overfører en eksisterende SQL Server-database, kan du bruge et tredjeparts-værktøj, [Azure SQL Database DTU Lommeregner](http://dtucalculator.azurewebsites.net/), til at få en estimering af niveauet for ydeevne og servicere niveau kan kræve, at din database i Azure SQL-Database.

### <a name="dtu-vs-edtu"></a>DTU kontra eDTU

DTU for enkelt databaser oversætter direkte til eDTU for elastiske databaser. For eksempel indeholder en database i en grundlæggende elastiske database pulje op til 5 eDTUs. Det er den samme ydeevne som en enkelt grundlæggende database. Forskellen er, elastiske databasen ikke bruge en hvilken som helst eDTUs fra puljen, indtil den har til. 

![Introduktion til SQL-Database: elastiske grupper efter niveau.](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Et simpelt eksempel hjælper med at. Få en grundlæggende elastiske database pulje med 1000 DTUs, og slip 800 databaser i den. Så længe kun 200 af 800 databaserne bliver brugt når som helst tidspunkt (5 DTU X 200 = 1000), du klikker ikke på kapaciteten i puljen, og databasen ydeevnen ikke. I dette eksempel er forenklet til klarhed. Den reelle matematik er lidt mere avanceret. Portalen gør beregningerne for dig, og gør et anbefaling, der er baseret på historiske database brugen. Se [pris og ydeevne overvejelser i forbindelse med en elastiske database puljen](../articles/sql-database/sql-database-elastic-pool-guidance.md) at lære, hvordan anbefalingerne fungerer eller for at foretage beregningerne dig selv. 
