<properties 
    pageTitle="Forespørgsler med flere shard | Microsoft Azure" 
    description="Køre forespørgsler på tværs af shards ved hjælp af biblioteket elastiske database klient." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/12/2016" 
    ms.author="torsteng"/>

# <a name="multi-shard-querying"></a>Flere shard forespørgsler

## <a name="overview"></a>Oversigt

Med [elastiske Databaseværktøjer](sql-database-elastic-scale-introduction.md), kan du oprette databaseløsninger, der er delt. **Forespørgsler med flere shard** bruges til opgaver som samling/datarapportering, der kræver kører en forespørgsel, der kan strækkes på tværs af flere shards. (Kontrast dette til [distribution af data-afhængige](sql-database-elastic-scale-data-dependent-routing.md), som udfører alt arbejde på en enkelt shard.) 

## <a name="overview"></a>Oversigt

1. Få en [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) eller [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx) bruger [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), [**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)eller metoden [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) . Se [**bygning af et ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) og [**få en RangeShardMap eller ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Oprette et **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)** objekt.
2. Oprette en **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
3. Angiv **[egenskaben CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)** til en T-SQL-kommando.
3. Udføre kommandoen ved at ringe til **[ExecuteReader metode](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
4. Få vist resultaterne ved hjælp af **[MultiShardDataReader klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## <a name="example"></a>Eksempel

Følgende kode illustrerer brugen af flere shard forespørgsler ved hjælp af en given **ShardMap** med navnet *myShardMap*. 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                } 
           } 
    } 

 
Vigtige forskel er konstruktion af flere shard forbindelser. Hvor **SqlConnection** fungerer på en enkelt database, tager **MultiShardConnection** en ***samling af shards*** som input. Udfylde samlingen af shards fra et shard kort. Forespørgslen udføres derefter på samlingen af shards ved hjælp af **UNION ALL** semantik samle et enkelt overordnede resultat. Du kan vælge kan navnet på det sted, hvor rækken stammer fra shard føjes til output ved hjælp af **ExecutionOptions** egenskaben kommando. 

Bemærk opkaldet til **myShardMap.GetShards()**. Denne metode henter alle shards fra shard kortet og giver en nem måde at køre en forespørgsel på tværs af alle relevante databaser. Samling af shards for en forespørgsel med flere shard kan være elegant yderligere ved at udføre en LINQ forespørgsel over samlingen returneres fra opkaldet til **myShardMap.GetShards()**. Sammen med politikken delvise resultater, er den aktuelle evne i forespørgsler med flere shard udviklet til fungerer for mange op til hundredvis af shards.

En begrænsning med flere shard forespørgsler er i øjeblikket manglende validering for shards og shardlets, der spørges. Mens data-afhængige routing kontrollerer, at en given shard er en del af kortet shard på tidspunktet for forespørgsler, udfører med flere shard forespørgsler ikke denne kontrol. Dette kan føre til flere shard forespørgsler, der kører på databaser, der er blevet fjernet fra shard kortet.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Flere shard forespørgsler og opdelte Flet handlinger

Flere shard forespørgsler kontrollerer ikke, om shardlets på den forespørgselsdatabase deltager i igangværende opdelt Flet handlinger. (Se [skalering ved hjælp af elastiske opdelt Flet databaseværktøjet](sql-database-elastic-scale-overview-split-and-merge.md)). Det kan medføre uoverensstemmelser hvor rækker fra den samme shardlet vise til flere databaser i den samme forespørgsel med flere shard. Vær opmærksom på disse begrænsninger, og Medtag draining igangværende opdelt Flet aktiviteter og ændringer til kortet, shard udførelse af forespørgsler med flere shard.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>Se også
**[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)** klasser og metoder.


Administrere shards ved hjælp af [elastiske Database klientbibliotek](sql-database-elastic-database-client-library.md). Indeholder et navneområde, kaldet [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) , der gør muligheden at forespørge på flere shards ved hjælp af en enkelt forespørgsel og resultat. Den indeholder en forespørgsel fremstilling over en samling af shards. Den indeholder også alternative udførelse af politikker, især delvise resultater håndtere fejl, når forespørgsler over mange shards.  

 