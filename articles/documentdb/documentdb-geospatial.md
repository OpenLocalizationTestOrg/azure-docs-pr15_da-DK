<properties 
    pageTitle="Arbejde med geospatiale data i Azure DocumentDB | Microsoft Azure" 
    description="Forstå, hvordan du opretter, indeks og forespørgsel geografiske objekter med Azure DocumentDB." 
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/25/2016" 
    ms.author="arramac"/>
    
# <a name="working-with-geospatial-data-in-azure-documentdb"></a>Arbejde med geospatiale data i Azure DocumentDB

I denne artikel er en introduktion til funktionerne geospatiale i [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). Når du har læst, vil du kunne besvare spørgsmål, der er følgende:

- Hvordan gemmer jeg geografiske data i Azure DocumentDB?
- Hvordan kan jeg forespørge geospatiale data i Azure DocumentDB i SQL og LINQ?
- Hvordan aktiverer eller deaktiverer geografiske indeksering i DocumentDB?

Se denne [Github project](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) til kodeeksempler.

## <a name="introduction-to-spatial-data"></a>Introduktion til geografiske data

Geografiske data beskrives placering og udformningen af objekter i område. Dette svarer til objekter på earth, det vil sige geospatiale data i de fleste programmer. Geografiske data kan bruges til at repræsentere placeringen af en person, et interessant sted eller i rammen på en by eller en sø. Almindelige brug tilfælde involverer ofte afstand forespørgsler til f.eks., "find alle kaffe butikker i nærheden af min nuværende placering". 

### <a name="geojson"></a>GeoJSON
DocumentDB understøtter indeksering og forespørgsler med geospatiale punkt data, der er repræsenteret ved hjælp af [GeoJSON specifikation](http://geojson.org/geojson-spec.html). GeoJSON datastrukturer er altid gyldig JSON-objekter, så de kan være gemt og forespørges ved hjælp af DocumentDB uden nogen specielle funktioner eller biblioteker. DocumentDB SDK'er giver Hjælpeklasser og metoder, der gør det nemt at arbejde med geografiske data. 

### <a name="points-linestrings-and-polygons"></a>Punkter, linestrings og polygoner
Et **punkt** angiver en enkelt placering i område. I geospatiale data repræsenterer et punkt den nøjagtige placering, som kan være en adresse i en butik, en kiosk, en bil eller en by.  Et punkt er repræsenteret i GeoJSON (og DocumentDB) ved hjælp af dens koordinere par eller breddegrader og længde. Her er et eksempel JSON til et punkt.

**Punkter i DocumentDB**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE] Specifikationen GeoJSON angiver breddegrader for- og længde anden. Som i andre tilknytningsprogrammer breddegrader og længde er vinkler og er repræsenteret med hensyn til grader. Breddegrader værdier er målt fra Prime vestlig og er mellem-180 og 180.0 grader og længde værdier er målt fra ækvator og er mellem-90.0 og 90.0 grader. 
>
> DocumentDB fortolker koordinater, som repræsenteret per WGS 84 referencesystemet. Skal du se under få mere at vide om koordinere referencesystemer.

Det kan være integreret i et DocumentDB dokument, som vist i dette eksempel på en brugerprofil, der indeholder placeringsdata:

**Brug profil med placering, der er gemt i DocumentDB**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

Ud over punkter understøtter GeoJSON også LineStrings og polygoner. **LineStrings** repræsenterer en række to eller flere punkter i plads og linjesegmenterne, der forbinder dem. Linestrings bruges i geospatiale data ofte til at repræsentere hovedveje eller floder. En **polygon** er kanten af forbundne punkter, der danner en lukket LineString. Polygoner bruges ofte til at repræsentere fysiske lag som søer eller politiske regioner som byer og tilstand. Her er et eksempel på en polygon i DocumentDB. 

**Polygoner i DocumentDB**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE] Specifikationen GeoJSON kræver, at for gyldige polygoner sidste koordinere parret leveres skal være den samme som først til at oprette en lukket figur.
>
>Punkter i en polygon skal angives i mod uret rækkefølge. En polygon, der er angivet i med uret ordre repræsenterer den inverse fordelingsfunktion for området i modellen.

Ud over punkt, LineString og Polygon angiver GeoJSON også repræsentation til hvordan til at gruppere flere geospatiale placeringer, samt hvordan du kan knytte vilkårlig egenskaber til geolokalitet som en **funktion**. Fordi disse objekter er gyldige JSON, kan de alle gemt og behandles i DocumentDB. Men DocumentDB understøtter kun automatisk indeksering af punkter.

### <a name="coordinate-reference-systems"></a>Koordinere referencesystemer

Da earth form er uregelmæssige, repræsenteres koordinaterne for geospatiale data i mange koordinere referencesystemer (CRS), hver med deres egen rammer for reference og måleenheder. Den "nationale gitter af Storbritannien" er for eksempel en referencesystem er meget nøjagtigt for Storbritannien, men ikke uden for den. 

De mest populære CRS i brug er i dag verden geodætisk System [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). GPS-enheder og mange tilknytning-tjenesterne, herunder Google kort og Bing Maps API'er bruger WGS 84. DocumentDB understøtter indeksering og forespørgsler i geospatiale data ved hjælp af det WGS 84 CRS. 

## <a name="creating-documents-with-spatial-data"></a>Oprette dokumenter med geografiske data
Når du opretter dokumenter, der indeholder GeoJSON værdier, er de automatisk indekseret med et geografiske indeks i henhold til politikken indeksering af gruppen af websteder. Hvis du arbejder med en DocumentDB SDK i et dynamisk indtastede sprog som Python eller Node.js, skal du oprette gyldige GeoJSON.

**Oprette dokument med geospatiale data i Node.js**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
        // additional code within the callback
    });

Hvis du arbejder med .NET (eller Java) SDK'er, kan du bruge de nye sted og Polygon klasser i Microsoft.Azure.Documents.Spatial navneområdet integrere placeringsoplysninger i dit programobjekter. Disse klasser hjælper med at forenkle serialisering og fjernelse af serienummeret for geografiske data i GeoJSON.

**Oprette dokument med geospatiale data i .NET**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

Hvis du ikke har bredde- og længdegrader oplysningerne, men har fysiske adresser eller placeringens navn som by eller land, kan du slå faktisk koordinaterne ved hjælp af en geokodning tjeneste som Bing Maps RESTEN tjenester. Lær mere om Bing Maps geokodning [her](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Forespørgsler geografiske typer

Nu hvor vi har truffet nærmere på, hvordan du indsætter geospatiale data, Lad os se nærmere på forespørgsler på disse data ved hjælp af DocumentDB ved hjælp af SQL og LINQ.

### <a name="spatial-sql-built-in-functions"></a>Geografiske SQL indbyggede funktioner
DocumentDB understøtter følgende Åbn geospatiale Consortium (OGC) indbyggede funktioner til geospatiale forespørgsler. Se [Forespørgsel DocumentDB](documentdb-sql-query.md)kan finde flere oplysninger om det komplette sæt af indbyggede funktioner i SQL-sproget.

<table>
<tr>
  <td><strong>Brugen</strong></td>
  <td><strong>Beskrivelse</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Returnerer afstanden mellem de to GeoJSON punkt udtryk.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Returnerer en boolesk udtryk, der angiver, om det GeoJSON punkt, der er angivet i det første argument er i GeoJSON polygon i det andet argument.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Returnerer en boolesk værdi, der angiver, om det angivne GeoJSON punkt eller polygon udtryk er gyldig.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Returnerer en JSON-værdi, der indeholder en boolesk værdi, hvis det angivne GeoJSON punkt eller polygon udtryk er gyldig, og hvis det er ugyldige, desuden årsagen til som en strengværdi.</td>
</tr>
</table>

Geografiske funktioner kan bruges til at udføre afstand forespørgsler geografiske data. Her er for eksempel en forespørgsel, der returnerer alle family dokumenter, der er inden for 30 km af den angivne placering ved hjælp af funktionen ST_DISTANCE indbyggede. 

**Forespørgsel**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultater**

    [{
      "id": "WakefieldFamily"
    }]

Hvis du medtager geografiske indeksering i din indeksering politik, vil derefter "afstanden forespørgsler" være served effektivt gennem indekset. Du kan finde flere oplysninger om geografiske indeksering, skal du se afsnittet nedenfor. Hvis du ikke har et geografiske indeks til de angivne stier, kan du stadig udføre geografiske forespørgsler ved at angive `x-ms-documentdb-query-enable-scan` anmodning sidehoved med værdi er angivet til "true". I .NET, kan dette gøres ved at overføre det valgfrie argument **FeedOptions** til forespørgsler med [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) er indstillet til sand. 

ST_WITHIN kan bruges til at kontrollere, hvis et punkt er placeret i en polygon. Ofte bruges polygoner til at repræsentere grænser som postnumre, tilstand grænser eller neutralt lag. Igen Hvis du medtager geografiske indeksering i din indeksering politik, derefter "i" forespørgsler vil være served effektivt gennem indekset. 

Polygon argumenter i ST_WITHIN kan indeholde kun en enkelt ringetone, det vil sige polygoner, må ikke indeholde huller i dem. 

**Forespørgsel**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultater**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Svarer til hvordan uoverensstemmelse mellem datatyper fungerer i DocumentDB forespørgsel, hvis værdien placering er angivet i enten argument er forkert udformet eller ugyldige, og den vil evaluere **Udefineret** og evalueret dokumentet skal udelades fra forespørgselsresultaterne. Hvis forespørgslen ikke returnerer nogen resultater, du Kør ST_ISVALIDDETAILED til fejlfinding hvorfor typen spatail er ugyldige.     

DocumentDB understøtter også udføre inverse forespørgsler, dvs. Du kan indeksere polygoner og linjer i DocumentDB og derefter forespørge efter de områder, der indeholder et bestemt punkt. Dette mønster bruges normalt i logistik til at identificere, f.eks. Når en maskine placeres i eller forlader et dedikeret område. 

**Forespørgsel**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Resultater**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID og ST_ISVALIDDETAILED kan bruges til at kontrollere, om et geografiske objekt er gyldig. For eksempel kontrollerer følgende forespørgsel gyldighed et punkt med et fraværende-område længde værdi (-132.8). ST_ISVALID returnerer en boolesk værdi, og ST_ISVALIDDETAILED returnerer booleske og en streng, der indeholder årsagen hvorfor det betragtes som ugyldig.

**Forespørgsel**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultater**

    [{
      "$1": false
    }]

Disse funktioner kan også bruges til at validere polygoner. For eksempel bruge her vi ST_ISVALIDDETAILED til at validere en polygon, der ikke er lukket. 

**Forespørgsel**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultater**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
### <a name="linq-querying-in-the-net-sdk"></a>LINQ forespørgsler i .NET SDK

DocumentDB .NET SDK også udbydere stub metoder `Distance()` og `Within()` til brug i LINQ udtryk. Provideren DocumentDB LINQ oversætter disse metode opkald til de tilsvarende indbyggede SQL-funktionskald (ST_DISTANCE og ST_WITHIN henholdsvis). 

Her er et eksempel på en LINQ forespørgsel, der søger efter alle dokumenter i samlingen DocumentDB Hvis "placering" værdi er inden for en radius af 30km til det angivne Peg ved hjælp af LINQ.

**LINQ forespørgsel til afstand**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

På samme måde, er her en forespørgsel til at finde alle de dokumenter, hvis "placering" er i den angivne felt/polygon. 

**LINQ forespørge efter i**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Nu hvor vi har truffet se, hvordan forespørge dokumenter ved hjælp af LINQ og SQL, Lad os se, hvordan du konfigurerer DocumentDB til geografiske indeksering.

## <a name="indexing"></a>Indeksering

Som beskrevet i [Skemaet Agnostic indeksering med Azure DocumentDB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) papiret, vi udviklet Documentdbs databaseprogram er helt igennem skema agnostic og førsteklasses understøtter JSON. Skriv optimeret databaseprogram af DocumentDB forstår oprindeligt geografiske data (punkter, polygoner og linjer), der repræsenteres i GeoJSON-standarden.

I en nøddeskal er geometrien planlagt fra geodætisk koordinater til et 2D plan derefter gradvist er inddelt i celler ved hjælp af en **quadtree**. Disse celler er tilknyttet 1D, der er baseret på placeringen af cellen i en **Hilbert mellemrum udfylder kurve**, der bevarer lokalitet af punkter. Desuden, når placeringsdata er indekseret, gennemgår en såkaldt **mosaik**, det vil sige alle de celler, der skærer en placering er defineret og gemt som taster i DocumentDB indekset. Forespørgsel tid er argumenter som punkter og polygoner også mosaikrepræsentationer Hvis du vil udtrække de relevante celleområder-ID, skal bruges til at hente data fra indekset.

Hvis du angiver en indeksering politik, der indeholder geografiske indeks for / * (alle stier), og derefter alle punkter fundet i gruppen af websteder er indekseret for effektiv geografiske forespørgsler (ST_WITHIN og ST_DISTANCE). Geografiske indeks der ikke har en præcisionsværdi og Brug altid en præcision standardværdi.

>[AZURE.NOTE] DocumentDB understøtter automatisk indeksering af punkter, polygoner og LineStrings

Den følgende JSON kodestykke viser en politik for indeksering med geografiske indeksering er aktiveret, det vil sige indeks helst GeoJSON fundet i dokumenter til geografiske forespørgsler. Hvis du vil redigere politikken indeksering ved hjælp af portalen Azure, kan du angive følgende JSON for indeksering politik til at aktivere geografiske indeksering på din samling.

**Samling indeksering politik JSON med Spatial aktiveret for punkter og polygoner**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Her er et kodestykke i .NET, der viser, hvordan du opretter en samling med geografiske indeksering slået til for alle stier, der indeholder punkter. 

**Oprette en samling med geografiske indeksering**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

Og her er, hvordan du kan ændre en eksisterende af websteder for at kunne udnytte geografiske indeksering over eventuelle point, der er gemt i dokumenter.

**Ændre en eksisterende af websteder med geografiske indeksering**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE] Hvis placeringen GeoJSON værdi i dokumentet er forkert udformet eller ugyldige, vil derefter det ikke få indekseret for geografiske forespørgsler. Du kan kontrollere placering værdier ved hjælp af ST_ISVALID og ST_ISVALIDDETAILED.
>
> Hvis definitionen for din samling omfatter en Partitionsnøgle, rapporteres indeksering status for transformeringen ikke. 

## <a name="next-steps"></a>Næste trin
Nu hvor du har erfaringer om, hvordan du kommer i gang med geospatiale support i DocumentDB, kan du:

- Begynd at kode med [geospatiale .NET kodeeksempler på Github](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
- Få hænder på med geospatiale forespørgsler på [DocumentDB forespørgsel tennisklub](http://www.documentdb.com/sql/demo#geospatial)
- Få flere oplysninger om [DocumentDB forespørgsel](documentdb-sql-query.md)
- Lær mere om [DocumentDB indeksering politikker](documentdb-indexing-policies.md)
