<properties
    pageTitle="DocumentDB global databasereplikering | Microsoft Azure"
    description="Lær, hvordan du administrerer global replikering af kontoen DocumentDB via Azure-portalen."
    services="documentdb"
    keywords="Global database, gentagelse"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="mimig"/>

# <a name="how-to-perform-documentdb-global-database-replication-using-the-azure-portal"></a>Hvordan du udfører DocumentDB global databasereplikering ved hjælp af portalen Azure

Lær at bruge Azure-portalen til at kopiere data i flere områder til globale tilgængeligheden af data i Azure DocumentDB.

Oplysninger om, hvordan global database gentagelse fungerer i DocumentDB, skal du se [fordel data globalt med DocumentDB](documentdb-distribute-data-globally.md). Du kan finde oplysninger om globale databasereplikering fra et program, [udvikling med flere områder DocumentDB konti](documentdb-developing-with-multiple-regions.md).

> [AZURE.NOTE] Global fordelingen af DocumentDB databaser er alment tilgængelig og automatisk aktiveret for en hvilken som helst nyoprettede DocumentDB konti. Vi arbejder på for at aktivere global fordeling for alle eksisterende konti, men i mellemtiden, hvis du vil globale fordeling aktiveret på din konto, skal du [kontakte support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , og vi skal aktivere det for dig nu.

## <a id="addregion"></a>Tilføje globale database områder

DocumentDB findes i de fleste [Azure områder] [azureregions]. Når du har valgt konsistens Standardniveau for din database-konto, kan du knytte en eller flere områder (afhængigt af dit valg af standard konsistens niveau og globale fordeling behov).

1. Klik på **DocumentDB konti**i [Azure-portalen](https://portal.azure.com/), i Jumpbar.
2. Vælg databasekontoen til at ændre i bladet **DocumentDB konto** .
3. Klik på **gentage data globalt** fra menuen i bladet konto.
4. Vælg områderne, tilføje eller fjerne i bladet **gentage data globalt** , og klik derefter på **Gem**. Der er en omkostninger for at tilføje områder, skal du se [priser side](https://azure.microsoft.com/pricing/details/documentdb/) eller [Distribuer data globalt med DocumentDB](documentdb-distribute-data-globally.md) artiklen kan finde flere oplysninger.

    ![Klik på områderne i kortet for at tilføje eller fjerne dem.][1]

### <a name="selecting-global-database-regions"></a>Valg af globale database områder

Når du konfigurerer to eller flere områder, anbefales det at områder er markeret, baseret på de område par, der er beskrevet i de [Business løbende og genoprettelse efter nedbrud (BCDR): Azure parvis områder]  [ bcdr] artikel.

Specifikt, når du konfigurerer til flere områder, skal du vælge det samme antal områder (+/-1 for ulige/lige) fra hver af kolonnerne parvis område. For eksempel, hvis du vil installere på fire områder i USA, skal vælge du to USA områder fra kolonnen yderst til venstre og to fra højre. Så følgende ville være et passende sæt: vest USA, af USA, nord Central os og Syd Central os.

Denne vejledning er vigtigt at følge, når kun to områder er konfigureret til genoprettelse efter nedbrud scenarier. Er god praksis Følg denne vejledning for mere end to områder, men ikke kritiske, som nogle af de valgte områder overholde denne parring.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a id="next"></a>Næste trin

Lær, hvordan du administrerer konsistens på din globalt replikerede konto ved at læse [konsistens niveauer i DocumentDB](documentdb-consistency-levels.md).

Oplysninger om, hvordan global database gentagelse fungerer i DocumentDB, skal du se [fordel data globalt med DocumentDB](documentdb-distribute-data-globally.md). Du kan finde oplysninger om automatisk replikering af data i flere områder [udvikling med flere områder DocumentDB konti](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/
