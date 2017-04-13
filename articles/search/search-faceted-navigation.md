<properties 
    pageTitle="Sådan implementeres facetteret navigation i Azure Søg | Microsoft Azure | søge i navigationskategorier" 
    description="Tilføj facetteret navigation til programmer, der integreres med Azure søgning, en skyen hostet søgetjenesten på Microsoft Azure." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

#<a name="how-to-implement-faceted-navigation-in-azure-search"></a>Sådan implementeres facetteret navigation i Azure Søg

Facetteret navigation er en filtreringsmetode, hvor du kan selv-dirigeret analysér ned navigation i søgeprogrammer. Ordet "facetteret navigation" være fremmede, men det er næsten en, du har brugt en før. Som i følgende eksempel vises, er facetteret navigation ikke noget mere end de kategorier, der bruges til at filtrere resultaterne.

## <a name="what-it-looks-like"></a>Hvordan det ser ud

 ![][1]
  
Facetter kan hjælpe dig med at finde det, du leder efter, og sikre, at du ikke får nul resultater. Som en udvikler kan facetter du få vist de mest nyttige søgekriterier til at navigere i din søgning indekserede. I onlinedetail programmer indbygget facetteret navigation ofte over mærker, afdelinger (Spørg sko), størrelse, pris, popularitets og bedømmelser. 

Implementere facetteret navigation varierer på tværs af Søg teknologier og kan være meget komplekse. I Azure Søg bygger facetteret navigation på Forespørgselsestid, der er tildelt felterne tidligere angivet i dit skema. I de forespørgsler, der opbygger dit program, en forespørgsel, send *grene forespørgselsparametre* for at modtage filterværdier tilgængelige grene for dette resultat dokumentgruppe. Angive faktisk trimme dokument resultatet, programmet skal anvende en `$filter` udtryk.

Med hensyn til udvikling af programmer udgør størstedelen af arbejdet, der skriver kode, der konstruktioner forespørgsler. Mange af de problemer med programmet, som du vil have, fra facetteret navigation leveres af tjenesten, herunder indbygget understøttelse af konfiguration af områder og få tæller for grene resultater. Tjenesten indeholder også fornuftig standardindstillinger, der hjælper dig med at undgå uhåndterlige navigation strukturer. 

I denne artikel indeholder følgende afsnit:

- [Sådan oprettes den](#howtobuildit)
- [Opbygge præsentation lag](#presentationlayer)
- [Oprette indekset](#buildindex)
- [Kontrollér, om datakvalitet](#checkdata)
- [Opbygge forespørgslen](#buildquery)
- [Tip til, hvordan du styrer facetteret navigation](#tips)
- [Facetteret navigation, der er baseret på Områdeværdier](#rangefacets)
- [Facetteret navigation, der er baseret på GeoPoints](#geofacets)
- [Prøv det!](#tryitout)

##<a name="why-use-it"></a>Hvorfor bruge det.
De mest effektive søgeprogrammer har flere interaktion modeller ud over et søgefelt. Facetteret navigation er en alternativ indgangspunkt til at søge, giver et praktisk alternativ til at skrive komplekst søgeudtryk manuelt.

##<a name="know-the-fundamentals"></a>Vide grundlæggende

Hvis du er ny bruger af søge udvikling, er den bedste måde at betragte facetteret navigation, der vises mulighederne for selv-dirigeret søgning. Det er en slags analysere ned søgeoplevelse, baseret på foruddefinerede filtre, der bruges til at indsnævre hurtigt ned søgeresultater via punkt, og klik på Handlinger. 

**Interaktion Model**

Søgeoplevelse for facetteret navigation er gentaget, så lad os starte med at forstå den som en sekvens af forespørgsler, der foldes ud som svar på brugerhandlinger.

Startpunktet er et programside, der giver facetteret navigation, typisk, som anbringes på ud. Facetteret navigation er ofte en træstruktur med afkrydsningsfelter for hver værdi eller klikbart tekst. 

1.  En forespørgsel, der er sendt til Azure søgning angiver facetteret navigationsstrukturen via en eller flere grene forespørgselsparametre. For eksempel forespørgslen kan indeholde `facet=Rating`, måske med en `:values` eller `:sort` mulighed for at finjustere præsentationen.
2.  Præsentation laget gengiver en side, der indeholder facetteret navigation, ved hjælp af de facetter, der er angivet i anmodningen.
3.  Hvis du har en facetteret navigationsstruktur, der indeholder bedømmelse, brugeren klikker på "4" til at angive, at kun produkter med en vurdering af 4 eller nyere skal vises. 
4.  Svar sender programmet en forespørgsel, der indeholder`$filter=Rating ge 4` 
5.  Præsentation laget opdaterer siden, som viser et reduceret resultatsæt, der indeholder de elementer, der opfylder de nye kriterier (i dette tilfælde produkter bedømt 4 og mere).

En facet er en parameter i forespørgslen, men forveksles ikke med forespørgsel input. Det bruges aldrig som udvælgelseskriterier i en forespørgsel. Tænk i stedet på facet forespørgselsparametre som input til den navigationsstruktur, kommer tilbage i svaret. For hver forespørgselsparameter, du angiver som grene, evaluerer Azure søgning, hvor mange dokumenter er i de delvise resultater for hver grene værdi.

Meddelelse om den `$filter` i trin 4. Dette er et vigtigt aspekt af facetteret navigation. Selvom facetter og filtre er uafhængig i API, skal du både til at levere den oplevelse, du har til hensigt. 

**Designe mønstre**

I programkode er mønstret grene forespørgselsparametre bruges til at returnere facetteret navigationsstrukturen sammen med grene resultater samt et $filter udtryk, der håndterer klikhændelsen. Tænk på den `$filter` udtrykket som underliggende faktisk justering af kode i søgeresultaterne returneres til præsentation lag. Givet en farver grene, klikke på farven Rød implementeres via en `$filter` udtryk, der markerer kun indeholder de elementer, der har en farve i rødt. 

**Grundlæggende om forespørgsel i Azure søgning**

I Azure Søg skal angives en anmodning om gennem en eller flere parametre (se [Søg i dokumenter](http://msdn.microsoft.com/library/azure/dn798927.aspx) til en beskrivelse af hver enkelt). Ingen af parametrene til forespørgslen er påkrævet, men du skal have mindst én i rækkefølge for en forespørgsel skal gælde.

Præcision, normalt opfattede som muligheden for at filtrere irrelevante besøg opnås gennem en eller begge af disse udtryk:

- **Søg =**<br/>
Værdien af denne parameter udgør søgeudtrykket. Det kan være et enkelt stykke tekst eller et komplekst søgeudtryk, der indeholder flere ord og operatorer. På serveren bruges et søgeudtryk til søgning i hele teksten, forespørgsler søgefelterne i indekset for tilsvarende udtryk, der returnerer resultater i plads rækkefølge. Hvis du angiver `search` til null, forespørgsel er udførelse af over hele indekset (det vil sige, `search=*`). I dette tilfælde andre elementer af forespørgslen, som en `$filter` eller vundne profil, bliver de primære faktorer, der påvirker hvilke dokumenter returneres `($filter`) og i hvilken rækkefølge (`scoringProfile` eller `$orderb`y).

- **$filter =**<br/>
Et filter er en effektiv funktion til at begrænse størrelsen af søgeresultater baseret på værdierne i bestemt dokumentattributter. A `$filter` er evalueres først, efterfulgt af faceting logik, der genererer de tilgængelige værdier og tilsvarende tæller for hver værdi

Komplekst søgeudtryk formindskes ydeevnen for forespørgslen. Hvor det er muligt, kan du udnytte ordentligt bygget filterudtryk for at øge præcision og forbedre forespørgslens performance.

Sammenligne et komplekst søgeudtryk til en, der indeholder et filterudtryk for bedre at forstå hvordan filteret adderer mere præcist:

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Selvom begge forespørgsler er gyldige, er anden overordnede, hvis du leder efter ikke-moteller med parkering i Seattle. Den første forespørgsel er afhængig af disse bestemte ord, der nævnes eller ikke er nævnt i strengfelter som navn, beskrivelse og et andet felt, der indeholder data, der kan søges i. Den anden forespørgsel søger efter nøjagtig match på strukturerede data, og der sandsynligvis er meget mere præcis.

I programmer, der indeholder facetteret navigation, vil du skal du sørge for, at hver brugerhandling over en facetteret navigationsstruktur leveres med en indsnævre søgeresultaterne opnås via et filterudtryk.

<a name="howtobuildit"></a>
##<a name="how-to-build-it"></a>Sådan oprettes den

Facetteret navigation i Azure Søg er implementeret i din programkode, der opbygger anmodningen, men er afhængig af foruddefinerede elementer i dit skema.

Foruddefinerede i søgningen er indekset den `Facetable [true|false]` indeksattribut, angive på valgte felter for at aktivere eller deaktivere deres brug i en facetteret navigationsstruktur. Uden `"Facetable" = true`, kan ikke bruges et felt i grene navigation.

Forespørgsel tid din programkode opretter en anmodning, der indeholder `facet=[string]`, en anmodning om parameter, der indeholder feltet til grene ved. En forespørgsel kan have forskellige aspekter, såsom `&facet=color&facet=category&facet=rating`, hver enkelt adskilt af et og-tegnet (&).

Programkode skal også oprette en `$filter` udtryk til at håndtere hændelserne, klik på i facetteret navigation. A `$filter` reducerer søgeresultaterne ved hjælp af værdien grene som filterkriterier.

Azure søgning returnerer per de betingelser, der angives af brugeren, sammen med opdateringer til facetteret navigationsstrukturen i søgeresultaterne. I Azure Søg facetteret navigation er en ét niveau byggeri med grene værdier og tæller, hvor mange resultater der blev fundet for hver enkelt.

Præsentation lag i din kode giver brugeroplevelsen. Det skal indeholde bestanddele af facetteret navigation, som etiketten, værdier, afkrydsningsfelter og antallet. Azure Search REST-API er platforme, så brug uanset sprog og platform, du vil. Det er vigtigt er at medtage elementer i Brugergrænsefladen, der understøtter trinvis opdatering med opdaterede brugergrænseflade-tilstand, som hver ekstra grene er markeret. 

I de følgende afsnit, skal vi tage et nærmere Kig på hvordan du opretter hver del starter med præsentation lag.

<a name="presentationlayer"></a>
##<a name="build-the-presentation-layer"></a>Opbygge præsentation lag

Arbejde tilbage fra præsentation lag kan hjælpe dig med at afdække krav, der kan være mistede ellers og forstå, hvilke funktioner er afgørende for brugernes søgeoplevelse.

Med hensyn til facetteret navigation, siden internettet eller program viser facetteret navigationsstrukturen, registrerer brugerinput på siden, og indsætter de ændrede elementer. 

For webprogrammer bruges AJAX ofte i præsentation lag, fordi det giver dig mulighed at opdatere trinvise ændringer. Du kan også bruge ASP.NET MVC eller andre visuelle effekter platform, der kan oprette forbindelse til en Azure søgetjenesten via HTTP. Eksempelprogrammet, der henvises til i denne artikel – **AdventureWorks katalog** -der sker med ASP.NET MVC ansøgning.

I følgende eksempel hentes fra filen **index.cshtml** af eksempelprogrammet opbygger en dynamisk HTML-struktur for at vise facetteret navigation i din side med søgeresultater. I eksemplet, facetteret navigation er bygget til siden med søgeresultater, og vises, når brugeren har sendt et søgeord.

Meddelelse om, at hver grene har et navn (farver, kategorier priser), en binding til et facetteret felt (farve, kategorinavn, listPrice) og en `.count` parameter, der bruges til at returnere antallet elementer, der er fundet for det pågældende grene resultat.

  ![][2]
 

> [AZURE.TIP] Når du designer siden med søgeresultater, skal du huske at tilføje en funktion til at fjerne facetter. Hvis du bruger afkrydsningsfelter, kan brugere nemt intuit hvordan du kan rydde filtre. For andre layout, skal du muligvis et brødkrummen mønster eller en anden kreativ fremgangsmåde. I programmet AdventureWorks katalog eksempel kan du klikke på titlen, AdventureWorks kataloget, for at nulstille søgesiden.

<a name="buildindex"></a>
##<a name="build-the-index"></a>Oprette indekset

Faceting er aktiveret på grundlag af felt i indekset, via denne indeksattribut: `"Facetable": true`.  
Alle felttyper, der muligvis bruges i facetteret navigation er `Facetable` som standard. Sådan felttyper omfatter `Edm.String`, `Edm.DateTimeOffset`, og alle de numeriske felter af typen (stort set alle felttyper er facetable undtagen `Edm.GeographyPoint`, som kan ikke bruges i facetteret navigation). 

Når du opretter et indeks, er den bedste fremgangsmåde for facetteret navigation eksplicit deaktivere faceting for felter, der aldrig skal bruges som en facet.  Især strengfelter for separat værdier, som et ID eller produkt navn, skal du vælge `"Facetable": false` for at forhindre deres utilsigtede (og ineffektiv) Brug i en facetteret navigation.

Følgende er skemaet for AdventureWorks katalog eksempel app (trimmet nogle attributter at reducere samlede størrelse):

 ![][3]
 
Bemærk, at `Facetable` er slået fra for strengfelter, der ikke bør bruges som facetter, som et ID eller navn. Slå faceting fra hvor du ikke behøver det hjælper med at holde størrelsen på indekset små og generelt forbedrer ydeevnen.

> [AZURE.TIP] Som en bedste fremgangsmåde at medtage det samlede sæt af indeksattributter for hvert felt. Selvom `Facetable` er aktiveret som standard for næsten alle felter, bevidst at hver attribut kan hjælpe dig med at gennemgå konsekvenserne af hver skema beslutning. 

<a name="checkdata"></a>
##<a name="check-for-data-quality"></a>Kontrollér, om datakvalitet 

Når udvikling af et datacentriske program, er forberedelse af dataene ofte et af de større dele af jobbet. Søgeprogrammer er ingen undtagelse. Kvaliteten af dine data har en direkte indvirkning på om strukturen facetteret navigation indtræden, som du forventer at, samt dens effektivitet hjælpe dig med at oprette filtre, der reducerer resultatet angive.

Søg indekserede dannes i Azure Søg fra dokumenter, som udfylder et indeks. Dokumenter, der indeholder de værdier, der bruges til at beregne facetter. Hvis du vil grene ved Brand eller pris, skal hvert dokument indeholder værdier for *BrandName* og *ProductPrice* , der er gyldige, ensartet og produktiv som en filterindstilling.

Et par påmindelser du skal flytte skydekontrollen vises nedenfor:

- For hvert felt, du vil grene ved Spørg dig selv om den indeholder værdier, der er egnet som filtre i selv-dirigeret Søg. Værdierne, der skal være kort, beskrivende og tilstrækkeligt karakteristisk til at tilbyde et klart valg mellem konkurrerende indstillinger.
- Stavefejl eller næsten tilsvarende værdier. Hvis du grene på farve og feltværdier omfatter Orange og Ornage (en stavefejl), en grene, der er baseret på feltet farve ville løfte begge.
- Blandet tilfælde tekst kan også medføre problemer i facetteret navigation med orange og Orange, der vises som to forskellige værdier. 
- Enkelt og flertalsformerne versioner af den samme værdi kan resultere i en separat facetten for hver.

Som du sikkert kan se, er et afgørende aspekt af effektive facetteret navigation omhu i forberedelse af dataene.

<a name="buildquery"></a>
##<a name="build-the-query"></a>Opbygge forespørgslen

Den kode, du skriver til at oprette forespørgsler skal angive alle dele af en gyldig forespørgsel, herunder søgeudtryk, facetter, filtre, vundne profiler – noget bruges til at udforme en anmodning. I dette afsnit ser vi nærmere hvor facetter passer ind i en forespørgsel, og hvordan filtre bruges sammen med facetter til at levere en reduceret resultatsættet.

Et eksempel er ofte et godt sted at starte. I følgende eksempel hentes fra filen **CatalogSearch.cs** opbygger en anmodning, der opretter grene navigation, der er baseret på farve, kategori og pris. 

Bemærk, at facetter er integreret i dette eksempelprogram. Søgeoplevelse i AdventureWorks katalog er designet omkring facetteret navigation og filtre. Dette er klart i fremtrædende placeringen af facetteret navigation på siden. Eksempelprogrammet indeholder URI parametre for facetter (farve, kategori og priser) som egenskaber på metoden Search (som opbygget i eksempelprogrammet).

  ![][4]
 
En facet forespørgselsparameter er indstillet til et felt og afhængigt af datatypen, kan være yderligere parameteriseret af kommasepareret liste, der indeholder `count:<integer>`, `sort:<>`, `intervals:<integer>`, og `values:<list>`. En værdilisten understøttes for numeriske data, når du konfigurerer områder. Du kan finde oplysninger om brugen i [Søg i dokumenter (Azure Søg API)](http://msdn.microsoft.com/library/azure/dn798927.aspx) .

Sammen med facetter oprette anmodningen udarbejdet af dit program også filtre til at indskrænke sæt af kandidat dokumenter baseret på en grene værdi markering. For en cykler butik facetteret navigation giver tip til spørgsmål som "hvilke farver, producenter og typer af cykler er tilgængelig", mens filtrering svar på spørgsmål som "hvilke nøjagtige cykler er røde, mountainbikecykler i denne pris område".

Når en bruger klikker på "Rød" for at angive, at kun rød produkter skal vises, medtages den næste forespørgsel, programmet sender `$filter=Color eq ‘Red’`.

## <a name="best-practices-for-faceted-navigation"></a>Bedste fremgangsmåder for facetteret navigation

Følgende liste indeholder en oversigt over nogle bedste fremgangsmåder.

- **Præcision**<br/>
Bruge filtre. Hvis du skal have lige søgeudtryk alene stammer kan medføre et dokument, der skal returneres, der ikke har den præcise grene værdi i et af felterne. 

- **Destinationsfelter**<br/>
I facetteret analysér ned vil du typisk kun at medtage dokumenter, der har grene værdi i et bestemt (facetteret) felt ikke et vilkårligt sted på tværs af alle felter, der kan søges i. Tilføje et filter forstærker destinationsfeltet ved at dirigere tjenesten kun vil søge i feltet facetteret for en tilsvarende værdi.

- **Indeks effektivitet**<br/>
Hvis dit program bruger facetteret navigation udelukkende (det vil sige, ingen søgefeltet), kan du markere feltet som `searchable=false`, `facetable=true` at producere et mere kompakt indeks. Desuden opstår indeksering kun på hele grene værdier, med ingen word-pause eller indeksering af komponenterne i en flere ord værdi.

- **Ydeevne**<br/>
Filtre indskrænke sættet af kandidat dokumenter til søgning og Udelad dem fra rangering. Hvis du har et stort antal dokumenter, giver ved hjælp af en meget selektiv grene analysér ned ofte dig betydeligt bedre ydeevne.


<a name="tips"></a> 
##<a name="tips-on-how-to-control-faceted-navigation"></a>Tip til, hvordan du styrer facetteret navigation

Nedenfor er et tip ark med retningslinjer for specifikke problemer.

**Tilføje navne for hvert felt i grene navigation**

Etiketter er normalt defineret i HTML- eller formularen (**index.cshtml** i eksempelprogrammet). Der er ingen API i Azure Søg efter grene navigation etiketter eller enhver anden type metadata.

**Definer, hvilke felter der kan bruges som facet**

Tilbagekalde, skemaet for indekset bestemmer, hvilke felter der kan bruges som en facet. Hvis du allerede et felt er facetable, angiver forespørgslen, hvilke felter der skal grene ved. Det felt, hvor du skal være faceting indeholder de værdier, der vises under etiketten. 

De værdier, der vises under hver etiket er hentet fra indekset. Eksempelvis hvis feltet grene er *farve*, skal være de værdier, der er tilgængelige til filtrering af flere værdier for det pågældende felt (rød, sort og osv.).

For numeriske og DateTime kun værdier, kan du eksplicit angive værdier i feltet grene (for eksempel `facet=Rating,values:1|2|3|4|5`). En værdilisten er tilladt for følgende felttyper til at forenkle adskillelse af salgsstrategi resultater i sammenhængende områder (begge områder, der er baseret på numeriske værdier eller perioder). 

**Trimme grene resultater**

Grene resultater er dokumenter, der findes i søgeresultaterne, der svarer til et grene udtryk. I eksemplet nedenfor har i søgeresultaterne for *cloud computing*, 254 elementer også *interne specifikation* som en indholdstype. Elementer, der ikke behøver at være udelukker. Hvis et element opfylder kriterierne i begge filtre, tælles den i hver enkelt. Det er muligt, når faceting på `Collection(Edm.String)` felter, som ofte bruges til at implementere mærkning af dokumentet.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Generelt, hvis du synes, at grene resultater vedvarende er for stor, anbefaler vi, at tilføje du flere filtre, som beskrevet i tidligere afsnit til at give brugerne programmet flere muligheder for at indsnævre søgningen.

**Begræns elementer i navigationsruden facet**

For hvert facetteret felt i navigationstræet er der et standardgrænse på 10 værdier. Denne standard giver mening for navigation strukturer, fordi den bevarer værdilisten til en god størrelse. Du kan tilsidesætte standard ved at tildele en værdi til at tælle.

- `&facet=city,count:5`Angiver, at kun de første 5 byer, der findes i den øverste rangerede resultater returneres som en grene resultat. Hvis forespørgslen angiver givet et søgeord af "airport" og 32 matches `&facet=city,count:5`, kun de fem første entydige byer med mest dokumenter i søgeresultaterne er inkluderet i grene resultaterne.

Meddelelse om forskellen mellem grene resultater og søgeresultater. Søgeresultater er alle de dokumenter, der svarer til forespørgslen. Grene resultaterne er ens for hver grene værdi. I eksemplet med omfatter søgeresultater bynavne, der ikke er på listen grene klassifikation (5 i vores eksempel). Resultater, der er filtreret gennem facetteret navigation være synlige for brugeren, når han eller hun rydder facetter eller vælger andre aspekter end by. 

> [AZURE.NOTE] Omhandler `count` når der er mere end én type kan være forvirrende. Den følgende tabel indeholder en kort oversigt over hvordan ordet skal bruges i Azure Søg API, kodeeksempler og dokumentation. 

- `@colorFacet.count`<br/>
I præsentation kode, skal du se en Tæl parameter på facet, bruges til at vise antallet grene resultater. Grene i søgeresultaterne angiver tælle antallet af dokumenter, der svarer på den grene ord eller et område.

- `&facet=City,count:12`<br/>
Du kan angive antal til en værdi i en grene forespørgsel.  Standard er 10, men du kan angive den højere eller lavere. Angive `count:12` henter øverst 12 stemmer overens med grene i søgeresultaterne ved dokumentantal.

- "`@odata.count`"<br/>
I svaret forespørgsel angiver denne værdi antallet af tilsvarende elementer i søgeresultaterne. I gennemsnit den er større end summen af alle grene resultater kombineres på grund af tilstedeværelsen af elementer, der svarer til søgeordet, men har ingen facet værdi forekomster.


**Niveauer i facetteret navigation** 

Som nævnt, er der ingen direkte understøttelse for indlejring facetter i et hierarki. Af feltet understøtter facetteret navigation kun ét niveau af filtre. Men der findes løsninger. Du kan koder en hierarkisk grene struktur i en `Collection(Edm.String)` Peg per hierarki med én post. Implementere denne løsning er ikke medtaget i denne artikel, men du kan læse mere om af websteder i [OData efter eksempel](http://msdn.microsoft.com/library/ff478141.aspx). 

**Validere felter**

Hvis du opretter på listen over facetter dynamisk baseret på upålidelige brugerinput, skal du enten validere, at navnene på de facetteret felter er gyldige, eller escape navnene, når du opbygger en URL-adresser ved hjælp af enten `Uri.EscapeDataString()` i .NET eller det tilsvarende beløb i din foretrukne platform.

**Tæller grene i søgeresultaterne**

Når du tilføjer et filter til en facetteret forespørgsel, kan du vil bevare sætningen grene (for eksempel `facet=Rating&$filter=Rating ge 4`). Teknisk set grene = bedømmelse er ikke det er nødvendigt, men beholde det returnerer optællinger for de grene værdier for bedømmelser 4 eller nyere. Eksempelvis hvis en bruger klikker på "4", og forespørgslen indeholder et filter for større eller lig med "4", returneres tæller for hver rangering, der er 4 og op.  

**Sharding konsekvenserne på facet tæller**

Under visse omstændigheder, kan du finde, facet tæller ikke matcher de resultatsæt (se [facetteret navigation i Azure søgning (forumindlæg)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Grene tæller kan være forkerte på grund af sharding arkitektur. Hver søgeindekset har flere shards og hver enkelt rapporter øverste N facetter ved dokument Tæl, som derefter kombineres til et enkelt resultat. Hvis nogle shards har mange af tilsvarende værdier, mens andre har mindre, kan det være, at nogle grene værdier mangler eller under-tælles i resultaterne.

Selvom denne funktionsmåde kan ændre når som helst, hvis du støder på problemet i dag, du kan arbejde omkring det ved kunstigt inflating antallet:<number> til et stort antal til at gennemtvinge fuld rapportering fra hver shard. Hvis værdien af count: er større end eller lig med antallet af entydige værdier i feltet, er du garanti præcise resultater. Når dokumentet tæller er virkelig høj, der er en ydeevnen, så bruges imidlertid denne indstilling med omtanke.

<a name="rangefacets"></a>
##<a name="facet-navigation-based-on-a-range-values"></a>Grene navigation, der er baseret på værdier i et område

Faceting over områder er et almindelige Søg programmet krav. Områder, der understøttes for numeriske data og DateTime-værdier. Du kan læse mere om hver enkelt tilgang i [Søg i dokumenter (Azure Søg API)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Azure Søg forenkler område byggeri ved at indsende to metoder til et område på computeren. For begge tilgange opretter Azure Søg de relevante områder, der er givet de materialer, du har angivet. Eksempelvis hvis du angiver værdier i det område af 10 | 20 | 30, vil den automatisk oprette områder af 0 -10, 10-20, 20-30. Eksempelprogrammet fjerner alle mellemrum, der er tomme. 

**Metode 1: Bruge parameteren interval**<br/>
Hvis du vil angive pris facetter i $10 ryk, skal angive du:`&facet=price,interval:10`


**Metode 2: Brug af en liste med værdier**<br/>
Du kan bruge en liste med værdier for numeriske data.  Overvej grene området for listPrice, gengives på følgende måde:

  ![][5]

Området er angivet i filen **CatalogSearch.cs** ved hjælp af en liste med værdier:

    facet=listPrice,values:10|25|100|500|1000|2500

Hvert område er oprettet ved hjælp af 0 som udgangspunkt en værdi på listen som et slutpunkt, og derefter beskæres i det forrige område til at oprette separate intervaller. Azure søgning gør dette som en del af facetteret navigation. Du behøver ikke at skrive programkode til at strukturere hvert interval.

### <a name="build-a-filter-for-facet-ranges"></a>Oprette et filter for grene områder ###

Hvis du vil filtrere dokumenter baseret på et område, der er valgt af brugeren, kan du bruge den `"ge"` og `"lt"` filtrere operatorer i et udtryk i to dele, der definerer slutpunkterne på området. Eksempelvis hvis brugeren vælger området 10-25 filteret ville være `$filter=listPrice ge 10 and listPrice lt 25`.

I eksempelprogrammet bruger filterudtrykket **priceFrom** og **priceTo** parametre til at angive slutpunkterne. Metoden **BuildFilter** i **CatalogSearch.cs** indeholder det filterudtryk, som giver dig dokumenter i et område.

  ![][6]

<a name="geofacets"></a> 
##<a name="filtered-navigation-based-on-geopoints"></a>Filtrerede navigation, der er baseret på GeoPoints

Det er fælles for at se filtre, der hjælper, du vælger en butik, restaurant eller destination, der er baseret på dens afstand til din aktuelle placering. Mens denne type filter kan se ud facetteret navigation, er det faktisk blot et filter. Vi nævnes det her til folk, for hvem der specifikt søger efter implementering råd til dette problem med bestemt design.

Der findes to geospatiale funktioner i Azure søgning, **geo.distance** og **geo.intersects**.

- Funktionen **geo.distance** returnerer afstanden i kilometer mellem to punkter, én der et felt og en være en konstant overføres som en del af filteret. 

- Funktionen **geo.intersects** returnerer SAND, hvis et bestemt punkt er placeret i en given polygon, hvor punktet er et felt, og polygonen er angivet som en konstant liste over koordinater blev overført som en del af filteret.

Du kan finde filter eksempler i [OData udtrykkenes syntaks (Azure søgning)](http://msdn.microsoft.com/library/azure/dn798921.aspx).

<a name="tryitout"></a>
##<a name="try-it-out"></a>Prøv det!

Azure Søg Adventure Works Demo på Codeplex indeholder eksempler, der er nævnt i denne artikel. Når du arbejder med søgeresultater, kan du se URL-adressen til ændringer i forespørgslen byggeri. Dette program der sker med Føj facetter til URI, når du vælger hver enkelt.

1.  Konfigurere eksempel program tilladelse til at bruge din URL-adressen og api-nøgle. 

    Bemærk skemaet, der er defineret i filen Program.cs af CatalogIndexer projektet. Det angiver facetable felter for farve, listPrice, størrelse, tykkelse, kategorinavn og modelName.  Nogle af disse (farve, listPrice, kategorinavn) er faktisk implementeret i facetteret navigation.

3.  Kør programmet. 

    I første omgang vises lige søgefeltet. Du kan klikke på knappen Søg det samme for at få alle resultater, eller skriv et søgeord.

    ![][7]
 
4.  Skriv et søgeord, som cykler, og klik på **Søg**. Forespørgslen udfører hurtigt.
 
    En facetteret navigationsstruktur returneres også med søgeresultaterne.  URL-adressen er facetter for farver, kategorier og priser null. I søgeresultatsiden omfatter facetteret navigationsstrukturen tæller for hvert grene resultat.

     ![][8]
 
5.  Klik på en farve, kategori og pris område. Facetter er null-værdi på en indledende søgning, men som de udføre på værdier, der er beskåret søgeresultaterne af elementer, der ikke længere svarer. Bemærk, at URI opfanger ændringerne i din forespørgsel.

    ![][9]
 
6.  For at rydde forespørgslen facetteret, så du kan prøve forskellige forespørgsel funktionsmåder, skal du klikke på **AdventureWorks katalog** øverst på siden.

    ![][10]
 
<a name="nextstep"></a>
##<a name="next-step"></a>Næste trin

Hvis du vil teste din viden, kan du tilføje et grene felt til *modelName*. Indekset er allerede konfigureret for denne grene, så der kræves ingen ændringer til indekset. Men du skal ændre HTML-koden for at medtage en ny grene for modeller, og Føj feltet grene til forespørgsel parametre.

For mere viden på designprincipper for facetteret navigation anbefaler vi følgende links:

- [Designe til facetteret søgning](http://www.uie.com/articles/faceted_search/)
- [Designe mønstre: Facetteret Navigation](http://alistapart.com/article/design-patterns-faceted-navigation)

Du kan også se [Azure Søg undersøgelse](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). I 45:25 er der en demo om, hvordan du implementere facetter.

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

 