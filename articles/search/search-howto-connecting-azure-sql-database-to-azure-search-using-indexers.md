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
    ms.date="10/27/2016" 
    ms.author="eugenesh"/>

#<a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Oprette forbindelse Azure SQL-Database til Azure søgning ved hjælp af indeks

Azure søgetjenesten er en hostet skyen søgetjeneste, der gør det nemt at give en god søgeoplevelse. Før du kan søge, skal du udfylde en Azure søgeindekset med dine data. Hvis dataene findes i en Azure SQL-database, kan den nye **Azure Søg indekseringsprogram til Azure SQL-Database** (eller **Azure SQL indekseringsprogram** til kort) automatisere indeksering processen. Det betyder, at du har mindre kode til at skrive og mindre infrastruktur til betyder noget for dig.

I denne artikel beskrives funktionen af brug af indeks indstillingen, men det også beskrives de funktioner, der er kun tilgængelige med Azure SQL-databaser (for eksempel integreret registrering af ændringer). Azure søgning understøtter også andre datakilder, som DocumentDB Azure blob-lager og tabellagring. Hvis du gerne vil se understøttelse af flere datakilder, give din feedback på [Azure Søg feedback-forummet](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indeks og datakilder

Du kan oprette og konfigurere en Azure SQL-indekseringsprogram ved hjælp af: 

- Guiden Import Data i [Azure-portalen](https://portal.azure.com)
- Azure Søg [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)
- Azure Search [REST-API](http://go.microsoft.com/fwlink/p/?LinkID=528173)

I denne artikel bruger vi REST-API til viser, hvordan du oprette og administrere **indeks** og **datakilder**. 

En **datakilde** angiver, hvilke data der skal indekseres, legitimationsoplysninger, der er behov for at få adgang til data og politikker, der identificerer effektivt ændringer i data (nye, ændret eller slettet rækker). Det er defineret som en uafhængig ressource, så den kan bruges af flere indeks.

Et **indeks** er en ressource, der forbinder datakilder med target søgeindeks. Et indekseringsprogram bruges på følgende måder:
 
- Udføre en enkelt kopi af data til at udfylde et indeks.
- Opdatere et indeks med ændringer i datakilden på en tidsplan.
- Køre efter behov at opdatere et indeks, efter behov. 

## <a name="when-to-use-azure-sql-indexer"></a>Hvornår skal jeg bruge Azure SQL indekseringsprogram

Brug af SQL Azure indekseringsprogram kan afhængigt af flere faktorer, der vedrører dine data, eller muligvis ikke er passende. Hvis dine data opfylder følgende krav, kan du bruge Azure SQL indekseringsprogram: 

- Alle dataene kommer fra en enkelt tabel eller visning
    - Hvis dataene er spredt på tværs af flere tabeller, kan du oprette en visning og bruge den pågældende visning med indekseringstjenesten. Men hvis du bruger en visning, du ikke kunne bruge SQL Server integreret ændring registrering. Du kan finde yderligere oplysninger finder [i dette afsnit](#CaptureChangedRows). 
- De datatyper, der bruges i datakilden, der understøttes af indekseringstjenesten. De fleste, men ikke alle SQL-datatyper understøttes. Yderligere oplysninger finder du [tilknytning af datatyper i Azure søgning](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
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

Opret derefter target Azure søgeindekset, hvis du ikke allerede har et. Du kan oprette et indeks ved hjælp af [portalen brugergrænseflade](https://portal.azure.com) eller [Oprette indekset API](https://msdn.microsoft.com/library/azure/dn798941.aspx). Sikre, at skemaet for dine mål indeks er kompatible med skemaet i kildetabellen – se [tilknytning mellem datatyperne SQL og Azure søgning](#TypeMapping).

Til sidst skal oprette indekseringsprogrammet ved at give den et navn og refererer til data kilde- og destinationswebsteder indeks:

    POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

Et indeks, der er oprettet på denne måde har ikke en tidsplan. Der køres automatisk, når når den er oprettet. Du kan køre programmet igen når som helst ved hjælp af en **køre indekseringsprogram** anmodning:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
    api-key: admin-key

Du kan tilpasse flere aspekter af indekseringsprogram funktionsmåde, som batchstørrelse, og hvor mange dokumenter kan blive ignoreret, før en indekseringsprogram udførelse mislykkes. Se [Oprette indekseringsprogram API](https://msdn.microsoft.com/library/azure/dn946899.aspx)kan finde flere oplysninger.
 
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

Du kan også arrangere indekseringsprogram til at køre med jævne mellemrum af en tidsplan. Tilføje egenskaben **tidsplan** , når du opretter eller opdatere indekseringsprogrammet for at gøre dette. Eksemplet herunder viser en læg anmodning om at opdatere indekseringsprogrammet:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key 

    { 
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**Interval** parameter er påkrævet. Intervallet, der refererer til tiden mellem starten af to efterfølgende indekseringsprogram udførelser. Det mindste tilladte interval er 5 minutter. den længste er én dag. Det skal være formateret som en XSD "dayTimeDuration" værdi (et begrænset undersæt af en [ISO 8601 varighed](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) værdi). Mønsteret for dette er: `P(nD)(T(nH)(nM))`. Eksempler: `PT15M` for hver 15 minutter viser `PT2H` for alle Office-dokumenter.

Valgfri **Starttidspunkt** angiver, når de planlagte udførelser skal begynde. Hvis den udelades, bruges det aktuelle klokkeslæt i UTC. Denne gang kan være tidligere – som første udførelse af store og små bogstaver er planlagt ud som om indekseringsprogrammet har kørt løbende siden starttidspunkt.  

Kun én udførelse af et indekseringsprogram kan køre ad gangen. Hvis et indekseringsprogram kører, når dens udførelse af planlægges, udskudt udførelsen, indtil næste planlagte tid.

Lad os se et eksempel for at gøre dette mere cement. Antag, at vi følgende hver time tidsplanen konfigureret: 

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Her er, hvad der sker: 

1. Den første indekseringsprogram udførelse af startes på eller omkring marts 1 2015 12:00 AM UTC.
1. Forudsætter denne udførelse af tager 20 minutter (eller helst mindre end 1 time).
1. Anden udførelse af startes på eller omkring marts 1 2015 1:00 AM 
1. Lad os antage, at denne udførelse af tager mere end en time – for eksempel 70 minutter –, så det afsluttes omkring 2:10 AM
1. Det er nu kl, tidspunktet for den tredje udførelse af at starte. Men fordi den anden udførelse af fra 1 AM er stadig kører, tredje udførelse af ignoreret. Den tredje udførelse af starter kl.

Du kan tilføje, ændre eller slette en tidsplan til et eksisterende indekseringsprogram ved hjælp af en **PLACERE indekseringsprogram** anmodning. 

<a name="CaptureChangedRows">, /a >
## <a name="capturing-new-changed-and-deleted-rows"></a>Indsamle nye, ændres, og slettede rækker

Hvis tabellen har mange rækker, skal du bruge en politik for registrering af ændringer. Ændre registrering gør det muligt for en effektiv hentning af kun de nye eller ændrede rækker uden at indeksere hele tabellen.

### <a name="sql-integrated-change-tracking-policy"></a>SQL integreret registrering af ændringer politik

Hvis din SQL-database understøtter [registrering af ændringer](https://msdn.microsoft.com/library/bb933875.aspx), anbefaler vi bruger **SQL integreret ændre sporingsmetode**. Dette er den mest effektive politik. Det kan desuden Azure Søg til at identificere slettede rækker uden at du vil tilføje en eksplicit "bløde Slet" kolonne til tabellen.

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

<a name="HighWaterMarkPolicy"></a>
### <a name="high-water-mark-change-detection-policy"></a>Høj vand Markér ændring registrering politik

Mens politikken SQL integreret ændringsregistrering anbefales, kan det kun bruges med tabeller, ikke visninger. Hvis du bruger en visning, skal du overveje at bruge politikken høj vand er markeret. Denne politik kan bruges, hvis din tabel eller visning indeholder en kolonne, der opfylder følgende kriterier:

- Alle indsætter angive en værdi for kolonnen. 
- Alle opdateringer til et element kan også ændre værdien af kolonnen. 
- Værdien af kolonnen øges med hver enkelt ændring.
- Forespørgsler med følgende hvor og ORDER BY-udtryk kan udføres effektivt: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`.

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

> [AZURE.WARNING] Hvis tabellen ikke har et indeks på kolonnen høj vand er markeret, kan opstå timeout for forespørgsler, der bruges af SQL-indekseringsprogram. Især på `ORDER BY [High Water Mark Column]` -klausul kræver et indeks til at køre effektivt, når tabellen indeholder mange rækker. 

Hvis du støder på timeoutfejl, kan du bruge den `queryTimeout` indekseringsprogram denne indstilling til at angive timeout for forespørgsel til en værdi, der er højere end den standard 5 minutters timeout. Oprette for eksempel for at angive timeouten til 10 minutter, eller opdatere indekseringsprogrammet med følgende konfiguration: 

    {
      ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Du kan også deaktivere den `ORDER BY [High Water Mark Column]` delsætningen. Dette er ikke anbefales, da Hvis indekseringsprogram udførelsen afbrydes ved en fejl, indekseringstjenesten har igen behandles alle rækker Hvis der køres senere –, selvom indekseringsprogrammet er allerede behandlet næsten alle rækkerne på det tidspunkt, den blev afbrudt. Sådan deaktiverer du den `ORDER BY` -delsætning, Brug den `disableOrderByHighWaterMarkColumn` indstilling i indekseringsprogram definitionen:  

    {
     ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Bløde Slet kolonne sletningen registrering politik

Når rækker er slettet fra kildetabellen, vil du sandsynligvis slette rækkerne fra søgeindekset samt. Hvis du bruger SQL integreret registrering politik, er dette taget sig af for dig. Dog hjælpe ikke høj vand Markér registrering politik dig med slettede rækker. Sådan gør du? 

Hvis rækkerne, der er fysisk fjernes fra tabellen, har Azure Søg ikke nogen mulighed for at udlede tilstedeværelsen af poster, der ikke længere findes.  Du kan dog bruge "bløde Slet" metode til at slette logisk rækker uden at fjerne dem fra tabellen. Føje en kolonne til din tabel eller Vis og markere rækker, som slettes ved hjælp af kolonnen.

Når du bruger den bløde Slet teknik, kan du angive blødt slette politik på følgende måde, når du opretter eller opdaterer datakilden: 

    { 
        …, 
        "dataDeletionDetectionPolicy" : { 
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]", 
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
        }
    }

**SoftDeleteMarkerValue** skal være en streng – Brug Strengrepræsentationen af din faktiske værdi. Hvis du har en heltalskolonne, hvor slettede rækker er markeret med værdien 1, f.eks `"1"`. Hvis du har en BIT kolonne, hvor slettede rækker er markeret med den sande booleske værdi, skal du bruge `"True"`. 

<a name="TypeMapping"></a>
## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Tilknytning mellem SQL-datatyper og Azure Søg-datatyper

|SQL-datatype | Tilladt target indeks felttyper |Noter 
|------|-----|----|
|bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| reelt tal, slæk |Edm.Double, Edm.String | |
| Access, penge decimal numerisk | Edm.String| Azure søgning understøtter ikke konvertere decimal typer til Edm.Double, fordi dette mister præcision |
| CHAR, nchar, varchar, nvarchar | Edm.String<br/>Collection(EDM.String)|En SQL-streng kan bruges til at udfylde et Collection(Edm.String) felt, hvis strengen, der repræsenterer en JSON matrix af strenge:`["red", "white", "blue"]` | 
|smalldatetime datetime datetime2, dato, og datetimeoffset |Edm.DateTimeOffset, Edm.String| |
|uniqueidentifer | Edm.String | |
|Geografi | Edm.GeographyPoint | Understøttes kun Geografi forekomster af typen punkt med SRID 4326 (som er standard) | | 
|med navnet ROWVERSION| I/T. |Række-version kolonner kan ikke gemmes i søgeindekset, men de kan bruges til registrering af ændringer | |
| tid, timespan, binær, varbinary, billede, xml, geometri, CLR-datatyper | I/T. |Ikke understøttet |

## <a name="configuration-settings"></a>Indstillinger for søgekonfiguration

SQL indekseringsprogram Fremviser flere konfigurationsindstillinger: 

|Indstilling |Datatype | Formål | Standardværdi |
|--------|----------|---------|---------------|
| queryTimeout | streng | Angiver timeout for udførelse af SQL-forespørgsel | 5 minutter ("00: 05:00") |
| disableOrderByHighWaterMarkColumn | Boolesk | Bevirker, at den SQL-forespørgsel, der bruges af politikken høj vand er markeret til udelader delsætningen ORDER BY. Se [politik for høj vand er markeret](#HighWaterMarkPolicy) | FALSK | 

Disse indstillinger anvendes i den `parameters.configuration` objekt i indekseringsprogram definitionen. Oprette for eksempel for at angive timeout for forespørgsel til 10 minutter, eller opdatere indekseringsprogrammet med følgende konfiguration: 

    {
      ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

**Q:** Kan jeg bruge Azure SQL indekseringsprogram med SQL-databaser, der kører på IaaS FOS i Azure?

SV: Ja. Skal du tillade din søgetjenesten til at oprette forbindelse til databasen. Se [konfigurere en forbindelse fra et Azure Søg indekseringsprogram til SQL Server på en Azure VM](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)kan finde flere oplysninger.


**Q:** Kan jeg bruge Azure SQL indekseringsprogram med SQL-databaser, der kører i det lokale miljø? 

SV: vi ikke anbefaler eller understøtter det, som dette kræver dig om at åbne dine databaser til internettrafik. 

**Q:** Kan jeg bruge Azure SQL indekseringsprogram med databaser end SQL Server kører i IaaS på Azure? 

SV: Vi understøtter ikke dette scenario, fordi vi ikke har testet indekseringsprogram med nogen databaser end SQL Server.  

**Q:** Kan jeg oprette flere indeks, der kører på en tidsplan? 

SV: Ja. Men kun én indekseringsprogram kan køre på en node ad gangen. Hvis du har brug for flere indeks, der kører samtidig, kan du overveje at skalering op search-tjenesten til mere end én Søg enhed. 

**Q:** Køre et indekseringsprogram påvirker arbejdsbelastningen min forespørgsel? 

SV: Ja. Indekseringsprogram kører på en af noderne i search-tjenesten, og denne node ressourcer deles mellem indeksering og fungerer forespørgsel trafik og andre API anmodninger. Hvis du kører intensivt indeksering og forespørgsel arbejdsmængder og støder på en høj sats med 503 fejl eller stigende svar klokkeslæt, kan du overveje at skalering op search-tjenesten.
