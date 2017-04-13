<properties
    pageTitle="Skalering ud med Azure SQL-Database | Microsoft Azure"
    description="Software som en tjeneste (SaaS) udviklere kan nemt oprette elastiske, SVG databaser i skyen ved hjælp af disse værktøjer"
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
    ms.date="09/06/2016"
    ms.author="ddove"/>

# <a name="scaling-out-with-azure-sql-database"></a>Skalering af med Azure SQL-Database

Du kan nemt skalere ud Azure SQL-databaser ved hjælp af værktøjerne **Elastiske Database** . Disse værktøjer og funktioner kan du bruge ressourcerne, der stort set ubegrænset database af **Azure SQL-Database** til at oprette løsninger til transaktions arbejdsmængder og især Software som en tjenesteprogrammer (SaaS). Elastiske Databasefunktioner består af følgende:

* [Elastiske Database klientbibliotek](sql-database-elastic-database-client-library.md): klientbibliotek er en funktion, der gør det muligt at oprette og vedligeholde delt databaser.  Se [Introduktion til elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md).
* [Elastiske databaseværktøjet opdelt Flet](sql-database-elastic-scale-overview-split-and-merge.md): flytter data mellem delt databaser. Dette er nyttigt til at flytte data fra en database med flere lejer til en enkelt lejer database (eller omvendt). Se [elastiske database opdelt Flet værktøjet selvstudium](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Elastiske Database job](sql-database-elastic-jobs-overview.md) (preview): Brug opgaver til at administrere stort antal Azure SQL-databaser. Nemt udføre administrative handlinger som skemaændringer, administration af legitimationsoplysninger, reference dataopdateringer, ydeevne dataindsamling eller lejer (kunde) telemetri af websteder ved hjælp af sager.
* [Elastiske databaseforespørgsel](sql-database-elastic-query-overview.md) (preview): gør det muligt at køre en Transact-SQL-forespørgsel, der strækker sig over flere databaser. Dette gør det muligt for forbindelse til rapporteringsværktøjer som Excel, PowerBI, Tableau osv.
* [Elastiske transaktioner](sql-database-elastic-transactions-overview.md): Denne funktion kan du køre transaktioner, der strækker sig over flere databaser i Azure SQL-Database. Elastiske databasetransaktioner er tilgængelige for .NET-programmer ved hjælp af ADO .NET og integrere med den velkendte programming oplevelse ved hjælp af [System.Transaction klasser](https://msdn.microsoft.com/library/system.transactions.aspx).

I nedenstående illustration vises en arkitektur, som indeholder **elastiske Databasefunktioner** i forhold til en samling af databaser.

I denne grafik repræsenterer farverne i databasen ved hjælp af skemaer. Databaser med samme farve dele det samme skema.

1. Et sæt af **Azure SQL-databaser** er hostet på Azure ved hjælp af sharding arkitektur.
2. **Elastiske Database klientbibliotek** bruges til at administrere et shard sæt.
3. Et undersæt af databaserne, der er placeret i en **elastiske Database puljen**. (Se [Hvad er en samling?](sql-database-elastic-pool.md)).
4. Et **elastiske Database job** kører planlagt eller ad hoc-T-SQL scripts mod alle databaser.
5. **Opdel Flet værktøjet** bruges til at flytte data fra én shard til en anden.
6. **Elastiske databaseforespørgsel** kan du skrive en forespørgsel, der strækker sig over alle databaser i sættet shard.
7. **Elastiske transaktioner** giver dig mulighed at køre transaktioner, der strækker sig over flere databaser. 


![Elastiske Databaseværktøjer][1]


## <a name="why-use-the-tools"></a>Hvorfor bruge værktøjerne?

Nå Elasticitet og skalering til skyen programmer er blevet enkle for FOS og blob-lager – blot tilføje subtrahere enheder eller øge power. Men det har været en udfordring for med høj sikkerhed databehandling i relationsdatabaser. Udfordringer opstået i følgende scenarier:

* Vækst og formindskelse kapacitet for den relationsdatabase del af din arbejdsbyrde.
* Administration af hotspots, der kan opstå påvirker et angivet delsæt af data – som en særligt optaget slutningen-kunde (lejer).

Traditionelt har scenarier som disse rettet ved at investere i større skalering databaseservere for at understøtte programmet. Denne indstilling er dog begrænset i skyen, hvor alle behandling sker på foruddefinerede vare hardware. I stedet er distribution af data og behandling på tværs af mange identisk strukturerede databaser et alternativ traditionelle skalere op tilgange både med hensyn til omkostninger og Elasticitet (et skala fra mønster kaldet "sharding").

## <a name="horizontal-and-vertical-scaling"></a>Vandret og lodret skalering

I nedenstående illustration vises de vandrette og lodrette dimensioner af skalering, som er den grundlæggende måder elastiske databaser kan skaleres.

![Vandret eller lodret Scaleout][2]

Vandret skalering refererer til at tilføje eller fjerne databaser for at justere kapacitet eller overordnede ydeevne. Dette kaldes også "skalering". Sharding, hvor data er opdelt på tværs af en samling af identisk strukturerede databaser, er et almindelige metode til at implementere vandret skalering.  

Lodret skalering refererer til stigende eller faldende en enkelt database præstationsniveau – også kendt som "skalering".

De fleste skyen skalering databaseprogrammer anvender en kombination af disse to strategier. For eksempel en Software som et tjenesteprogram, kan bruge vandret skalering til at klargøre nye slutningen-kunder og lodret skalering for at hver enkelt slutningen-kundes databasen kan Forøg eller Formindsk ressourcer efter behov ved arbejdsbelastningen.

* Vandret skalering administreres ved hjælp af [elastiske Database klientbibliotek](sql-database-elastic-database-client-library.md).

* Lodret skalering opnås ved hjælp af Azure PowerShell-cmdlet'er til at ændre webtjenesten, eller ved at placere databaser i en elastiske puljen.

## <a name="sharding"></a>Sharding

*Sharding* er en metode til at distribuere store mængder identisk strukturerede data på tværs af et antal uafhængige databaser. Det er især populære med skyen udviklere oprette Software som en tjeneste (SAAS) tilbud til kunder eller virksomheder. Disse kunder kaldes ofte "lejere". Sharding kan det være nødvendigt for en hvilken som helst antal årsager:  

* Den samlede mængde af data er for stor til at passe ind i begrænsninger for en enkelt database
* Transaktions overførselshastighed af overordnede arbejdsbelastningen overstiger funktionerne i en enkelt database
* Lejere kan kræve fysisk isolationsniveauet fra hinanden, så du skal bruge separate databaser til hver lejer
* Forskellige dele af en database kan være nødvendigt at være placeret i forskellige lande for overholdelse af regler, ydeevnen eller geopolitiske årsager.

Sharding kan bruges til at udfylde et sæt af databaser, der er organiseret temporally andre scenarier, som indtagelse af data fra fordelt enheder. En separat database kan for eksempel være dedikeret til hver dag eller uge. I så fald nøglen sharding kan være et heltal, der repræsenterer datoen (findes i alle rækker i tabellerne delt) og forespørgsler, der henter oplysninger for et datointerval skal distribueres af programmet til undersættet af databaser, der dækker det pågældende område.

Sharding fungerer bedst, når hver transaktion i et program kan være begrænset til en enkelt værdi af en sharding nøgle. Der sikrer, at alle transaktioner bliver lokale til en bestemt database.

## <a name="multi-tenant-and-single-tenant"></a>Flere lejer og enkelt lejer

Nogle programmer bruger den nemmeste metode til oprettelse af en separat database for hver lejer. Dette er det **enkelt lejer sharding mønster** , der leverer isolationsniveauet, mulighed for sikkerhedskopiering/gendannelse og ressource skalering på granularitet i lejeren. Hver database er knyttet til en bestemt lejer-id-værdi (eller kunde nøgleværdi) med enkelt lejer sharding, men tasten skal altid ikke findes i selve dataene. Det er programmets ansvar at distribuere hver anmodning til den rette database – og biblioteket klient kan forenkle dette.

![Enkelt lejer kontra med flere lejer][4]

Andre scenarier pack flere lejere sammen i databaser i stedet for at isolere dem i separate databaser. Dette er et typisk **med flere lejer sharding mønster** – og det kan styres ved, at et program administrerer stort antal meget lille lejere. I flere lejer sharding er rækker i tabellerne i databasen alle designet til at udføre en nøgle, der identificerer lejer-ID eller sharding nøgle. Igen, kan programmet niveauet er ansvarlig for routing anmodning om en lejer til den rette database, og dette kan understøttes af biblioteket elastiske database klient. Desuden kan række-sikkerhed på brugerniveau bruges til at filter, hvilke rækker, der hver lejer kan få adgang til – få mere at vide, kan se [flere lejer programmer med elastiske Databaseværktøjer og sikkerhed på brugerniveau række](sql-database-elastic-tools-multi-tenant-row-level-security.md). Fordeling af data mellem databaser kan det være nødvendigt med flere lejer sharding mønster, og dette sker via opdelt Flet elastiske databaseværktøjet. Hvis du vil vide mere om designs til SaaS programmer, der bruger elastiske grupper skal du se [Designs til flere lejer SaaS programmer med Azure SQL-Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Flytte data fra flere enkelt arkitekturer databaser

Når du opretter et SaaS program, er det typisk til at tilbyde kundeemner en prøveversion af softwaren. I dette tilfælde er det økonomisk at bruge en database med flere arkitekturer til dataene. Når et kundeemne, bliver en kunde, er en enkelt lejer database dog bedre fordi det giver bedre ydeevne. Hvis kunden oprettede data i prøveperioden, kan du bruge [opdelt Flet værktøj](sql-database-elastic-scale-overview-split-and-merge.md) til at flytte data fra flere lejeren til den nye enkelt lejer database.

## <a name="next-steps"></a>Næste trin

Til en eksempel-app, der viser biblioteket klienten på computeren, kan du se [komme i gang med elastiske Datababase værktøjer](sql-database-elastic-scale-get-started.md).

For at konvertere eksisterende databaser for at bruge værktøjerne, skal du se [overføre eksisterende databaser til skala ud](sql-database-elastic-convert-to-use-elastic-tools.md).

For at se detaljerne for puljen elastiske Database skal du se [pris og ydeevne overvejelser i forbindelse med en puljen elastiske database](sql-database-elastic-pool-guidance.md)eller oprette en ny gruppe med [selvstudium](sql-database-elastic-pool-create-portal.md).  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

