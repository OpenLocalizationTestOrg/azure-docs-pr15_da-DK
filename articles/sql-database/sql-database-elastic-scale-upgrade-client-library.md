< egenskaber
    
    pageTitle="Upgrade to the latest elastic database client library | Microsoft Azure" 
    description="Upgrade apps and libraries using Nuget" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Opgradere en app til at bruge det seneste elastiske database klientbibliotek

Nye versioner af [elastiske Database klientbibliotek](sql-database-elastic-database-client-library.md) er tilgængelige via NuGetand grænsefladen NuGetPackage Manager i Visual Studio. Opgraderinger indeholder fejlrettelser og understøttelse af nye funktioner i biblioteket klient.

**Til den nyeste version:** Gå til [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Genopbygge dit program med det nye bibliotek, samt ændre metadata for din eksisterende Shard kort Manager gemt i din Azure SQL-databaser til at understøtte nye funktioner.

Udføre disse trin i rækkefølge sikrer, at ældre versioner af biblioteket klienten ikke længere findes i dit miljø når metadataobjekter er opdateret, hvilket betyder, at gamle version metadataobjekter ikke kan oprettes efter opgradering.   

## <a name="upgrade-steps"></a>Opgraderingstrin

**1. opgradere dine programmer.** I Visual Studio, skal du hente og referere til den nyeste version af klienten bibliotek i alle dine udviklingsprojekter, der bruger biblioteket derefter genopbygge og installere. 

 * Vælg **Funktioner**i Visual Studio-løsning --> **NuGet Package Manager** -->  **Administrere NuGet pakker til løsning**. 
 * (Visual Studio 2013) Vælg **opdateringer**i panelet til venstre, og vælg derefter knappen **Opdater** i pakken **Azure SQL Database elastiske skala klientbibliotek** , der vises i vinduet.
 * (Visual Studio-2015) Angiv feltet Filter til at **opgradere tilgængelige**. Vælg pakken, opdatere, og klik på knappen **Opdater** .
    
 
 * Opbygge og anvende. 

**2. opgradere dine scripts.** Hvis du bruger **PowerShell** -scripts til at administrere shards, [hente den nye bibliotek version](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) og kopiere den til den mappe, hvor du udfører scripts. 

**3. opgradere din delt-Flet tjeneste.** Hvis du bruger opdelt Flet elastiske databaseværktøjet reorganisere delt data, [hente og installere den nyeste version af værktøjet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Nærmere opgraderingstrin for tjenesten kan findes [her](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. opgradere din Shard kort Manager-databaser**. Opgradere metadataene understøttende din Shard kort i Azure SQL-Database.  Der er to måder, du kan udføre dette, ved hjælp af PowerShell eller C#. Begge muligheder er vist nedenfor.

***Mulighed 1: Opgradere metadata ved hjælp af PowerShell***

1. Hent den nyeste kommandolinjen værktøj til NuGet fra [her](http://nuget.org/nuget.exe) , og Gem i en mappe. 

2. Åbn en kommandoprompt, Naviger til den samme mappe og udsteder kommandoen:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. Gå til den undermappe, der indeholder den nye klient dll-version, du har netop har hentet, for eksempel:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`

4. Hente elastiske database klient opgradering scriptlet fra [Script Center](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9), og Gem den til den samme mappe, der indeholder DLL-filen.

5. Kør "PowerShell.\upgrade.ps1" fra kommandoprompten, og følg instruktionerne fra den pågældende mappe.
 
***Mulighed 2: Opgradere metadata ved hjælp af C#***

Du kan også oprette et Visual Studio-program, der åbner din ShardMapManager, som gentages over alle shards og udfører opgradering af metadata ved at ringe til metoder [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) og [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) som i dette eksempel: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 
    
    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Disse teknikker til metadata opgraderinger kan anvendes flere gange uden skade. Hvis en ældre klientversion opretter en shard ved et uheld, efter at du allerede har opdateret, kan du f.eks, køre opgraderingen igen på tværs af alle shards til at sikre, at den nyeste version af metadata, findes i hele din infrastruktur. 

**Note:**  Nye versioner af biblioteket klient publiceret til-dato fortsætte med at arbejde med tidligere versioner af Shard kort Manager metadata på Azure SQL DB og omvendt.   Men for at drage fordel af nogle af de nye funktioner i den seneste klient, metadata skal opgraderes.   Bemærk, at metadata opgraderinger ikke påvirker nogen brugerdata eller program-specifikke data, kun objekter oprettes og bruges af Shard kort Manager.  Og programmer fortsætte med at fungere gennem den opgradering sekvens, der er beskrevet ovenfor. 

## <a name="elastic-database-client-version-history"></a>Elastiske database klient versionshistorik 

Versionshistorik, gå til [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
 