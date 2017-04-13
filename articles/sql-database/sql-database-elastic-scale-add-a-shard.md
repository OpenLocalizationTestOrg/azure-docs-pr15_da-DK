<properties 
    pageTitle="Tilføje en shard ved hjælp af elastiske Databaseværktøjer | Microsoft Azure" 
    description="Sådan bruges elastiske skala API'er til at føje nye shards til en shard angive." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="adding-a-shard-using-elastic-database-tools"></a>Tilføje en shard ved hjælp af elastiske Databaseværktøjer

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Tilføje en shard til en ny område eller nøgle  

Programmer skal ofte blot tilføje nye shards for at håndtere data, der forventes af nye nøgler eller vigtige områder efter et shard kort, der allerede findes. For eksempel et program, der er delt efter lejer ID måske nødt til at klargøre en ny shard for nye lejere, eller data delt månedlig muligvis en ny shard klargjort inden begyndelsen af hver ny måned. 

Hvis det nye celleområde nøgleværdier ikke allerede er en del af en eksisterende tilknytning er det nemt at tilføje den nye shard og knytte den nye nøgle eller et område til shard. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Eksempel: tilføje en shard og dens område til en eksisterende shard kort
I dette eksempel bruger [TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx) [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx) [CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0})) metoder og opretter en forekomst af klassen [ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.) . I eksemplet nedenfor, der er oprettet en database med navnet **sample_shard_2** og alle nødvendige skemaobjekter inde i den for at holde område [300, 400).  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


Som alternativ kan bruge du Powershell til at oprette en ny Shard kort Manager. Et eksempel findes [her](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).
## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Tilføje en shard til et tomt område på et eksisterende område  

I nogle tilfælde kan du har allerede knyttet et område til en shard og delvist udfyldt det med data, men du vil nu kommende data sendes direkte til en anden shard. For eksempel du shard ved antallet af dage og har allerede tildelte 50 dage til en shard, men på dag 24, du vil fremtidige data til lander i en anden shard. Elastiske database [opdelt Flet værktøjet](sql-database-elastic-scale-overview-split-and-merge.md) kan udføre denne handling, men hvis flytning af data ikke er nødvendigt (for eksempel, data til området af dage [25, 50), det vil sige, dag 25 inklusive til 50 eksklusivt, ikke findes) du kan udføre dette udelukkende ved hjælp af Shard kort Management API'er direkte.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Eksempel: opdele et område og tildele den tomme del til en nyligt tilføjede shard

En database med navnet "sample_shard_2" og alle nødvendige skemaobjekter inde i den er blevet oprettet.  

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Vigtigt**: bruge denne teknik, kun hvis du er sikker på, at området for opdaterede tilknytningen er tom.  Disse metoder kontrollerer ikke data til det område, der skal flyttes, så det er bedst at medtage Kontroller i din kode.  Hvis rækker findes i det område, der flyttes, matcher den faktiske datafordeling ikke opdateret shard kortet. Bruge [opdelt Flet værktøj](sql-database-elastic-scale-overview-split-and-merge.md) til at udføre handlingen, i stedet i disse tilfælde.  


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 
