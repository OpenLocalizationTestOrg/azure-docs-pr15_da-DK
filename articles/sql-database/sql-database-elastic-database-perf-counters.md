<properties
    pageTitle="Tællere i ydeevne for shard kort manager"
    description="ShardMapManager klasse og data afhængige routing tællere i ydeevne"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="SilviaDoomra"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="SilviaDoomra"/>

# <a name="performance-counters-for-shard-map-manager"></a>Tællere i ydeevne for shard kort manager

Du kan hente ydeevnen for en [shard kort manager](sql-database-elastic-scale-shard-map-management.md), især hvis ved hjælp af [afhængige distribution af data](sql-database-elastic-scale-data-dependent-routing.md). Tællere er oprettet med metoder til klassen Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Tællere bruges til at registrere ydeevnen for de [data afhængige routing](sql-database-elastic-scale-data-dependent-routing.md) handlinger. Disse tællere er tilgængelige i den ydeevne skærm, under kategorien "Elastiske Database: Shard Management".

**Til den nyeste version:** Gå til [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Se også [opgradere en app til at bruge det seneste elastiske database klientbibliotek](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Forudsætninger

* Hvis du vil oprette ydeevne kategori og tællere, skal brugeren være en del af **den lokale administratorgruppe på den computer, der er vært for programmet** .  

* Hvis du vil oprette en forekomst af ydeevnen tæller og opdatere tællerne, skal brugeren være medlem af gruppen **Administratorer** eller **Ydeevne overvåge brugere** gruppe. 

## <a name="create-performance-category-and-counters"></a>Oprette ydeevne kategori og tællere 

For at oprette tællerne, skal du ringe til [ShardMapManagmentFactory klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx)CreatePeformanceCategoryAndCounters metode. Det er kun administratorer kan udføre metoden: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Du kan også bruge [denne](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell-script til at udføre metoden. Metoden opretter de følgende tællere i ydeevne:  

* **Cachelagret tilknytninger**: antal tilknytninger cachelagrede for shard kortet.
*  **DDR handlinger/sec**: rente data afhængige routing handlinger til shard kortet. Tælleren opdateres, når et opkald til [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) resultater i en vellykket forbindelse til destination shard. 
*  **Tilknytning af opslag cache besøg/sec**: hastighed for vellykket cache opslag handlinger for tilknytninger i shard kortet. 
*  **Tilknytning af opslag cache kiksere/sec**: hastighed for mislykkedes cache opslag handlinger for tilknytninger i shard kortet.
*  **Tilknytninger, der er tilføjet eller opdateret i cachen/sec**: hastighed, hvilke tilknytninger føjes eller opdateret i cachen til shard kortet. 
*  **Tilknytninger fjernet fra cachen/sec**: rente, hvormed tilknytninger der fjernes fra cachen til shard kortet. 

Tællere i ydeevne oprettes for hver cachelagrede shard tilknytning hver proces.  


## <a name="notes"></a>Noter
Følgende hændelser udløse oprettelse af tællerne i ydeevne:  

* Initialisering af [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) med [rundvisningen indlæsning](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), hvis ShardMapManager indeholder en hvilken som helst shard kort. Disse omfatter [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) og [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) metoder.
* Vellykket opslag af et shard kort (ved hjælp af [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) eller [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 

* Korrekt oprettelse af shard kort ved hjælp af CreateShardMap().

Tællerne i ydeevne vil blive opdateret med alle cache handlinger, der udføres på shard kort og tilknytninger. Vellykket fjernelse af shard kortet ved hjælp af DeleteShardMap () reults i sletningen af ydeevnen tællere forekomst.  

## <a name="best-practices"></a>Bedste fremgangsmåder

* Oprettelse af ydeevnen kategori og tællere skal kun udføres én gang før oprettelsen af ShardMapManager objekt. Hver udførelse af kommandoen CreatePerformanceCategoryAndCounters() rydder de forrige tællere (at miste data, der rapporteres af alle forekomster) og opretter nye.  

* Ydeevnen tællerforekomster oprettes hver proces. En hvilken som helst programmet går ned eller fjernelse af et shard kort fra cachen medfører sletning af ydeevnen tællere forekomster.  

### <a name="see-also"></a>Se også

[Oversigt over elastiske Database-funktioner](sql-database-elastic-scale-introduction.md)  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

