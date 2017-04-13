<properties 
    pageTitle="Oprette forbindelse Azure SQL-Database til Azure søgning ved hjælp af indeks | Microsoft Azure | Indeks" 
    description="Lær at hente data fra Azure SQL-Database til en Azure søgeindeks ved hjælp af indeks." 
    services="search" 
    documentationCenter="" 
    authors="chaosrealm" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="05/26/2016" 
    ms.author="eugenesh"/>

#<a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Oprette forbindelse Azure SQL-Database til Azure søgning ved hjælp af indeks

Azure søgetjenesten er en hostet skyen søgetjeneste, der gør det nemt at give en god søgeoplevelse. Før du kan søge, skal du udfylde en Azure søgeindekset med dine data. Hvis dataene findes i en Azure SQL-database, kan den nye **Azure Søg indekseringsprogram til Azure SQL-Database** (eller **Azure SQL indekseringsprogram** til kort) i Azure søgning automatisere indeksering processen. Det betyder, at du har mindre kode til at skrive og mindre infrastruktur til betyder noget for dig.

Indeks fungerer på nuværende tidspunkt kun med Azure SQL-Database, SQL Server på Azure FOS og [Azure DocumentDB](../documentdb/documentdb-search-indexer.md). I denne artikel fokuserer vi på indeks, der arbejder med Azure SQL-Database. Hvis du gerne vil se understøttelse af flere datakilder, skal du give din feedback på [Azure Søg feedback-forummet](https://feedback.azure.com/forums/263029-azure-search/).

I denne artikel beskrives funktionen af brug af indeks indstillingen, men vi kan også gå ned i funktioner og -funktionsmåder, som kun er tilgængelige med SQL-databaser (for eksempel integreret registrering af ændringer).

## <a name="indexers-and-data-sources"></a>Indeks og datakilder

Hvis du vil indstille og konfigurere et Azure SQL-indekseringsprogram, kan du ringe til [Azure Search REST-API](http://go.microsoft.com/fwlink/p/?LinkID=528173) for at oprette og administrere **indeks** og **datakilder**. 

Du kan også bruge [indekseringsprogram klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) i [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)eller guiden importdata i [Azure portal](https://portal.azure.com) til at oprette og planlægge et indekseringsprogram.

En **datakilde** angiver, hvilke data der skal indekseres, legitimationsoplysninger, der er behov for at få adgang til data og politikker, der aktiverer Azure søgningen til effektivt at identificere ændringer i data (nye, ændret eller slettet rækker). Det er defineret som en uafhængig ressource, så den kan bruges af flere indeks.

Et **indeks** er en ressource, der forbinder datakilder med target søgeindeks. Et indekseringsprogram bruges på følgende måder:
 
- Udføre en enkelt kopi af data til at udfylde et indeks.
- Opdatere et indeks med ændringer i datakilden på en tidsplan.
- Køre efter behov at opdatere et indeks, efter behov. 

## <a name="when-to-use-azure-sql-indexer"></a>Hvornår skal jeg bruge Azure SQL indekseringsprogram

Brug af SQL Azure indekseringsprogram kan afhængigt af flere faktorer, der vedrører dine data, eller muligvis ikke er passende. Hvis dine data opfylder følgende krav, kan du bruge Azure SQL indekseringsprogram: 

- Alle dataene kommer fra en enkelt tabel eller visning
    - Hvis dataene er spredt på tværs af flere tabeller, kan du oprette en visning og bruge den pågældende visning med indekseringstjenesten. Dog være opmærksom på, hvis du bruger en visning, du ikke kan bruge SQL Server integreret Skift registrering. Se dette afsnit for at få flere oplysninger. 
- De datatyper, der bruges i datakilden, der understøttes af indekseringstjenesten. De fleste, men ikke alle datatyper understøttes af SQL-Sætningen. Yderligere oplysninger finder du [tilknytning af datatyper i Azure søgning](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
- Du skal ikke nær realtid opdateringer til indekset, når en række ændres. 
    - Indekseringstjenesten kan indeksere tabellen højst hver 5 minutter. Hvis ændringerne data ofte og ændringerne, skal afspejles i indekset i sekunder eller minutter enkelt, anbefaler vi, at du bruger [Azure Søg indeks API](https://msdn.microsoft.com/library/azure/dn798930.aspx) direkte. 
- Hvis du har et stort datasæt og vil Kør indekseringsprogram på en tidsplan, dit skema gør det muligt for os til at identificere effektivt ændret (og slettet, hvis det er relevant) rækker. Få mere at vide under "Indsamle ændret og slettet rækker" nedenfor. 
- Størrelsen af de indekserede felter i en række overstiger ikke den maksimale størrelse på en Azure-søgning, indeksering anmodningen, som er 16 MB. 

## <a name="create-and-use-an-azure-sql-indexer"></a>Oprette og bruge et Azure SQL-indekseringsprogram

Opret først datakilden: 

    POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


Du kan få forbindelsesstrengen fra [Azure klassisk Portal](https://portal.azure.com). Brug den `ADO.NET connection string` indstilling.

Opret derefter target Azure søgeindekset, hvis du ikke allerede har et. Du kan gøre dette fra [portalen brugergrænseflade](https://portal.azure.com) eller ved hjælp af [Opret indeks API](https://msdn.microsoft.com/library/azure/dn798941.aspx).  Sikre, at skemaet for dine mål indeks er kompatible med skemaet i kildetabellen – se [tilknytning mellem datatyperne SQL og Azure Søg](#TypeMapping) få mere at vide.

Til sidst skal oprette indekseringsprogrammet ved at give den et navn og refererer til data kilde- og destinationswebsteder indeks:

    POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

Et indeks, der er oprettet på denne måde har ikke en tidsplan. Den kører automatisk en gang så snart den er oprettet. Du kan køre programmet igen når som helst ved hjælp af en **køre indekseringsprogram** anmodning:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
    api-key: admin-key

Du kan tilpasse flere aspekter af indekseringsprogram funktionsmåde, som batchstørrelse, og hvor mange dokumenter kan blive ignoreret, før en indekseringsprogram udførelse ikke bliver gennemført. Du kan finde flere detaljer, [Oprette indekseringsprogram API](https://msdn.microsoft.com/library/azure/dn946899.aspx).
 
Du skal muligvis tillade Azure services til at oprette forbindelse til databasen. Se [Oprette forbindelse fra Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) for vejledning om, hvordan du gør dette.

Hvis du vil overvåge indekseringsprogram status og udførelse af Brug oversigt (antallet af elementer, der er indekseret, fejl osv.), en anmodning om **indekseringsprogram status** : 

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
    api-key: admin-key

Svaret skal ligne følgende: 

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null 
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null 
            },
            ... earlier history items 
        ]
    }

Oversigt over udførelse af indeholder op til 50 af de seneste fuldførte udførelser, som er sorteret i omvendt kronologisk rækkefølge (så seneste udførelse af kommer først i svaret). Yderligere oplysninger om svaret kan findes i [Hent indekseringsprogram Status](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Køre indeks på en tidsplan

Du kan også arrangere indekseringsprogram til at køre med jævne mellemrum af en tidsplan. For at gøre dette skal du bare tilføje egenskaben **tidsplan** , når du opretter eller opdatere indekseringstjenesten. Eksemplet herunder viser en læg anmodning om at opdatere indekseringsprogrammet:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key 

    { 
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**Interval** parameter er påkrævet. Intervallet, der refererer til tiden mellem starten af to efterfølgende indekseringsprogram udførelser. Det mindste tilladte interval er 5 minutter. den længste er én dag. Det skal være formateret som en XSD "dayTimeDuration" værdi (et begrænset undersæt af en [ISO 8601 varighed](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) værdi). Mønsteret for dette er: `P(nD)(T(nH)(nM))`. Eksempler: `PT15M` for hver 15 minutter viser `PT2H` for alle Office-dokumenter.

Valgfri **Starttidspunkt** angiver, når de planlagte udførelser skal påbegyndes; Hvis den udelades, bruges det aktuelle klokkeslæt i UTC. Denne gang kan være tidligere – hvor sag første kørsel planlægges, som om indekseringsprogrammet har kørt løbende siden starttidspunkt.  

Kun én udførelse af en given indekseringsprogram kan køre ad gangen. Hvis et indekseringsprogram allerede køres, når det er meningen til at starte, udførelsen udelades, og CV'er på det næste interval, hvis ingen andre indekseringsprogram job kører.

Lad os se et eksempel for at gøre dette mere cement. Antag, at vi følgende hver time tidsplanen konfigureret: 

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Her er, hvad der sker: 

1. Den første indekseringsprogram udførelse af startes på eller omkring marts 1 2015 12:00 AM UTC.
1. Forudsætter denne udførelse af tager 20 minutter (eller helst mindre end 1 time).
1. Anden udførelse af startes på eller omkring marts 1 2015 1:00 AM 
1. Lad os antage, at denne udførelse af tager mere end en time (dette kræver et stort antal dokumenter til denne indstilling for at rent faktisk virkelighed, men det er en praktisk illustration) – for eksempel 70 minutter – så det afsluttes omkring 2:10 AM
1. Det er nu kl, tidspunktet for den tredje udførelse af at starte. Men fordi den anden udførelse af fra 1 AM er stadig kører, tredje udførelse af ignoreret. Den tredje udførelse af starter kl.

Du kan tilføje, ændre eller slette en tidsplan til et eksisterende indekseringsprogram ved hjælp af en **PLACERE indekseringsprogram** anmodning. 

## <a name="capturing-new-changed-and-deleted-rows"></a>Indsamle nye, ændret og slettede rækker

Hvis du bruger en tidsplan og din tabel indeholder en ikke-trivial antallet af rækker, skal du bruge data Skift registrering politik, så indekseringsprogrammet effektivt kan hentes kun de nye eller ændrede rækker uden at indeksere hele tabellen.

### <a name="sql-integrated-change-tracking-policy"></a>SQL integreret registrering af ændringer politik

Hvis din SQL-database understøtter [registrering af ændringer](https://msdn.microsoft.com/library/bb933875.aspx), anbefaler vi bruger **SQL integreret ændre sporingsmetode**. Denne politik aktiverer den mest effektive registrering af ændringer, og gør det også Azure Søg til at identificere slettede rækker uden at du vil tilføje en eksplicit "bløde Slet" kolonne til tabellen.

Integreret registrering understøttes i de følgende versioner af SQL Server-database:
 
- SQL Server 2008 R2 eller nyere, hvis du bruger SQL Server på Azure FOS. 
- Azure SQL-Database version 12, hvis du bruger Azure SQL-Database.

Når bruger SQL integreret registrering politikken, ikke angiver en separat registrering politik for sletning – denne politik har indbygget understøttelse af identificere slettede rækker.

Denne politik kan kun bruges med tabeller. Det kan ikke bruges med visninger. Du skal aktivere registrering af ændringer til den tabel, du bruger, før du kan bruge denne politik. Se [aktivere og deaktivere registrering af ændringer](https://msdn.microsoft.com/library/bb964713.aspx) til instruktionerne. 

Hvis du vil bruge denne politik, oprette eller opdatere din datakilde således:
 
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
      }
    }

### <a name="high-water-mark-change-detection-policy"></a>Høj vand Markér ændring registrering politik

Mens politikken SQL integreret ændringsregistrering anbefales, kan du ikke kan bruge den, hvis dine data er i en visning, eller hvis du bruger en ældre version af Azure SQL-database. Overvej at bruge politikken høj vand er markeret i så fald. Denne politik kan bruges, hvis din tabel indeholder en kolonne, der opfylder følgende kriterier:

- Alle indsætter angive en værdi for kolonnen. 
- Alle opdateringer til et element kan også ændre værdien af kolonnen. 
- Værdien af kolonnen øges med hver enkelt ændring.
- Forespørgsler, der bruger en `WHERE` delsætningen ligner `WHERE [High Water Mark Column] > [Current High Water Mark Value]` kan udføres effektivt.

For eksempel er en indekserede **med navnet rowversion** kolonne en perfekt kandidat for kolonnen høj vand er markeret. Hvis du vil bruge denne politik, oprette eller opdatere din datakilde således: 

    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
      }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Bløde Slet kolonne sletningen registrering politik

Når rækker er slettet fra kildetabellen, vil du sandsynligvis slette rækkerne fra søgeindekset samt. Hvis du bruger SQL integreret registrering politik, er dette taget sig af for dig. Dog hjælpe ikke høj vand Markér registrering politik dig med slettede rækker. Sådan gør du? 

Hvis rækkerne, der er fysisk fjernes fra tabellen, du forlader og lykke – der findes ingen måde at udlede tilstedeværelsen af poster, der ikke længere findes.  Du kan dog bruge "bløde Slet" metode til at markere rækker som logisk slettede uden at fjerne dem fra tabellen ved at tilføje en kolonne og markere rækker, som slettes ved hjælp af en markør værdi i den pågældende kolonne.

Når du bruger den bløde Slet teknik, kan du angive blødt slette politik på følgende måde, når du opretter eller opdaterer datakilden: 

    { 
        …, 
        "dataDeletionDetectionPolicy" : { 
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]", 
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
        }
    }

Bemærk, at **softDeleteMarkerValue** skal være en streng – Brug Strengrepræsentationen af din faktiske værdi. Hvis du har en heltalskolonne, hvor slettede rækker er markeret med værdien 1, f.eks `"1"`; Hvis du har en BIT kolonne, hvor slettede rækker er markeret med den sande booleske værdi, skal du bruge `"True"`. 

<a name="TypeMapping"></a>
## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Tilknytning mellem SQL-datatyper og Azure Søg-datatyper

|SQL-datatype | Tilladt target indeks felttyper |Noter 
|------|-----|----|
|bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| reelt tal, slæk |Edm.Double, Edm.String | |
| Access, penge decimal numerisk | Edm.String| Azure søgning understøtter ikke konvertere decimal typer til Edm.Double, fordi dette mister præcision |
| CHAR, nchar, varchar, nvarchar | Edm.String<br/>Collection(EDM.String)|Transformere en strengkolonne til Collection(Edm.String) kræver brug af et eksempel API version 2015-02-28-Preview. I [denne artikel](search-api-indexers-2015-02-28-preview.md#CreateIndexer) finder du oplysninger| 
|smalldatetime datetime datetime2, dato, og datetimeoffset |Edm.DateTimeOffset, Edm.String| |
|uniqueidentifer | Edm.String | |
|Geografi | Edm.GeographyPoint | Understøttes kun Geografi forekomster af typen punkt med SRID 4326 (som er standard) | | 
|med navnet ROWVERSION| I/T. |Række-version kolonner kan ikke gemmes i søgeindekset, men de kan bruges til registrering af ændringer | |
| tid, timespan, binær, varbinary, billede, xml, geometri, CLR-datatyper | I/T. |Ikke understøttet |


## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

**Q:** Kan jeg bruge Azure SQL indekseringsprogram med SQL-databaser, der kører på IaaS FOS i Azure?

SV: Ja. Skal du tillade din søgetjenesten til at oprette forbindelse til databasen ved at gøre følgende to ting. Skal du se artiklen [konfigurere en forbindelse fra et Azure Søg indekseringsprogram til SQL Server på en Azure VM](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) kan finde flere oplysninger.

1. Du skal muligvis konfigurere din database med et certifikat, der er tillid til, så search-tjenesten kan åbne SSL-forbindelser til databasen.
2. Konfigurere firewallen til at tillade adgang til din søgetjenesten IP-adresse.

**Q:** Kan jeg bruge Azure SQL indekseringsprogram med SQL-databaser, der kører i det lokale miljø? 

SV: vi ikke anbefaler eller understøtter det, som dette kræver dig om at åbne dine databaser til internettrafik. 

**Q:** Kan jeg bruge Azure SQL indekseringsprogram med databaser end SQL Server kører i IaaS på Azure? 

SV: Vi understøtter ikke dette scenario, fordi vi ikke har testet indekseringsprogram med nogen databaser end SQL Server.  

**Q:** Kan jeg oprette flere indeks, der kører på en tidsplan? 

SV: Ja. Men kun én indekseringsprogram kan køre på en node ad gangen. Hvis du har brug for flere indeks, der kører samtidig, kan du overveje at skalering op search-tjenesten til mere end én Søg enhed. 

**Q:** Køre et indekseringsprogram påvirker arbejdsbelastningen min forespørgsel? 

SV: Ja. Indekseringsprogram kører på en af noderne i search-tjenesten, og denne node ressourcer deles mellem indeksering og fungerer forespørgsel trafik og andre API anmodninger. Hvis du kører intensivt indeksering og forespørgsel arbejdsmængder og støder på en høj sats med 503 fejl eller stigende svar klokkeslæt, kan du overveje at skalering op search-tjenesten.
