<properties
   pageTitle="Azure Service RESTEN API-Version 2015-02-28-visning af søgeresultater | Microsoft Azure | Azure søgeresultater API"
   description="Azure Service RESTEN API-Version 2015-02-28-visning af søgeresultater omfatter forsøg funktioner som programmer naturligt sprog til analyse og moreLikeThis søgninger."
   services="search"
   documentationCenter="na"
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search"
   ms.date="09/07/2016"
   ms.author="brjohnst"/>

# <a name="azure-search-service-rest-api-version-2015-02-28-preview"></a>Azure Search-tjenesten REST-API: Version 2015-02-28-eksempel

I denne artikel er referencedokumentationen for `api-version=2015-02-28-Preview`. Dette eksempel udvider den aktuelle alment tilgængelig version [api-version = 2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), ved at indsende følgende forsøg funktioner:

- `moreLikeThis`forespørge parameter i [Søg i dokumenter](#SearchDocs) API. Der er angivet andre dokumenter, der er relevante for en anden bestemt dokument.

Et par andre dele af den `2015-02-28-Preview` REST-API er beskrevet separat. Dette omfatter:

- [Vundne profiler](search-api-scoring-profiles-2015-02-28-preview.md)
- [Indeks](search-api-indexers-2015-02-28-preview.md)

Azure Search-tjenesten er tilgængelig i flere versioner. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) få mere at vide.

## <a name="apis-in-this-document"></a>API'er i dette dokument

