<properties
   pageTitle="Overføre eksisterende databaser til skala ud | Microsoft Azure"
   description="Konvertere delt databaser for at bruge elastiske Databaseværktøjer ved at oprette en shard kort manager"
   services="sql-database"
   documentationCenter=""
   authors="ddove"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/24/2016"
   ms.author="ddove"/>

# <a name="migrate-existing-databases-to-scale-out"></a>Overføre eksisterende databaser til skala ud

Nemt administrere dine eksisterende delt skaleret-databaser ved hjælp af Azure SQL-Database Databaseværktøjer (såsom [elastiske Database klientbibliotek](sql-database-elastic-database-client-library.md)). Først skal du konvertere et eksisterende sæt af databaser for at bruge den [shard tilknytte manager](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Oversigt
Overføre en eksisterende delt database: 

1. Forberede [shard kort manager-database](sql-database-elastic-scale-shard-map-management.md).
2. Oprette kortet shard.
3. Forberede individuelle shards.  
2. Føje tilknytninger til shard kortet.

Disse teknikker kan implementeres ved hjælp af [.NET Framework klientbibliotek](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)eller PowerShell-scripts, findes på [Azure SQL DB - elastiske Database værktøjer scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Her Ark6 PowerShell-scripts.

Du kan finde flere oplysninger om ShardMapManager, [Shard tilknytte administration](sql-database-elastic-scale-shard-map-management.md). Du kan finde en oversigt over de elastiske Databaseværktøjer, [Oversigt over funktioner elastiske Database](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Forberede shard kort manager-database

Shard kort manager er en speciel database, der indeholder dataene, hvis du vil administrere skaleret databaser. Du kan bruge en eksisterende database eller oprette en ny database. Bemærk, at en database, der fungerer som shard kort manager ikke skal være den samme database som en shard. Bemærk også, at PowerShell-script ikke oprette databasen for dig. 

## <a name="step-1-create-a-shard-map-manager"></a>Trin 1: oprette en shard kort manager

    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Til at hente shard kort manager

Efter oprettelse af, kan du hente shard kort manager med denne cmdlet. Dette trin er behov for hver gang du skal bruge objektet ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 

  
## <a name="step-2-create-the-shard-map"></a>Trin 2: oprette kortet shard

Du skal vælge typen shard afbildning for at oprette. Valget, afhænger af databasearkitekturen: 

1. Enkelt lejer per database (se vilkår, [Ordliste](sql-database-elastic-scale-glossary.md).) 
2. Flere lejere per database (to typer):
    3. Liste over tilknytning
    4. Område tilknytning
 

Opret en **liste over tilknytning** shard plotning for en enkelt lejer model. Enkelt lejer modellen tildeler én database per lejer. Dette er en effektiv model for SaaS udviklere, som den forenkler administration.

![Liste over tilknytning][1]

Modellen med flere lejer tildeler flere lejere til en enkelt database (og du kan distribuere grupper af lejere på tværs af flere databaser). Brug denne model, når du forventer, at hver lejer have small data behov. I denne model tildele vi et område af lejere til en database ved hjælp af **området tilknytning**. 
 

![Område tilknytning][2]

Eller du kan implementere en med flere lejer databasemodel ved hjælp af en *liste over tilknytning* til at tildele flere lejere til en enkelt database. For eksempel Saldo 1 bruges til at gemme oplysninger om lejer id 1 og 5, og DB2 gemmer data for lejer 7 og 10-lejer. 

![Flere lejere på enkelt DB][3] 

**Baseret på dit valg, Vælg en af disse indstillinger:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Mulighed 1: oprette et shard kort til en liste over tilknytning
Oprette et shard kort ved hjælp af objektet ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 
 
 
### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Mulighed 2: oprette et shard kort til et område afbildning

Bemærk, at hvis du vil bruge denne tilknytning mønster, lejer id værdier skal være fortløbende områder, og det kan accepteres skal have hul i feltet områder, ved blot at springe over området, når du opretter for databaser.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>Mulighed 3: Listen tilknytninger på en enkelt database
Konfiguration af dette mønster kræver også oprettelse af et kort på listen, som vist i trin 2, valgmulighed 1.

## <a name="step-3-prepare-individual-shards"></a>Trin 3: Forberede individuelle shards

Føje hver shard (database) til shard kort manager. Dette forbereder de enkelte databaser til lagring af forbindelsesoplysninger. Udføre denne metode for hver shard.
     
    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.
 

## <a name="step-4-add-mappings"></a>Trin 4: Tilføje tilknytninger

Tilføjelse af tilknytninger, afhænger af typen shard kort, du har oprettet. Hvis du har oprettet et kort på listen, kan du tilføje listen tilknytninger. Hvis du har oprettet et område kort, kan du tilføje område tilknytninger.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Mulighed 1: tilknytte dataene til en liste over tilknytning

Knytte data ved at tilføje en liste over tilknytning for hver lejer.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Mulighed 2: Tilknyt dataene til et område-tilknytning

Tilføje område tilknytninger til alle lejer id området – database tilknytninger:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>Trin 4 indstillingen 3: tilknytte dataene til flere lejere på en enkelt database

For hver lejer køre Tilføj-ListMapping (indstilling 1, ovenfor). 


## <a name="checking-the-mappings"></a>Kontrollere tilknytningerne

Oplysninger om de eksisterende shards og de tilknytninger, der er knyttet til dem kan forespørges hjælp af følgende kommandoer:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Oversigt

Når du har afsluttet konfigurationen, kan du begynde at bruge biblioteket elastiske Database klient. Du kan også bruge [afhængige distribution af data](sql-database-elastic-scale-data-dependent-routing.md) og [med flere shard forespørgsel](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Næste trin


Få PowerShell-scripts fra [DB elastiske Azure SQL-Database værktøjer sripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Værktøjerne, der er også på GitHub: [Azure/elastiske-db-værktøjer](https://github.com/Azure/elastic-db-tools).

Bruge værktøjet delt brevfletning til at flytte data til eller fra en model med flere lejer til en enkelt lejer datamodel. Se [delt Flet værktøj](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Yderligere ressourcer

Du kan finde oplysninger på almindelige data arkitektur mønstre af flere lejer software som en tjeneste (SaaS) databaseprogrammer [Designs til flere lejer SaaS programmer med Azure SQL-Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Spørgsmål og anmode om funktioner

Spørgsmål, skal du Henvend dig til os på [SQL-Database forum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) og anmode om funktioner, skal du føje dem til [SQL-Database feedback-forummet](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 
