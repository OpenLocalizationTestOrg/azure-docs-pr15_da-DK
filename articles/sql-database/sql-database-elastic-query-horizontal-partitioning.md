<properties
    pageTitle="Rapportering af hele skaleret skyen databaser | Microsoft Azure"
    description="hvordan du konfigurerer elastiske forespørgsler over vandret partitioner"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/27/2016"
    ms.author="torsteng" />

# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Rapportering af hele skaleret skyen databaser (preview)

![Forespørgsel på tværs af shards][1]

Delt databaser fordel rækker på tværs af en skaleret ud data niveau. Skemaet er identiske på alle deltagende databaser, også kendt som vandret partitionering. Ved hjælp af en elastiske forespørgsel, kan du oprette rapporter, der strækker sig over alle databaser i en delt database.

Se [rapportering på tværs af skaleret skyen databaser](sql-database-elastic-query-getting-started.md)for en hurtig start.

Se [forespørgsel på tværs af skyen databaser med forskellige skemaer](sql-database-elastic-query-vertical-partitioning.md)til ikke delt databaser. 

 
## <a name="prerequisites"></a>Forudsætninger

* Oprette et shard kort ved hjælp af biblioteket elastiske database klient. Se [Shard tilknytte administration](sql-database-elastic-scale-shard-map-management.md). Eller brug appen eksempel i [komme i gang med elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md).
* Alternativt kan du se [overføre eksisterende databaser skaleret databaser](sql-database-elastic-convert-to-use-elastic-tools.md).
* Brugeren skal have tilladelse ændre alle eksterne DATAKILDE. Denne tilladelse er inkluderet i tilladelsen JUSTER DATABASE.
* ÆNDRE alle eksterne DATAKILDE tilladelser er nødvendige for at referere til den underliggende datakilde.

## <a name="overview"></a>Oversigt

Disse sætninger oprette metadata repræsentation af din delt data niveauet i databasen elastiske forespørgsel. 


1. [OPRETTE MASTER NØGLE](https://msdn.microsoft.com/library/ms174382.aspx)
2. [OPRETTE DATABASE, DER ER FASTSAT LEGITIMATIONSOPLYSNINGER](https://msdn.microsoft.com/library/mt270260.aspx)
3. [OPRETTE EKSTERNE DATAKILDE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [OPRETTE EKSTERNE TABEL](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 oprette databasen fastsat master nøgle og legitimationsoplysninger 

Legitimationsoplysninger, der bruges af elastiske forespørgslen til at oprette forbindelse til eksterne databaser.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
>[AZURE.NOTE] Sørg for, at den *"\<brugernavn\>"* ikke inkluderer en *"@servername"* suffiks. 

## <a name="12-create-external-data-sources"></a>1.2 oprette eksterne datakilder

Syntaks:

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                            
            (TYPE = SHARD_MAP_MANAGER,
                    LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>Eksempel 

    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );
 
Hente listen over aktuel eksterne datakilder: 

    select * from sys.external_data_sources; 

Den eksterne datakilde referencer shard kortet. En elastiske forespørgsel bruger derefter den eksterne datakilde og underliggende shard kortet optælles for databaser, der deltager i data lag. De samme legitimationsoplysninger bruges til at læse shard kortet og få adgang til dataene på shards under behandling af en elastiske forespørgsel. 

## <a name="13-create-external-tables"></a>1.3 oprette eksterne tabeller 
 
Syntaks:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  
    
    <sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Eksempel**

    CREATE EXTERNAL TABLE [dbo].[order_line]( 
         [ol_o_id] int NOT NULL, 
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL, 
         [ol_number] tinyint NOT NULL, 
         [ol_i_id] int NOT NULL, 
         [ol_delivery_d] datetime NOT NULL, 
         [ol_amount] smallmoney NOT NULL, 
         [ol_supply_w_id] int NOT NULL, 
         [ol_quantity] smallint NOT NULL, 
         [ol_dist_info] char(24) NOT NULL 
    ) 
    
    WITH 
    ( 
        DATA_SOURCE = MyExtSrc, 
        SCHEMA_NAME = 'orders', 
        OBJECT_NAME = 'order_details', 
        DISTRIBUTION=SHARDED(ol_w_id)
    ); 

Hente listen over eksterne tabeller fra den aktuelle database: 

    SELECT * from sys.external_tables; 

At slippe eksterne tabeller:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Bemærkninger

DATAENE\_KILDE-delsætning definerer den eksterne datakilde (et shard kort), der bruges til den eksterne tabel.  

SKEMAET\_navn og objekt\_navn delsætninger knytte eksterne tabeldefinition til en tabel i et andet skema. Hvis udelades, antages skemaet for objektet remote for at være "dbo", og navnet antages for at være identisk med navnet på den eksterne tabel, der defineres. Dette er nyttigt, hvis navnet på din remote tabel er allerede i brug i databasen hvor du vil oprette den eksterne tabel. For eksempel, du vil definere en ekstern tabel for at få en samlet oversigt over katalog visninger eller DMVs på dataene skalerede ud klasse. Da katalog visninger og DMVs allerede findes lokalt, kan du ikke bruge deres navne for de eksterne tabeldefinition. I stedet bruge et andet navn og bruge visningen katalog eller DMV navn i SKEMAET\_navn og/eller objekt\_navn delsætninger. (Se eksemplet nedenfor). 

Delsætningen fordeling angiver data fordelingen bruges til denne tabel. Forespørgsel processor anvender oplysningerne i delsætningen fordeling til at oprette de mest effektive forespørgselsplaner.  

1. **SHARDED** betyder, at dataene er vandret opdelt på tværs af databaserne. Tasten leverandør for datafordeling er parameteren **< sharding_column_name >** .
2. **REPLIKERET** betyder, at identiske kopier af tabellen findes på hver enkelt database. Det er din ansvaret for at sikre, at replikaerne er ens på tværs af databaserne.
3. **Afrund\_ROBIN** betyder, at tabellen vandret er opdelt ved hjælp af en program-afhængige fordelingsmetode. 

**Data klasse reference**: eksterne tabellen DDL refererer til en ekstern datakilde. Den eksterne datakilde angiver et shard kort, som indeholder den eksterne tabel med de oplysninger, der er nødvendige for at finde alle databaser i dine data lag. 


### <a name="security-considerations"></a>Overvejelser om sikkerheden 

Brugere med adgang til den eksterne tabel få automatisk adgang til de underliggende remote tabeller under de legitimationsoplysninger, der er angivet i den eksterne datakilde datadefinitionsforespørgsel. Undgå uønskede udvidelse af rettigheder via legitimationsoplysninger for den eksterne datakilde. Brug give eller TILBAGEKALDE til en ekstern tabel, som hvis det var en almindelig tabel.  

Når du har defineret den eksterne datakilde og din eksterne tabeller, kan du nu bruge fuld T-SQL over dine eksterne tabeller.

## <a name="example-querying-horizontal-partitioned-databases"></a>Eksempel: forespørgsler vandret partitioneret databaser 

Følgende forespørgsel udfører en tre-vejs joinforbindelse mellem lagre, ordrer og linjer og bruger flere samlinger og et avanceret filter. Det antages (1) vandret leverandør (sharding) og (2), lagre, ordrer og linjer er delt efter kolonnen warehouse-id, og, forespørgslen elastiske kan samtidig Find joinforbindelser på shards og behandle den dyr del af forespørgslen på shards parallelt. 

    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount, 
         min(ol_delivery_d) as min_deliv_date
    from warehouse 
    join orders 
    on w_id = o_w_id
    join order_line 
    on o_id = ol_o_id and o_w_id = ol_w_id 
    where w_id > 100 and w_id < 200 
    group by w_id, o_c_id 
 
## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Gemt procedure for udførelse af remote T-SQL: sp\_execute_remote

Elastiske forespørgsel introducerer også en lagret procedure, som indeholder direkte adgang til shards. Den lagrede procedure hedder [sp\_udføre \_remote](https://msdn.microsoft.com/library/mt703714) og kan bruges til at udføre remote lagrede procedurer eller T-SQL-koden for de eksterne databaser. Det kræver følgende parametre: 

* Datakildenavnet (nvarchar): navnet på den eksterne datakilde af typen RDBMS. 
* Forespørgsel (nvarchar): feltet T-SQL-forespørgsel, der skal udføres på hver shard. 
* Parameter definition (nvarchar) - Valgfrit: strengen med definitioner af datatyper for de parametre, bruges i forespørgselsparameter (som sp_executesql). 
* Parameter værdiliste - Valgfrit: kommasepareret liste over parameterværdier (som sp_executesql).

Sp\_udføre\_remote bruger den eksterne datakilde, der er angivet i aktiveringsparametrene til at udføre angivne T-SQL-sætningen for de eksterne databaser. Legitimationsoplysninger for den eksterne datakilde bruges til at oprette forbindelse til shardmap manager-databasen og de eksterne databaser.  

Eksempel: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Forbindelse til værktøjer  

Bruge almindelige SQL Server-forbindelsesstrenge til at forbinde dit program dine BI og data integration værktøjer til databasen med dine ekstern tabeldefinitioner. Sørg for, at SQL Server understøttes som en datakilde til din værktøj. Derefter reference elastiske forespørgsel databasen som andre SQL Server-database tilsluttet værktøjet og bruge eksterne tabeller fra din værktøj eller et program som var de lokale tabeller. 

## <a name="best-practices"></a>Bedste fremgangsmåder 

* Sørg for, at elastiske forespørgsel slutpunkt databasen har er tildelt adgang til databasen shardmap og alle shards via SQL DB firewalls.  

* Validere eller gennemtvinge data fordelingen defineret af den eksterne tabel. Hvis din faktiske datafordeling er forskellig fra en fordeling, der er angivet i tabeldefinitionen for din, kan dine forespørgsler afkast uventede resultater. 

* Elastiske forespørgsel udfører i øjeblikket ikke shard fjernelse når prædikaterne over tasten sharding vil gøre det muligt at sikkert udelade bestemte shards fra behandling.

* Elastiske forespørgsel fungerer bedst for forespørgsler, hvor de fleste af beregningen kan udføres af shards. Du typisk får den bedste ydeevne forespørgsel med selektivt filter prædikaterne, der kan evalueres på shards eller joinforbindelser via tasterne leverandør, der kan udføres på en partition venstrejusteret måde på alle shards. Andre forespørgsel mønstre kan være nødvendigt at indlæse store mængder data fra shards til noden hoved og kan udføre dårligt

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
