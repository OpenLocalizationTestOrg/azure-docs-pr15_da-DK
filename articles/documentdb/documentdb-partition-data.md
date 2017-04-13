<properties 
    pageTitle="Partitionering og skalering i Azure DocumentDB | Microsoft Azure"      
    description="Få mere at vide om, hvordan leverandør fungerer i Azure DocumentDB, hvordan du konfigurerer partitionering og partition taster, og hvordan du skal vælge den rigtige Partitionsnøgle for dit program."         
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/> 

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="arramac"/> 

# <a name="partitioning-and-scaling-in-azure-documentdb"></a>Partitionering og skalering i Azure DocumentDB
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) er udviklet til at hjælpe dig med at opnå hurtigt, forudsigelige ydeevne og skalering problemfrit sammen med dit program, når den vokser. I denne artikel giver et overblik over hvordan leverandør fungerer i DocumentDB, og i denne artikel beskrives, hvordan du kan konfigurere DocumentDB af websteder for at tilpasse effektivt dine programmer.

Når du har læst i denne artikel, vil du kunne besvare spørgsmål, der er følgende:   

- Hvordan fungerer leverandør sammen i Azure DocumentDB?
- Hvordan kan jeg konfigurere partitionering i DocumentDB
- Hvad er partition taster, og hvordan jeg vælge tasten højre partition til mit program?

For at komme i gang med kode, kan du hente projektet fra [DocumentDB ydeevne test Driver eksempel](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

## <a name="partitioning-in-documentdb"></a>Partitionering i DocumentDB

I DocumentDB, kan du gemme og forespørge på skema mindre JSON dokumenter med rækkefølge af millisekunder svar gange på en hvilken som helst skala. DocumentDB indeholder beholdere til lagring af data, der kaldes **af websteder**. Samlinger er logiske ressourcer og kan strækker sig over en eller flere fysiske partitioner eller -servere. Antallet af partitioner bestemmes af DocumentDB baseret på lagerstørrelse og den klargjorte overførselshastighed i af websteder. Alle partitioner i DocumentDB har en fast mængde SSD-sikkerhedskopier lagerplads, der er knyttet til den, og replikeres til høj tilgængelighed. Administration af partition administreres fuldt af Azure DocumentDB, og du behøver ikke at skrive komplekse kode eller administrere partitionerne. DocumentDB af websteder er **næsten ubegrænset** med hensyn til lager og overførselshastighed. 

Partitionering er helt gennemsigtig i dit program. DocumentDB understøtter hurtig læser og skriver, SQL og LINQ forespørgsler, JavaScript baseret transaktions logik, konsistens niveauer og detaljerede adgangskontrol via REST-API opkald til en enkelt samling ressource. Tjenesten håndterer distribuere data på tværs af partitioner og routing forespørgsler til den rigtige partition. 

Hvordan fungerer det? Når du opretter en samling i DocumentDB, vil du bemærke, at der er en værdi for **partition vigtige egenskaben** konfiguration, som du kan angive. Dette er JSON egenskaben (eller stien) i dine dokumenter, der kan anvendes af DocumentDB til at distribuere dine data mellem flere forskellige servere eller partitioner. DocumentDB skal hash partition nøgleværdi og bruge hash'et resultatet til at bestemme den partition, hvor JSON-dokumentet skal gemmes. Alle dokumenter med den samme Partitionsnøgle gemmes i den samme partition. 

For eksempel bør du overveje et program, der lagrer data om medarbejdere og deres afdelinger i DocumentDB. Lad os vælge `"department"` som partition vigtige egenskab, hvis du vil skalere data ud efter afdeling. Alle dokumenter i DocumentDB skal indeholde et obligatorisk `"id"` egenskab, der skal være entydige for alle dokumenter med samme partition nøgleværdi, f.eks. `"Marketing`". Alle dokumenter, der er gemt i en samling skal have en entydig kombination af Partitionsnøgle og id, f.eks. `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }`, og `{ "Department": "Sales", "id": "0001" }`. Det vil sige, sammensat egenskaben for (partition skal id) er den primære nøgle til af websteder.

### <a name="partition-keys"></a>Partition taster
Valg af tasten partition er en vigtig beslutning, som du er nødt til at foretage i designfasen. Du skal vælge en JSON navn, som indeholder et bredt udvalg af værdier, og der sandsynligvis er jævnt fordelt access mønstre. Tasten partition er angivet som en JSON-sti, f.eks. `/department` repræsenterer afdelingen egenskab. 

Den følgende tabel vises eksempler på partition vigtige definitioner og de JSON-værdier, der svarer til hver.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Partition vigtige sti</strong></p></td>
            <td valign="top"><p><strong>Beskrivelse</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>Svarer til JSON værdien af doc.department, hvor dokumentet er dokumentet.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ Egenskaber/navn</p></td>
            <td valign="top"><p>Svarer til JSON værdien af doc.properties.name, hvor dokumentet er dokumentet (indlejrede egenskab).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>Svarer til JSON værdien af doc.id (-id og partition nøgle er den samme egenskab).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ "Afdelingsnavn"</p></td>
            <td valign="top"><p>Svarer til JSON værdien af dokument ["Afdelingsnavn"], hvor dokumentet er dokumentet.</p></td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] Syntaksen for partition vigtige sti ligner angivelsen af stien til indeksering politik stier med den vigtige forskel, at stien svarer til egenskaben i stedet for værdien, det vil sige der er ingen jokertegn i slutningen. For eksempel, du vil angive/afdeling /? Hvis du vil indeksere værdierne under afdeling, men angive /department som partition vigtige definitionen. Stien til partition nøglen indekseres implicit og kan ikke skal udelukkes fra indeksering ved hjælp af indeksering politik tilsidesætter.