Azure Søg service API understøtter to URL-syntaks for API handlinger: enkle og OData (se [understøttelse af OData (Azure Søg API)](http://msdn.microsoft.com/library/azure/dn798932.aspx) få mere at vide). Den nedenstående liste viser simple syntaksen.

[Oprette indeks](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Opdater indeks](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Få indeks](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Listen over indeks](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Få indeks statistik](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Test Analyzer](#TestAnalyzer)

    POST /indexes/[index name]/analyze?api-version=2015-02-28-Preview

[Slette et indeks](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Tilføj, Slet, og opdatere Data i et indeks](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Søg i dokumenter](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[Opslag dokument](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Antal dokumenter](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Forslag](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

________________________________________
<a name="IndexOps"></a>
## <a name="index-operations"></a>Indeks handlinger

Du kan oprette og administrere indeks i Azure søgetjenesten via enkel HTTP-anmodninger (INDLÆG, GET, er LAGT, Slet) på en given indeks ressource. Hvis du vil oprette et indeks, skal SENDE du først et JSON-dokument, der beskriver indeks skema. Felterne i indekset, deres datatyper, og hvordan de kan bruges (for eksempel i hele teksten søgninger, filtre, sortering eller faceting) definerer skemaet. Den definerer også vurdering profiler, suggesters og andre attributter for at konfigurere funktionsmåden for indekset.

I følgende eksempel indeholder en illustration af et skema, der bruges til at søge på Hoteloplysninger med feltet Beskrivelse, der er defineret i to sprog. Bemærk, hvordan attributter styre, hvordan feltet skal bruges. Eksempel på `hotelId` bruges som tasten dokument (`"key": true`) og udelades fra hele teksten søgninger (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
      {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

Når indekset er oprettet, skal overføre du dokumenter, som udfylder indekset. Se [tilføje eller opdatere dokumenter](#AddOrUpdateDocuments) til denne næste trin.

Du kan finde en Videointroduktion til indeksering i Azure Søg [kanal 9 skyen dækker afsnit på Azure søgning](http://go.microsoft.com/fwlink/p/?LinkId=511509).


<a name="CreateIndex"></a>
## <a name="create-index"></a>Oprette indeks

Et indeks er den primære metode til organisering og søgning dokumenter i Azure Søg, svarende til hvordan organiserer poster i en database i en tabel. Hvert indeks er en samling af dokumenter, at alle de svarer til indeks skema (feltnavne, datatyper og egenskaber), men indeks også angive flere konstruktioner (suggesters vurdering profiler indstillinger, og CORS), der definerer andre Søg funktionsmåder.

Du kan oprette et nyt indeks i en Azure Search-tjenesten ved hjælp af en HTTP POST eller læg anmodning. Brødteksten i anmodningen er et JSON-skema, der angiver oplysningerne om indeks og konfiguration.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Du kan også bruge læg og angive navnet på indekset på URI. Hvis indekset ikke findes, oprettes den.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

Oprette et indeks bestemmer strukturen i de dokumenter, der er gemt og bruges i søgninger. Udfylde indekset er en separat handling. Du kan bruge et [indekseringsprogram](https://msdn.microsoft.com/library/azure/mt183328.aspx) (tilgængeligt for understøttede datakilder) eller handlingen [Tilføj, Opdater eller Slet dokumenter](https://msdn.microsoft.com/library/azure/dn798930.aspx) til dette trin. Omvendt indekset genereres, når dokumenterne, der er skrevet.

**Bemærk**: det maksimale antal indeks, der er tilladt varierer efter priser niveau. Tjenesten gratis kan op til 3 indeks. Standard service kan 50 indeks per Search-tjenesten. Se [grænser og begrænsninger](http://msdn.microsoft.com/library/azure/dn798934.aspx) for detaljer.

**Anmode om**

HTTPS er påkrævet for alle serviceanmodninger. **Create Index** anmodningen kan opbygget ved hjælp af et INDLÆG eller læg metode. Når du bruger INDLÆG, skal du angive indeksnavnet på en i anmodningsteksten sammen med skemadefinitionen indeks. Med læg er navnet på indekset en del af URL-adressen. Hvis indekset, ikke findes, oprettes den. Hvis de allerede forefindes, bliver de opdateret til den nye definition.

Navnet på indekset skal skrives med små bogstaver, starter med et bogstav eller et tal, har ingen skråstreger eller prikker og være mindre end 128 tegn. Resten af navnet kan efter navnet på indekset starter med et bogstav eller et tal, omfatter en hvilken som helst bogstav, tal og stiplet, så længe stregerne ikke der er sammenhængende.

Den `api-version` er påkrævet. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) for en liste over tilgængelige versioner.

**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning overskrifterne.

- `Content-Type`: Der er påkrævet. Angiv denne indstilling til`application/json`
- `api-key`: Der er påkrævet. Den `api-key` der bruges til at
- godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din tjeneste. **Create Index** anmodningen skal indeholde en `api-key` sidehoved, der er angivet til din administrator nøgle (i modsætning til en forespørgsel nøgle).

Du skal også navnet på tjenesten at opbygge URL-anmodningen. Du kan få både navnet på tjenesten og `api-key` fra dashboardet tjeneste i portalen Azure. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

<a name="RequestData"></a>
**Syntaksen for anmodning om brødtekst**

Brødteksten i anmodningen indeholder en skemadefinitionen, som indeholder listen over datafelter i dokumenter, der skal indføres i dette indeks, datatyper, attributter, såvel som en valgfri liste med pointmodel for kundeemner profiler, der bruges til at give tilsvarende dokumenter på Forespørgselsestid.

Bemærk, at anmode om til et INDLÆG, skal du angive navnet på indekset i anmodningsteksten.

Der kan kun være et nøglefelt i indekset. Det skal være et string-felt. Dette felt repræsenterer det entydige id for hvert dokument, der er gemt i indekset.

De vigtigste dele af et indeks omfatter følgende:

- `name`
- `fields`der skal indføres i dette indeks, herunder navn, datatype og egenskaber, der definerer tilladte handlinger på det pågældende felt.
- `suggesters`bruges til autofuldførelse eller til Autoudfyldning forespørgsler.
- `scoringProfiles`bruges til Brugerdefineret søgning karakteren rangering. Du kan finde oplysninger i [Tilføj vurdering profiler](https://msdn.microsoft.com/library/azure/dn798928.aspx) .
- `analyzers`, `charFilters`, `tokenizers`, `tokenFilters` bruges til at definere, hvordan dine dokumenter/forespørgsler er opdelt kan indekseres/søgbare tokens. Du kan finde oplysninger i [analysen i Azure søgning](https://aka.ms//azsanalysis) .
- `defaultScoringProfile`bruges til at overskrive standard vundne funktionsmåder.
- `corsOptions`tillade indekset tværs origin forespørgsler.

Syntaksen til at strukturere anmodning om data er som følger. En eksempel-anmodning leveres yderligere på i dette emne.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,              
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

**Indeksattributter**

Følgende attributter kan angives, når du opretter et indeks. Flere oplysninger om profiler vurdering og resultat, i [føje vurdering profiler](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name`-Angiver navnet på feltet.

`type`-Angiver datatypen for feltet. Se [Understøttede datatyper](#DataTypes) til en liste over understøttede datatyper.

`searchable`-Markerer feltet som hele teksten kan søgning. Det betyder, at den skal igennem analyse som word seneste under indeksering. Hvis du angiver en `searchable` felt til en værdi som "solskin dag" internt den kan opdeles i de enkelte tokens "solskin" og "dag". Dette gør det muligt for hele teksten søger efter disse vilkår. Felter af typen `Edm.String` eller `Collection(Edm.String)` er `searchable` som standard. Felterne andre typer må ikke være `searchable`.

  - **Bemærk**: `searchable` felter optager ekstra plads i indekset, da Azure søgning skal gemmes en ekstra tokenized version af feltværdien for hele teksten søgninger. Hvis du vil spare plads i indekset, og du ikke har brug for et felt, der skal medtages i søgninger, angive `searchable` til `false`.

`filterable`-Giver mulighed for det felt, der skal refereres til i `$filter` forespørgsler. `filterable`er forskellig fra `searchable` i hvordan strenge håndteres. Felter af typen `Edm.String` eller `Collection(Edm.String)` , der er `filterable` ikke undergå word seneste, så sammenligninger er for nøjagtige svarer kun. For eksempel, hvis du angiver dette felt `f` til "solskin dag" `$filter=f eq 'sunny'` kan finde nogen resultater, men `$filter=f eq 'sunny day'` vil. Alle felter er `filterable` som standard.

`sortable`-Systemet sorterer som standard resultaterne efter resultat, men i mange oplevelser brugere vil sortere efter felter i dokumenterne. Felter af typen `Collection(Edm.String)` må ikke være `sortable`. Alle andre felter er `sortable` som standard.

`facetable`-Typisk bruges i en præsentation med søgeresultater, der indeholder besøg Tæl efter kategori (for eksempel, søge efter digitale kameraer og se forekomster af brand, når antallet af megapixel, ved pris osv.). Denne indstilling kan ikke bruges med felter af typen `Edm.GeographyPoint`. Alle andre felter er `facetable` som standard.

  - **Bemærk**: felter af typen `Edm.String` , der er `filterable`, `sortable`, eller `facetable` kan være højst 32 KB længde. Dette skyldes, at sådanne felter, behandles det som et enkelt søgeord, og den maksimale længde på et ord i Azure Søg er 32KB. Hvis du har brug at gemme mere tekst end dette i en enkelt strengfelt, du skal eksplicit er angivet `filterable`, `sortable`, og `facetable` til `false` i indeksdefinitionen for din.

  - **Bemærk**: Hvis et felt har ingen af de ovenfor attributter, der er angivet til `true` (`searchable`, `filterable`, `sortable`, eller`facetable`) feltet effektivt medtages ikke i omvendt indekset. Denne indstilling er nyttig for felter, der ikke bruges i forespørgsler, men du skal bruge i søgeresultaterne. Udelukke sådanne felter fra indekset forbedrer ydeevnen.

`key`-Markerer feltet, som der indeholder entydige identifikationskoder for dokumenter i indekset. Ét felt skal vælges som den `key` felt, og det skal være af typen `Edm.String`. Nøgle-felter kan bruges til at slå op dokumenter direkte via [Opslag API](#LookupAPI).

`retrievable`-Angiver, om feltet kan returneres i et søgeresultat.  Dette er nyttigt, når du vil bruge et felt (for eksempel margen) som et filter, sortering eller vundne ordning, men du ikke vil feltet for at være synlige for brugeren. Denne attribut skal være `true` for `key` felter.

`analyzer`-Angiver navnet på analyzer skal bruges for feltet på tid på søgning og indeksering tid. Du kan finde de tilladte værdisæt [programmer til analyse](https://msdn.microsoft.com/library/mt605304.aspx). Denne indstilling kan kun bruges sammen med `searchable` felter og den kan ikke indstilles sammen med enten `searchAnalyzer` eller `indexAnalyzer`.  Når analyzer er valgt, kan ikke ændres for feltet.

`searchAnalyzer`-Angiver navnet på den analyzer bruges på søgning tidspunkt til feltet. Du kan finde de tilladte værdisæt [programmer til analyse](https://msdn.microsoft.com/library/mt605304.aspx). Denne indstilling kan kun bruges sammen med `searchable` felter. Det skal være angivet sammen med `indexAnalyzer` og den kan ikke angives sammen med den `analyzer` indstilling. Denne analyzer kan blive opdateret på et eksisterende felt.

`indexAnalyzer`-Angiver navnet på den analyzer bruges på indeksering tidspunkt til feltet. Du kan finde de tilladte værdisæt [programmer til analyse](https://msdn.microsoft.com/library/mt605304.aspx). Denne indstilling kan kun bruges sammen med `searchable` felter. Det skal være angivet sammen med `searchAnalyzer` og den kan ikke angives sammen med den `analyzer` indstilling. Når analyzer er valgt, kan ikke ændres for feltet.

`suggesters`-Indstiller Søgetilstand og felter, der er kilde til indholdet til forslag. Du kan finde oplysninger i [Suggesters](#Suggesters) .

`scoringProfiles`-Definerer brugerdefinerede vurdering funktionsmåder, som du kan påvirke hvilke elementer vises højere i søgeresultaterne. Vurdering profiler består af felt tykkelser og funktioner. Se [tilføje vundne profiler](https://msdn.microsoft.com/library/azure/dn798928.aspx) kan finde flere oplysninger om de attributter, der bruges i en vurdering profil.

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a>
**Understøttelse af sprog**

Søgefelterne undergå analyse, at det meste ofte omfatter word seneste tekst normalisering og bortfiltrere udtryk. Som standard er søgefelterne i Azure Søg analysere med [Apache Lucene Standard analyzer](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html) som opdeler tekst i elementer, der følger["Unicode-tekst segmentering"](http://unicode.org/reports/tr29/) regler. Desuden konverterer standard analyzer alle tegn til små bogstaver formularen. Både indekserede dokumenter og søgeord du gennemfører analysen under indeksering og behandling af forespørgsler.

Azure søgning understøtter en række forskellige sprog. De enkelte sprog kræver en ikke-standardiseret tekst analyzer som konti for egenskaber for et bestemt sprog. Azure søgning indeholder to typer af programmer til analyse:

- 35 programmer til analyse sikkerhedskopien af Lucene.
- 50 programmer til analyse sikkerhedskopien af beskyttede Microsoft naturligt sprog behandling teknologi, der bruges i Office og Bing.

Nogle udviklere kan foretrækker det mere velkendte, enkle, open source-løsning af Lucene. Lucene programmer til analyse er hurtigere, men programmer til analyse Microsoft har avancerede funktioner, som lemmatization, word decompounding (i sprog som tysk, dansk, hollandsk, svensk, norsk, estisk, Udfør, ungarsk, slovakisk) og enhed talegenkendelse (URL-adresser, mails, datoer, tal). Hvis det er muligt, skal du køre sammenligninger af programmer til både Microsoft og Lucene analyse til at afgøre, hvilket der er bedre ud.

***Hvordan de sammenligning***

Lucene analyzer til engelsk udvider standard analyzer. Det fjerner possessive pronomener (efterfølgende) fra ord, gælder stammer ud fra [Porter stammer algoritme](http://tartarus.org/~martin/PorterStemmer/)og fjerner engelsk [stoppe ord](http://en.wikipedia.org/wiki/Stop_words).

I sammenligning udfører Microsoft analyzer lemmatization i stedet for stammer. Det betyder, at det kan håndtere bøjede og uregelmæssige word formularer meget bedre hvilke resultater i flere relevante søgeresultater (se modul 7 af [Azure Søg MVA præsentation](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps) få mere at vide).

Indeksering med programmer til Microsoft analyse er i gennemsnit to til tre gange langsommere end Lucene tilsvarende, afhængigt af sproget. Søgeydelse bør ikke berøres betydeligt for gennemsnitlige størrelse forespørgsler.

***Konfiguration***

For hvert felt i indeksdefinitionen af, kan du angive den `analyzer` egenskab til en analyzer navn, der angiver, hvilke sprog og leverandør. Den samme analyzer anvendes, når indeksering og søgning for det pågældende felt.
Du kan for eksempel har separate felter til engelsk, fransk og spansk hotel beskrivelser, der findes side om side i det samme indeks. Du kan bruge ['searchFields' forespørgselsparameter](#SearchQueryParameters) til at angive, hvilket sprogspecifikke felt til at søge mod i dine forespørgsler. Du kan gennemse forespørgsel eksempler, der indeholder den `analyzer` egenskab i [Søg i dokumenter](#SearchDocs). 

***Analyzer liste***

Nedenfor vises på listen over understøttede sprog sammen med Lucene og Microsoft analyzer navne.

<table style="font-size:12">
    <tr>
        <th>Sprog</th>
        <th>Microsoft analyzer navn</th>
        <th>Lucene analyzer navn</th>
    </tr>
    <tr>
        <td>arabisk</td>
        <td>ar.Microsoft</td>
        <td>ar.lucene</td>      
    </tr>
    <tr>
        <td>armensk</td>
        <td></td>
        <td>hy.lucene</td>
    </tr>
    <tr>
        <td>Bengalsk</td>
        <td>bn.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>baskisk</td>
        <td></td>
        <td>EU.lucene</td>
    </tr>
    <tr>
        <td>bulgarsk</td>
        <td>BG.Microsoft</td>
        <td>BG.lucene</td>
    </tr>
    <tr>
        <td>catalansk</td>
        <td>ca.Microsoft</td>
        <td>ca.lucene</td>          
    </tr>
    <tr>
        <td>Kinesisk, forenklet</td>
        <td>zh Hans.microsoft</td>
        <td>zh Hans.lucene</td>     
    </tr>
    <tr>
        <td>Kinesisk, traditionelt</td>
        <td>zh Hant.microsoft</td>
        <td>zh Hant.lucene</td>     
    <tr>
    <tr>
        <td>kroatisk</td>
        <td>hr.Microsoft</td>
        <td/></td>
    </tr>
    <tr>
        <td>tjekkisk</td>
        <td>CS.Microsoft</td>
        <td>CS.lucene</td>      
    </tr>    
    <tr>
        <td>dansk</td>
        <td>Da.Microsoft</td>
        <td>Da.lucene</td>      
    </tr>    
    <tr>
        <td>nederlandsk</td>
        <td>NL.Microsoft</td>
        <td>NL.lucene</td>  
    </tr>    
    <tr>
        <td>engelsk</td>        
        <td>en.Microsoft</td>
        <td>en.lucene</td>      
    </tr>
    <tr>
        <td>estisk</td>
        <td>et.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>finsk</td>
        <td>Fi.Microsoft</td>
        <td>Fi.lucene</td>      
    </tr>    
    <tr>
        <td>fransk</td>
        <td>fr.Microsoft</td>
        <td>fr.lucene</td>      
    </tr>
    <tr>
        <td>galisisk</td>
        <td></td>
        <td>GL.lucene</td>      
    </tr>
    <tr>
        <td>tysk</td>
        <td>de.Microsoft</td>
        <td>de.lucene</td>      
    </tr>
    <tr>
        <td>græsk</td>
        <td>el.Microsoft</td>
        <td>el.lucene</td>      
    </tr>
    <tr>
        <td>gujarati</td>
        <td>Gu.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>hebraisk</td>
        <td>HE.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>hindi</td>
        <td>Hi.Microsoft</td>
        <td>Hi.lucene</td>      
    </tr>
    <tr>
        <td>ungarsk</td>      
        <td>hu.Microsoft</td>
        <td>hu.lucene</td>
    </tr>
    <tr>
        <td>islandsk</td>
        <td>is.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Indonesisk (Bahasa)</td>
        <td>id.Microsoft</td>
        <td>id.lucene</td>      
    </tr>
    <tr>
        <td>irsk</td>
        <td></td>
        <td>GA.lucene</td>
    </tr>
    <tr>
        <td>italiensk</td>
        <td>it.Microsoft</td>
        <td>it.lucene</td>      
    </tr>
    <tr>
        <td>japansk</td>
        <td>Ja.Microsoft</td>
        <td>Ja.lucene</td>
        
    </tr>
    <tr>
        <td>kannada</td>
        <td>Ka.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>koreansk</td>
        <td>ko.Microsoft</td>
        <td>ko.lucene</td>
    </tr>
    <tr>
        <td>lettisk</td>        
        <td>LV.Microsoft</td>
        <td>LV.lucene</td>  
    </tr>
    <tr>
        <td>litauisk</td>
        <td>lt.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>malayalam</td>
        <td>ml.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malaysisk (latinsk)</td>
        <td>MS.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>marathi</td>
        <td>Mr.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>norsk</td>
        <td>NB.Microsoft</td>
        <td>No.lucene</td>      
    </tr>
    <tr>
        <td>Persisk</td>
        <td></td>
        <td>fa.lucene</td>      
    </tr>
    <tr>
        <td>polsk</td>
        <td>PL.Microsoft</td>
        <td>PL.lucene</td>      
    </tr>
    <tr>
        <td>Portugisisk (Brasilien)</td>
        <td>pt Br.microsoft</td>
        <td>pt Br.lucene</td>       
    </tr>
    <tr>
        <td>Portugisisk (Portugal)</td>
        <td>pt Pt.microsoft</td>        
        <td>pt Pt.lucene</td>
    </tr>
    <tr>
        <td>punjabi</td>
        <td>Pa.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>rumænsk</td>
        <td>ro.Microsoft</td>
        <td>ro.lucene</td>
    </tr>
    <tr>
        <td>russisk</td>
        <td>RU.Microsoft</td>
        <td>RU.lucene</td>  
    </tr>
    <tr>
        <td>serbisk (kyrillisk)</td>
        <td>SR cyrillic.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Serbisk (latinsk)</td>
        <td>SR latin.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>slovakisk</td>
        <td>SK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>slovensk</td>
        <td>SL.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>spansk</td>
        <td>ES.Microsoft</td>
        <td>ES.lucene</td>
    </tr>
    <tr>
        <td>svensk</td>
        <td>Sv.Microsoft</td>
        <td>Sv.lucene</td>
    </tr>

    <tr>
        <td>tamil</td>
        <td>TA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>telugu</td>
        <td>te.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>thai</td>
        <td>th.Microsoft</td>
        <td>th.lucene</td>
    </tr>
    <tr>
        <td>tyrkisk</td>
        <td>TR.Microsoft</td>
        <td>TR.lucene</td>      
    </tr>
    <tr>
        <td>ukrainsk</td>
        <td>UK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>urdu</td>
        <td>Your.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>vietnamesisk</td>
        <td>Vi.Microsoft</td>
        <td></td>
    </tr>
    <td colspan="3">Desuden gør Azure-søgning sprog agnostic analyzer konfigurationer</td>
    <tr>
        <td>Standard ASCII False</td>
        <td>standardasciifolding.lucene</td>
        <td>
        <ul>
            <li>Unicode-tekst segmentering (Standard Tokenizer)</li>
            <li>ASCII-klapsæder filter - konverterer Unicode-tegn, der ikke hører til de første 127 ASCII-tegn i ASCII-tilsvarende. Dette er nyttigt til at fjerne diakritiske tegn.</li>
        </ul>
        </td>
    </tr>
</table>

Alle programmer til analyse med navne kommenterede med <i>lucene</i> er udstyret med [Apache Lucene sprog programmer til analyse](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). Du kan finde flere oplysninger om ASCII False filter [her](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Suggesters**

A `suggester` definerer, hvilke felter i et indeks bruges til at understøtte autofuldførelse i søgninger. Typisk sendes delsøgning strenge til [Forslag API](#Suggestions) , mens brugeren skriver en søgeforespørgsel, og API returnerer et sæt af foreslåede udtryk. En suggester, som du definerer i indekset bestemmer, hvilke felter der bruges til at opbygge autoudfyldningssøgning betingelser. Du kan finde oplysninger om konfiguration i [Suggesters](#Suggesters) .

**Vundne profiler**

A `scoringProfile` definerer brugerdefinerede vurdering funktionsmåder, som du kan påvirke hvilke elementer vises højere i søgeresultaterne. Vurdering profiler består af felt tykkelser og funktioner. Hvis du vil bruge dem, angiver du en profil ved navn på forespørgselsstrengen.

Standard vundne profil fungerer i baggrunden til at beregne et Søg resultat for hvert element i et resultatsæt. Du kan bruge den interne ikke-navngivet vurdering profil. Du kan også angive `defaultScoringProfile` at bruge en brugerdefineret profil som standard, aktiveret, når en brugerdefineret profil ikke er angivet på forespørgselsstrengen.

Du kan finde oplysninger i [Tilføj vurdering profiler til søgeindekset (Azure Søg Service REST-API)](search-api-scoring-profiles-2015-02-28-preview.md) .

**CORS indstillinger**

Klientsiden Javascript kan ikke kalde en hvilken som helst API'er som standard, da browseren, kan det blokere alle anmodninger i tværs origin. Aktivere CORS (tværs Origin ressourcedeling) ved at angive den `corsOptions` attribut tillade tværs origin forespørgsler til indekset. Bemærk denne kun forespørgsel API'er support CORS af sikkerhedsmæssige årsager. Du kan angive følgende indstillinger for CORS:

- `allowedOrigins`(påkrævet): Dette er en liste over baggrunden, der skal tildeles adgang til dit indeks. Det betyder, at nogen Javascript-kode served fra disse baggrunden, tilladelse til at forespørge indekset (hvis den indeholder den korrekte API-nøgle). Hver origin er typisk i formularen `protocol://fully-qualified-domain-name:port` selvom port udelades ofte. Se [denne artikel](http://go.microsoft.com/fwlink/?LinkId=330822) for at få mere at vide.
 - Hvis du vil give adgang til alle baggrunden, skal du medtage `*` som et enkelt element i den `allowedOrigins` matrix. Bemærk, at **det anbefales ikke praksis for fremstilling søgetjenester.** Det kan dog være nyttige til udvikling eller i forbindelse med fejlfinding.
- `maxAgeInSeconds`(valgfrit): browsere bruge denne værdi til at bestemme varigheden (i sekunder) cache CORS Forhåndskontrol svar. Det skal være et positivt heltal. Jo større denne værdi er, vil være bedre ydeevne, men jo længere tid det tager for CORS Politikændringerne kan træde i kraft. Hvis det ikke er angivet, bruges en standardvarighed på fem minutter.

<a name="CreateUpdateIndexExample"></a>
**Anmode om brødteksten eksempel**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Svar**

For en vellykket anmodning: "201 oprettet".

Som standard indeholder svar brødteksten JSON for indeksdefinitionen af, der blev oprettet. Hvis den `Prefer` anmodning sidehoved er indstillet til `return=minimal`, svar brødteksten være tomt og vil være statuskode succes "204 intet indhold" i stedet for "201 oprettet". Dette gælder, uanset om læg eller INDLÆG blev brugt til at oprette indekset.

**Bemærkninger**

Der er i øjeblikket, begrænset understøttelse af indeks skemaopdateringer. Skemaopdateringer, der kræver reindeksere eksempelvis ændring af felttyper understøttes ikke i øjeblikket. Selvom eksisterende felter ikke kan ændres eller slettes, kan nye felter føjes til et eksisterende indeks når som helst. Når der føjes et nyt felt, har alle eksisterende dokumenter i indekset automatisk en null-værdi for det pågældende felt. Ingen yderligere lagerplads skal blive brugt, indtil nye dokumenter, der er tilføjet til indekset.

<a name="Suggesters"></a>
## <a name="suggesters"></a>Suggesters

Funktionen forslag i Azure Søg er en forespørgsel til Autoudfyldning eller Autofuldførelse egenskab, lave en liste over potentielle søgeord som svar på delvis streng input, der indtastes i søgefeltet. Har du sikkert bemærket forespørgselsforslag når du bruger kommercielle web søgemaskiner: skrive ".NET" i Bing giver en liste over vilkårene for ".NET 4,5", ".NET Framework 3,5" osv. Når du bruger tjenesten Search REST-API, kræver implementere forslag i et brugerdefineret Azure søgeprogram følgende:

- Aktivere forslag ved at tilføje en **suggester** byggeri i indekset, giver navnet, Søgetilstand og en liste over felter, til Autoudfyldning er aktiveret. Eksempelvis hvis du angiver "cityName" som et felt i datakilden, skrive en delvis søgestreng af "Hav" medfører "Seattle", "Søværts" og "Seatac" (alle tre er faktisk bynavne) tilbudt som forespørgselsforslag brugeren.

- Kalde forslag ved at ringe [Forslag API](#Suggestions) din programkode. Typisk sendes delsøgning strenge til tjenesten, mens brugeren skriver en søgeforespørgsel, og denne API returnerer et sæt af foreslåede udtryk.

I denne artikel beskrives det, hvordan du kan konfigurere en **suggester**. Du skal også se [Forslag API](#Suggestions) oplysninger om, hvordan en suggester bruges.

**Brugen**

`Suggesters`der oprettes i indeks og arbejde bedst, når den bruges til at foreslå bestemte dokumenter i stedet for at løse ord eller sætninger. Felterne bedste kandidat er titler, navne og andre relativt korte udtryk, der kan identificere et element. Mindre effektive er gentagne felter, som kategorier og de mærker, eller meget lang-felter som beskrivelser eller kommentarer felter.

Som en del af indeksdefinitionen af, kan du tilføje en enkelt suggester til den `suggesters` af websteder. Egenskaber, der definerer en suggester omfatter følgende:

- `name`: Navnet på suggester. Du kan bruge navnet på suggester, når du ringer til den `suggest` API.
- `searchMode`: Den strategi, der bruges til at søge efter kandidat udtryk. Tilstanden kun understøttes i øjeblikket er `analyzingInfixMatching`, som udfører fleksible tilsvarende af udtryk i starten eller midt sætninger.
- `sourceFields`: En liste over en eller flere felter, der er kilde til indholdet til forslag. Kun felter af typen `Edm.String` og `Collection(Edm.String)` muligvis kilder til forslag. Kun de felter, der ikke har et brugerdefineret sprog analyzer angive kan bruges.

**Eksempel på suggester**

En suggester er en del af indekset. Kun én suggester kan findes i den `suggesters` af websteder i den aktuelle version, sammen med samlingen fields og `scoringProfiles`.

        {
          "name": "hotels",
          "fields": [
             . . .
           ],
          "suggesters": [
            {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields: ["hotelName", "category"]
            }
          ],
          "scoringProfiles": [
             . . .
          ]
        }

> [AZURE.NOTE]  Hvis du har brugt den offentlige foreløbige version af Azure søgning, `suggesters` erstatter en ældre boolesk egenskab (`"suggestions": false`), der kun understøttes præfiks forslag til korte strenge (3-25 tegn). Dens erstatning `suggesters`, understøtter infix-stemmer overens, finder tilsvarende udtryk i starten eller midt på feltets indhold med bedre tolerance for fejl i søgestrenge. Start med den alment tilgængelig udgave, og er dette nu kun implementering af forslagene API. Den ældre `suggestions` egenskab, der blev introduceret i `api-version=2014-07-31-Preview` fortsætter med at arbejde i den pågældende version, men fungerer ikke i den `2015-02-28` eller en nyere version af Azure søgning.

<a name="UpdateIndex"></a>
## <a name="update-index"></a>Opdater indeks

Du kan opdatere et eksisterende indeks i Azure søgning ved hjælp af en HTTP PLACERE anmodning. Opdateringer kan omfatte nye felter tilføjes det eksisterende skema, at redigere CORS indstillinger og ændre vurdering profiler. Yderligere oplysninger finder du i [tilføje vundne profiler](https://msdn.microsoft.com/library/azure/dn798928.aspx) . Angiv navnet på indekset til at opdatere på anmodningen URI:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Vigtige:** Understøttelse af indeks skemaopdateringer er begrænset til handlinger, der ikke kræver genopbygge søgeindekset. Skemaopdateringer, der kræver reindeksere, eksempelvis ændring af felttyper, understøttes ikke i øjeblikket. Nye felter kan tilføjes når som helst, selvom eksisterende felter ikke kan ændres eller slettes. Det samme gælder `suggesters`. Nye felter kan blive føjet til en suggester på tidspunktet, føjes felterne, men felter ikke kan fjernes fra `suggesters` og eksisterende felter kan ikke føjes til `suggesters`.

Når du tilføjer et nyt felt til et indeks, har alle eksisterende dokumenter i indekset automatisk en null-værdi for det pågældende felt. Ingen yderligere lagerplads skal blive brugt, indtil nye dokumenter, der er tilføjet til indekset.

**Anmode om**

HTTPS er påkrævet for alle serviceanmodninger. **Opdater indeks** anmodningen er opbygget ved hjælp af HTTP PLACERE. Med læg er navnet på indekset en del af URL-adressen. Hvis indekset, ikke findes, oprettes den. Hvis indekset allerede findes, bliver de opdateret til den nye definition.

Navnet på indekset skal skrives med små bogstaver, starter med et bogstav eller et tal, har ingen skråstreger eller prikker og være mindre end 128 tegn. Resten af navnet kan efter navnet på indekset starter med et bogstav eller et tal, omfatter en hvilken som helst bogstav, tal og stiplet, så længe stregerne ikke der er sammenhængende.

`api-version=[string]`(påkrævet). Den foreløbige version er `api-version=2015-02-28-Preview`. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) til detaljer og andre versioner.

**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning overskrifterne.

- `Content-Type`: Der er påkrævet. Angiv denne indstilling til`application/json`
- `api-key`: Der er påkrævet. Den `api-key` bruges til at godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din tjeneste. **Opdater indeks** anmodningen skal indeholde en `api-key` sidehoved, der er angivet til din administrator nøgle (i modsætning til en forespørgsel nøgle).

Du skal også navnet på tjenesten at opbygge URL-anmodningen. Du kan finde navnet på tjenesten og `api-key` fra dashboardet tjeneste i portalen Azure. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

**Syntaksen for anmodning om brødtekst**

Når du opdaterer en eksisterende indeks, skal brødteksten indeholder den oprindelige skemadefinitionen plus de nye felter, du tilføjer, samt de ændrede vurdering profiler, suggesters og CORS indstillinger, hvis en hvilken som helst. Hvis du ikke redigerer vurdering profiler og CORS indstillinger, skal du medtage oprindelige fra når indekset blev oprettet. Generelt det bedste mønster skal bruges til opdateringer er til at hente indeksdefinitionen med få, redigere og derefter opdatere den med læg.

Skema syntaksen bruges til at oprette et indeks er gengivet her for at gøre nemmere. Du kan finde flere oplysninger i [Oprette indekset](#CreateIndex) .

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false, 
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }


**Svar**

For en vellykket anmodning: "204 intet indhold".

Som standard vil svar brødteksten være tom. Men hvis de `Prefer` anmodning sidehoved er indstillet til `return=representation`, svar brødteksten indeholder JSON for indeksdefinitionen af, der blev opdateret. I dette tilfælde statuskode succes bliver "200 OK".

**Opdatere indeksdefinition med brugerdefinerede programmer til analyse**

Når en analyzer, en tokenizer, et token filter eller et char filter er defineret, kan den ikke ændres. Nye kan føjes til et eksisterende indeks, kun, hvis den `allowIndexDowntime` flag er indstillet til sand i anmodning om opdatering af indeks: 

`PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true`

Bemærk, at denne handling vil lægge indekset offline for mindst et par sekunder, medfører, at din indeksering og forespørgsler mislykkes. Tilgængelighed af ydeevne og skrive i indekset over kan være forringet for flere minutter, efter at indekset er opdateret eller længere tid for meget store indeks.

<a name="ListIndexes"></a>
## <a name="list-indexes"></a>Listen indeks

Handlingen **Listen indeks** returnerer en liste over indeksene i øjeblikket i din Azure Search-tjenesten.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Anmode om**

HTTPS er påkrævet for alle serviceanmodninger. **Listen indeks** anmodningen kan opbygget ved hjælp af metoden GET.

`api-version=[string]`(påkrævet). Den foreløbige version er `api-version=2015-02-28-Preview`. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) til detaljer og andre versioner.

**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning overskrifterne.

- `api-key`: Der er påkrævet. Den `api-key` bruges til at godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din tjeneste. **Listen indeks** anmodningen skal indeholde en `api-key` indstillet til en administrator nøgle (i modsætning til en forespørgsel nøgle).

Du skal også navnet på tjenesten at opbygge URL-anmodningen. Du kan finde navnet på tjenesten og `api-key` fra dashboardet tjeneste i portalen Azure. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

**Anmodningsteksten**

Ingen.

**Svar**

Statuskode: 200 OK returneres for en vellykket svar.

Her er et eksempel svar brødtekst:

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Bemærk, at du kan filtrere svaret ned til kun de egenskaber, du er interesseret i. Eksempelvis hvis du vil kun en liste over indeksnavne, bruge OData `$select` forespørgsel indstilling:

    GET /indexes?api-version=2015-02-28-Preview&$select=name

I dette tilfælde ville svaret fra eksemplet ovenfor se således ud:

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Dette er en praktisk metode til at gemme båndbredde, hvis du har en masse indeks i Search-tjenesten.

<a name="GetIndex"></a>
## <a name="get-index"></a>Få indeks

Handlingen **Få indeks** får indeksdefinitionen fra Azure søgningen.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Anmode om**

HTTPS er påkrævet for serviceanmodninger. **Få indeks** anmodningen kan opbygget ved hjælp af metoden GET.

[Indeksnavn] i anmodningen URI angiver hvilket indeks til at returnere fra samlingen indeks.

`api-version=[string]`(påkrævet). Den foreløbige version er `api-version=2015-02-28-Preview`. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) til detaljer og andre versioner.

**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning overskrifterne.

- `api-key`: Det `api-key` bruges til at godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din tjeneste. **Få indeks** anmodningen skal indeholde en `api-key` indstillet til en administrator nøgle (i modsætning til en forespørgsel nøgle).

Du skal også navnet på tjenesten at opbygge URL-anmodningen. Du kan finde navnet på tjenesten og `api-key` fra dashboardet tjeneste i portalen Azure. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

**Anmodningsteksten**

Ingen.

**Svar**

Statuskode: 200 OK returneres for en vellykket svar.

Se eksemplet JSON i [oprette og opdatere et indeks](#CreateUpdateIndexExample) for et eksempel på svar data.

<a name="DeleteIndex"></a>
## <a name="delete-index"></a>Slette indeks

Handlingen **Slet indeks** fjerner et indeks og tilknyttede dokumenter fra din Azure Search-tjenesten. Du kan finde navnet på indekset fra dashboardet tjeneste i portalen Azure eller fra API. Du kan finde oplysninger i [Listen indeks](#ListIndexes) .

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Anmode om**

HTTPS er påkrævet for serviceanmodninger. **Slette indekset** anmodningen kan opbygget ved hjælp af metoden DELETE.

[Indeksnavn] i anmodningen URI angiver hvilket indeks til at slette fra samlingen indeks.

`api-version=[string]`(påkrævet). Den foreløbige version er `api-version=2015-02-28-Preview`. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) til detaljer og andre versioner.

**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning overskrifterne.

- `api-key`: Der er påkrævet. Den `api-key` bruges til at godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din URL-adressen. **Slette indekset** anmodningen skal indeholde en `api-key` sidehoved, der er angivet til din administrator nøgle (i modsætning til en forespørgsel nøgle).

Du skal også navnet på tjenesten at opbygge URL-anmodningen. Du kan finde navnet på tjenesten og `api-key` fra dashboardet tjeneste i portalen Azure. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

**Anmodningsteksten**

Ingen.

**Svar**

Statuskode: 204 intet indhold returneres for en vellykket svar.

<a name="GetIndexStats"></a>
## <a name="get-index-statistics"></a>Få indeks statistik

Handlingen **Få indeks statistik** returnerer fra Azure søgningen en dokumentantal for den aktuelle indeks plus lagerforbrug.

    GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] Statistik på dokumentstørrelse Tæl og lagerplads indsamles alle par minutter, ikke i realtid. Derfor kan de stikprøvefunktioner returneres af denne API ikke afspejle ændringer forårsaget af seneste indeksering handlinger.

**Anmode om**

HTTPS er påkrævet for alle tjenester anmodninger. **Få indeks statistik** anmodningen kan opbygget ved hjælp af metoden GET.

[Indeksnavn] i anmodningen URI fortæller tjenesten til at returnere indeks statistik for det angivne indeks.

`api-version=[string]`(påkrævet). Den foreløbige version er `api-version=2015-02-28-Preview`. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) til detaljer og andre versioner.


**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning overskrifterne.

- `api-key`: Det `api-key` bruges til at godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din tjeneste. **Få indeks statistik** anmodningen skal indeholde en `api-key` indstillet til en administrator nøgle (i modsætning til en forespørgsel nøgle).

Du skal også navnet på tjenesten at opbygge URL-anmodningen. Du kan finde navnet på tjenesten og `api-key` fra dashboardet tjeneste i portalen Azure. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

**Anmodningsteksten**

Ingen.

**Svar**

Statuskode: 200 OK returneres for en vellykket svar.

Der er svar brødteksten i følgende format:

    {
      "documentCount": number,
      "storageSize": number (size of the index in bytes)
    }

<a name="TestAnalyzer"></a>
## <a name="test-analyzer"></a>Test Analyzer

**Analysere API** viser, hvordan en analyzer opdeler tekst i tokens.

    POST https://[service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Anmode om**

HTTPS er påkrævet for alle tjenester anmodninger. **Analysere API** -anmodning kan opbygget ved hjælp af metoden POST.

`api-version=[string]`(påkrævet). Den foreløbige version er `api-version=2015-02-28-Preview`. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) til detaljer og andre versioner.


**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning overskrifterne.

- `api-key`: Det `api-key` bruges til at godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din tjeneste. **Analysere API** anmodningen skal indeholde en `api-key` indstillet til en administrator nøgle (i modsætning til en forespørgsel nøgle).

Du skal også navnet på indekset og navnet på tjenesten at opbygge URL-anmodningen. Du kan finde navnet på tjenesten og `api-key` fra dashboardet tjeneste i portalen Azure. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

**Anmodningsteksten**

    {
      "text": "Text to analyze",
      "analyzer": "analyzer_name"
    }

eller

    {
      "text": "Text to analyze",
      "tokenizer": "tokenizer_name",
      "tokenFilters": (optional) [ "token_filter_name" ],
      "charFilters": (optional) [ "char_filter_name" ]
    }

Den `analyzer_name`, `tokenizer_name`, `token_filter_name` og `char_filter_name` skal være gyldige navne på foruddefineret eller brugerdefineret programmer til analyse, tokenizers, token filtre og tegn filtre for indekset. Du kan finde flere oplysninger om processen med at Leksikalsk analyse i [analyse i Azure Søg](https://aka.ms/azsanalysis).

**Svar**

Statuskode: 200 OK returneres for en vellykket svar.

Der er svar brødteksten i følgende format:

    {
      "tokens": [
        {
          "token": string (token),
          "startOffset": number (index of the first character of the token),
          "endOffset": number (index of the last character of the token),
          "position": number (position of the token in the input text)
        },
        ...
      ]
    }

**Analysere API eksempel**

**Anmode om**

    {
      "text": "Text to analyze",
      "analyzer": "standard"
    }

**Svar**

    {
      "tokens": [
        {
          "token": "text",
          "startOffset": 0,
          "endOffset": 4,
          "position": 0
        },
        {
          "token": "to",
          "startOffset": 5,
          "endOffset": 7,
          "position": 1
        },
        {
          "token": "analyze",
          "startOffset": 8,
          "endOffset": 15,
          "position": 2
        }
      ]
    }

________________________________________
<a name="DocOps"></a>
## <a name="document-operations"></a>Dokument handlinger

I Azure Søg et indeks er gemt i skyen og udfyldt ved hjælp af JSON-dokumenter, du overfører til tjenesten. Alle de dokumenter, du overfører omfatter indekserede af dataene søgning. Dokumenter indeholder felter, hvoraf nogle er tokenized i søgeord, som de er overført. Den `/docs` URL-adressen segmentet i Azure Søg API repræsenterer samlingen af dokumenter i et indeks. Alle handlinger, der udføres på samlingen som overførsel, fletning, slette eller oprette forespørgsler dokumenter Tag placere i forbindelse med et enkelt indeks, så URL-adresserne for disse handlinger starter altid med `/indexes/[index name]/docs` for et givet indeksnavn.

Din programkode skal enten generere JSON-dokumenter til at overføre til Azure Søg, eller du kan bruge et [indeks](https://msdn.microsoft.com/library/dn946891.aspx) til at indlæsning af dokumenter, hvis datakilden er Azure SQL-Database eller DocumentDB. Indeks er typisk udfyldt fra et enkelt datasæt, du angiver.

Du skal planlægge på har du et dokument for hvert element, du vil søge efter. En film leje programmet kan have et dokument på hver film et storefront program muligvis ét dokument på hver SKU et online courseware-program kan have et dokument på hver kursus, og en opslag virksomhed kan have et dokument for hver akademisk papir i deres lager, og så videre.

Dokumenter består af et eller flere felter. Felter kan indeholde tekst, som er tokenized via Azure-søgning til søgeord samt ikke-tokenized eller ikke-tekstværdier, der kan bruges i filtre eller vurdering profiler. De navne, datatyper og søgefunktioner understøttes for hvert felt, bestemmes af indeks skemaet. Et af felterne i hvert indeks skema skal være angivet som et ID, og hvert dokument skal indeholde en værdi for feltet ID, der entydigt identificerer dokumentet i indekset. Alle andre dokumentfelter er valgfri, og som standard en null-værdi, hvis venstre ikke er angivet. Bemærk, at null-værdier ikke optager plads i søgeindekset.

Før du kan overføre dokumenter, skal du allerede har oprettet indekset på tjenesten. Du kan finde oplysninger om det første trin i [Create Index](#CreateIndex) .

<a name="AddOrUpdateDocuments"></a>
## <a name="add-update-or-delete-documents"></a>Tilføj, Opdater eller slette dokumenter

Du kan overføre, Flet, Flet eller Overfør eller Slet dokumenter fra et bestemt indeks ved hjælp af HTTP POST. Samling af dokumenter op til (1000 dokumenter per batchen) eller om 16 MB til hver batchen anbefales til et stort antal opdateringer.

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Anmode om**

HTTPS er påkrævet for alle serviceanmodninger. Du kan overføre, Flet, Flet eller Overfør eller Slet dokumenter fra et bestemt indeks ved hjælp af HTTP POST.

Anmodningen URI omfatter [indeksnavn], der angiver hvilket indeks for at sende dokumenter. Du kan kun sende dokumenter til et indeks ad gangen.

`api-version=[string]`(påkrævet). Den foreløbige version er `api-version=2015-02-28-Preview`. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) til detaljer og andre versioner.

**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning overskrifterne.

- `Content-Type`: Der er påkrævet. Angiv denne indstilling til`application/json`
- `api-key`: Der er påkrævet. Den `api-key` bruges til at godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din tjeneste. **Tilføje dokumenter** anmodningen skal indeholde en `api-key` sidehoved, der er angivet til din administrator nøgle (i modsætning til en forespørgsel nøgle).

Du skal også navnet på tjenesten at opbygge URL-anmodningen. Du kan finde navnet på tjenesten og `api-key` fra dashboardet tjeneste i portalen Azure. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

**Anmodningsteksten**

Brødteksten i anmodningen indeholder en eller flere dokumenter, der skal indekseres. Dokumenter, der er identificeret med en entydig nøgle. Hvert dokument er knyttet til en handling: Overfør, Flet, mergeOrUpload eller Slet. Overfør anmodninger skal indeholde dokument dataene som et sæt af nøgle/værdi-par.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [AZURE.NOTE] Dokument taster kan kun indeholde bogstaver, tal, tankestreger ("-"), understregningstegn ("_") og lighedstegn ("="). Du kan få mere at vide under [Naming regler](https://msdn.microsoft.com/library/azure/dn857353.aspx).

**Dokumenthandlinger**

- `upload`: En Overfør handling er lig med "upsert" hvor dokumentet skal indsættes, hvis det er nye og opdaterede/erstattet, hvis den findes. Bemærk, at alle felter erstattes i opdatering store og små bogstaver.
- `merge`: Flet opdaterer et eksisterende dokument med de angivne felter. Hvis dokumentet ikke findes, mislykkes fletningen. Et felt, du angiver i en fletning erstatter det eksisterende felt i dokumentet. Dette omfatter felter af typen `Collection(Edm.String)`. For eksempel, hvis dokumentet indeholder et felt "koder" med værdi `["budget"]` og du udfører en brevfletning med værdi `["economy", "pool"]` for "koder", bliver den sidste værdi i feltet "koder" `["economy", "pool"]`. Det vil **ikke** være `["budget", "economy", "pool"]`.
- `mergeOrUpload`: opfører sig som `merge` Hvis et dokument med den givne nøgle allerede findes i indekset. Hvis dokumentet ikke findes opfører sig som `upload` med et nyt dokument.
- `delete`: Slet fjerner det angivne dokument fra indekset. Bemærk, at eventuelle felter, du angiver i en `delete` handlingen end det nøglefelt, blive ignoreret. Hvis du vil fjerne et enkelt felt fra et dokument, skal du bruge `merge` i stedet, og du skal blot angive feltet eksplicit til `null`.

**Svar**

Statuskode 200 returneres (OK) for en vellykket svar, hvilket betyder, at alle elementer er blevet indekseret. Dette er angivet med den `status` egenskab er sat til Sand for alle elementer, også som den `statusCode` egenskaben angives til enten 201 (for den overførte dokumenter) eller 200 (for flettede eller slettede dokumenter):

    {
      "value": [
        {
          "key": "unique_key_of_new_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 201
        },
        {
          "key": "unique_key_of_merged_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        },
        {
          "key": "unique_key_of_deleted_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        }
      ]
    }  

Statuskode 207 (med flere Status) returneres, når mindst ét element ikke blev indekseret. Elementer, der ikke er indekseret har den `status` indstillet til falsk. Den `errorMessage` og `statusCode` egenskaber kan angive årsagen til fejlen indeksering:

    {
      "value": [
        {
          "key": "unique_key_of_document_1",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later.",
          "statusCode": 503
        },
        {
          "key": "unique_key_of_document_2",
          "status": false,
          "errorMessage": "Document not found.",
          "statusCode": 404
        },
        {
          "key": "unique_key_of_document_3",
          "status": false,
          "errorMessage": "Index is temporarily unavailable because it was updated with the 'allowIndexDowntime' flag set to 'true'. Please try again later.",
          "statusCode": 422
        }
      ]
    }  

I følgende tabel beskrives de forskellige statuskoder til enkelte dokument, der kan returneres i svaret. Bemærk, at nogle angiver problemer med anmodningen sig selv, mens andre angiver midlertidige fejlbetingelser. Disse skal du prøve igen efter en forsinkelse.

<table style="font-size:12">
    <tr>
        <th>Statuskode</th>
        <th>Betydning</th>
        <th>Kan prøves igen</th>
        <th>Noter</th>
    </tr>
    <tr>
        <td>200</td>
        <td>Dokument er blevet ændret eller slettet.</td>
        <td>i/t.</td>
        <td>Slet handlinger er <a href="https://en.wikipedia.org/wiki/Idempotence">idempotent</a>. Det vil sige, selvom en dokumentnøgle ikke findes i indekset, medfører forsøger en sletning med tasten en 200 statuskode.</td>
    </tr>
    <tr>
        <td>201</td>
        <td>Dokumentet blev oprettet.</td>
        <td>i/t.</td>
        <td></td>
    </tr>
    <tr>
        <td>400</td>
        <td>Der opstod en fejl i det dokument, forhindres indekseres.</td>
        <td>Nej</td>
        <td>Fejlmeddelelse i svaret angiver, hvad er der galt med dokumentet.</td>
    </tr>
    <tr>
        <td>404</td>
        <td>Dokumentet kunne ikke flettes, fordi den angivne nøgle ikke findes i indekset.</td>
        <td>Nej</td>
        <td>Denne fejl opstår ikke til overførsler, da de opretter nye dokumenter, og den opstår ikke for sletter, fordi de er <a href="https://en.wikipedia.org/wiki/Idempotence">idempotent</a>.</td>
    </tr>
    <tr>
        <td>409</td>
        <td>En versionskonflikt blev fundet, når du forsøger at indeksere et dokument.</td>
        <td>Ja</td>
        <td>Dette kan ske, når du forsøger at indeksere det samme dokument mere end én gang samtidig.</td>
    </tr>
    <tr>
        <td>422</td>
        <td>Indekset er ikke tilgængelig i øjeblikket, fordi det er blevet opdateret med 'allowIndexDowntime' flag indstillet til "sand".</td>
        <td>Ja</td>
        <td></td>
    </tr>
    <tr>
        <td>503</td>
        <td>Search-tjenesten er ikke tilgængelig i øjeblikket, sandsynligvis på grund af belastet.</td>
        <td>Ja</td>
        <td>Din kode skal vente, før du prøver igen i dette tilfælde eller risikerer du forlænge service hvorfor.</td>
    </tr>
</table> 

**Bemærk**: Hvis din klientkode ofte støder på et 207 svar, en mulig årsag er, at systemet er belastning. Du kan bekræfte dette ved at markere den `statusCode` egenskab for 503. Hvis det er tilfældet, anbefaler vi, ***throttling indeksering anmodninger***. Ellers hvis indeksering trafik ikke subside kan systemet starte afvise alle anmodninger med 503 fejl.

Statuskode 429 angiver, at du har overskredet kvoten for antallet af dokumenter i indekset. Du skal oprette et nyt indeks eller opgradere til højere kapacitet grænser.

**Eksempel:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
          "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
          "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
          "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
          "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
          "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
          "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## <a name="search-documents"></a>Søg i dokumenter

**En Søgehandling** er udstedt som en GET eller Send anmodning og angiver parametre, som giver kriterierne for at vælge tilsvarende dokumenter.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Hvornår skal jeg bruge INDLÆG i stedet for at få**

Når du bruger HTTP GET til at ringe til **søgning** API, skal du være opmærksom på, at længden af URL-anmodningen ikke må overstige 8 KB. Det er normalt nok til de fleste programmer. Nogle programmer giver dog meget store forespørgsler eller OData filterudtryk. For disse programmer er ved hjælp af HTTP POST et bedre valg fordi det giver mulighed for større filtre og forespørgsler end få. Med INDLÆG er antallet af ord eller udtryk i en forespørgsel den begrænse faktor, ikke på størrelsen af forespørgslen rå da størrelsesgrænsen anmodning om for INDLÆGGET er cirka 16 MB.

> [AZURE.NOTE] Selvom Send anmodning om størrelsesgrænsen er meget store, kan søgeforespørgsler og filterudtryk ikke vilkårligt komplekse. Se [Lucene forespørgselssyntaksen](https://msdn.microsoft.com/library/mt589323.aspx) og [OData udtrykkenes syntaks](https://msdn.microsoft.com/library/dn798921.aspx) for flere oplysninger om begrænsninger for søgning forespørgsel og Filtrer kompleksitet.

**Anmode om**

HTTPS er påkrævet for serviceanmodninger. **Søgningen** kan være opbygget ved hjælp af metoderne GET eller POST.

Anmodningen URI angiver hvilket indeks forespørgslen, i alle nye dokumenter, der svarer til parametrene. Parametre er angivet på forespørgselsstreng i forbindelse med GET-anmodninger, og i anmodningen brødteksten i forbindelse med INDLÆG anmoder om.

Som en bedste fremgangsmåde, når du opretter GET-anmodninger, skal du huske at [URL-kodning](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) bestemte forespørgselsparametre, når du ringer REST-API direkte. **Søg** handlinger omfatter dette:

- `$filter`
- `facet`
- `highlightPreTag`
- `highlightPostTag`
- `search`
- `moreLikeThis`

URL-adressen kodning anbefales kun på forespørgselsparametrene ovenfor. Hvis du ved et uheld URL-kodning på hele forespørgselsstreng (alt efter den?), vil skifte anmodninger.

Desuden er URL-adressen kodning kun nødvendigt når du ringer til REST-API direkte ved hjælp af GET. Det er nødvendigt at ingen URL-adressen kodning, når du ringer til **søgning** ved hjælp af INDLÆG, eller når du bruger [.NET klientbibliotek](https://msdn.microsoft.com/library/dn951165.aspx), der håndterer URL-adressen kodning for dig.

<a name="SearchQueryParameters"></a>
**Forespørgselsparametre**

**Søg** accepterer flere parametre, der indeholder kriterier for forespørgsler og også angive Søg funktionsmåde. Du angiver disse parametre i URL-adressen forespørgsel streng, når du ringer **søgning** via Hent, og som JSON egenskaber i anmodningsteksten, når du ringer til **søgning** via INDLÆG. Syntaksen for nogle parametre er lidt anderledes mellem få og INDLÆG. Disse forskelle er anført som det er relevant nedenfor:

`search=[string]`(valgfrit) - teksten til at søge efter. Alle `searchable` felter der skal søges som standard, medmindre `searchFields` er angivet. Når du søger `searchable` felter, søgeteksten selve tokenized, så flere betingelser kan være adskilt af mellemrum (for eksempel: `search=hello world`). For at matche en hvilken som helst ord skal bruge `*` (det kan være nyttige for boolesk filterforespørgsler). Udelader denne parameter har den samme effekt som indstille den til `*`. Du kan se [Enkel forespørgselssyntaksen](https://msdn.microsoft.com/library/dn798920.aspx) til specifikke oplysninger om syntaksen søgning.

  - **Bemærk**: resultaterne kan sommetider være overraskende, når forespørgsler `searchable` felter. Tokenizer omfatter logik for at håndtere de tilfælde, der er fælles for dansk tekst som apostroffer, kommaer i tal osv. For eksempel `search=123,456` matcher et enkelt ord 123,456 i stedet for det enkelte gældende 123 og 456, da kommaer bruges som tusinde separatorer for store tal på engelsk. Derfor det anbefales at bruge mellemrum i stedet for tegnsætning til at adskille betingelserne i den `search` parameter.

`searchMode=any|all`(valgfrit, som standard `any`) – om et eller alle af søgeordene skal matches for at tælle dokumentet som et match.

`searchFields=[string]`(valgfrit) - listen over kommaseparerede feltnavne til at søge efter den angivne tekst. Destinationsfelter skal markeres som `searchable`.

`queryType=simple|full`(valgfrit, som standard `simple`) – når indstillet til "enkel" søgning tekst fortolkes ved hjælp af en simpel forespørgselssprog, som muliggør symboler såsom +, * og "". Forespørgsler evalueres på tværs af alle søgefelterne (eller felter, der er angivet i `searchFields`) i hvert dokument som standard. Når forespørgselstypen er indstillet til `full` søgeteksten fortolkes ved hjælp af den Lucene forespørgselssprog som giver mulighed for felt-specifikke og vægtet søgninger. Se [Enkel forespørgselssyntaksen](https://msdn.microsoft.com/library/dn798920.aspx) og [Lucene forespørgselssyntaksen](https://msdn.microsoft.com/library/mt589323.aspx) for specifikke oplysninger på søgning syntaks. 
 
> [AZURE.NOTE] Interval søgning i Lucene forespørgselssprog ikke understøttes tilsidesættes til fordel for $filter, indeholder tilsvarende funktionalitet.

`moreLikeThis=[key]`(valgfrit) **Vigtige:** Denne funktion er kun tilgængelig i `2015-02-28-Preview`. Denne indstilling kan ikke bruges i en forespørgsel, der indeholder parameteren tekst Søg `search=[string]`. Den `moreLikeThis` parameter finder dokumenter, der svarer til det dokument, der er angivet af tasten dokument. Når en søgeanmodning sker med `moreLikeThis`, en liste over søgeord genereres baseret på den hyppighed, og hvor sjælden betingelserne i kildedokumentet. Disse vilkår er derefter bruges til at foretage en anmodning. Som standard indholdet af alle `searchable` felter betragtes, medmindre `searchFields` bruges til at begrænse, hvilke felter der søges i.  

`$skip=#`(valgfrit) – antallet af søgeresultaterne for at springe; Må ikke være større end 100,000. Hvis du har brug at scanne dokumenter i rækkefølge, men kan ikke bruge `$skip` på grund af denne begrænsning, bør du overveje at bruge `$orderby` på en helt bestilt nøgle og `$filter` med et område forespørgsel i stedet.

> [AZURE.NOTE] Når du ringer til **søgning** ved hjælp af INDLÆG, denne parameter har navnet `skip` i stedet for `$skip`.

`$top=#`(valgfrit) – antallet af søgeresultaterne for at hente. Dette kan bruges sammen med `$skip` at implementere klientsiden sideopdeling over søgeresultater.

> [AZURE.NOTE] Når du ringer til **søgning** ved hjælp af INDLÆG, denne parameter har navnet `top` i stedet for `$top`.

`$count=true|false`(valgfrit, som standard `false`)-angiver, om at hente det samlede antal resultater. Dette er antallet af alle dokumenter, der svarer til den `search` og `$filter` parametre, ignorerer `$top` og `$skip`. Hvis du indstiller denne værdi `true` muligvis en indflydelse på ydeevnen. Bemærk, at antallet returneres en tilnærmelse.

> [AZURE.NOTE] Når du ringer til **søgning** ved hjælp af INDLÆG, denne parameter har navnet `count` i stedet for `$count`.

`$orderby=[string]`(valgfrit) – en liste over kommaseparerede udtryk til at sortere resultaterne efter. Hvert udtryk kan være enten et feltnavn eller et opkald til den `geo.distance()` funktionen. Hvert udtryk kan efterfølges af `asc` til angivet stigende, og `desc` til at angive faldende. Standard er stigende rækkefølge. TIES opdeles ved match resultater af dokumenter. Hvis ingen `$orderby` er angivet, standardsorteringsrækkefølgen faldende efter dokument match resultat. Er der en grænse på 32 delsætninger for `$orderby`.

> [AZURE.NOTE] Når du ringer til **søgning** ved hjælp af INDLÆG, denne parameter har navnet `orderby` i stedet for `$orderby`.

`$select=[string]`(valgfrit) – en liste over kommaseparerede felter til at hente. Hvis du ikke er angivet, medtages alle felter, der er markeret som størrelse i skemaet. Du kan også direkte anmode om alle felter ved at angive denne parameter til `*`.

> [AZURE.NOTE] Når du ringer til **søgning** ved hjælp af INDLÆG, denne parameter har navnet `select` i stedet for `$select`.

`facet=[string]`(nul eller flere) - et felt til grene ved. Du kan også strengen kan indeholde parametre for at tilpasse faceting udtrykt som kommaseparerede `name:value` par. Gyldige parametre er:

- `count`(maksimal antallet af salgsstrategi vilkår standard er 10). Er der intet maksimum, men høje værdier medfører en tilsvarende ydeevnen, især hvis feltet facetteret indeholder et stort antal entydige udtryk.
  - For eksempel: `facet=category,count:5` får fem kategorierne i grene resultater.  
  - **Bemærk**: Hvis den `count` parameter er mindre end antallet af entydige udtryk, resultaterne gælder ikke nødvendigvis. Dette er på grund af den måde, faceting forespørgsler er fordelt over shards. Øge `count` generelt øges nøjagtigheden af antallet af ord, men på en ydeevne omkostning.
- `sort`(en af `count` til at sortere *faldende* efter Tæl, `-count` skal sorteres, *stigende* efter optælling viser `value` skal sorteres, *stigende* efter værdi, eller `-value` til at sortere *faldende* efter værdi)
  - For eksempel: `facet=category,count:3,sort:count` får de tre vigtigste kategorier i grene resultater i faldende rækkefølge efter antallet af dokumenter med navnene by. Eksempelvis hvis de øverste tre kategorier er Budget, Motel og luksus, og budgettet er 5 søgeresultater, Motel har 6, og luksus har 4, bliver derefter grupperne i rækkefølgen, Motel, Budget, luksus.
  - For eksempel: `facet=rating,sort:-value` giver filsæt for alle mulige bedømmelser i faldende rækkefølge efter værdi. Eksempelvis hvis bedømmelser er fra 1 til 5, sorteres grupperne 5, 4, 3, 2, 1, uanset hvor mange dokumenter svarer til hver rangering.
- `values`(pipe-afgrænset numerisk eller `Edm.DateTimeOffset` værdier, der angiver et dynamisk sæt af salgsstrategi posten værdier)
  - For eksempel: `facet=baseRate,values:10|20` giver tre filsæt: én til grundlæggende rente 0 op til, men ikke inklusive 10, én til 10 op til, men ikke inklusive 20 og én til 20 eller nyere.
  - For eksempel: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` giver to filsæt: én for hoteller renoverede før februar 2010 og én til hoteller renoverede februar 1st, 2010 eller nyere.
- `interval`(heltal interval større end 0 for tal, eller `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` for dato, klokkeslæt-værdier)
  - For eksempel: `facet=baseRate,interval:100` giver filsæt baseret på grundlæggende rente områder af størrelse 100. Eksempelvis hvis basis satser er alle mellem $60 og $600, vil der være filsæt til 0-100, 100-200, 200-300, 300-400, 400-500 og 500-600.
  - For eksempel: `facet=lastRenovationDate,interval:year` giver en Malerbøtte årlige når hoteller blev renoverede.
- `timeoffset`([+-] TT, [+-] TTMM, eller [+-] [hh) `timeoffset` er valgfrit. Det kan kombineres med den `interval` indstilling, og kun hvis de anvendes på et felt af typen `Edm.DateTimeOffset`. Værdien, der angiver tidsforskydning UTC kontoen for i at tid grænser.
  - For eksempel: `facet=lastRenovationDate,interval:day,timeoffset:-01:00` bruger i dag rammen, der starter på 01:00:00 UTC (midnat i tidszone for mål)
- **Bemærk**: `count` og `sort` kan kombineres i samme grene specifikationen, men de kan ikke kombineres med `interval` eller `values`, og `interval` og `values` kan ikke kombineres sammen.
- **Bemærk**: Interval facetter på dato klokkeslæt er beregnes baseret på UTC tid, hvis `timeoffset` er ikke angivet. For eksempel: for `facet=lastRenovationDate,interval:day`, den dag, rammen starter ved 00:00:00 UTC. 

> [AZURE.NOTE] Når du ringer til **søgning** ved hjælp af INDLÆG, denne parameter har navnet `facets` i stedet for `facet`. Du også angive den som en JSON matrix af strenge, hvor de enkelte strenge er et separat grene udtryk.

`$filter=[string]`(valgfrit) – et strukturerede søgeudtryk i OData standardsyntaksen. Du kan finde oplysninger på undersæt af OData udtryk grammatikkontrol, der understøtter Azure søgning i [OData udtrykkenes syntaks](#ODataExpressionSyntax) .

> [AZURE.NOTE] Når du ringer til **søgning** ved hjælp af INDLÆG, denne parameter har navnet `filter` i stedet for `$filter`.

`highlight=[string]`(valgfrit) – et sæt kommaseparerede feltnavne bruges til forekomst fremhæves. Kun `searchable` felter kan bruges til besøg fremhævning.

`highlightPreTag=[string]`(valgfrit, som standard `<em>`) – et mærke, som føjer for at der påløber vigtige punkter i en streng. Skal være angivet med `highlightPostTag`.

> [AZURE.NOTE] Når ringe **søgning** ved hjælp af GET, reserverede tegn i URL-adressen skal procent-kodes (for eksempel, i stedet for # % 23).

`highlightPostTag=[string]`(valgfrit, som standard `</em>`)-mærket streng, der tilføjer for at der påløber vigtige punkter. Skal være angivet med `highlightPreTag`.

> [AZURE.NOTE] Når ringe **søgning** ved hjælp af GET, reserverede tegn i URL-adressen skal procent-kodes (for eksempel, i stedet for # % 23).

`scoringProfile=[string]`(valgfrit) - navnet på en vurdering profil for at evaluere svare pointtal efter tilsvarende dokumenter for at sortere resultaterne.

`scoringParameter=[string]`(nul eller flere) - angiver værdier for hver parameter, der er defineret i en vurdering funktion (for eksempel `referencePointParameter`) ved hjælp af formatet `name-value1,value2,...`.

- Eksempelvis hvis profilen vurdering definerer en funktion med en parameter ved navn "mylocation" indstillingen forespørgsel streng ville være `&scoringParameter=mylocation--122.2,44.8`. Den første tankestreg adskiller navnet på værdilisten, mens den anden tankestreg er en del af den første værdi (breddegrader i dette eksempel).
- For vurdering parametre, som for mærke øger, der kan indeholde kommaer, kan du gå disse værdier på listen med enkelte anførselstegn. Hvis værdierne selv indeholder enkelte anførselstegn, kan du gå dem ved at fordoble.
  - Eksempelvis hvis du har mærket øger parameter ved navn "mytag", og du vil øge på mærket værdier "Hej, O'Brien" og "Sørensen", forespørgslen streng indstilling ville være `&scoringParameter=mytag-'Hello, O''Brien',Smith`. Bemærk, at tilbud er kun påkrævet til værdier, der indeholder kommaer.

> [AZURE.NOTE] Når du ringer til **søgning** ved hjælp af INDLÆG, denne parameter har navnet `scoringParameters` i stedet for `scoringParameter`. Du også angive den som en JSON matrix af strenge, hvor de enkelte strenge er en separat `name-values` par.

`minimumCoverage`(valgfrit, som standard til 100)-et tal mellem 0 og 100, der angiver procentdelen af det indeks, skal være dækket af en søgeforespørgsel i rækkefølge for forespørgslen skal rapporteres som et gunstigt udfald. Som standard i hele indekset skal være tilgængelige eller `Search` vil returnere en HTTP-statuskode 503. Hvis du angiver `minimumCoverage` og `Search` lykkes, vil returnere HTTP 200 og medtage en `@search.coverage` værdi i det svar, der angiver procentdelen af det indeks, der er medtaget i forespørgslen.

> [AZURE.NOTE] Hvis denne parameter til en værdi, der er mindre end 100 kan være nyttige til at sikre Søg i tilgængelighed selv for tjenester med kun én replika. Ikke alle tilsvarende dokumenter er dog garanti for at være til stede i søgeresultaterne. Hvis Søg tilbagekaldelsen er vigtigere i dit program end tilgængelighed og derefter er det bedst at lade `minimumCoverage` til standardværdien på 100.

`api-version=[string]`(påkrævet). Den foreløbige version er `api-version=2015-02-28-Preview`. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) til detaljer og andre versioner.

Bemærk: For denne handling, den `api-version` er angivet som en forespørgselsparameter i URL-adressen uanset om du ringe til **søgning** med GET eller POST.

**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning overskrifterne.

- `api-key`: Det `api-key` bruges til at godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din URL-adressen. **Søgningen** kan angive enten en administrator nøgle eller forespørgsel nøgle til `api-key`.

Du skal også navnet på tjenesten at opbygge URL-anmodningen. Du kan finde navnet på tjenesten og `api-key` fra dashboardet tjeneste i portalen Azure. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

**Anmodningsteksten**

For få: ingen.

For INDLÆG:

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**Forlængelse af Delsøgning svar**

Nogle gange kan ikke Azure søgningen returnere alle de ønskede resultater i et enkelt Søg svar. Dette kan ske af forskellige årsager, som når forespørgslen beder om for mange dokumenter ved at angive ikke `$top` eller angive en værdi for `$top` , der er for stor. I så fald Azure søgningen omfatter den `@odata.nextLink` anmærkning i brødteksten svar samt `@search.nextPageParameters` Hvis den er en POST-anmodning. Du kan bruge værdierne i disse anmærkninger udforme en anden søgeanmodning at finde den næste del af Søg svaret. Dette kaldes en ***fortsættelse*** af den oprindelige søgeanmodning, og anmærkningerne kaldes Generelt ***fortsættelse tokens***. Se [eksemplet nedenfor](#SearchResponse) for oplysninger om syntaksen i disse anmærkninger, og hvor de vises i brødteksten svar. 

Hvorfor Azure søgning kan returnere fortsættelse tokens grundene er implementeringsspecifik og kan ændres. Robust klienter skal altid være klar til at håndtere tilfælde, hvor der skal returneres, dokumenter, der er færre end forventet, og et fortsættelse token er inkluderet for at fortsætte med at hente dokumenter. Bemærk også, skal du bruge metoden samme HTTP som den oprindelige forespørgsel for at fortsætte. Eksempelvis hvis du har sendt en GET-anmodning, du sender for anmodninger skal også bruge få (og på samme måde for INDLÆG).

<a name="SearchResponse"></a>
**Svar**

Statuskode: 200 OK returneres for en vellykket svar.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**Eksempler:**

Du kan finde flere eksempler på siden [OData udtrykkenes syntaks til Azure søgning](https://msdn.microsoft.com/library/azure/dn798921.aspx) .

1)  Søge i indekset, sorteret faldende efter dato.


    Hent /indexes/hotels/docs? Søg = * & $orderby = lastRenovationDate desc & api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "*", "SorterEfter": "lastRenovationDate desc"}

2)  Søge i indekset og hente facetter for kategorier, bedømmelse, mærker samt elementer med baseRate i bestemte områder i en facetteret søgning:


    Hent /indexes/hotels/docs? Søg = test & grene = kategori & grene = bedømmelse & grene = mærker og grene = baseRate, værdier: 80 | 150 | 220 & api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "test", "facetter": ["kategori", "bedømmelse", "koder", "baseRate, værdier: 80 | 150 | 220"]}

3)  Ved hjælp af et filter, indskrænke forrige facetteret forespørgselsresultaterne, når brugeren klikker på vurdering af 3 og kategori "Motel":


    Hent /indexes/hotels/docs? Søg = test & grene = mærker og grene = baseRate, værdier: 80 | 150 | 220 & $filter = bedømmelse eq 3 og kategori eq 'Motel' & api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "test", "facetter": ["koder", "baseRate, værdier: 80 | 150 | 220"], "filter": "bedømmelse eq 3 og kategori eq 'Motel'"}

4) Angive en øvre grænse på entydige betingelser, der returneres i en forespørgsel i en facetteret søgning. Standard er 10, men du kan øge eller mindske denne værdi ved hjælp af den `count` parameter på den `facet` attribut:


    Hent /indexes/hotels/docs? Søg = test & grene = by, antal: 5 & api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "test", "facetter": ["by, antal: 5"]}

5)  Søge i indekset i bestemte felter For eksempel en sprogspecifikke felt:


    Hent /indexes/hotels/docs? Søg = hôtel & searchFields = description_fr & api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "hôtel", "searchFields": "description_fr"}

6) Søge i indekset på tværs af flere felter. Du kan for eksempel opbevare og forespørgsel søgefelterne på flere sprog, alt sammen i det samme indeks.  Hvis der findes samtidig engelsk og fransk beskrivelser i det samme dokument, kan du vende tilbage et eller alle i forespørgselsresultaterne:


    Hent /indexes/hotels/docs? Søg = hotel & searchFields = beskrivelse, description_fr & api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "hotel", "searchFields": "beskrivelse, description_fr"}

Bemærk, at du kan kun forespørge ét indeks ad gangen. Kan ikke oprette flere indekser for de enkelte sprog, medmindre du planlægger at forespørge én ad gangen.

7)  Sideinddeling – Hent siden 1st elementer (sidestørrelse er 10):


    Hent /indexes/hotels/docs? Søg = * & $skip = 0 & $top = 10 & api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "*", "springe": 0, "øverste": 10}

8)  Sideinddeling – Hent siden 2. elementer (sidestørrelse er 10):


    Hent /indexes/hotels/docs? Søg = * & $skip = 10 & $top = 10 & api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "*", "springe": 10, "øverste": 10}

9)  Hente et bestemt sæt felter:


    Hent /indexes/hotels/docs? Søg = * & $select = hotelName, beskrivelse og api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "*", "Vælg": "hotelName, beskrivelse"}

10)  Hente dokumenter, der svarer til et bestemt filterudtryk:


    Hent /indexes/hotels/docs? $filter = (baseRate Flet 60 og baseRate lt 300) eller hotelName eq 'Smart holde' og api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"filter": "(baseRate Flet 60 og baseRate lt 300) eller hotelName eq 'Smart Hold'"}

11) Søge på indeks og retur fragmenter med besøg vigtige punkter


    Hent /indexes/hotels/docs? Søg = noget & fremhæve = beskrivelse & api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "noget", "fremhæve": "beskrivelse"}

12) Søge i indekset og returnerer dokumenter, der er sorteret fra tættere på længere væk fra en reference placering


    Hent /indexes/hotels/docs? Søg = noget & $orderby=geo.distance (placering, geography'POINT(-122.12315 47.88121) ") & api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "noget", "SorterEfter": "geo.distance (placering, geography'POINT(-122.12315 47.88121)')"}

13) Søge i indekset, hvis der er en vurdering profil kaldet "geografisk" med to afstanden vurdering funktioner, en definition af en parameter titlen "currentLocation" og en definition af en parameter ved navn "lastLocation"


    Hent /indexes/hotels/docs? Søg = noget & scoringProfile = geografisk & scoringParameter = currentLocation – 122.123,44.77233 & scoringParameter = lastLocation – 121.499,44.2113 & api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "noget", "scoringProfile": "geografisk", "scoringParameters": ["currentLocation – 122.123,44.77233", "lastLocation – 121.499,44.2113"]}

14) Finde dokumenter i indekset ved hjælp af [syntaksen for Simpel forespørgsel](https://msdn.microsoft.com/library/dn798920.aspx). Denne forespørgsel returnerer hoteller hvor søgefelterne indeholde vilkår "komfort" og "placering" men ikke "motel":


    Hent /indexes/hotels/docs? Søg = komfort + placering-motel & searchMode = all & api-version = 2015-02-28-eksempel

    INDLÆG /indexes/hotels/docs/search? api-version = 2015-02-28-eksempel {"search": "komfort + placering-motel", "searchMode": "alle"}

Bemærk brugen af `searchMode=all` ovenfor. Herunder denne parameter tilsidesætter standard af `searchMode=any`, sikrer, `-motel` betyder "AND NOT" i stedet for "Eller NOT". Uden `searchMode=all`, du får vist "Eller NOT" som udvider i stedet for begrænser søgeresultaterne, og det kan være selvmodsigende for nogle brugere.

15) Finde dokumenter i indekset ved hjælp af [lucene forespørgselssyntaksen](https://msdn.microsoft.com/library/mt589323.aspx). Denne forespørgsel returnerer hoteller, hvor kategorifeltet indeholder ordet "budget" og alle søgefelterne, der indeholder ordet "senest renoverede". Dokumenter, der indeholder ordet "senest renoverede" klassificeres højere som et resultat af udtryk boost værdi (3)

    Hent /indexes/hotels/docs?search = kategori: budget og \"senest renoverede\"^ 3 & searchMode = all & api-version = 2015-02-28-Preview & forespørgselstype = full

    INDLÆG /indexes/hotels/docs/search?api-version = 2015-02-28-eksempel {"search": "kategori: budget og \"senest renoverede\"^ 3", "forespørgselstype": "fuld", "searchMode": "alle"}

<a name="LookupAPI"></a>
## <a name="lookup-document"></a>Opslag dokument

Handlingen **Opslag dokument** henter et dokument fra Azure søgningen. Dette er nyttigt, når en bruger klikker på et bestemt søgeresultat, og du vil søge efter bestemte detaljer om dette dokument.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**Anmode om**

HTTPS er påkrævet for serviceanmodninger. **Opslag dokument** anmodningen kan være opbygget på følgende måde.

    GET /indexes/[index name]/docs/key?[query parameters]

Du kan også bruge den traditionelle OData-syntaks for vigtige opslag:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

Anmodningen URI omfatter en [indeksnavn på] og [vigtige], der angiver hvilken dokumentet for at hente fra hvilket indeks. Du kan bare ét dokument ad gangen. Bruge **funktionen Søg** til at hente flere dokumenter i en enkelt anmodning.

**Forespørgselsparametre**

`$select=[string]`(valgfrit) – en liste over kommaseparerede felter til at hente. Hvis ikke er angivet eller er angivet `*`, alle felter, der er markeret som størrelse i skemaet er inkluderet i prognosen.

`api-version=[string]`(påkrævet). Den foreløbige version er `api-version=2015-02-28-Preview`. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) til detaljer og andre versioner.

Bemærk: For denne handling, den `api-version` er angivet som en parameter i forespørgslen.

**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning overskrifterne.

- `api-key`: Det `api-key` bruges til at godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din URL-adressen. **Opslag dokument** anmodningen kan angive enten en administrator nøgle eller forespørgsel nøgle til `api-key`.

Du skal også navnet på tjenesten at opbygge URL-anmodningen. Du kan finde navnet på tjenesten og `api-key` fra dashboardet tjeneste i portalen Azure. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

**Anmodningsteksten**

Ingen.

**Svar**

Statuskode: 200 OK returneres for en vellykket svar.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Eksempel**

Opslag det dokument, der har nøgle '2'

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Opslag det dokument, der har nøgle '3' ved hjælp af OData-syntaks:

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>
## <a name="count-documents"></a>Antal dokumenter

Handlingen **Antal dokumenter** henter en optælling af antallet af dokumenter i en søgeindekset. Den `$count` syntaks er en del af OData-protokollen.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**Anmode om**

HTTPS er påkrævet for serviceanmodninger. **Antal dokumenter** anmodningen kan opbygget ved hjælp af metoden GET.

[Indeksnavn] i anmodningen URI fortæller tjenesten til at returnere en optælling af alle elementer i dokumenter samling af det angivne indeks.

`api-version=[string]`(påkrævet). Den foreløbige version er `api-version=2015-02-28-Preview`. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) til detaljer og andre versioner.

**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning overskrifterne.

- `Accept`: Denne værdi skal være angivet til `text/plain`.
- `api-key`: Det `api-key` bruges til at godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din URL-adressen. **Antal dokumenter** anmodningen kan angive enten en administrator nøgle eller forespørgsel nøgle til `api-key`.

Du skal også navnet på tjenesten at opbygge URL-anmodningen. Du kan finde navnet på tjenesten og `api-key` fra dashboardet tjeneste i portalen Azure. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

**Anmodningsteksten**

Ingen.

**Svar**

Statuskode: 200 OK returneres for en vellykket svar.

Svar brødteksten indeholder værdien Tæl som et heltal, der er formateret som almindelig tekst.

<a name="Suggestions"></a>
## <a name="suggestions"></a>Forslag

Handlingen **forslag** henter forslag baseret på delsøgning input. Det bruges typisk i søgefelter giver til Autoudfyldning forslag, som brugerne angiver søgeord.

Anmodninger om forslag rettes mod foreslå target dokumenter, så den foreslåede tekst kan gentages, hvis flere kandidat dokumenter svarer til den samme søgning, der er indtastet. Du kan bruge `$select` til at hente andre dokumentfelter (herunder tasten dokument), så du kan se, hvilke dokument fungerer som kilde for hvert forslag.

Handlingen **forslag** udstedes som en anmodning om GET eller POST.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Hvornår skal jeg bruge INDLÆG i stedet for at få**

Når du bruger HTTP GET til at ringe til **forslag** API, skal du være opmærksom på, at længden af URL-anmodningen ikke må overstige 8 KB. Det er normalt nok til de fleste programmer. Dog giver nogle programmer meget store forespørgsler, specifikt OData filterudtryk. For disse programmer er ved hjælp af HTTP POST et bedre valg fordi det giver mulighed for større filtre end få. Med INDLÆG er antallet af betingelser i et filter den begrænse faktor, ikke på størrelsen af Filterstrengen rå, da størrelsesgrænsen anmodning om for INDLÆGGET er cirka 16 MB.

> [AZURE.NOTE] Selvom Send anmodning om størrelsesgrænsen er meget store, kan filterudtryk ikke vilkårligt komplekse. Du kan få flere oplysninger om filter kompleksitet begrænsninger i [OData udtrykkenes syntaks](https://msdn.microsoft.com/library/dn798921.aspx) .

**Anmode om**

HTTPS er påkrævet for serviceanmodninger. **Forslag** anmodningen kan opbygget ved hjælp af metoderne GET eller POST.

Anmodningen URI angiver navnet på indekset for forespørgsel. Parametre, som delvist input søgeordet er angivet på forespørgselsstreng i forbindelse med GET-anmodninger, og i anmodningen brødteksten i forbindelse med INDLÆG anmoder om.

Som en bedste fremgangsmåde, når du opretter GET-anmodninger, skal du huske at [URL-kodning](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) bestemte forespørgselsparametre, når du ringer REST-API direkte. **Forslag** handlinger omfatter dette:

- `$filter`
- `highlightPreTag`
- `highlightPostTag`
- `search`

URL-adressen kodning anbefales kun på forespørgselsparametrene ovenfor. Hvis du ved et uheld URL-kodning på hele forespørgselsstreng (alt efter den?), vil skifte anmodninger.

Desuden er URL-adressen kodning kun nødvendigt når du ringer til REST-API direkte ved hjælp af GET. Det er nødvendigt at ingen URL-adressen kodning, når du ringer til **forslag** ved hjælp af INDLÆG, eller når du bruger [.NET klientbibliotek](https://msdn.microsoft.com/library/dn951165.aspx), der håndterer URL-adressen kodning for dig.

**Forespørgselsparametre**

**Forslag** accepterer flere parametre, der indeholder kriterier for forespørgsler og også angive Søg funktionsmåde. Du angiver disse parametre i URL-adressen forespørgsel streng, når du ringer **forslag** via Hent, og som JSON egenskaber i anmodningsteksten, når du ringer til **forslag** via INDLÆG. Syntaksen for nogle parametre er lidt anderledes mellem få og INDLÆG. Disse forskelle er anført som det er relevant nedenfor:

`search=[string]`-Søgeteksten kan bruge til at foreslå forespørgsler. Skal være mindst 1 tegn og højst 100 tegn.

`highlightPreTag=[string]`(valgfrit) – en streng mærke, føjer for at søge søgeresultater. Skal være angivet med `highlightPostTag`.

> [AZURE.NOTE] Når ringe **forslag** ved hjælp af GET, reserverede tegn i URL-adressen skal procent-kodes (for eksempel, i stedet for # % 23).

`highlightPostTag=[string]`(valgfrit) – en streng mærke, som tilføjer for at søge søgeresultater. Skal være angivet med `highlightPreTag`.

> [AZURE.NOTE] Når ringe **forslag** ved hjælp af GET, reserverede tegn i URL-adressen skal procent-kodes (for eksempel, i stedet for # % 23).

`suggesterName=[string]`-navnet på suggester som angivet i den `suggesters` af websteder, der er en del af indeksdefinitionen. A `suggester` bestemmer, hvilke felter scannes for foreslåede forespørgsler. Du kan finde oplysninger i [Suggesters](#Suggesters) .

`fuzzy=[boolean]`(valgfri, som standard = false)-når den er indstillet til sand denne API kan finde forslag selvom der ikke er et erstattede eller manglende tegn i søgeteksten. Mens dette giver en bedre oplevelse i visse scenarier med leveres den med en ydeevne omkostningerne uskarpt forslag søgninger er langsommere og forbruge flere ressourcer.

`searchFields=[string]`(valgfrit) - listen over kommaseparerede feltnavne til at søge efter den angivne søgeteksten. Destinationsfelter skal være aktiveret for forslag.

`$top=#`(valgfri, som standard = 5)-antallet af forslag til at hente. Skal være et tal mellem 1 og 100.

> [AZURE.NOTE] Når du ringer **forslag** ved hjælp af INDLÆG, denne parameter har navnet `top` i stedet for `$top`.

`$filter=[string]`(valgfrit) – et udtryk, der filtrerer dokumenterne, der anses for forslag.

> [AZURE.NOTE] Når du ringer **forslag** ved hjælp af INDLÆG, denne parameter har navnet `filter` i stedet for `$filter`.

`$orderby=[string]`(valgfrit) – en liste over kommaseparerede udtryk til at sortere resultaterne efter. Hvert udtryk kan være enten et feltnavn eller et opkald til den `geo.distance()` funktionen. Hvert udtryk kan efterfølges af `asc` til angivet stigende, og `desc` til at angive faldende. Standard er stigende rækkefølge. Er der en grænse på 32 delsætninger for `$orderby`.

> [AZURE.NOTE] Når du ringer **forslag** ved hjælp af INDLÆG, denne parameter har navnet `orderby` i stedet for `$orderby`.

`$select=[string]`(valgfrit) – en liste over kommaseparerede felter til at hente. Hvis du ikke er angivet, returneres kun nøgle og forslag dokumentteksten. Du kan eksplicit anmode om alle felter ved at angive denne parameter til `*`.

> [AZURE.NOTE] Når du ringer **forslag** ved hjælp af INDLÆG, denne parameter har navnet `select` i stedet for `$select`.

`minimumCoverage`(valgfrit, som standard til 80)-et tal mellem 0 og 100, der angiver procentdelen af det indeks, skal være dækket af et forslag til forespørgsel i rækkefølge for forespørgslen skal rapporteres som et gunstigt udfald. Som standard mindst 80% af indekset skal være tilgængelige eller `Suggest` vil returnere en HTTP-statuskode 503. Hvis du angiver `minimumCoverage` og `Suggest` lykkes, vil returnere HTTP 200 og medtage en `@search.coverage` værdi i det svar, der angiver procentdelen af det indeks, der er medtaget i forespørgslen.

> [AZURE.NOTE] Hvis denne parameter til en værdi, der er mindre end 100 kan være nyttige til at sikre Søg i tilgængelighed selv for tjenester med kun én replika. Ikke alle tilsvarende forslag er dog garanti for at blive medtaget i resultaterne. Hvis tilbagekaldelsen er vigtigere i dit program end tilgængelighed og derefter det anbefales ikke at sænke `minimumCoverage` under er standardværdien 80.

`api-version=[string]`(påkrævet). Den foreløbige version er `api-version=2015-02-28-Preview`. Se [Søg Service versionsstyring](http://msdn.microsoft.com/library/azure/dn864560.aspx) til detaljer og andre versioner.

Bemærk: For denne handling, den `api-version` er angivet som en forespørgselsparameter i URL-adressen uanset om du ringe til **forslag** med GET eller POST.

**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning

- `api-key`: Det `api-key` bruges til at godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din URL-adressen. **Forslag** anmodningen kan angive enten en administrator nøgle eller forespørgselsnøglen som den `api-key`.

Du skal også navnet på tjenesten at opbygge URL-anmodningen. Du kan finde navnet på tjenesten og `api-key` fra dashboardet tjeneste i portalen Azure. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

**Anmodningsteksten**

For få: ingen.

For INDLÆG:

    {
      "filter": "odata_filter_expression",
      "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
      "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**Svar**

Statuskode: 200 OK returneres for en vellykket svar.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Hvis indstillingen projicering bruges til at hente de er inkluderet i hvert element i matrixen felter:

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**Eksempel**

Hente 5 forslag, hvor delsøgning input er 'lux'

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }
