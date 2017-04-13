<properties
    pageTitle="Opret typer og -Model kvalitet: anbefalinger API | Microsoft Azure"
    description="Azure Machine Learning anbefalinger – guiden Hurtig start"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="luisca"/>

#  <a name="build-types-and-model-quality"></a>Opbygge typer og -model kvalitet #

<a name="TypeofBuilds"></a>
## <a name="supported-build-types"></a>Understøttede build typer ##

Anbefalinger API understøtter i øjeblikket to typer af build: *anbefaling* og *FBT*. Hver er oprettet ved hjælp af forskellige algoritmer, og de har forskellige styrker. I dette dokument beskrives hver af disse builds og teknikker, så sammenligning kvaliteten af de modeller, der er oprettet.

Hvis du allerede ikke har gjort det, anbefaler vi, at du har fuldført den [Startvejledning](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### <a name="recommendation-build-type"></a>Anbefaling build type ###

Typen anbefaling build bruger matrix factorization til at give anbefalinger. Den genererer [latente funktion](https://en.wikipedia.org/wiki/Latent_variable) vektorer baseret på dine transaktioner til at beskrive hvert element, og derefter bruges disse latente vektorer til at sammenligne elementer, der ligner hinanden.

Hvis du oplære den model, der er baseret på køb foretaget i din electronics store og angiver en Lumia 650 telefon som input til modellen, returnerer modellen et sæt af elementer, der er tendens til købes af personer, som har sandsynligvis at købe en Lumia 650 telefon. Elementerne, muligvis ikke komplementær. I dette eksempel er det muligt, at modellen vil returnere andre telefoner, fordi de personer, der som Lumia 650 kan have andre telefoner.

Opret anbefaling har to funktioner, som gør det flot:

* *Opret anbefaling understøtter *kolde element* placeringen **

Elementer, der ikke har betydeligt brugen kaldes kolde elementer. Eksempelvis hvis du modtager en overførsel af en telefon, du aldrig har solgt før, kan ikke systemet udlede anbefalinger til dette produkt på transaktioner alene. Det betyder, at systemet skal lære af oplysninger om selve produktet.

Hvis du vil bruge kolde element placering, skal du angive oplysninger om funktioner for hver af dine elementer i kataloget. Følgende er, hvad de første par linjer af dit katalog kan se ud som (note key = værdiformat til funktionerne).

>Pro2 6CX 00001, grundflade-, grundflade, Skriv = Hardware lagerplads = 128 GB hukommelse = 4G, producenten = Microsoft

>73H-00013, følge Xbox 360, Gaming,, Type = softwaren, sprog = engelsk, vurdering af ansøger = fuldt

>WAH 0F05, Minecraft Xbox 360, Gaming,, * Type = softwaren, sprog = spansk, vurdering af ansøger = unge

Du skal også angive følgende build parametre:

| Opbygge parameter         | Noter
|------------------     |-----------
|*useFeaturesInModel*     | Indstillet til **Sand**.  Angiver, hvis funktioner kan bruges til at forbedre anbefaling modellen.
|*allowColdItemPlacement*   | Indstillet til **Sand**. Angiver, hvis anbefalingen også skal push kolde elementer via funktionen lighed.
| *modelingFeatureList*   | Kommasepareret liste over funktionsnavne skal bruges i anbefaling Opret til at forbedre anbefalingen. Eksempelvis "sprog, lagerplads" for det foregående eksempel.

**Opret anbefaling understøtter bruger anbefalinger**

Et anbefaling build understøtter [bruger anbefalinger](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Det betyder, at det kan give personlige anbefalinger til brugere, der er baseret på deres transaktionsoversigter for. Bruger anbefalinger, kan du angive bruger-ID eller seneste oversigten over transaktioner for den pågældende bruger.

En klassisk eksempel på, hvor du muligvis vil anvende bruger anbefalinger er i logon på siden Velkommen. Der kan du opgradere indhold, der gælder for den specifikke bruger.

Du kan også anvende en anbefalinger build type når brugeren er ved at tjekke ud. På det pågældende tidspunkt, du har på listen over elementer, brugeren er ved at købe, og du kan angive anbefalinger baseret på den aktuelle marked indkøbskurv.

#### <a name="recommendations-build-parameters"></a>Anbefalinger opbygge parametre

| Navn  |   Beskrivelse |    Type <br>  gyldige værdier <br> (standardværdien)
|-------|-------------------|------------------
| *NumberOfModelIterations* |   Antallet af gentagelser modellen udfører afspejles ved den overordnede Beregn tid og model nøjagtigheden. Jo højere tal, jo mere nøjagtig modellen, men Beregn tid tager længere tid.  |   Heltal, <br>  10 til 50 <br>(40)
| *NumberOfModelDimensions* |   Antallet af dimensioner, der relaterer til antallet af funktioner modellen forsøger at finde inden for dine data. Øge antallet af dimensioner, så bedre finjustere af resultater i mindre klynger. Dog forhindrer for mange dimensioner modellen i finde korrelationer mellem elementerne. |  Heltal, <br> 10-40 <br>(20) |
| *ItemCutOffLowerBound* |  Definerer det mindste antal brugen punkter skal være et element i for at det betragtes som del af modellen. |        Heltal, <br> 2 eller mere <br> (2) |
| *ItemCutOffUpperBound* |  Definerer det maksimale antal brugen punkter skal være et element i for at det betragtes som del af modellen. |  Heltal, <br>2 eller mere<br> (2147483647) |
|*UserCutOffLowerBound* |   Definerer det mindste antal transaktioner, en bruger skal have udført for at betragtes som del af modellen. | Heltal, <br> 2 eller mere <br> (2)
| *UserCutOffUpperBound* |  Definerer det maksimale antal transaktioner, en bruger skal have udført for at betragtes som del af modellen. | Heltal, <br>2 eller mere <br> (2147483647)|
| *UseFeaturesInModel* |    Angiver, hvis funktioner kan bruges til at forbedre anbefaling modellen. |     Boolesk værdi<br> Som standard: SAND
|*ModelingFeatureList* |    Kommasepareret liste over funktionsnavne skal bruges i anbefaling Opret til at forbedre anbefalingen. Det afhænger af de funktioner, der er vigtige. |    Streng, op til 512 tegn
| *AllowColdItemPlacement* |    Angiver, hvis anbefalingen også skal push kolde elementer via funktionen lighed. | Boolesk værdi <br> Som standard: falsk
| *EnableFeatureCorrelation*    | Angiver, hvis funktioner kan bruges i Kognitiv. | Boolesk værdi <br> Som standard: falsk
| *ReasoningFeatureList* |  Kommasepareret liste over funktionsnavne skal bruges til reasoning sætninger, som anbefaling forklaringer. Det afhænger af de funktioner, der er vigtige for kunder. | Streng, op til 512 tegn
| *EnableU2I* | Aktivere tilpassede anbefalinger, også kaldet bruger for at elementet (U2I) anbefalinger. | Boolesk værdi <br>Som standard: SAND
|*EnableModelingInsights* | Definerer, om der skal udføres offline evaluering indsamle modellering indsigt (det vil sige, præcision og forskellige metrisk). Hvis indstilles til sand, et undersæt af data, der ikke skal bruges til uddannelse, fordi den skal være reserveret til test af modellen. Læs mere om [offline evaluering](#OfflineEvaluation). | Boolesk værdi <br> Som standard: falsk
| *SplitterStrategy* | Hvis Aktivér modeling indsigt er indstillet til *Sand*, er dette, hvordan data skal opdeles til evalueringsformål.  | Streng, *RandomSplitter* eller *LastEventSplitter* <br>Som standard: RandomSplitter


<a name="FBTBuild"></a>
### <a name="fbt-build-type"></a>FBT build type ###

Ofte indkøbte sammen (FBT) Opret understøtter en analyse, der tæller antallet gange to eller tre forskellige produkter forekomme samtidig sammen. Det sorterer sæt baseret på en lighed funktion (**samtidig forekomster**, **Jaccard** **løft**).

Tænk på **Jaccard** og **løfte** som måder at normalisere samtidig forekomster.  Det betyder, at elementerne, der returneres kun, hvis de hvor købt sammen med elementet frø.

I eksemplet Lumia 650 telefon returneres telefon X kun, hvis telefon X blev købt i den samme session som Lumia 650 telefonen. Da dette kan være sandsynlighed, forventer vi elementer komplementære til Lumia 650 skal returneres. for eksempel en skærmen beskytter, eller en power-adapter til Lumia 650.

To elementer er i øjeblikket, forudsættes købes i samme session, hvis de forekommer i en transaktion med samme bruger-ID og tidsstempel.

FBT builds understøtter ikke kolde elementer, fordi ved definition de forventer to elementer, der skal købes i samme transaktion. Mens FBT builds kan returnere sæt af elementer (gange), understøtter de ikke tilpassede anbefalinger, fordi de accepterer et enkelt frø element som input.


#### <a name="fbt-build-parameters"></a>FBT build parametre

| Navn  |   Beskrivelse |       Type  <br> gyldige værdier <br> (standardværdien)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | Hvordan konservativ modellen er. Antallet af samtidig forekomster af elementer, der skal betragtes som for modellering. |  Heltal, <br> 3-50 <br> (6)
| *FbtMaxItemSetSize* | Bounds antallet af elementer i et hyppige sæt.| Heltal  <br> 2-3 <br> (2)
| *FbtMinimalScore* | Minimale resultat, som skal have et hyppige sæt skal medtages i de returnerede resultater. Jo højere bedre. | Dobbelt <br> 0 og derover <br> (0)
| *FbtSimilarityFunction* | Definerer funktionen lighed der skal bruges i den nyeste version. **Løft** prioriteres højere serendipity, **samtidig forekomst** prioriteres højere forudsigelighed og **Jaccard** er en skade mellem to. | Streng,  <br>  <i>cooccurrence, elevatoren jaccard</i><br> Standard: <i>jaccard</i>

<a name="SelectBuild"></a>
## <a name="build-evaluation-and-selection"></a>Opbygge evaluering og udvælgelse ##

Denne vejledning kan hjælpe dig med at afgøre, om du skal bruge et anbefalinger build eller et FBT build, men det indeholder ikke en endelig answer i tilfælde, hvor du kan bruge en af dem. Også selvom du ved, at du vil bruge en FBT build type, kan du stadig vil vælge **Jaccard** eller **løfte** som funktionen lighed.

Den bedste måde at vælge mellem to forskellige builds er at afprøve dem i den virkelige verden (online evaluering) og registrere en konvertering sats for forskellige builds. Konvertering rente kunne måles baseret på anbefaling Klik, antallet faktisk køb fra anbefalinger, der vises, eller endda på de faktiske købsbeløb, når de forskellige anbefalinger blev vist. Du kan vælge din konvertering rente metrisk, der er baseret på dine business mål.

I nogle tilfælde vil du måske evaluere modellen offline, før du placerer i fremstilling. Mens beregning i offline ikke er en erstatning for online evaluering, kan det fungere som en metrikværdi.

<a name="OfflineEvaluation"></a>
## <a name="offline-evaluation"></a>Offline beregning  ##

Formålet med en offline evaluering er at forudsige præcision (antallet af brugere, der kan købe et af de anbefalede elementer) og forskellige anbefalinger (antallet af elementer, der anbefales).
Som en del af præcision og forskellige målepunkter evalueringen systemet finder en stikprøve af brugere og opdeles transaktioner for disse brugere i to grupper: kursus datasættet og test datasættet.

> [AZURE.NOTE]Hvis du vil bruge offline målepunkter, skal du have tidsstempler i dine brugerdata.
> Tidsdata er påkrævet for at opdele brugen korrekt mellem-kurser og test datasæt.

> Offline evaluering afkast kan ikke resultater for små brugen filer. Til vurdering skal være fuldstændig, skal der være et minimum af 1.000 brugen punkter i test datasættet.

<a name="Precision"></a>
### <a name="precision-at-k"></a>Præcision på k ###
Den følgende tabel repræsenterer output fra præcision på k offline evalueringen.

| K | 1 | 2 | 3 |   4 |     5
|---|---|---|---|---|---|
|Procent |   13.75 | 18.04   | 21 |  24.31 | 26.61
|Brugere i test |    10.000 |    10.000 |    10.000 |    10.000 |    10.000
|Brugere, der betragtes som | 10.000 |    10.000 |    10.000 |    10.000 |    10.000
|Brugere, der medtages ikke | 0 | 0 | 0 | 0 | 0

#### <a name="k"></a>K
I tabellen ovenfor repræsenterer *k* antallet af anbefalinger, der vises til kunden. Tabellen læser på følgende måde: "Hvis inden for prøveperioden blev kun én anbefaling vises for kunderne, kun 13.75 for brugerne, ville have købt pågældende anbefaling." Denne sætning er baseret på den antagelse, at der er accepteret modellen med køb af data. En anden måde at sig dette er, at præcision ved 1 13.75.

Du vil bemærke, som er vist flere elementer til kunden, går sandsynligheden for, at kunden købe en anbefalede element. Til den foregående forsøg fordobles sandsynligheden næsten til 26.61 procent når 5 elementer er anbefalet.

#### <a name="percentage"></a>Procent
Procentdelen af brugere, der sammen mindst én af *k* anbefalinger vises. Procentdelen beregnes ved at dividere antallet af brugere, der mindst én anbefaling sammen med det samlede antal brugere, der betragtes som. Se brugere, der anses for at få flere oplysninger.

#### <a name="users-in-test"></a>Brugere i test
Data i denne række repræsenterer det samlede antal brugere i test datasættet.

#### <a name="users-considered"></a>Brugere, der betragtes som
En bruger betragtes kun, hvis systemet anbefalede mindst *k* elementer, der er baseret på den model, der er oprettet ved hjælp af kursus datasættet.

#### <a name="users-not-considered"></a>Brugere, der medtages ikke
Data i denne række repræsenterer alle brugere, medtages ikke. De brugere, der ikke har modtaget mindst *k* anbefalede elementer.

Bruger ikke betragtes som = brugere i test – anses for brugere

<a name="Diversity"></a>
### <a name="diversity"></a>Forskellige ###
Forskellige målepunkter mål typen elementer, der anbefales. Den følgende tabel repræsenterer output fra forskellige offline evalueringen.

|Fraktil Malerbøtte |    0-90|  90-99| 99 100
|------------------|--------|-------|---------
|Procent        | 34.258 | 55.127| 10.615


Samlet antal elementer, der anbefales: 100.000

Entydige elementer anbefalede: 954

#### <a name="percentile-buckets"></a>Fraktil filsæt
Hver fraktil Malerbøtte er repræsenteret af et tidsinterval (mindste og største værdier, der interval mellem 0 og 100). Elementer tæt på 100 er de mest populære elementer, og elementerne tæt 0 er den mindst populære. Hvis værdien procentdel til 99 100 fraktil Malerbøtte er 10.6, betyder det f.eks, returneres 10.6 procentdel af anbefalingerne kun den øverste en procentdel mest populære elementer. Den mindste værdi fraktil Malerbøtte er inklusive, og den maksimale værdi er eksklusivt, med undtagelse af 100.
#### <a name="unique-items-recommended"></a>Entydige elementer, der anbefales
De entydige elementer, der anbefales metrisk viser antallet af forskellige elementer, der blev returneret til evaluering.
#### <a name="total-items-recommended"></a>Anbefalede elementer i alt
De samlede elementer anbefalede metrisk viser antallet af elementer, der anbefales. Nogle muligvis dubletter.

<a name="ImplementingEvaluation"></a>
### <a name="offline-evaluation-metrics"></a>Offline evaluering målepunkter ###
Præcision og forskellige offline målepunkter kan være nyttigt, når du vælger hvilke build til brug. Opbygge parametre build tid som en del af den respektive FBT eller anbefaling:

-   Angive parameteren *enableModelingInsights* build til **Sand**.
-   Du kan også vælge *splitterStrategy* (enten *RandomSplitter* eller *LastEventSplitter*).
*RandomSplitter* opdeles Brugsdata i tog og test angiver baseret på den angivne *randomSplitterParameters* test procentdel og Tilfældige basistal værdier.
*LastEventSplitter* opdeles Brugsdata i tog og teste sæt baseret på den sidste post for hver bruger.

Dette vil udløse en build, som bruger kun et undersæt af dataene til uddannelse og resten af dataene til at beregne evaluering målepunkter.  Når Opret er fuldført, for at få output af evalueringen, skal du ringe til [få build målepunkter API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f), der passerer de respektive *modelId* og *buildId*.

 Følgende er JSON output til eksempel evalueringen.


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }
