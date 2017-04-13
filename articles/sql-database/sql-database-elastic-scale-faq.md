<properties 
    pageTitle="Azure SQL elastiske skala ofte stillede spørgsmål om | Microsoft Azure" 
    description="Ofte stillede spørgsmål om Azure SQL Database elastiske skala." 
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
    ms.date="05/03/2016" 
    ms.author="ddove"/>

# <a name="elastic-database-tools-faq"></a>Elastiske Databaseværktøjer ofte stillede spørgsmål 

#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Hvis jeg har en enkelt lejer per shard og ingen sharding nøgle, hvordan jeg så udfylde tasten sharding skema oplysninger?

Objektet skema oplysninger bruges kun til at opdele Flet scenarier. Hvis et program er fuldstændig enkelt lejer, den kræver ikke værktøjet delt flette, og dermed der er ingen grund til at udfylde objektet skema info.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Jeg har klargjort en database, og jeg har allerede en Shard kort Manager, hvordan registrerer jeg den nye database som en shard?

Se **[tilføje en shard til programmet ved hjælp af biblioteket elastiske database klient](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Hvor meget koster elastiske Databaseværktøjer?

Brug af biblioteket elastiske database klient, betale omkostningerne, ikke. Omkostninger periodiseres kun for de Azure SQL-databaser, du bruger til shards og Shard kort Manager samt rollerne web/arbejder du klargør til værktøjet delt flette.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Hvorfor virker legitimationsoplysninger ikke, når jeg tilføjer en shard fra en anden server?
Brug ikke legitimationsoplysninger i form af "bruger ID=username@servername”, i stedet for blot bruge" bruger-ID = brugernavn ".  Husk også, at "brugernavn" login har tilladelser på shard.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Har jeg brug for til at oprette en Shard kort leder og udfylde shards, hver gang jeg starter mit programmer?

Nej – oprettelse af Shard kort Manager (for eksempel **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) er en enkeltstående handling.  Dit program skal bruge opkaldet **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** på programmet opstart tid.  Der bør kun én indkaldelse per programmet domæne.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Jeg har spørgsmål om brug af elastiske Databaseværktøjer, hvordan fjerner jeg dem besvaret? 

Skal du Henvend dig til os på [Azure SQL Database-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Når jeg får en databaseforbindelse ved hjælp af en sharding nøgle, kan jeg stadig forespørgselsdata for andre sharding taster på den samme shard.  Er dette ved design?

De elastiske skala API'er give dig en forbindelse til den korrekte database til din sharding nøgle, men leverer ikke sharding vigtige filtrering.  Føje **hvor** delsætninger til din forespørgsel til at begrænse omfanget til tasten medfølgende sharding, hvis det er nødvendigt.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Kan jeg bruge en anden udgave Azure-Database til hver shard i min shard sæt?

Ja, en shard er en enkelt database, og dermed én shard kan være en Premium edition, mens en anden være et Standard edition. Yderligere, kan udgaven af en shard skalere op eller ned flere gange under levetiden for shard.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Betyder opdelt Flet værktøjet klargøring (eller slette) en database under en opdele eller sammenflette handling? 

Nej. **Opdele** handlinger, destinationsdatabasen skal findes med relevante skemaet og være registreret hos Shard kort Manager.  For **Flet** handlinger, skal du slette shard fra shard kort manager og derefter slette databasen.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 