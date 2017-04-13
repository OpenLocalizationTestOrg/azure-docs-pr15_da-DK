<properties 
    pageTitle="DocumentDB .NET API og SDK | Microsoft Azure" 
    description="Få mere at vide om .NET API og SDK, herunder release datoer, lukning af datoer og ændringer mellem hver version af DocumentDB .NET SDK." 
    services="documentdb" 
    documentationCenter=".net" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB API'er og SDK'er 

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTEN](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-net-api-and-sdk"></a>DocumentDB .NET API og SDK

<table>
<tr><td>**Henter SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**API dokumentation**</td><td>[.NET API referencedokumentation](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**Eksempler**</td><td>[.NET kodeeksempler](documentdb-dotnet-samples.md)</td></tr>
<tr><td>**Komme i gang**</td><td>[Komme i gang med DocumentDB .NET SDK](documentdb-get-started.md)</td></tr>
<tr><td>**Web app selvstudium**</td><td>[Udvikling af webprogrammer med DocumentDB](documentdb-dotnet-application.md)</td></tr>
<tr><td>**Aktuelle understøttede framework**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Produktbemærkninger

> [AZURE.IMPORTANT] Starter med 1.9.2-versionen, modtager du muligvis System.NotSupportedException ved forespørgsler partitioneret af websteder. For at undgå denne fejl, skal du sikre, at host-processen er 64-bit. For eksekverbare projekter kan dette gøres ved at fjerne markeringen af indstillingen "Foretrækker 32-bit" i vinduet projekt egenskaber under fanen Opret.

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)

  - Tilføjede direkte forbindelse understøttelse af partitioneret af websteder.
  - Forbedret ydeevne for afgrænset Staleness konsistens niveau.
  - Tilføjede Polygon og LineString DataTypes mens angivelse af websteder indeksering politik for geografisk indhegning geografiske forespørgsler.
  - Tilføjet LINQ understøttelse af StringEnumConverter, IsoDateTimeConverter og UnixDateTimeConverter under oversættelsen prædikaterne.
  - Forskellige SDK fejlrettelser.

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)

  - Fast et problem, der forårsagede følgende NotFoundException: Læs sessionen er ikke tilgængelig for input session tokenet. Denne undtagelse opstod i nogle tilfælde, når forespørgsler til et læst område for en geografisk fordeles konto.
  - Egenskaben ResponseStream i klassen ResourceResponse, som giver direkte adgang til den underliggende stream fra et svar, der vises.

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)

  - Ændrede ResourceResponse, FeedResponse, StoredProcedureResponse og MediaResponse klasser for at implementere den tilsvarende offentlige grænseflade, så de kan være mocked til test indsatsbaserede installation (TDD).
  - Fast et problem, der forårsagede et forkert udformet partition vigtige sidehoved, når du bruger et brugerdefineret JsonSerializerSettings objekt til serialisering data.

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)

  - Fast et problem, der forårsagede længerevarende forespørgsler mislykkes med fejl: godkendelse Tokenet er ikke gyldige på nuværende tidspunkt.
  - Fast et problem, fjernes den oprindelige SqlParameterCollection i tværs partition top/Sortér efter-forespørgsler.

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)

  - Understøttelse af parallelle forespørgsler til partitioneret samlinger tilføjet.
  - Understøttelse af tilføjet på tværs af partition ORDER BY og TOP forespørgsler for partitioneret af websteder.
  - Fast de manglende referencer til DocumentDB.Spatial.Sql.dll og Microsoft.Azure.Documents.ServiceInterop.dll, der kræves, når der henvises til et DocumentDB projekt med en reference til pakken DocumentDB Nuget.
  - Fast muligheden for at bruge parametre af forskellige typer, når du bruger brugerdefinerede funktioner i LINQ. 
  - Fast en fejl for globalt replikerede konti, hvor Upsert opkald er der blevet ført til at læse placeringer i stedet for Skriv placeringer.
  - Tilføjede metoder til IDocumentClient brugergrænsefladen, der manglede: 
      - UpsertAttachmentAsync metode, der tager mediaStream og indstillinger som parametre
      - CreateAttachmentAsync afskrivningsmetode, som benytter indstillinger som en parameter
      - CreateOfferQuery metode, der tager querySpec som en parameter.
  - Offentliggjorte offentlige klasser, der vises i grænsefladen IDocumentClient.

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - Tilføjet understøttelse af flere områder database konti.
  - Understøttelse af gentagne forsøg for begrænset anmodninger tilføjet.  Brugeren kan tilpasse antallet af nye forsøg og Maks ventetiden ved at konfigurere egenskaben ConnectionPolicy.RetryOptions.
  - Tilføjet en ny IDocumentClient brugergrænseflade, der definerer signaturer for alle DocumenClient egenskaber og metoder.  Som en del af denne ændring, ændres også lokalnummer metoder, som opretter IQueryable og IOrderedQueryable til metoder på selve DocumentClient klassen.
  - Tilføjet konfiguration muligheden for at angive ServicePoint.ConnectionLimit for et givet DocumentDB slutpunkt Uri.  Brug ConnectionPolicy.MaxConnectionLimit til at ændre standardværdien, som er angivet til 50.
  - Frarådede IPartitionResolver og dets gennemførelse.  Understøttelse af IPartitionResolver er nu forældet. Det anbefales, at du bruger opdelt samlinger for højere lager og overførselshastighed.


### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - Tilføjet en overbelastning til Uri baseret ExecuteStoredProcedureAsync afskrivningsmetode, som tager RequestOptions som en parameter.
  
### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - Tilføjede tid til live (TTL) support til dokumenter.

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - Fast en fejl i Nuget emballagen af .NET SDK for pakker den som en del af en løsning Azure skybaseret tjeneste.
  
### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - Implementeret [partitioneret af websteder](documentdb-partition-data.md) og [brugerdefinerede ydeevne](documentdb-performance-levels.md). 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Fast]** Forespørgsler DocumentDB slutpunkt udløses: ' System.Net.Http.HttpRequestException: fejl under kopieringen indhold til en stream.

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - Udvidet LINQ understøtter, herunder nye operatorer til sideinddeling, betinget udtryk og interval sammenligning.
    - Tage operator for at aktivere Vælg toppen funktionsmåde i LINQ
    - CompareTo operator for at aktivere område strengsammenligninger
    - Betinget (?) og samling operatorer (?)
  - **[Fast]** ArgumentOutOfRangeException når kombineres Model projicering med hvor i i linq forespørgsel.  [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Fast]** Hvis Vælg ikke er det sidste udtryk provideren LINQ antages ingen projicering og produceret Vælg * forkert.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Implementeret Upsert, tilføjet UpsertXXXAsync metoder
 - Forbedring af ydeevnen for alle anmodninger
 - LINQ udbyder understøttelse af betinget, samling og CompareTo metoder til strenge
 - **[Fast]** LINQ udbyder--> implementere indeholder metode på listen for at oprette den samme SQL som på IEnumerable og matrix
 - **[Fast]** BackoffRetryUtility bruger den samme HttpRequestMessage igen i stedet for at oprette en ny på Prøv igen
 - **[Forældede]** UriFactory.CreateCollection--> nu bruge UriFactory.CreateDocumentCollection
 
### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Fast]** Lokalisering problemer, når du bruger ikke en kultur oplysninger som nl-NL osv. 
 
### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - ID-baserede Routing
    - Ny UriFactory hjælper til at hjælpe med at oprettelse ID baseret ressourcehyperlinks
    - Ny overloads på DocumentClient du tager med URI
  - Tilføjede IsValid() og IsValidDetailed() i LINQ for geospatiale
  - Forbedret understøttelse af LINQ udbyder
    - **Matematisk** - Abs, ARCCOS, ARCSIN ARCTAN, loft Cos EKSP, Floor, Log, Log10, Pow, runde, log, Sin, KVROD, Tan, afkorte
    - **Streng** - kontakt, indeholder, EndsWith, IndexOf, antal, ToLower, TrimStart, erstatte, spejlvende, skal TrimEnd, StartsWith, understreng, ToUpper
    - **Matrix** - kontakt, indeholder, antal
    - **I** operator

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Understøttelse af ændring af indeksering politikker tilføjet
    - Ny ReplaceDocumentCollectionAsync metode i DocumentClient
    - Ny IndexTransformationProgress egenskab i ResourceResponse<T> spore procent fremskridt i indeks politikændringer
    - DocumentCollection.IndexingPolicy er nu af
  - Understøttelse af geografiske indeksering og forespørgsel tilføjet
    - Nye Microsoft.Azure.Documents.Spatial navneområde for serialisering/deserialisering geografiske typer som punkt og Polygon
    - Nye SpatialIndex klasse for indeksering GeoJSON data, der er gemt i DocumentDB
  - **[Fast]** : forkert SQL-forespørgsel, der er dannet ud fra linq udtryk [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Afhængighed af Newtonsoft.Json v5.0.7 
- Ændringer til at understøtte Order By
  - LINQ udbyder understøttelse af OrderBy() eller OrderByDescending()
  - IndexingPolicy til at understøtte Order By 
  
        **NB: Possible breaking change** 
  
        If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Understøttelse af partitionering data ved hjælp af de nye HashPartitionResolver og RangePartitionResolver klasser og IPartitionResolver
- DataContract serialisering
- Understøttelse af GUID i LINQ udbyder
- Understøttelse af UDF i LINQ

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- GA SDK

> [AZURE.NOTE]
Der opstod en ændring af NuGet pakkenavn mellem preview og GA. Vi flyttes fra **Microsoft.Azure.Documents.Client** **Microsoft.Azure.DocumentDB**
<br/>


### <a name="a-name09x-preview09x-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentsclient"></a><a name="0.9.x-preview"/>[0.9.x-Preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- Preview SDK'er [forældede]

## <a name="release--retirement-dates"></a>Udgave og lukning af datoer
Microsoft giver besked om mindst **12 måneder** før pension en SDK for at udjævne overgangen til en nyere/understøttet version.

Nye funktioner og funktionalitet og optimeringer føjes kun til den aktuelle SDK, så det anbefales, at du altid har opgraderet til den nyeste SDK version så tidligt som muligt. 

En anmodning om at DocumentDB ved hjælp af en udgåede SDK afvises af tjenesten.

> [AZURE.WARNING]
Alle versioner af Azure DocumentDB SDK til .NET før version **1.0.0** skal udgå på **29 februar 2016**. 
 
<br/>
 
| Version | Udgivelsesdato | Lukning af dato 
| ---     | ---          | ---
| [1.10.0](#1.10.0) | September 27 2016 |---
| [1.9.5](#1.9.5) | September 01 2016 |---
| [1.9.4](#1.9.4) | August 24 2016 |---
| [1.9.3](#1.9.3) | August 15 2016 |---
| [1.9.2](#1.9.2) | Juli 23 2016 |---
| 1.9.1 | Forældet |---
| 1.9.0 | Forældet |---
| [1.8.0](#1.8.0) | Juni 14 2016 |---
| [1.7.1](#1.7.1) | Maj 06 2016 |---
| [1.7.0](#1.7.0) | April 26 2016 |---
| [1.6.3](#1.6.3) | April 08 2016 |---
| [1.6.2](#1.6.2) | Marts 29 2016 |---
| [1.5.3](#1.5.3) | Februar 19 2016 |---
| [1.5.2](#1.5.2) | December 14 2015 |---
| [1.5.1](#1.5.1) | November 23 2015 |---
| [1.5.0](#1.5.0) | Oktober 05 2015 |---
| [1.4.1](#1.4.1) | August 25 2015 |---
| [1.4.0](#1.4.0) | August 13 2015 |---
| [1.3.0](#1.3.0) | August 05 2015 |---
| [1.2.0](#1.2.0) | Juli 06 2015 |---
| [1.1.0](#1.1.0) | 30 april 2015 |---
| [1.0.0](#1.0.0) | 08 april 2015 |---
| [0.9.3-prelease](#0.9.x-preview) | 12 marts 2015 | Februar 29 2016
| [0.9.2-prelease](#0.9.x-preview) | Januar 2015 | Februar 29 2016
| [.9.1-prelease](#0.9.x-preview) | 13 oktober 2014 | Februar 29 2016
| [0.9.0-prelease](#0.9.x-preview) | 21 august 2014 | Februar 29 2016

## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Se også

Hvis du vil vide mere om DocumentDB skal du se [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) service side. 
