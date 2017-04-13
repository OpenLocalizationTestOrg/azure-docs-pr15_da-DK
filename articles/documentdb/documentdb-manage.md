<properties
    pageTitle="DocumentDB storage og ydeevne | Microsoft Azure" 
    description="Få mere at vide om lagring af data og lagring af dokumenter i DocumentDB, og hvordan du kan skalere DocumentDB at opfylde kapacitet passer til dit program." 
    keywords="lagring af dokumenter"
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="syamk"/>

# <a name="learn-about-storage-and-predictable-performance-provisioning-in-documentdb"></a>Få mere at vide om lagring og mere forudsigelige ydeevne klargøring i DocumentDB
Azure DocumentDB er en fuldt administreret, SVG dokumentorienterede NoSQL database tjeneste for JSON dokumenter. Med DocumentDB behøver du ikke at udleje virtuelle maskiner, installere software eller overvåge databaser. DocumentDB er drevet og løbende overvåges af Microsoft teknikere til at levere verden klasse tilgængelighed, ydeevne og databeskyttelse.  

Du kan komme i gang med DocumentDB ved at [oprette en database-konto](documentdb-create-account.md) og en [DocumentDB database](documentdb-create-database.md) via [Azure-portalen](https://portal.azure.com/). DocumentDB databaser, der tilbydes i enheder af Solid State drev (SSD) sikkerhedskopier lager og overførselshastighed. Disse lagerplads enheder er klargjort ved at [oprette databasen samlinger](documentdb-create-collection.md) i din database-konto, hver af websteder med reserveret overførselshastighed, der kan skaleres op eller ned på en hvilken som helst tidspunkt for at overholde krav for dit program. 

Hvis dit program overskrider din reserveret overførsel for en eller flere af websteder, begrænset anmodninger på grundlag af hver af websteder. Det betyder, at nogle anmodninger om programmet kan lykkes, mens andre kan være begrænset.

I denne artikel giver et overblik over de ressourcer og målepunkter, der er tilgængelige til at administrere kapacitet og planlægge datalagring. 

## <a name="database-account"></a>Database-konto
Som en Azure abonnement, kan du klargøre en eller flere DocumentDB database konti for at administrere din databaseressourcer. Hvert abonnement er knyttet til et enkelt Azure abonnement. 

DocumentDB konti kan oprettes via [Azure portal](documentdb-create-account.md)eller ved hjælp af [en ARM skabelon eller Azure CLI](documentdb-automation-resource-manager-cli.md).

## <a name="databases"></a>Databaser
En enkelt DocumentDB database kan indeholde næsten en ubegrænset mængde lagring af dokumenter er inddelt i af websteder. Samlinger giver ydeevne isolationsniveauet – hver enkelt af websteder kan klargøres med overførselshastighed, der ikke deles med andre samlinger i den samme database eller en konto. En DocumentDB database er elastiske i størrelse, lige fra GB til lagring af dokumenter TBs af SSD sikkerhedskopier og klargjort overførselshastighed. I modsætning til en traditionel RDBMS database, en database i DocumentDB, ikke er fastsat til en enkelt maskine og kan strækker sig over flere computere eller grupper.  

Med DocumentDB, som du vil skalere dine programmer, kan du oprette flere samlinger eller databaser eller begge dele. Databaser kan oprettes via [Azure portal](documentdb-create-database.md) eller via en af [DocumentDB SDK'er](documentdb-dotnet-samples.md).   

## <a name="database-collections"></a>Database af websteder
Hver DocumentDB database kan indeholde et eller flere af websteder. Samlinger fungere som meget tilgængelige datapartitioner til lagring af dokumenter og behandling. Hver af websteder kan gemme dokumenter med forskellige skema. Documentdbs automatisk indeksering og forespørgsel egenskaber kan du hurtigt og nemt at filtrere og finde dokumenter. En samling indeholder området for udførelse af dokument lager og forespørgsel. En samling er også en transaktion domæne for alle de dokumenter, der er indeholdt i den. Samlinger er allokeret overførselshastighed baseret på den værdi, der er angivet i portalen Azure eller via SDK'er. 

Samlinger er automatisk opdelt i en eller flere fysiske servere ved DocumentDB. Når du opretter en samling, kan du angive den klargjorte overførselshastighed med hensyn til anmodning om enheder hver anden og en partition vigtige egenskab. Værdien af denne egenskab bruges ved DocumentDB til at distribuere dokumenter mellem partitioner og distribuere anmodninger som forespørgsler. Den partition nøgleværdi fungerer også som posteringen grænsen for lagrede procedurer og udløsere. Hver af websteder har en reserveret mængde overførselshastighed, der er specifikke for den af websteder, som ikke deles med andre samlinger i den samme konto. Du kan derfor skalere ud af dit program både med hensyn til lager og overførselshastighed. 

Af websteder kan oprettes via [Azure portal](documentdb-create-collection.md) eller via en af [DocumentDB SDK'er](documentdb-sdk-dotnet.md).   
 
## <a name="request-units-and-database-operations"></a>Anmode om enheder og databasehandlinger

Når du opretter en samling, kan du reservere overførselshastighed med hensyn til [anmodning om enheder (RU)](documentdb-request-units.md) sekundet. I stedet tænker over og administration af hardwareressourcer, kan du betragte en **anmodning om enhed** som et enkelt mål for ressourcerne, der kræves for at udføre forskellige databasehandlinger og servicere en anmodning om anvendelse. En læsning af et dokument med 1 KB bruger den samme 1 RU uanset antallet af elementer, der er gemt i gruppen af websteder eller antallet af samtidige anmodninger, der kører på samme måde. Alle anmodninger mod DocumentDB, herunder komplekse handlinger, såsom SQL-forespørgsler har en mere forudsigelige RU værdi, der kan bestemmes på udviklingstid. Hvis du kender størrelsen på dine dokumenter og hyppigheden for hver operation (læser, skrivning og forespørgsler) til at understøtte for dit program, kan du klargør den nøjagtige mængde overførselshastighed til dit program behov og skalere databasen op og ned, som din ydeevne behov ændres. 

Hver af websteder kan være reserveret med overførselshastighed i blokke på 100 anmodning enheder sekundet fra hundredvis op til millioner af anmodning om enheder sekundet. Klargjort overførselshastigheden kan tilpasses i hele levetid og en samling til at tilpasse sig ændrede behov og behandling og få adgang til mønstre af dit program. Du kan finde yderligere oplysninger finder [DocumentDB ydeevneniveauer](documentdb-performance-levels.md). 

>[AZURE.IMPORTANT] Samlinger er fakturerbar enheder. Omkostninger, bestemmes af den klargjorte overførsel af samlingen målt i anmodning enheder sekundet sammen med den samlede anvendte lagerplads i gigabyte. 

Hvor mange anmodning om enheder, en bestemt handling som Indsæt, Slet, forespørgsel eller lagret procedure udførelse af forbruger? En anmodning om enhed er et standardiseret mål for anmodning om behandling af omkostninger. En læsning af en 1 KB dokument er 1 RU, men en anmodning om at indsætte, erstatte eller slette det samme dokument vil forbruge flere behandling af tjenesten og dermed flere anmodning enheder. Hvert svar fra tjenesten indeholder et brugerdefineret sidehoved (`x-ms-request-charge`), der rapporter på anmodning om enheder, der benyttes for anmodningen. Denne overskrift er også tilgængelig via [SDK'er](documentdb-sdk-dotnet.md). I SDK .NET er [RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge) en egenskab for objektet [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx) . Hvis du vil estimere dine overførselshastighed behov før du foretager et enkelt opkald, kan du bruge [kapacitet teamplanlægning](documentdb-request-units.md#estimating-throughput-needs) med hjælp til denne skøn. 

>[AZURE.NOTE] Oprindelig plan for 1 anmodning enhed for et dokument, 1 KB svarer til en enkelt Hent af dokumentet med [Session konsistens](documentdb-consistency-levels.md). 

Der er flere faktorer, der påvirker de anmodning om enheder, der benyttes for en handling på en DocumentDB database-konto. Disse faktorer:

- Dokumentstørrelse. Når dokumentstørrelser øger de enheder, der benyttes for at læse eller skrive dataene, øges.
- Egenskaben antal. Under antagelse standard indeksering af alle egenskaber, de enheder, der benyttes for at skrive et dokument, øger når egenskaben Tæl øges.
- Data konsistens. Når du bruger data konsistens niveauer af stærke eller afgrænset Staleness, skal blive brugt flere enheder for at læse dokumenter.
- Indekserede egenskaber. En indeks-politik på hver samling bestemmer, hvilke egenskaber er indekseret som standard. Du kan reducere din anmodning om enhed forbrug ved at begrænse antallet af indekserede egenskaber. 
- Dokument indeksering. Som standard hvert dokument automatisk indekseret, vil du forbruge færre anmodning om enheder, hvis du vælger ikke at indeksere nogle af dine dokumenter.

Du kan finde yderligere oplysninger finder [DocumentDB anmodning enheder](documentdb-request-units.md). 

Her er for eksempel en tabel, der viser, hvor mange anmodning enheder at blive klargjort på tre forskellige dokumentstørrelser (1KB, 4KB og 64KB) og på to forskellige ydeevneniveauer (500 læser/andet + 100 skriver/andet og 500 læser/andet + 500 skriver/sekund). Data konsistens blev konfigureret på Session, og den indeksering politik er indstillet til ingen.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Dokumentstørrelse</strong></p></td>
            <td valign="top"><p><strong>Læser/sekund</strong></p></td>
            <td valign="top"><p><strong>Skriver/sekund</strong></p></td>
            <td valign="top"><p><strong>Anmode om enheder</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1) + (100* 5) = 1.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *5) + (100* 5) = 3.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1.3) + (100* 7) = 1,350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *1.3) + (500* 7) = 4,150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *10) + (100* 48) = 9,800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *10) + (500* 48) = 29,000 RU/s</p></td>
        </tr>
    </tbody>
</table>

Forespørgsler, lagrede procedurer og udløsere forbruge anmodning om enheder, der er baseret på de handlinger, der udføres kompleksitet. Undersøg overskriften anmodning gebyr for bedre at forstå, hvordan hver operation forbrug anmodning enhed kapacitet, som du udvikler dit program.  


## <a name="choice-of-consistency-level-and-throughput"></a>Valg af konsistens niveau og overførselshastighed
Valg af konsistens Standardniveau har indflydelse på overførselshastighed og ventetid. Du kan angive konsistens Standardniveau både fra et program og via Azure-portalen. Du kan også tilsidesætte niveauet konsistens på grundlag af hver anmodning. Niveauet konsistens er som standard angivet til **Session**, som indeholder monotone læst/skriver og læse din Skriv garantier. Session konsistens er velegnet til bruger centreret programmer og giver en perfekt balance af konsistens og ydeevne kompromiser.    

Finde en vejledning i at ændre dit konsistens niveau på Azure-portalen, se, [hvordan til at administrere en DocumentDB-konto](documentdb-manage-account.md#consistency). Eller se [Brug af konsistens niveauer](documentdb-consistency-levels.md)kan finde flere oplysninger om konsistens niveauer.

## <a name="provisioned-document-storage-and-index-overhead"></a>Klargjort dokument lager og indeks omkostninger
DocumentDB understøtter oprettelse af både enkelt partition og partitioneret af websteder. Hver partition i DocumentDB understøtter op til 10 GB lagerplads SSD sikkerhedskopier. 10GB for lagring af dokumenter indeholder dokumenter plus lagerplads til indekset. En DocumentDB af websteder er som standard konfigureret til at indeksere alle dokumenterne, der automatisk en sekundær indeks eller skema uden udtrykkelig. Baseret på programmer, der bruger DocumentDB, er typisk indeks omkostninger mellem 2-20%. Den indeksering teknologi, der bruges af DocumentDB sikrer, uanset værdierne af egenskaberne, indeks omkostninger ikke overstiger mere end 80% af størrelsen på dokumenter med standardindstillingerne. 

Som standard indekseres alle dokumenter af DocumentDB automatisk. Men hvis du vil finjustere indeks omkostninger, kan du vælge at fjerne bestemte dokumenter fra indekseres på tidspunktet for indsættelse af eller erstatte et dokument, som beskrevet i [DocumentDB indeksering politikker](documentdb-indexing-policies.md). Du kan konfigurere en DocumentDB af websteder for at udelade alle dokumenter i gruppen af websteder fra indekseres. Du kan også konfigurere en DocumentDB af websteder for at indeksere selektivt kun bestemte egenskaber eller netværksstier med jokertegn JSON dokumenter, som beskrevet i [konfigurere politikken indeksering af en samling](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). Udelukke egenskaber eller dokumenter også forbedrer overførselshastigheden Skriv – hvilket betyder, at du vil bruge færre anmodning enheder.   

## <a name="next-steps"></a>Næste trin

Hvis du vil fortsætte med at lære om, hvordan DocumentDB fungerer, skal du se [partitionering og skalering i Azure DocumentDB](documentdb-partition-data.md).

Du kan finde vejledning til overvågning af ydeevneniveauer på Azure-portalen, [overvåge en DocumentDB-konto](documentdb-monitor-accounts.md). Du kan finde flere oplysninger om at vælge ydeevneniveauer for samlinger, [ydeevneniveauer i DocumentDB](documentdb-performance-levels.md).
 