Lad os se nærmere på hvordan valg af Partitionsnøgle påvirker ydeevnen for dit program.

### <a name="partitioning-and-provisioned-throughput"></a>Leverandør og klargjort overførselshastighed
DocumentDB er beregnet til mere forudsigelige ydeevne. Når du opretter en samling, kan du reservere overførselshastighed med hensyn til ** [anmodning om enheder](documentdb-request-units.md) (RU) sekundet**. Hver enkelt anmodning er tildelt en anmodning om enhedspris, der er proportionalt med mængden systemressourcer som CPU og EY consumed ved handlingen. En læsning af en 1 kB dokument med Session konsistens forbruger 1 anmodning enhed. En læsning er 1 RU uanset antallet af elementer, der er gemt eller antallet af samtidige anmodninger, der kører på samme måde. Større dokumenter kræver højere anmodning enheder afhængigt af størrelsen. Hvis du kender størrelsen på dine enheder og antallet af læser du har brug for at understøtte for dit program, kan du klargøre den nøjagtige mængde overførselshastighed, der kræves for dit program er læse behov. 

Når DocumentDB gemmer dokumenter, fordeler den dem jævnt mellem partitioner, der er baseret på den partition nøgleværdi. Overførselshastighed er også der er jævnt fordelt mellem de tilgængelige partitioner det vil sige overførselshastighed per partition = (samlet overførselshastighed per af websteder) / (antal partitioner). 

>[AZURE.NOTE] For at opnå den fulde overførsel af gruppen af websteder, skal du vælge en Partitionsnøgle, der gør det muligt at distribuere jævnt anmodninger mellem en række forskellige partitionsnøgleværdier.

## <a name="single-partition-and-partitioned-collections"></a>Enkelt Partition og partitioneret af websteder
DocumentDB understøtter oprettelse af både enkelt partition og partitioneret af websteder. 

- **Partitioned af websteder** kan strækker sig over flere partitioner og understøtter meget store mængder lager og overførselshastighed. Du skal angive en Partitionsnøgle til samlingen.
- **Enkelt partition af websteder** har lavere pris indstillinger og muligheden for at forespørge om og udføre transaktioner på tværs af alle dataene på skærmen af websteder. De har skalerbarhed og lagerplads grænser for en enkelt partition. Du behøver ikke at angive en Partitionsnøgle for disse samlinger. 

