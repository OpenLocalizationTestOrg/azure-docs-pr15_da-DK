<properties 
    pageTitle="Anmode om enheder i DocumentDB | Microsoft Azure" 
    description="Få mere at vide om, hvordan du forstå, angive og anslå anmodning enhed krav i DocumentDB." 
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="syamk"/>

#<a name="request-units-in-documentdb"></a>Anmode om enheder i DocumentDB
Nu tilgængeligt: DocumentDB [anmodning enhed Lommeregner](https://www.documentdb.com/capacityplanner). Yderligere oplysninger i [din overførselshastighed skal Estimating](documentdb-request-units.md#estimating-throughput-needs).

![Lommeregner til overførselshastighed][5]

##<a name="introduction"></a>Introduktion
I denne artikel indeholder en oversigt over anmodning om enheder i [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). 

Når du har læst i denne artikel, vil du kunne besvare spørgsmål, der er følgende:  

-   Hvad er anmode om enheder og anmode om gebyrer?
-   Hvordan kan jeg angive anmodning enhed kapacitet for en af websteder?
-   Hvordan jeg anslå mit program anmodning enhed skal?
-   Hvad sker der, hvis jeg overskrider anmodning enhed kapacitet for en af websteder?


##<a name="request-units-and-request-charges"></a>Anmode om enheder og Anmod om gebyrer
DocumentDB leverer hurtigt, forudsigelige ydeevne ved *at reservere* ressourcer til at opfylde programmets overførselshastighed skal.  Da programmet indlæse og få adgang til mønstre ændres over tid, kan du nemt at øge eller mindske mængden af reserveret overførselshastighed, der er tilgængelige for dit program DocumentDB.

Reserverede overførselshastighed angives med DocumentDB, med hensyn til anmodning om enheder behandling sekundet.  Du kan betragte anmodning enheder som overførselshastighed valuta, hvorved du *reservere* en mængde garanti for anmodning om enheder, der er tilgængelige for dit program på hver anden datotype.  Hver handling i DocumentDB - oprettelsen af et dokument, udføre en forespørgsel, opdatering af et dokument - forbruger CPU, hukommelse og IOP'ER.  Det vil sige, medfører hver operation en *anmodning om gebyr*, som er angivet i *enheder af anmodning*.  Forstå de faktorer, der påvirker anmodning enhed gebyrer, sammen med dit program overførselshastighed krav, gør det muligt at køre programmet som omkostninger effektivt som muligt. 

##<a name="specifying-request-unit-capacity"></a>Angive anmodning enhed kapacitet
Når du opretter en DocumentDB af websteder, kan du angive antallet anmodning enheder sekundet (RUs) ønskede reserveret til samlingen.  Når samlingen er oprettet, er fuld tildeling af RUs angivet reserveret til de websteder er i brug.  Hver af websteder er sikkert at have dedikeret og isolerede overførselshastighed egenskaber.  

Det er vigtigt at være opmærksom på, at DocumentDB fungerer på en reservation model; Det vil sige, er du faktureret for den overførselshastighed *reserveret* til samlingen, uanset hvor meget af overførselshastighed er aktivt *bruges*.  Husk på, men, som dit program indlæsning, data og brug mønstre ændringer du nemt kan skalere op og ned mængden af reserveret RUs gennem DocumentDB SDK'er eller ved hjælp af [Azure-portalen](https://portal.azure.com).  Du kan finde flere oplysninger på skala overførselshastighed op og ned [DocumentDB ydeevneniveauer](documentdb-performance-levels.md).

##<a name="request-unit-considerations"></a>Overvejelser i forbindelse med anmodning om enhed
Når beregning antallet af anmodning om enheder reservere til din DocumentDB samling, er det vigtigt at tage højde for følgende variabler:

- **Dokumentstørrelse**. Når dokumentstørrelser øger de enheder, der benyttes for at læse eller skrive dataene, øges.
- **Egenskaben dokumentantal**. Under antagelse standard indeksering af alle egenskaber, de enheder, der benyttes for at skrive et dokument, øger når egenskaben Tæl øges.
- **Data konsistens**. Når du bruger data konsistens niveauer af stærke eller afgrænset Staleness, skal blive brugt flere enheder for at læse dokumenter.
- **Indekseret egenskaber**. En indeks-politik på hver samling bestemmer, hvilke egenskaber er indekseret som standard. Du kan reducere din anmodning om enhed forbrug, ved at begrænse antallet af indekserede egenskaber eller ved at aktivere fløde indeksering.
- **Dokument indeksering**. Som standard hvert dokument automatisk indekseret, vil du forbruge færre anmodning om enheder, hvis du vælger ikke at indeksere nogle af dine dokumenter.
- **Forespørgsel mønstre**. Kompleksiteten af en forespørgsel påvirker, hvor mange anmode om enheder er consumed for en handling. Antallet af prædikaterne, karakter i prædikaterne, prognoser, antallet af brugerdefinerede funktioner og størrelsen af datasættet kilde alle påvirke omkostninger for forespørgslerne.
- **Brug af script**.  Forbruger anmodning om enheder, der er baseret på de handlinger, der udføres kompleksitet, som i forespørgsler, lagrede procedurer og udløsere. Undersøg overskriften anmodning gebyr for bedre at forstå, hvordan hver operation forbrug anmodning enhed kapacitet, som du udvikler dit program.

##<a name="estimating-throughput-needs"></a>Estimering af overførselshastighed behov
En anmodning om enhed er et standardiseret mål for anmodning om behandling af omkostninger. En enkelt anmodning enhed repræsenterer behandling kapacitet kræves til at læse en enkelt 1KB JSON dokument bestående af 10 entydige egenskabsværdier (bortset fra egenskaber for system) (via selv link eller -id). En anmodning om at oprette (insert), erstatte eller slette det samme dokument vil forbruge flere behandling af tjenesten og dermed flere anmodning enheder.   

> [AZURE.NOTE] Den oprindelige plan for 1 anmodning enhed for et dokument, 1KB svarer til en enkelt Hent ved selv link eller id for dokumentet.

###<a name="use-the-request-unit-calculator"></a>Bruge anmodning enhed Lommeregner
Kan hjælpe kunder fin finjustere deres overførselshastighed skøn, der er en webbaserede [anmodning enhed Lommeregner](https://www.documentdb.com/capacityplanner) til at anslå anmodning enhed kravene til almindelige handlinger, herunder:

- Dokument opretter (skriver)
- Læser dokumentet
- Sletter dokument
- Dokumentopdateringer

Værktøjet indeholder også support til vurdering af datalagring baseret på de eksempeldokumenter, du angiver.

Ved hjælp af værktøjet er det nemt:

1. Overfør et eller flere repræsentant JSON-dokumenter.

    ![Overføre dokumenter til anmodning om enhed Lommeregner][2]

2. Angiv det samlede antal dokumenter, du forventer at gemme for at estimere krav til opbevaring af data.

3. Angiv antallet af dokument oprette, læse, opdatere og slette handlinger, du har brug for (på grundlag per sekund). Overføre en kopi af eksempeldokument fra trin 1 ovenfor, der indeholder feltopdateringer, der typisk for at anslå anmodning enhed gebyrer dokument Opdater handlinger.  Eksempelvis hvis Dokumentopdateringer ændrer typisk to egenskaber med navnet lastLogin og userVisits, derefter blot kopiere eksempeldokumentet, opdatere værdierne for disse to egenskaber og overføre det kopierede dokument.

    ![Angiv overførselshastighed krav i anmodning enhed Lommeregner][3]

4. Klik på Beregn og undersøge resultaterne.

    ![Anmode om enhed Lommeregner resultater][4]

>[AZURE.NOTE]Hvis du har dokumenttyper, varierer dramatisk med hensyn til størrelse og antallet af indekserede egenskaber, og derefter uploade en stikprøve af hver *type* dokument, der typisk til værktøjet og derefter Beregn resultaterne.

###<a name="use-the-documentdb-request-charge-response-header"></a>Brug DocumentDB anmodning gebyr svarheaderen
Alle svar fra DocumentDB tjenesten indeholder et brugerdefineret sidehoved (x-ms-anmodning-gebyr), der indeholder de anmodning om enheder, der benyttes for anmodningen. Denne overskrift er også tilgængelig via DocumentDB SDK'er. I SDK .NET er RequestCharge en egenskab for objektet ResourceResponse.  For forespørgsler indeholder DocumentDB forespørgsel Explorer i portalen Azure anmodning gebyr oplysninger om udførte forespørgsler.

![At undersøge RU gebyrer i Explorer forespørgsel][1]

Med dette i huske, en metode til beregning af mængden reserveret overførselshastighed, der kræves af dit program er at registrere den anmodning om enhedspris, der er tilknyttet igangværende almindelige handlinger mod en repræsentant dokument, der bruges af dit program og derefter estimering af antallet handlinger du forventer at skulle udføre hvert sekund.  Sørg for at måle og medtage typisk forespørgsler og DocumentDB script brugen samt.

>[AZURE.NOTE]Hvis du har dokumenttyper, varierer dramatisk med hensyn til størrelse og antallet af indekserede egenskaber, derefter registrere enhedspris gældende handlingen anmodning, der er knyttet til hver *type* typisk dokument.

Eksempel:

1. Optage enhedspris anmodning for at oprette (indsætte) et typisk dokument. 
2. Anmodning om enhedspris af læser et typisk dokument-post.
3. Registrere enhedspris anmodning for at opdatere et typisk dokument.
3. Anmodning om enhedspris forespørgselstyper, der typisk, fælles dokument-post.
4. Registrere den anmodning om enhedspris af alle de brugerdefinerede scripts (lagrede procedurer, udløsere, brugerdefinerede funktioner), opgraderede af programmet
5. Beregne de nødvendige anmodning enheder, der gives det anslåede antal handlinger, du forventer at skulle for at køre hver andet.

##<a name="a-request-unit-estimation-example"></a>En anmodning om enhed skøn eksempel
Overvej følgende ~ 1KB dokument:

    {
     "id": "08259",
    "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
    "tags": [
        {
        "name": "cereals ready-to-eat"
        },
        {
        "name": "kellogg"
        },
        {
        "name": "kellogg's crispix"
        }
    ],
    "version": 1,
    "commonName": "Includes USDA Commodity B855",
    "manufacturerName": "Kellogg, Co.",
    "isFromSurvey": false,
    "foodGroup": "Breakfast Cereals",
    "nutrients": [
        {
        "id": "262",
        "description": "Caffeine",
        "nutritionValue": 0,
        "units": "mg"
        },
        {
        "id": "307",
        "description": "Sodium, Na",
        "nutritionValue": 611,
        "units": "mg"
        },
        {
        "id": "309",
        "description": "Zinc, Zn",
        "nutritionValue": 5.2,
        "units": "mg"
        }
    ],
    "servings": [
        {
        "amount": 1,
        "description": "cup (1 NLEA serving)",
        "weightInGrams": 29
        }
    ]
    }

>[AZURE.NOTE]Dokumenter er minified i DocumentDB, så systemet beregnede størrelse af ovenstående dokumentet er en smule mindre end 1KB.


Følgende tabel viser tilnærmet anmodning enhed gebyrer til almindelige handlinger for dette dokument (enhedspris tilnærmet anmodning antages, niveauet konto konsistens er indstillet til "Session", og, at alle dokumenter indekseres automatisk):

Handling|Anmode om enhedspris 
---|---
Oprette dokument|~ 15 RU 
Læs dokument|~ 1 RU
Forespørgsel dokument efter id|~2.5 RU

I denne tabel vises desuden tilnærmet anmodning enhed gebyrer for typisk forespørgsler, der bruges i programmet på computeren:

Forespørgsel|Anmode om enhedspris|# af returnerede dokumenter
---|---|--- 
Vælg mad efter id|~2.5 RU|1 
Vælg fødevarer af producenten|~ 7 RU|7
Markér ved mad gruppe og rækkefølge efter vægt|~ 70 RU|100
Vælg øverste 10 fødevarer i en mad gruppe|~ 10 RU|10

>[AZURE.NOTE]RU gebyrer varierer afhængigt af antallet af dokumenter, der returneres.

Disse oplysninger, kan vi beregne RU kravene til dette program, der er givet antallet af handlinger og vi forventer sekundet forespørgsler:

Handlingen/forespørgsel|Skøn sekundet|Påkrævet RUs 
---|---|--- 
Oprette dokument|10|150 
Læs dokument|100|100 
Vælg fødevarer af producenten|25|175 
Vælg efter mad gruppe|10|700 
Vælg øverste 10|15|150 total|155|1275

I dette tilfælde forventer vi en gennemsnitlig overførselshastighed kravet af 1,275 RU/s.  Afrunding op til nærmeste 100, vil vi Klargør 1.300 RU/s for dette program af websteder.

##<a id="RequestRateTooLarge"></a>Begrænsninger for overstiger reserveret overførselshastighed
Tilbagekalde, anmodning enhed forbrug evalueres som en sats sekundet. For programmer, der overskrider den klargjorte anmodning enhed rente for en samling, sænkes anmodninger om til samlingen, før rente, der falder under det reserverede niveau. Når en begrænsning, der opstår, afslutte anmodningen med RequestRateTooLargeException (http-statuskode 429) og vende tilbage overskriften x-ms-retry-efter-ms, der angiver mængden af tid i millisekunder, som brugeren skal gå, før mellem anmodningen overskrides i serveren.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Hvis du bruger .NET klient SDK og LINQ forespørgsler, og klik derefter i de fleste tilfælde du aldrig skal håndtere denne undtagelse, som den aktuelle version af .NET klient SDK fanger implicit dette svar, respekterer server angivet forsøg igen efter sidehovedet, og forsøg anmodningen. Medmindre din konto åbnes samtidigt af flere klienter, lykkes næste prøv igen.

Hvis du har mere end én klient kumulativt operativsystem over omkostningssatser anmodning forsøg standardfunktionsmåden muligvis ikke tilstrækkelig og klienten vil udløse en DocumentClientException med statuskode 429 til programmet. I tilfælde som dette, kan du overveje at håndtering af forsøg funktionsmåde og logik i dit program fejl håndtering af rutiner eller øger hastigheden reserveret til samlingen.

##<a name="next-steps"></a>Næste trin

Hvis du vil vide mere om reserveret overførselshastighed med Azure DocumentDB databaser, kan du gennemse disse ressourcer:
 
- [DocumentDB priser](https://azure.microsoft.com/pricing/details/documentdb/)
- [Administrere DocumentDB kapacitet](documentdb-manage.md) 
- [Modellering data i DocumentDB](documentdb-modeling-data.md)
- [DocumentDB ydeevneniveauer](documentdb-partition-data.md)

Hvis du vil vide mere om DocumentDB skal du se Azure DocumentDB [dokumentation](https://azure.microsoft.com/documentation/services/documentdb/). 

For at komme i gang med skala og performance-test med DocumentDB skal du se [skala test med Azure DocumentDB af ydeevne og](documentdb-performance-testing.md).


[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
