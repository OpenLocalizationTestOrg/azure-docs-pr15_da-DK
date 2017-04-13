<properties
    pageTitle="Lucene forespørgsel eksempler til Azure søgning | Microsoft Azure-søgning"
    description="Lucene forespørgselssyntaksen for uskarpt søgning, afstand søgning, ord øger, regulære udtryk Søg og søgning med jokertegn."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="Lucene query analyzer syntax"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="liamca"
/>

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Lucene forespørgsel syntakseksempler for at oprette forespørgsler i Azure Søg

Når bygning af forespørgsler til Azure søgning, kan du bruge enten standard [enkel forespørgselssyntaksen](https://msdn.microsoft.com/library/azure/dn798920.aspx) eller alternative [Lucene forespørgsel parseren i Azure Søg](https://msdn.microsoft.com/library/azure/mt589323.aspx). Lucene forespørgsel parseren understøtter mere kompleks forespørgsel konstruktioner som felt-fastsat forespørgsler, uskarpt søgning, afstand søgning, ord øger og reqular udtryk søgning.

I denne artikel kan du gennemgå eksempler, der viser Lucene forespørgselssyntaksen og resultater ved siden af hinanden. Eksempler kører i forhold til en forudindlæst søgeindekset i [JSFiddle](https://jsfiddle.net/), redaktør online kode til test skriftsprog og HTML. 

Højreklik på forespørgsel eksempel URL-adresserne til at åbne JSFiddle i et separat browservindue.

> [AZURE.NOTE] I følgende eksempler udnytte en bestående af tilgængelige sager baseret på et datasæt, der leveres af [By af New York OpenData](https://nycopendata.socrata.com/) initiativet søgeindekset. Disse data betragtes ikke, aktuelle eller fuldført. Indekset er på en sandkasse tjeneste, der leveres af Microsoft. Du behøver ikke et Azure-abonnement eller Azure søgning kan du prøve disse forespørgsler.

## <a name="viewing-the-examples-in-this-article"></a>Se eksemplerne i denne artikel

Alle eksemplerne i denne artikel Angiv Lucene forespørgsel parseren via parameteren**Forespørgselstype** søgning. Når du bruger Lucene forespørgsel parseren fra din kode, skal du angive **Forespørgselstype** på hver anmodning.  Gyldige værdier omfatter **simple**|**fuld**, med **enkel** som standard- og **fuld** for Lucene forespørgsel-parseren. Du kan finde oplysninger om angivelse af forespørgselsparametre i [Søg i dokumenter (Azure Søg Service REST-API)](https://msdn.microsoft.com/library/azure/dn798927.aspx) .

**Eksempel 1** – Højreklik på følgende forespørgsel kodestykke at åbne den i en ny browser-side, der indlæser JSFiddle og kører forespørgslen:
- [& forespørgselstype = full & søgning = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Denne forespørgsel returnerer dokumenter fra vores job indeks (indlæst på en sandkasse tjeneste)

I det nye browservindue, får du vist JavaScript kilde- og HTML-output side om side. Scriptet refererer til en forespørgsel, der leveres af eksempel URL-adresserne i denne artikel. For eksempel i **Eksempel 1**er den underliggende forespørgsel som følger:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Meddelelse om forespørgslen bruger en forudkonfigureret Azure-søgeindekset kaldet nycjobs. Parameteren **searchFields** begrænser søgningen til kun business titelfeltet. **Forespørgselstype** er angivet til **fuld**, som får Azure Søg til at bruge Lucene forespørgsel Parser for denne forespørgsel.

### <a name="fielded-query-operation"></a>Fielded forespørgsel

Du kan ændre eksemplerne i denne artikel ved at angive en **fieldname:searchterm** byggeri for at definere en forespørgselshandling af fielded, hvor feltet er et enkelt ord, og søgeordet er også et enkelt ord eller en sætning, du kan også med booleske operatorer. Nogle eksempler kan nævnes:

- business_title:(senior NOT junior)
- staten: ("New York" og "Ny Jersey")

Sørg for at placere flere tekststrenge i anførselstegn, hvis du vil have begge strenge, der skal evalueres som et enkelt objekt, som i dette tilfælde, søge efter to forskellige byer i placeringsfeltet. Du skal også kontrollere operatoren aktiveres, når du ikke ser og and

Det felt, der er angivet i **fieldname:searchterm** skal være et felt, der kan søges i. Du kan finde oplysninger om, hvordan indeksattributter bruges i feltet definitioner i [Create Index (Azure Søg Service REST-API)](https://msdn.microsoft.com/library/azure/dn798941.aspx) .

## <a name="fuzzy-search"></a>Uskarpt søgning

En uskarpt søgning finder matches i ord, der har en lignende byggeri. Per [Lucene dokumentation](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), er uskarpt søgninger baseret på [Damerau Levenshtein afstand](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Brug tilden til at udføre en uskarpt søgning "~" symbol i slutningen af et enkelt ord med en valgfri parameter, en værdi, der angiver Rediger afstanden mellem 0 og 2. For eksempel "blå ~" eller "blå ~ 1" vil returnere blå, blå og klæb.

**Eksempel 2** – Højreklik på følgende forespørgsel kodestykke for at give det en prøve. Denne forespørgsel søger efter business titler med udtryk senior i dem, men ikke nyansat:

- [& forespørgselstype = fulde & søge = business_title:senior ikke nyansat](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="proximity-search"></a>Afstand søgning

Afstand søgninger bruges til at finde ord, der er tæt på hinanden i et dokument. Indsætte en tilde "~" symbol i slutningen af et udtryk efterfulgt af antallet af ord, der danner i nærheden af rammen. For eksempel "hotel airport" ~ 5 finde vilkår hotel og airport inden for 5 ord fra hinanden, i et dokument.

**Eksempel 3** – skal du højreklikke på følgende forespørgsel kodestykke. Denne forespørgsel søger efter sager med de ord Tilknyt (hvor det er stavet forkert):

- [& forespørgselstype = fulde & søge = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Eksempel 4** – Højreklik på forespørgslen. Søge efter job med ordet "senior analyse", hvor det er adskilt af ikke mere end ét ord:

- [& forespørgselstype = fulde & søge = business_title: "senior analyse" ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Eksempel 5** – Prøv det igen fjerne ord mellem ordet "senior analyse".

- [& forespørgselstype = fulde & søge = business_title: "senior analyse" ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting"></a>Sigt at

Ord øger refererer til rangering et dokument, der er højere, hvis den indeholder boosted ordet, i forhold til dokumenter, der ikke indeholder ordet. Dette er forskellig fra vundne profiler, i den valgte vurdering profiler gavne bestemte felter i stedet for bestemte betingelser. I følgende eksempel hjælper med at illustrere forskellene.

Overvej, der svarer til en vurdering profil, der øger i et bestemt felt, som **genre** i eksemplet musicstoreindex. Ord øger kan bruges til at yderligere betingelser, der er højere end andre gavne visse søgning. For eksempel "japanske ^ 2 elektronisk" vil gavne dokumenter, der indeholder søgeordene i feltet **genre** højere end andre søgbar felter i indekset. Dokumenter, der indeholder søgeordet "japanske" kan desuden være rangerer højere end andre søgeordet "elektronisk" som et resultat af udtryk boost værdi (2).

Øge et ord, skal du bruge indsætningstegnet, "^", symbol med en faktor boost (tal) i slutningen af det ord, du søger. Jo højere boost faktor, flere relevante bliver ordet i forhold til andre søgeord. Som standard er boost faktoren 1. Selvom boost faktor skal være positive, kan det være mindre end 1 (for eksempel, 0,2).

**Eksempel 6** – Højreklik på forespørgslen. Søge efter sager med ordet "computer analyse", hvor vi se der er ingen resultater med både ord computer og analyse, men analyse job er øverst i resultaterne.

- [& forespørgselstype = fulde & søge = business_title:computer analyse](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Eksempel 7** – Prøv det igen, denne gang øger søgeresultater med udtryk computeren over udtryk analyse, hvis begge ord ikke findes.

- [& forespørgselstype = fulde & søge = business_title:computer ^ 2 analyse](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression"></a>Regulære udtryk

En regulære udtryk søgning finder et match, der er baseret på indholdet mellem skråstreg "/", som dokumenteret [RegExp klasse](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Eksempel 8** – Højreklik på forespørgslen. Søge efter sager med enten ordet Senior eller studerer.

- `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

URL-adressen i dette eksempel gengives ikke korrekt på siden. Kopiere URL-adressen nedenfor som en løsning, og sæt dem ind i browseren URL-adressen:    `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`


## <a name="wildcard-search"></a>Søgning med jokertegn

Du kan bruge generelt genkendte syntaks for flere (\*) eller enkelt søgninger med jokertegn (?) tegn. Bemærk Lucene forespørgsel parseren understøtter brugen af disse symboler med et enkelt ord og ikke en sætning.

**Eksempel 9** – Højreklik på forespørgslen. Søge efter job, der indeholder præfikset 'prog', som medtages business titler med betingelserne programming og programmer i den.

- [& forespørgselstype = fuld & $select = business_title & Søg = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:prog*)

Du kan ikke bruge en * eller? Symbol som det første tegn i en søgning.


## <a name="next-steps"></a>Næste trin

Prøv at angive Lucene forespørgsel parseren i din kode. Følgende links beskriver, hvordan du konfigurerer søgeforespørgsler til både .NET og REST-API. Linkene Brug enkel standardsyntaksen, så du skal anvende det, du har lært fra denne artikel for at angive **Forespørgselstype**.

- [Forespørgsel din Azure søgeindekset ved hjælp af .NET SDK](search-query-dotnet.md)
- [Forespørgsel din Azure søgeindekset ved hjælp af REST-API](search-query-rest-api.md)


 