<properties
   pageTitle="Distribuere data globalt med DocumentDB | Microsoft Azure"
   description="Få mere at vide om kloden skalering geografisk gentagelse, failover, og gendannelse af data ved hjælp af globale databaser fra Azure DocumentDB, en komplet administreret NoSQL database tjeneste."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="kipandya"/>
   
   
# <a name="distribute-data-globally-with-documentdb"></a>Distribuere data globalt med DocumentDB

> [AZURE.NOTE] Global fordelingen af DocumentDB databaser er alment tilgængelig og automatisk aktiveret for en hvilken som helst nyoprettede DocumentDB konti. Vi arbejder på for at aktivere global fordeling for alle eksisterende konti, men i mellemtiden, hvis du vil globale fordeling aktiveret på din konto, skal du [kontakte support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , og vi skal aktivere det for dig nu.

Azure DocumentDB er udviklet til imødekommer behovet hos IoT programmer bestående af millioner af globalt fordelt enheder og internet skala programmer, der leverer meget svarede oplevelser til brugere på tværs af hele verden. Disse databasesystemer ud for udfordringen at opnå kort ventetid adgang til programmet data fra flere geografiske områder, hvor klart defineret data konsistens og tilgængelighed garantier. Som et globalt distribueret databasesystem forenkler DocumentDB global fordelingen af data ved at tilbyde fuldt administreret, flere områder database konti, som giver Ryd kompromiserne mellem konsistens, tilgængelighed og ydeevne, alle med tilsvarende garantier. DocumentDB database konti tilbydes med høj tilgængelighed, enkelt ciffer ms latenstider, flere [klart defineret konsistens niveauer] [consistency], gennemsigtig internationale failover med API'er til understøttelse af flere websteder og muligheden for at skalere elastically overførselshastighed og lager over hele verden. 

  
## <a name="configuring-multi-region-accounts"></a>Konfiguration af flere områder konti

Konfiguration af kontoen DocumentDB skalere over hele verden kan udføres på mindre end et minut via [Azure-portalen](documentdb-portal-global-replication.md). Alt, du skal gøre, er Vælg niveauet højre konsistens mellem flere understøttede klart defineret konsistens niveauer, og knytte en hvilken som helst antal Azure områder til databasekontoen. DocumentDB konsistens niveauer giver Ryd kompromiserne mellem bestemte konsistens GARANTIBRUD og ydeevne. 

![DocumentDB tilbyder flere, godt defineret (lavere) konsistens modeller kan vælge mellem][1]

DocumentDB tilbyder flere, godt defineret (lavere) konsistens modeller kan vælge mellem.

Hvis du vælger niveauet højre konsistens afhænger af data konsistens garantere programmets behov. DocumentDB kopieres dine data på tværs af alle angivne områder og garanterer konsistens, som du har valgt til din database konto automatisk. 


## <a name="using-multi-region-failover"></a>Brug af flere områder failover 

Azure DocumentDB er i stand til transparent failover database konti på tværs af flere Azure områder – den nye [understøttelse af flere websteder API'er] [ developingwithmultipleregions] GARANTIBRUD, at din app kan fortsætte med at bruge et logisk slutpunkt og er uafbrudt ved sekundære. Failover styres ved du, forudsat at fleksibilitet til at angive din database konto i begivenheden et område af mulige fejl under opstår, herunder programmet, infrastruktur, tjenesten eller internationale mislykkede (rigtigt eller simuleret). Tjenesten mislykkes transparent over din database-konto i tilfælde af en DocumentDB internationale fejl, og dit program fortsætter med at få adgang til data uden at miste tilgængelighed. Mens DocumentDB tilbyder [99,99% tilgængelighed SLA][sla], du kan teste dit program end for at slutningen tilgængelighed egenskaber ved at simulere en internationale mislykket begge, [ved hjælp af programmering] [ arm] såvel som via Azure-portalen.


## <a name="scaling-across-the-planet"></a>Skalering på tværs af kloden
DocumentDB gør det muligt at uafhængigt Klargør overførselshastighed og forbruge lagerplads for hver DocumentDB af websteder på en hvilken som helst skala, globalt på tværs af alle de områder, der er knyttet til databasekontoen. En DocumentDB samling distribueret globalt og administreres på tværs af alle de områder, der er knyttet til din database konto automatisk. Af websteder i din database-konto, må distribueres på tværs af nogen af de Azure områder, hvor den [DocumentDB tjenesten er tilgængelig][serviceregions]. 

Overførselshastighed købt og lagerplads consumed for hver DocumentDB af websteder er automatisk klargjort på tværs af alle områder lige. Dette giver mulighed for dit program problemfrit skalere på tværs af globus [betaler kun for overførselshastighed og lagerplads du bruger inden for hver time][pricing]. Eksempelvis hvis du har klargjort 2 millioner RUs for en DocumentDB af websteder, får derefter hvert af de områder, der er knyttet til databasekontoen 2 millioner RUs for den pågældende samling. Dette er vist nedenfor.

![Skalering overførsel for en DocumentDB af websteder på tværs af fire områder][2]

DocumentDB garanterer < 10 ms læse og < 15 ms skrive latenstider på P99. Læs anmodninger om dække aldrig datacenter rammen for at garantere [konsistens krav, du har valgt][consistency]. Skriver er altid quorum anvendt lokalt, før de er bekræftet til klienter. Hver database-konto er konfigureret med Skriv område prioritet. Det område, der er angivet med højeste prioritet skal fungere som det aktuelle Skriv område for kontoen. Alle SDK'er dirigere transparent konto skrivning til databasen til det aktuelle Skriv område. 

Til sidst, da DocumentDB er helt [skema agnostic] [ vldb] -du aldrig behøver at bekymre dig om at administrere/opdaterer skemaer eller sekundære indeks på tværs af flere datacentre. Dine [SQL-forespørgsler] [ sqlqueries] fortsætte med at arbejde, mens dit program og datamodeller fortsætte med at udvikle. 


## <a name="enabling-global-distribution"></a>Aktivere global fordeling 

Du kan vælge at gøre dataene lokalt eller globalt distribueret ved enten at tilknytte en eller flere Azure områder, hvor en DocumentDB database konto. Du kan tilføje eller fjerne områder til databasekontoen når som helst. For at aktivere global fordeling ved hjælp af portalen skal du se, [hvordan du udfører DocumentDB global databasereplikering ved hjælp af portalen Azure](documentdb-portal-global-replication.md). For at aktivere global fordeling programmeringsmæssigt skal du se [udvikling med flere områder DocumentDB konti](documentdb-developing-with-multiple-regions.md).

## <a name="next-steps"></a>Næste trin

Få mere at vide mere om de distribuere data globalt med DocumentDB i følgende artikler:

* [Klargøring af overførselshastighed og lagerplads på en af websteder] [throughputandstorage]
* [Understøttelse af flere websteder API'er via RESTEN. .NET, Java, Python og Node SDK'er] [developingwithmultipleregions]
* [Konsistens niveauer i DocumentDB] [consistency]
* [Tilgængelighed SLA] [sla]
* [Administrere database konto] [manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: documentdb-partition-data.md
[consistency]: documentdb-consistency-levels.md
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: documentdb-developing-with-multiple-regions.md
[createaccount]: documentdb-create-account.md
[manageaccount]: documentdb-manage-account.md
[manageaccount-consistency]: documentdb-manage-account.md#consistency
[throughputandstorage]: documentdb-manage.md
[arm]: documentdb-automation-resource-manager-cli.md
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/en-us/regions/#services 
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/ 
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: documentdb-sql-query.md

