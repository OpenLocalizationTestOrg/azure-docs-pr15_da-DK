<properties
    pageTitle="Forespørgsel på tværs af skyen databaser med forskellige skema | Microsoft Azure"
    description="hvordan du konfigurerer tværs databaseforespørgsler over lodret partitioner"    
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

# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Forespørgsel på tværs af skyen databaser med forskellige skemaer (preview)

![Forespørgsel på tværs af tabeller i forskellige databaser][1]

Lodret opdelt databaser bruge forskellige typer tabeller på forskellige databaser. Det betyder, at skemaet er anderledes ud på forskellige databaser. For eksempel er alle tabeller for lager på én database, mens alle accounting-relaterede tabeller er i en anden database. 

## <a name="prerequisites"></a>Forudsætninger

* Brugeren skal have tilladelse ændre alle eksterne DATAKILDE. Denne tilladelse er inkluderet i tilladelsen JUSTER DATABASE.
* ÆNDRE alle eksterne DATAKILDE tilladelser er nødvendige for at referere til den underliggende datakilde.

## <a name="overview"></a>Oversigt

**Bemærk**: i modsætning til med vandret partitionering disse DDL-sætninger ikke afhænger af definerer et data trin med et shard kort gennem biblioteket elastiske database klient.

1. [OPRETTE MASTER NØGLE](https://msdn.microsoft.com/library/ms174382.aspx)
2. [OPRETTE DATABASE, DER ER FASTSAT LEGITIMATIONSOPLYSNINGER](https://msdn.microsoft.com/library/mt270260.aspx)
3. [OPRETTE EKSTERNE DATAKILDE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [OPRETTE EKSTERNE TABEL](https://msdn.microsoft.com/library/dn935021.aspx) 


## <a name="create-database-scoped-master-key-and-credentials"></a>Oprette database, der er fastsat master nøgle og legitimationsoplysninger 

Legitimationsoplysninger, der bruges af elastiske forespørgslen til at oprette forbindelse til eksterne databaser.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
**Bemærk!**    Sørg for, at den *<username>* ikke inkluderer en *"@servername"* suffiks. 

## <a name="create-external-data-sources"></a>Oprette eksterne datakilder

Syntaks:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

**Vigtige**   Parameteren TYPE skal angives til **RDBMS**. 

### <a name="example"></a>Eksempel 

I følgende eksempel vises brugen af sætningen CREATE for eksterne datakilder. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 
 
Til at hente listen over aktuel eksterne datakilder: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Eksterne tabeller 

Syntaks:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 
    
    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Eksempel  

    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

I følgende eksempel viser, hvordan du kan hente listen over eksterne tabeller fra den aktuelle database: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Bemærkninger

Elastiske forespørgsel udvider syntaksen eksisterende ekstern tabel for at definere eksterne tabeller, der bruger eksterne datakilder af typen RDBMS. En ekstern tabeldefinition for lodret partitionering omfatter følgende forhold: 

* **Skema**: den eksterne tabel DDL definerer et skema, der kan bruge dine forespørgsler. Skemaet findes i din eksterne tabeldefinition skal svare til skemaet tabeller i fjerndatabasen, hvor de faktiske data er gemt. 

* **Fjerndatabasen reference**: eksterne tabellen DDL refererer til en ekstern datakilde. Den eksterne datakilde angiver det logiske servernavn og databasenavn af fjerndatabasen hvor faktiske tabeldataene er gemt. 

Ved hjælp af en ekstern datakilde, som beskrevet i forrige afsnit, er syntaks til at oprette eksterne tabeller som følger: 

Delsætningen DATA_SOURCE definerer den eksterne datakilde (det vil sige fjerndatabasen i tilfælde af lodret partitionering), der bruges til den eksterne tabel.  

SCHEMA_NAME og objektnavn delsætninger gør muligheden at tilknytte den eksterne tabeldefinition til en tabel i et andet skema på fjerndatabasen eller til en tabel med et andet navn, henholdsvis. Dette er nyttigt, hvis du vil oprette en ekstern tabel til et katalog eller DMV i din fjerndatabasen – eller alle andre situationer, hvor remote tabelnavnet allerede taget lokalt.  

Følgende DDL-sætningen udelader en eksisterende eksterne tabeldefinition fra det lokale katalog. Den påvirker ikke fjerndatabasen. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Tilladelser for Opret/slip ekstern tabel**: ændre alle eksterne DATAKILDE tilladelser, der skal bruges til ekstern tabel DDL som skal bruges også til at referere til den underliggende datakilde.  

## <a name="security-considerations"></a>Overvejelser om sikkerheden
Brugere med adgang til den eksterne tabel få automatisk adgang til de underliggende remote tabeller under de legitimationsoplysninger, der er angivet i den eksterne datakilde datadefinitionsforespørgsel. Du bør omhyggeligt administrere adgang til den eksterne tabel for at undgå uønskede udvidelse af rettigheder via legitimationsoplysninger for den eksterne datakilde. Almindelige SQL-tilladelser kan bruges til at TILDELE eller TILBAGEKALDE adgang til en ekstern tabel, som hvis det var en almindelig tabel.  


## <a name="example-querying-vertically-partitioned-databases"></a>Eksempel: forespørgsler lodret opdelt databaser 

Følgende forespørgsel udfører en tre-vejs joinforbindelse mellem de to lokale tabeller for ordrer og linjer og tabellen remote for kunder. Dette er et eksempel af en reference use case for elastiske forespørgsel: 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


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

Du kan bruge almindelige SQL Server-forbindelsesstrenge til at oprette forbindelse dine BI og data integration værktøjer til databaser på serveren SQL DB, der indeholder elastiske forespørgsel, der er aktiveret og eksterne tabeller, der er defineret. Sørg for, at SQL Server understøttes som en datakilde til din værktøj. Derefter henvise til den elastiske forespørgsel database og dens eksterne tabeller på samme måde som alle andre SQL Server-database, du vil oprette forbindelse til med din værktøjet. 

## <a name="best-practices"></a>Bedste fremgangsmåder 
 
* Sørg for, at den elastiske forespørgsel slutpunkt database har fået adgang til fjerndatabasen ved at aktivere adgang til Azure Services i SQL DB firewallkonfigurationen. Også sikre, at de legitimationsoplysninger, der er angivet i den eksterne datakilde datadefinitionsforespørgsel kan logge ind i den eksterne database og har tilladelse til at få adgang til tabellen remote.  

* Elastiske forespørgsel fungerer bedst for forespørgsler hvor de fleste af beregningen kan udføres af de eksterne databaser. Du får den bedste ydeevne forespørgsel med selektivt filter prædikaterne, der kan evalueres på ekstern database eller joinforbindelser, der kan udføres helt på fjerndatabasen typisk. Andre forespørgsel mønstre kan være nødvendigt at indlæse store mængder data fra fjerndatabasen og kan udføre dårligt. 


## <a name="next-steps"></a>Næste trin

Hvis du vil forespørge vandret partitioneret databaser (også kaldet delt databaser), skal du se [forespørgsler på tværs af delt skyen databaser (vandret opdelt)](sql-database-elastic-query-horizontal-partitioning.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
