<properties
    pageTitle="Vundne profiler (Azure RESTEN API-Version 2015-02-28-visning af søgeresultater) | Microsoft Azure | Azure søgeresultater API"
    description="Azure søgning er en hostet skyen søgetjeneste, der understøtter justering af rangerede resultater, der er baseret på brugerdefinerede vurdering profiler."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.author="heidist"
    ms.date="08/29/2016" />

# <a name="scoring-profiles-azure-search-rest-api-version-2015-02-28-preview"></a>Vundne profiler (Azure RESTEN API-Version 2015-02-28-visning af søgeresultater)

> [AZURE.NOTE] I denne artikel beskrives vurdering profiler i [2015-02-28-Preview](search-api-2015-02-28-preview.md). Der er i øjeblikket ingen forskel mellem den `2015-02-28` version dokumenteret på [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx) og `2015-02-28-Preview` version er beskrevet her, men vi tilbyder dette dokument alligevel for at give dokumentet på tværs af hele API.

## <a name="overview"></a>Oversigt

Vundne refererer til beregning af Søg point for hvert element, der returneres i søgeresultater. Resultatet er en indikator for et element relevans i konteksten for den aktuelle søgning handling. Jo højere pointtal, mere relevante elementet. I søgeresultaterne er elementer rangen bestilt fra høj til lav, baseret på søgning karakteren beregnes for hvert element.

Azure søgningen anvender standard vundne for at beregne en indledende resultat, men du kan tilpasse beregningen gennem en vurdering profil. Vurdering profiler giver dig bedre kontrol over rangorden af elementer i søgeresultaterne. Du vil muligvis gavne elementer, der er baseret på deres indtægter potentiale, hæve nyere elementer eller måske gavne elementer, der har været i lageret for lang.

En vurdering profil er en del af indeksdefinitionen af, bestående af felter, funktioner og parametre.

For at give dig en ide om, hvordan en vurdering profil ser ud, viser i følgende eksempel en enkelt profil med navnet 'geografisk'. Denne øger elementer, der har søgeordet i den `hotelName` felt. I eksemplet bruges også den `distance` funktionen til at prioritere elementer, der er i ti kilometer af den aktuelle placering. Hvis en person søger på ordet 'inn' og 'inn' sker der, skal være en del af hotel navnet, vises dokumenterne, der omfatter hoteller med 'inn' højere i søgeresultaterne.

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

Hvis du vil bruge denne vurdering profil, færdiggjorte din forespørgsel for at angive profilen på strengen query. Bemærk Forespørgselsparameteren i forespørgslen nedenfor `scoringProfile=geo` i anmodningen.

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

