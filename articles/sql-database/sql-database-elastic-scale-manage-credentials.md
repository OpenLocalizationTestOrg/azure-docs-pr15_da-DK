<properties 
    pageTitle="Administrere legitimationsoplysninger i biblioteket elastiske database klient | Microsoft Azure" 
    description="Sådan angives det rette niveau af legitimationsoplysninger, administrator for at skrivebeskyttet til elastiske database-apps" 
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

# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Legitimationsoplysninger, der bruges til at få adgang til biblioteket elastiske Database klient

[Elastiske Database klientbibliotek](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) bruger tre forskellige typer af legitimationsoplysninger til at få adgang til [shard kort manager](sql-database-elastic-scale-shard-map-management.md). Afhængigt af er nødvendigheden, kan du bruge legitimationsoplysninger med det laveste niveau af access mulige.

* **Administration af legitimationsoplysninger**: for at oprette eller manipulere en shard kort manager. (Se [Ordliste](sql-database-elastic-scale-glossary.md)). 
* **Legitimationsoplysninger**: at få adgang til en eksisterende shard kort manager for at få oplysninger om shards.
* **Forbindelse legitimationsoplysninger**: oprette forbindelse til shards. 

Se også [administrere databaser og logon i Azure SQL-Database](sql-database-manage-logins.md). 
 
## <a name="about-management-credentials"></a>Om administration af legitimationsoplysninger

Administration af legitimationsoplysninger bruges til at oprette et [**ShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) objekt til programmer, manipulerer shard kort. (For eksempel se [tilføje en shard ved hjælp af elastiske Databaseværktøjer](sql-database-elastic-scale-add-a-shard.md) og [afhængige distribution af Data](sql-database-elastic-scale-data-dependent-routing.md)) Brugeren af biblioteket elastiske skala klienten opretter SQL-brugere og SQL-logon og sikrer, at hver får læse-og skriveadgang tilladelser på den globale shard kort database og samt alle shard databaser. Disse legitimationsoplysninger anvendes til at vedligeholde globale shard kortet og de lokale shard kort, når der udføres ændringer til kortet, shard. For eksempel bruge management legitimationsoplysninger til at oprette shard kort manager objektet (ved hjælp af [**GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx): 

    // Obtain a shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmAdminConnectionString, 
            ShardMapManagerLoadPolicy.Lazy 
    ); 

Variabelt **smmAdminConnectionString** er en forbindelsesstreng, der indeholder management-legitimationsoplysningerne. Bruger-ID og din adgangskode giver læse-/ skriveadgang til både shard kort database og individuelle shards. Administration af forbindelsesstrengen indeholder også det servernavn og databasenavn til at identificere den globale shard kort database. Her er en typisk forbindelsesstreng hertil:

     "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

Brug ikke værdier i form af "username@server"—instead kan bruge værdien "username".  Dette skyldes, at legitimationsoplysninger skal arbejde på både shard kort manager-database og individuelle shards, som kan være på forskellige servere.

## <a name="access-credentials"></a>Legitimationsoplysninger
  
Når du opretter en shard kort manager i et program, der ikke administrere shard kort, kan du bruge legitimationsoplysninger, der har skrivebeskyttet tilladelser på kortet globale shard. De oplysninger, der er hentet fra den globale shard tilknytning under disse legitimationsoplysninger anvendes til [data-afhængige routing](sql-database-elastic-scale-data-dependent-routing.md) og til at udfylde shard kort cachen på klienten. Legitimationsoplysningerne, der er tilgængelige via det samme opkald mønster til **GetSqlShardMapManager** , som vist ovenfor: 

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

Bemærk brugen af **smmReadOnlyConnectionString** til at afspejle brugen af andre legitimationsoplysninger for denne adgang på vegne af brugere, der **ikke er administratorer** : disse legitimationsoplysninger kan ikke give tilladelse til at skrive på kortet globale shard. 

## <a name="connection-credentials"></a>Forbindelse legitimationsoplysninger 

Yderligere legitimationsoplysninger er påkrævet ved hjælp af metoden [**OpenConnectionForKey**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) at få adgang til en shard, der er knyttet til en sharding nøgle. Disse legitimationsoplysninger skal angive tilladelser for skrivebeskyttet adgang til de lokale shard kort tabeller, der er placeret på shard. Det er nødvendigt at udføre validering af forbindelse til data-afhængige routing på shard. Denne kodestykke tillader adgang til data i forbindelse med afhængige distribution af data: 
 
    using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
    targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

I dette eksempel indeholder **smmUserConnectionString** forbindelsesstrengen for brugerens legitimationsoplysninger. Azure SQL DB er her en typisk forbindelsesstreng for brugerlegitimationsoplysninger: 

    "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

Som med Administratoroplysninger, ikke værdier i form af "username@server". I stedet bruge lige "brugernavn".  Bemærk også, at forbindelsesstrengen ikke indeholder en servernavn og databasenavn. Det skyldes, at **OpenConnectionForKey** opkaldet bliver automatisk direkte forbindelse til den korrekte shard baseret på tasten. Det vil sige, databasenavnet og servernavnet er ikke angivet. 

## <a name="see-also"></a>Se også
[Administration af databaser og logon i Azure SQL-Database](sql-database-manage-logins.md)

[Sikring af SQL-Database](sql-database-security.md)

[Introduktion til elastiske Database job](sql-database-elastic-jobs-getting-started.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 