![Partitioneret af websteder i DocumentDB][2] 

Enkelt partition samlinger er for scenarier, der ikke skal bruge store mængder lagerplads eller overførselshastighed passer. Bemærk, at enkelt partition af websteder har skalerbarhed og lagergrænser for en enkelt partition, dvs. op til 10 GB lagerplads og op til 10.000 anmodning enheder sekundet. 

Partitioneret af websteder kan understøtte meget store mængder lager og overførselshastighed. Standard tilbud er dog konfigureret til at gemme op til 250 GB lagerplads og skalere op til 250.000 anmodning enheder sekundet. Hvis du har brug for højere lagerplads eller overførselshastighed per af websteder, skal du kontakte [Azure Support](documentdb-increase-limits.md) for at få disse øget for din konto.

I følgende tabel vises forskelle i arbejde med en enkelt partition og partitioneret af websteder:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Enkelt Partition af websteder</strong></p></td>
            <td valign="top"><p><strong>Partitioneret af websteder</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Partitionsnøgle</p></td>
            <td valign="top"><p>Ingen</p></td>
            <td valign="top"><p>Påkrævet</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Primær nøgle for dokument</p></td>
            <td valign="top"><p>"-id"</p></td>
            <td valign="top"><p>sammensatte nøgle &lt;Partitionsnøgle&gt; og "-id"</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Mindste lagerplads</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Maksimal Storage</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>Ubegrænset (250 GB som standard)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Mindste overførselshastighed</p></td>
            <td valign="top"><p>400 anmodning enheder sekundet</p></td>
            <td valign="top"><p>10.000 anmodning enheder sekundet</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Maksimale antal gennemløb</p></td>
            <td valign="top"><p>10.000 anmodning enheder sekundet</p></td>
            <td valign="top"><p>Ubegrænset (250.000 anmodning enheder sekundet som standard)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>API versioner</p></td>
            <td valign="top"><p>Alle</p></td>
            <td valign="top"><p>API 2015-12-16 og nyere</p></td>
        </tr>
    </tbody>
</table>

## <a name="working-with-the-sdks"></a>Arbejde med SDK'er

Azure DocumentDB tilføjet understøttelse af automatisk partitionering med [REST-API version 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx). For at oprette partitioneret af websteder, skal du hente SDK versioner 1.6.0 eller nyere på en af de understøttede platforme i SDK (.NET Node.js Java, og Python). 

### <a name="creating-partitioned-collections"></a>Oprette partitioneret af websteder

I følgende eksempel viser en .NET kodestykke til at oprette en samling for at gemme enhed telemetridata af 20.000 anmodning enheder sekundet af overførsel. SDK angiver værdien OfferThroughput (hvilket også angiver den `x-ms-offer-throughput` anmodning sidehoved i REST-API). Her vi angive den `/deviceId` som Partitionsnøgle. Valg af Partitionsnøgle gemmes sammen med resten af samling metadata som navn og indeksering politik.

Til dette eksempel, vi valgte `deviceId` da vi vide, at (a) fordi der er et stort antal enheder, skriver kan distribueres på tværs af partitioner jævnt og tillade os til at skalere databasen, så indtager store mængder data og (b) mange af anmodninger om som hentning af seneste læsning til en enhed er fastsat til en enkelt deviceId og kan hentes fra en enkelt partition.

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        

> [AZURE.NOTE] For at oprette partitioneret af websteder, skal du angive en overførselshastighed værdi på > 10.000 anmodning enheder sekundet. Da overførselshastighed er i multipla på 100, vil dette skal være 10,100 eller nyere.

Denne metode foretager et opkald til DocumentDB REST-API og tjenesten bliver Klargør et antal partitioner, der er baseret på den ønskede overførsel. Du kan ændre gennemløb af en samling, som din ydeevne behov udvikler. Du kan finde flere oplysninger i [Ydeevne](documentdb-performance-levels.md) .

### <a name="reading-and-writing-documents"></a>Læse og skrive dokumenter

Nu, Lad os indsætte data i DocumentDB. Her er et eksempel klasse, der indeholder en enhed, læse, og et opkald til CreateDocumentAsync til at indsætte en ny enhed læsning til en samling.

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


Lad os læse dokumentet ved at det er Partitionsnøgle og id, opdatere, og Slet den derefter som det sidste trin ved Partitionsnøgle og -id. Bemærk, at læser indsætte værdien PartitionKey (svarer til den `x-ms-documentdb-partitionkey` anmodning sidehoved i REST-API).

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });



### <a name="querying-partitioned-collections"></a>Forespørgsler partitioneret af websteder

Når du forespørger dataene i partitioneret samlinger, dirigerer DocumentDB automatisk forespørgslen til de partitioner, der svarer til de partitionsnøgleværdier, der er angivet i filteret (Hvis der ikke er nogen). For eksempel er denne forespørgsel distribueret til lige den partition, der indeholder tasten partition "XMS-0001".

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

Følgende forespørgsel har ikke et filter på tasten partition (DeviceId) og er fanned ud af, at alle partitioner, hvor det er køres i forhold til den partition indeks. Bemærk, at du skal angive EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` i REST-API) skal have SDK, til at udføre en forespørgsel på tværs af partitioner.

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### <a name="parallel-query-execution"></a>Udførelse af parallelle forespørgsler

DocumentDB SDK'er 1.9.0 og over parallelle forespørgsel udførelse af supportmuligheder, som gør det muligt at udfører partitioneret samlinger, lav ventetid forespørgsler, selvom de har brug at berøring et stort antal partitioner. For eksempel er følgende forespørgsel konfigureret til at køre parallelt på tværs af partitioner.

    // Cross-partition Order By Queries
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
        .OrderBy(m => m.MetricValue);

Du kan administrere udførelse af parallelle forespørgsler ved justering af følgende parametre:

- Ved at angive `MaxDegreeOfParallelism`, du kan styre graden af parallelitet det vil sige det maksimale antal samtidige netværksforbindelser til den samling partitioner. Hvis du angiver dette-1, graden af parallelitet administreres af SDK. Hvis den `MaxDegreeOfParallelism` ikke er angivet eller angivet til 0, som er standardværdien, der skal knyttes en enkelt netværksforbindelse til den samling partitioner.
- Ved at angive `MaxBufferedItemCount`, du kan handler fra forespørgsel ventetid og client side hukommelsesbrug. Hvis du udelader denne parameter eller angive dette-1, antallet af elementer i bufferen under udførelse af parallelle forespørgsler administreres af SDK.

Hvis du har de samme tilstanden for gruppen af websteder, returnerer en parallelle forespørgsel resultater i samme rækkefølge som seriel udførelse af. Når du udfører en forespørgsel i tværs partition, der omfatter sortering (ORDER BY og/eller TOP), DocumentDB SDK problemer forespørgslen parallelt på tværs af partitioner og fletter delvist sorteret resultaterne i klientsiden til at producere globalt bestilte resultater.

### <a name="executing-stored-procedures"></a>Udførelse af gemte procedurer

Du kan også udføre atomiske transaktioner mod dokumenter med det samme enheds-ID, f.eks. Hvis du vedligeholdelse af samlinger eller den seneste tilstand for en enhed i et enkelt dokument. 

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
        "XMS-001-FE24C");

I næste afsnit, kan vi se på hvordan du kan flytte til partitioneret af websteder fra enkelt partition af websteder.

<a name="migrating-from-single-partition"></a>
### <a name="migrating-from-single-partition-to-partitioned-collections"></a>Skifte fra enkelt partition til partitioneret af websteder
Når et program ved hjælp af en enkelt partition samling behov højere overførselshastighed (> 10.000 RU/s) eller større datalagring (> 10GB), du kan bruge [DocumentDB Data overførselsværktøjet til](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) at overføre data fra samlingen enkelt partition til en partitioneret af websteder. 

At overføre fra en enkelt partition af websteder til en partitioneret af websteder

1. Eksportere data fra samlingen enkelt partition til JSON. Se [eksportere til JSON-fil](documentdb-import-data.md#export-to-json-file) for at få yderligere oplysninger.
2. Importere dataene til en partitioneret samling, der er oprettet med en nøgle partition-definitionen og mere end 10.000 anmodning enheder per anden overførselshastighed, som vist i eksemplet nedenfor. Se [importere til DocumentDB](documentdb-import-data.md#DocumentDBSeqTarget) få mere at vide.

![Overføre Data til en Partitioned samling i DocumentDB][3]  

>[AZURE.TIP] Importér hurtigere, kan du overveje at øge antallet af parallelle anmodningerne til 100 eller nyere for at kunne udnytte den højere overførselshastighed, der er tilgængelige for partitioneret af websteder. 

Nu hvor vi har fuldført de grundlæggende funktioner, Lad os se på nogle vigtige Designovervejelser når du arbejder med partition taster i DocumentDB.

## <a name="designing-for-partitioning"></a>Designe for partitionering
Valg af tasten partition er en vigtig beslutning, som du er nødt til at foretage i designfasen. I dette afsnit beskrives nogle af kompromiserne involveret i at vælge en Partitionsnøgle til af websteder.

### <a name="partition-key-as-the-transaction-boundary"></a>Partition nøglen som posteringen rammen
Dit valg af Partitionsnøgle bør stemme for at aktivere brugen af transaktioner mod kravet om at distribuere dine enheder på tværs af flere partition taster til at sikre en SVG løsning. Du kan angive den samme Partitionsnøgle for alle dine dokumenter på et særligt, men dette kan begrænse skalerbarhed af din løsning. I andet grænsetilfælde, kan du tildele en entydig Partitionsnøgle for hvert dokument, som kan være meget SVG, men vil forhindre, at du ved hjælp af transaktioner i tværs dokument via lagrede procedurer og udløsere. En ideel Partitionsnøgle er et, der gør det muligt at bruge effektive forespørgsler, og som har tilstrækkelige kardinalitet til at sikre, at din løsning er SVG. 

### <a name="avoiding-storage-and-performance-bottlenecks"></a>At storage og ydeevne 
Det er også vigtigt til at vælge en egenskab, der gør det muligt for skrivning skal fordeles på tværs af et antal entydige værdier. Anmodninger om til den samme Partitionsnøgle må ikke overstige gennemløb af en enkelt partition og sænkes. Så er det vigtigt at vælge en Partitionsnøgle, der ikke resulterer i **"hotspots"** i dit program. Den samlede lagerstørrelse for dokumenter med den samme Partitionsnøgle kan også ikke overstige 10 GB i lagerplads. 

### <a name="examples-of-good-partition-keys"></a>Eksempler på gode partitionsnøgler
Her er nogle eksempler til, hvordan du skal vælge tasten partition for dit program:

* Hvis du implementere en bruger profil back-end, er bruger-ID et godt valg for Partitionsnøgle.
* Hvis du gemmer IoT data fx status for enheden, er et godt valg for Partitionsnøgle et enheds-ID.
* Hvis du bruger DocumentDB til logføring tidsserie data, er hostname eller proces ID er et godt valg for Partitionsnøgle.
* Hvis du har en flere arkitekturer, er lejer ID'ET et godt valg for Partitionsnøgle.

Bemærk, at ske brug (som de IoT og bruger profiler, der er beskrevet ovenfor), tasten partition kan være den samme som din-id (dokumentnøgle). Du kan have en Partitionsnøgle, der er anderledes end id i andre som serie tidsdata.

### <a name="partitioning-and-loggingtime-series-data"></a>Leverandør og logføring-tidsserie data
En af de mest almindelige use cases af DocumentDB er til logføring og telemetri. Det er vigtigt at vælge en god Partitionsnøgle, da det være nødvendigt at læse-og skriveadgang store datamængder. Valget, afhænger af din læse og skrive satser og typer af forespørgsler, du forventer at køre. Her er nogle tip til, hvordan du vælger en god Partitionsnøgle.

- Hvis din sag brug omfatter en lille rente skriver acculumating over en længere periode og forespørgsel ved områder af tidsstempler skal og andre filtre, derefter ved hjælp af en pakke bestående af tidsstemplet fx dato som en Partitionsnøgle er en god fremgangsmåde. Dette giver dig mulighed for forespørgsel over alle dataene til en dato fra en enkelt partition. 
- Hvis din arbejdsbyrde er skriveadgang aktiveret, som er generelt mere almindelige, skal du bruge en Partitionsnøgle, der ikke er baseret på tidsstempel så DocumentDB kan distribuere skriver jævnt på tværs af et antal partitioner. Her er et værtsnavn, proces-ID, aktivitet-ID eller en anden egenskab med høj kardinalitet et godt valg. 
- En tredje fremgangsmåde er en hybrid et sted, hvor du har flere samlinger, en for hver dag/måned og tasten partition er en detaljeret egenskab som hostname. Dette er den fordel, som du kan angive forskellige ydeevneniveauer, der er baseret på en tidsramme, fx samlingen for den aktuelle måned er klargjort med højere overførselshastighed da den fungerer læsning og skrivning, mens foregående måneder med lavere overførselshastighed, da de kun fungere læser.

### <a name="partitioning-and-multi-tenancy"></a>Partitionering og flere arkitekturer
Hvis du implementerer et med flere lejer program ved hjælp af DocumentDB, er der to overordnede mønstre for implementere arkitekturer med DocumentDB – én Partitionsnøgle per lejer, og én samling per lejer. Her er fordele og ulemper for hver:

* Én Partition tast per lejer: I denne tabel, er lejere samhusningsstedet inden for en enkelt samling. Men forespørgsler og indlæg til dokumenter i en enkelt lejer kan udføres med en enkelt partition. Du kan også implementere transaktions logik på tværs af alle dokumenter i en lejer. Da flere lejere dele en samling, kan du gemme lager og overførselshastighed omkostninger ved gruppering af ressourcer til lejere i en enkelt samling i stedet for klargøring ekstra kapacitet til hver lejer. Ulempen er, at du ikke har ydeevne isolationsniveauet per lejer. Ydeevnen/overførselshastighed øges, der gælder for hele samlingen vs målrettede øges for lejere.
* En samling per lejer: hver lejer har sin egen samling. I denne tabel, kan du reservere ydeevne per lejer. Standardformular Documentdbs nye forbrug baseret priser er denne model mere økonomisk for flere lejer programmer med et lille antal lejere.

Du kan også bruge en kombination/lagdelt fremgangsmåde, der collocates small lejere og overfører større lejere til deres egne af websteder.

## <a name="next-steps"></a>Næste trin
I denne artikel, har vi beskrives hvordan leverandør fungerer i Azure DocumentDB, hvordan du kan oprette partitioneret af websteder, og hvordan du kan vælge en god Partitionsnøgle for dit program. 

-   Udføre skala og performance-test med DocumentDB. Se [skala test med Azure DocumentDB af ydeevne og](documentdb-performance-testing.md) til en stikprøve.
-   Komme i gang kodningssprog med [SDK'er](documentdb-sdk-dotnet.md) eller [REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx)
-   Få mere at vide om [klargjort overførselshastighed i DocumentDB](documentdb-performance-levels.md)
-   Hvis du vil tilpasse, hvordan dit program udfører partitionering, kan du tilslutte din egen klientsiden leverandør implementering. Se [klientsiden partitionering support](documentdb-sharding.md).

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png  

 