Denne forespørgsel søger på ordet 'inn' og sender i den aktuelle placering. Bemærk, at denne forespørgsel omfatter andre parametre, som `scoringParameter`. Forespørgselsparametre er beskrevet i [Søg i dokumenter (Azure Søg API)](search-api-2015-02-28-preview.md#SearchDocs).

Klik på [eksempel](#example) for at få vist en mere detaljeret eksempel på en vurdering profil.

## <a name="what-is-default-scoring"></a>Hvad er standard vundne?

Vundne beregner en søgning point for hvert element i et plads bestilte resultatsæt. Hvert element i et resultatsæt søgning er tildelt en bogstavkarakter Søg og derefter rangerede højeste til laveste. Elementer med de højere pointtal returneres til programmet. De øverste 50 returneres som standard, men du kan bruge den `$top` parameter til at returnere en større eller mindre antal elementer (op til 1000 i et enkelt svar).

Som standard beregnes en søgning bogstavkarakter ud fra statistiske egenskaber for dataene, og forespørgslen. Azure Search finder dokumenter, som indeholder søgeordene i forespørgselsstrengen (nogle eller alle, alt efter `searchMode`), favoriserer dokumenter, der indeholder mange forekomster af søgeordet. Søg karakteren går endnu højere, hvis udtrykket er sjældne på tværs af data indekserede, men almindelige i dokumentet. Grundlaget for denne metode til databehandling relevans kaldes TF IDF eller (udtryk frekvens inverse dokument frekvens).

Hvis der ikke er nogen brugerdefineret sortering, derefter klassificeres resultater efter søgning resultat før de returneres til det program. Hvis `$top` ikke er angivet, 50 elementer, der har den højeste Søg karakteren returneres.

Du kan gentage Søg karakteren værdier i et resultatsæt. For eksempel skal du muligvis 10 elementer med et resultat af 1.2, 20 elementer med et resultat af 1.0 og 20 elementer med et resultat af 0,5. Når flere forekomster har det samme resultat af søgning, rækkefølgen af samme karakter elementer er ikke defineret og er ikke stabil. Kør forespørgslen igen, og du kan få vist elementer Skift placering. Hvis du har to elementer med en identisk resultat, er der ingen garanti, hvilken af skærmene vises først.

## <a name="when-to-use-custom-scoring"></a>Hvornår skal jeg bruge brugerdefinerede pointmodel for kundeemner

Når standard rangordne funktionsmåde ikke går langt nok i møde virksomhedens målsætninger, skal du oprette en eller flere vurdering profiler. For eksempel kan du beslutter, Hansen skal prioritere nyligt tilføjede elementer. På samme måde, kan du have et felt, der indeholder Overskudsmargen eller et andet felt, der angiver potentielle indtægter. Øger søgeresultater, der får fordele din virksomhed kan være en vigtig faktor i beslutter at bruge vurdering profiler.

Relevans-baserede rækkefølge implementeres også gennem vundne profiler. Overvej søgeresultatsider, du har brugt i tidligere, som du kan sortere efter pris, dato, bedømmelse eller relevans. I Azure Søg drive vurdering profiler indstillingen 'relevans'. Definitionen af relevans styres af du predicated på virksomhedens mål og typen af søgeoplevelse, du vil vise.

<a name="example"></a>
## <a name="example"></a>Eksempel

Som nævnt, er tilpassede vundne implementeret gennem vundne profiler, der er defineret i skemaet et indeks.

I dette eksempel vises skemaet for et indeks med to vurdering profiler (`boostGenre`, `newAndHighlyRated`). Angive en forespørgsel i forhold til dette indeks, der indeholder enten profil, som anvender en forespørgselsparameter profilen til at give resultatet.

[Prøv dette eksempel](search-get-started-scoring-profiles.md).

    {
      "name": "musicstoreindex",
      "fields": [
        { "name": "key", "type": "Edm.String", "key": true },
        { "name": "albumTitle", "type": "Edm.String" },
        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
        { "name": "genre", "type": "Edm.String" },
        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
        { "name": "artistName", "type": "Edm.String" },
        { "name": "orderableOnline", "type": "Edm.Boolean" },
        { "name": "rating", "type": "Edm.Int32" },
        { "name": "tags", "type": "Collection(Edm.String)" },
        { "name": "price", "type": "Edm.Double", "filterable": false },
        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
        { "name": "inventory", "type": "Edm.Int32" },
        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
      ],
      "scoringProfiles": [
        {
          "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1.5,
              "genre": 5,
              "artistName": 2
            }
          }
        },
        {
          "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## <a name="workflow"></a>Arbejdsproces

Tilføj en vurdering profil for at implementere en brugerdefineret vurdering funktionsmåde, til skemaet, der definerer indekset. Du kan have flere vurdering profiler i et indeks, men du kan kun angive én profil på tid i en given forespørgsel.

Start med den [skabelon](#bkmk_template) , der er angivet i dette emne.

Angiv et navn. Vurdering profiler er valgfrit, men hvis du føjer en, navnet er påkrævet. Sørg for at følge de navngivningskonventioner for felter (starter med et bogstav, undgår specialtegn og reserverede ord). Du kan få flere oplysninger i [Navngivning af regler](http://msdn.microsoft.com/library/azure/dn857353.aspx) .

Brødteksten i profilen resultat er opbygget fra vægtet felter og funktioner.

### <a name="weights"></a>Tykkelser ###

Den `weights` egenskab for en vurdering profil angiver navn / værdi-par, tildele en relative vægt til et felt. I [eksemplet](#example)er felterne albumTitle, genre og artistName øget 1,5, 5 og 2, henholdsvis. Hvorfor er genre øget så meget højere end de andre? Hvis søgningen udføres over data, der er noget homogent (som det er tilfældet med 'genre' i den `musicstoreindex`), du skal muligvis en større afvigelse i de relative tykkelser. For eksempel i den `musicstoreindex`, 'japanske' vises som både en genre og identisk formuleres genre beskrivelser. Hvis du vil genre til opveje genre beskrivelse, skal feltet genre en meget højere relative vægt.

### <a name="functions"></a>Funktioner ###

Når yderligere beregninger, der kræves til bestemte kontekster bruges funktionerne. Gyldige funktionstyper findes `freshness`, `magnitude`, `distance` og `tag`. Hver funktion har parametre, der er entydige for den.

  - `freshness`skal bruges, når du vil øge efter hvor nye og gamle et element er. Denne funktion kan kun bruges med datetime-felter (`Edm.DataTimeOffset`). Bemærk de `boostingDuration` attribut bruges kun sammen med funktionen friskhed.
  - `magnitude`skal bruges, når du vil øge baseret på hvordan høj eller lav er en numerisk værdi. Scenarier, der kalder for denne funktion omfatter øger ved Overskudsmargen, højeste kurs, laveste pris eller en optælling af overførsler. Du kan tilbageføre det område, høj til lav, hvis du vil have det inverse mønster (for eksempel til boost billigere varer mere end højere pris elementer). Givet et område med priser fra 100 til $1, skal du angive `boostingRangeStart` på 100 og `boostingRangeEnd` på 1 for at øge elementerne billigere. Denne funktion kan kun bruges med dobbelt og heltal felter.
  - `distance`skal bruges, når du vil øge ved afstand eller geografiske placering. Denne funktion kan kun bruges med `Edm.GeographyPoint` felter.
  - `tag`skal bruges, når du vil øge ved mærker fælles mellem dokumenter og søgeforespørgsler. Denne funktion kan kun bruges med `Edm.String` og `Collection(Edm.String)` felter.
  
#### <a name="rules-for-using-functions"></a>Regler for brug af funktioner ####

  - Funktionstype (friskhed omfanget afstand, og tag) skal være små bogstaver.
  - Funktioner må ikke indeholde null-værdier eller tomme værdier. Specifikt, hvis du medtager feltnavn, skal du angive den til noget.
  - Funktioner kan kun anvendes til filtrerbare felter. Du kan finde flere oplysninger om filtrerbare felter i [Create Index](search-api-2015-02-28-preview.md#CreateIndex) .
  - Funktioner kan kun anvendes på felter, der er defineret i samlingen felter i et indeks.

Når indekset er defineret, kan du bygge indekset ved at uploade indeks skemaet, efterfulgt af dokumenter. Se [Create Index](search-api-2015-02-28-preview.md#CreateIndex) og [Tilføj eller Opdater dokumenter](search-api-2015-02-28-preview.md#AddOrUpdateDocuments) yderligere oplysninger om disse handlinger. Når indekset er oprettet, bør du have en funktionelle vurdering profil, der fungerer sammen med din søgning data.

<a name="bkmk_template"></a>
## <a name="template"></a>Skabelon
I dette afsnit vises syntaksen og skabelon til vundne profiler. Referere til [indeks attributten reference](#bkmk_indexref) i næste afsnit for beskrivelser af attributterne.

    ...
    "scoringProfiles": [
      {
        "name": "name of scoring profile",
        "text": (optional, only applies to searchable fields) {
          "weights": {
            "searchable_field_name": relative_weight_value (positive #'s),
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
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>
## <a name="scoring-profile-property-reference"></a>Vundne profil egenskaben reference

**Bemærk!** En vurdering funktion kan kun anvendes på felter, der er filtrerbare.

| Egenskaben | Beskrivelse |
|----------|-------------|
| `name`   | Der er påkrævet. Dette er navnet på den vurdering profil. Det følger de samme navngivningskonventioner for et felt. Det skal starte med et bogstav, må ikke indeholde prikker, koloner eller @ symboler, og ikke kan starte med ordet "azureSearch" (store og små bogstaver). |
| `text` | Indeholder egenskaben tykkelser. |
| `weights` | Valgfrit. Et navn / værdi-par, der angiver et feltnavn og relative vægt. Relative vægt skal være et positivt heltal eller flydende tal. Du kan angive feltnavnet uden en tilhørende vægt. Tykkelser bruges til at angive vigtigheden af et felt i forhold til en anden. |
| `functions` | Valgfrit. Bemærk, at en vurdering funktion kan kun anvendes på felter, der er filtrerbare. |
| `type` | Kræves til vundne funktioner. Angiver typen af funktionen til at bruge. Gyldige værdier omfatter `magnitude`, `freshness`, `distance` og `tag`. Du kan medtage mere end én funktion i hver vurdering profil. Funktionens navn skal være små bogstaver. |
| `boost` | Kræves til vundne funktioner. Et positivt tal, der bruges som multiplikator for råt resultat. Det kan være lig med 1. |
| `fieldName` | Kræves til vundne funktioner. En vurdering funktion kan kun anvendes på felter, der er en del af feltsamlingen i indekset, og som er filtrerbare. Desuden introducerer hver funktionstype yderligere begrænsninger (friskhed bruges med datetime-felter, omfanget med heltal eller dobbelt felter, afstand med placering felter og mærke med streng eller streng samling felter). Du kan kun angive et enkelt felt i funktionen definition. For eksempel for at bruge omfanget to gange i den samme profil, skal du omfatter to definitioner omfanget, én for hvert felt. |
| `interpolation` | Kræves til vundne funktioner. Definerer hældningen, karakteren øger øges fra starten af området til slutningen af området. Gyldige værdier omfatter `linear` (standard), `constant`, `quadratic`, og `logarithmic`. Se [angive interpolations](#bkmk_interpolation) få mere at vide. |
| `magnitude` | Omfanget vundne funktionen bruges til at ændre bedømmelser baseret på området af værdier for et numerisk felt. Nogle af de mest almindelige brugen eksempler på dette er:<ul><li>Stjerneklassifikationer: ændre vundne baseret på værdien i feltet "Stjerne vurdering". Når to elementer er relevante, vises elementet med den nyere vurdering først.</li><li>Margen: Når to dokumenter det er relevant, en forhandler kan ønske at gavne dokumenter, som har højere margener først.</li><li>Klik på tæller: programmer, som følge skal du klikke gennem handlinger til produkter eller sider, du kan bruge omfang, at den boost elementer, der har tendens til at få mest muligt trafik.</li><li>Hente tæller: programmer, Registrer overførsler omfanget funktionen kan du øge elementer, der har mest overførsler.</li></ul> |
| `magnitude:boostingRangeStart` | Angiver startværdien i det område, hvor omfanget karakter. Værdien skal være et heltal eller flydende tal. Det vil sige 1 for stjerneklassifikationer fra 1 til 4. Det vil sige 50 for margener over 50%. |
| `magnitude:boostingRangeEnd` | Angiver det område, hvor omfanget karakter slutværdi. Værdien skal være et heltal eller flydende tal. Det vil sige 4 til stjerneklassifikationer fra 1 til 4. |
| `magnitude:constantBoostBeyondRange` | Gyldige værdier er SAND eller FALSK (standard). Når den er indstillet SAND, fortsætter med den fulde boost skal gælde for dokumenter, der har en værdi i destinationsfeltet, der er højere end den øverste ende af området. Hvis falsk, kan ikke boost med denne funktion anvendes på dokumenter med en værdi for destinationsfeltet, der falder uden for området. |
| `freshness` | Friskhed vundne funktionen bruges til at ændre rangering pointtal for elementer, der er baseret på værdierne i DateTimeOffset felter. Et element med en senere dato kan for eksempel være rangerer højere end ældre elementer. (Bemærk, at det er også muligt at plads elementer som kalenderbegivenheder med fremtidige datoer så elementer tættere på dags kan være rangerer højere end elementer yderligere i fremtiden). I den aktuelle udgave af tjenesten løses ene ende af området til det aktuelle klokkeslæt. Anden ende er et tidligere baseret på den `boostingDuration`. For at øge et område i fremtiden gange bruge et negativt `boostingDuration`. Den hastighed, som den øger ændringer fra maksimalt og mindste område bestemmes af interpolering gælder for den vurdering profil (se figuren nedenfor). Hvis du vil spejlvende den at faktor, der er anvendt, skal du vælge en boost faktor på mindre end 1. |
| `freshness:boostingDuration` | Sæt en udløbsperiode efter hvilket øger stopper for et bestemt dokument. Se [sæt boostingDuration] [#bkmk_boostdur] i følgende afsnit for syntaks og eksempler. |
| `distance` | Afstanden vurdering funktion bruges til at påvirke resultatet af dokumenter, der er baseret på, hvordan Luk eller langt de er i forhold til en reference geografiske placering. Reference placering er angivet som en del af forespørgslen i en parameter (ved hjælp af den `scoringParameter` forespørgsel parameter) som en lon, lat argument. |
| `distance:referencePointParameter` | En parameter, der skal overføres i forespørgsler til at bruge som reference placering. scoringParameter er en parameter i forespørgslen. Du kan finde beskrivelser af forespørgselsparametre i [Søg i dokumenter](search-api-2015-02-28-preview.md#SearchDocs) . |
| `distance:boostingDistance` | Et tal, der angiver afstanden i kilometer fra den reference placering, hvor at området slutter. |
| `tag` | Mærket vundne funktionen bruges til at påvirke resultatet af dokumenter baseret på mærker i dokumenter og søgeforespørgsler. Dokumenter, der har mærker til fælles med søgestrengen vil øget. Mærker til søgestrengen leveres som en vurdering parameter i hver søgeanmodning (ved hjælp af den `scoringParameter` forespørgsel parameter). |
| `tag:tagsParameter` | En parameter, der skal overføres i forespørgsler til at angive koder til en bestemt anmodning. `scoringParameter`er en parameter i forespørgslen. Du kan finde beskrivelser af forespørgselsparametre i [Søg i dokumenter](search-api-2015-02-28-preview.md#SearchDocs) . |
| `functionAggregation` | Valgfrit. Gælder kun, når funktioner er angivet. Gyldige værdier omfatter: `sum` (standard), `average`, `minimum`, `maximum`, og `firstMatching`. Et Søg resultat er en enkelt værdi, der er beregnet fra flere variabler, herunder flere funktioner. Denne attributter angiver, hvordan øger af alle funktionerne kombineres til en enkelt samling boost, derefter anvendes på det grundlæggende dokument resultat. Det grundlæggende resultat er baseret på værdien tf idf beregnes fra dokumentet og søgestrengen. |
| `defaultScoringProfile` | Når du kører en søgeanmodning, hvis ingen vurdering profil ikke er angivet, er standard vundne derefter anvendte (tf-idf kun). Standard vundne profilnavn kan angives her, forårsager Azure Søg til at bruge profilen, når der ingen bestemt profil ikke er angivet i søgningen. |

<a name="bkmk_interpolation"></a>
## <a name="set-interpolations"></a>Angiv interpolations

Interpolations kan du angive hældningen, karakteren øger øges fra starten af området til slutningen af området. De følgende interpolations kan bruges:

  - `Linear`: For elementer, der er inden for området Maks og min, vil blive udført boost anvendes på elementet i en konstant faldende mængde. Lineær er standard interpolering for en vurdering profil.
  - `Constant`: For elementer, der er inden for start og det sidste område, anvendes en konstant boost på plads resultaterne.
  - `Quadratic`: Kvadratisk formindskes indledningsvis i mindre tempo sammenlignet med en lineær interpolering, der indeholder en konstant faldende boost, og derefter som den nærmer sig området slutningen, reduceres det til et meget højere interval. Denne indstilling for interpolering er ikke tilladt i mærke vundne funktioner.
  - `Logarithmic`: Logaritmisk formindskes indledningsvis i højere tempo sammenlignet med en lineær interpolering, der indeholder en konstant faldende boost, og derefter som den nærmer sig området slutningen, reduceres det med meget mindre intervaller. Denne indstilling for interpolering er ikke tilladt i mærke vundne funktioner.

<a name="Figure1"></a>
 ![][1]

<a name="bkmk_boostdur"></a>
## <a name="set-boostingduration"></a>Angiv boostingDuration

`boostingDuration`er en attribut af funktionen friskhed. Du kan bruge det til at angive en udløbsdato punktum efter hvilket øger stopper for et bestemt dokument. Hvis du vil øge et produktlinje eller et bestemt mærke i 10-salgsfremmende dage, angiver du i 10-dages periode som "P10D" for disse dokumenter. Eller til at øge kommende begivenheder i den kommende uge angive "-P7D".

`boostingDuration`skal være formateret som en XSD "dayTimeDuration" værdi (et begrænset undersæt af en ISO 8601 varighedsværdi). Mønsteret for dette er: `[-]P[nD][T[nH][nM][nS]]`.

Den følgende tabel indeholder flere eksempler.

| Varighed | boostingDuration |
|----------|------------------|
| 1 dag | "P1D" |
| 2 dage og 12 timer | "P2DT12H" |
| 15 minutter | "PT15M" |
| 30 dage, 5 timer, 10 minutter, og 6.334 sekunder | "P30DT5H10M6.334S" |

Se flere eksempler, under [XML-skema: datatyper (W3.org websted)](http://www.w3.org/TR/xmlschema11-2/).

**Se også**
[Azure Search-tjenesten REST-API](http://msdn.microsoft.com/library/azure/dn798935.aspx) på MSDN <br/>
[Oprette indeks (Azure Søg API)](http://msdn.microsoft.com/library/azure/dn798941.aspx) på MSDN<br/>
[Tilføj en vurdering profil til en søgeindeks](http://msdn.microsoft.com/library/azure/dn798928.aspx) på MSDN<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png
