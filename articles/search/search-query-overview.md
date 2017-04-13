<properties
    pageTitle="Forespørgsel dit Azure søgeindeks | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Oprette en søgeforespørgsel i Azure Søg, og brug søgeparametre til at filtrere og sortere søgeresultaterne."
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index"></a>Forespørgsel dit Azure søgeindeks
> [AZURE.SELECTOR]
- [Oversigt](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTEN](search-query-rest-api.md)

Når du indsender search-anmodninger til Azure søgning, er der en række parametre, der kan angives sammen med de faktiske ord, der indtastes i søgefeltet i dit program. Disse forespørgselsparametre giver dig mulighed at opnå en nogle dybere styring af hele teksten brugernes søgeoplevelse.

Nedenfor vises en liste, som kort beskriver almindelige anvendelsesområder forespørgselsparametre i Azure Søg. Se de detaljerede sider til [REST-API](https://msdn.microsoft.com/library/azure/dn798927.aspx) og [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx)fuld om forespørgselsparametre og deres funktionalitet, skal.

## <a name="types-of-queries"></a>Typer af forespørgsler

Azure søgning indeholder mange indstillinger til at oprette meget effektive forespørgsler. De to primære typer forespørgsel, du vil bruge er `search` og `filter`. A `search` forespørgsel søger efter en eller flere udtryk i _alle søgefelterne i indekset_ , og fungerer på samme måde, du forventer en søgemaskine som Google eller Bing til at arbejde. A `filter` forespørgsel evaluerer et boolesk udtryk over alle _filtrerbare_ felter i et indeks. I modsætning til `search` forespørgsler `filter` forespørgsler svarer til de nøjagtige indholdet af et felt, hvilket betyder, at de er store og små bogstaver for strengfelter.

Du kan bruge søgninger og filtre sammen eller separat. Hvis du bruger dem sammen, filteret anvendes først på hele indekset, og klik derefter søgningen er udført på resultaterne af filteret. Filtre kan derfor være en praktisk metode til at forbedre forespørgselsydelse, da de reducere sættet af dokumenter, der skal behandle søgestrengen.

Syntaksen for filterudtryk er et undersæt af [OData filter sprog](https://msdn.microsoft.com/library/azure/dn798921.aspx). Du kan bruge enten [forenklet syntaksen](https://msdn.microsoft.com/library/azure/dn798920.aspx) eller [Lucene forespørgselssyntaksen](https://msdn.microsoft.com/library/azure/mt589323.aspx) der er beskrevet nedenfor for søgeforespørgsler.

### <a name="simple-query-syntax"></a>Syntaksen for Simpel forespørgsel
[Syntaksen for Simpel forespørgsel](https://msdn.microsoft.com/library/azure/dn798920.aspx) er standardsproget forespørgsel bruges til Azure søgning. Enkel forespørgselssyntaksen understøtter et antal almindelige Søg operatorer, herunder funktionerne og, eller ej, udtryk, suffiks og rangorden for operatorer.

### <a name="lucene-query-syntax"></a>Lucene forespørgselssyntaksen
[Lucene forespørgselssyntaksen](https://msdn.microsoft.com/library/azure/mt589323.aspx) kan du bruge den meget udbredte og udtryksfuld forespørgselssprog udviklet som en del af [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Med denne forespørgselssyntaksen kan du nemt opnå følgende funktioner: [felt-fastsat forespørgsler](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [uskarpt søgning](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [afstand søgning](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [ord øger](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [søgemønster søgning](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [søgning med jokertegn](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [syntaksen grundlæggende](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax)og [forespørgsler ved hjælp af booleske operatorer](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean).



## <a name="ordering-results"></a>Rækkefølge resultater
Når du modtager resultater for en søgeforespørgsel, kan du anmode om, at Azure søgning fungerer de resultater, der er sorteret efter værdierne i et bestemt felt. Som standard ordrer Azure søgningen baseret på pladsen for hvert dokument Søg resultat, der er afledt [TF IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)søgeresultaterne.

Hvis du vil Azure Søg til at returnere dine resultater, der er sorteret efter en anden værdi end søgning resultatet, kan du bruge den `orderby` søge parameter. Du kan angive værdien af den `orderby` parameter for at medtage feltnavne og opkald til den [ `geo.distance()` funktionen](https://msdn.microsoft.com/library/azure/dn798921.aspx) for geospatiale værdier. Hvert udtryk kan efterfølges af `asc` til at angive, at resultater der anmodes om i stigende rækkefølge, og `desc` til at angive, at resultater der anmodes om i faldende rækkefølge. Standard rangorden stigende rækkefølge.

## <a name="paging"></a>Sideinddeling
Azure søgning gør det nemt at implementere sideopdeling over søgeresultater. Ved hjælp af den `top` og `skip` parametre, kan du hurtigere udstede search-anmodninger, så du kan modtage det samlede sæt af søgeresultater i lettere håndterbare, sorteret undersæt, der aktiverer nemt god Søg Brugergrænsefladen fremgangsmåder. Når du modtager disse mindre undersæt af resultater, kan du også modtage antallet af dokumenter i det samlede sæt af søgeresultaterne.

Du kan lære mere om sideopdeling søgeresultater i artiklen [Sådan siden søgeresultater i Azure Søg](search-pagination-page-layout.md).


## <a name="hit-highlighting"></a>Tryk på fremhævning
I Azure Søg som understreger den nøjagtige del af søgeresultater, der svarer til søgestrengen er den nemme måde ved hjælp af den `highlight`, `highlightPreTag`, og `highlightPostTag` parametre. Du kan angive hvilke felter der _kan søges efter_ , skal have deres tilpasset tekst fremhæves samt angive de nøjagtige streng mærker til at føje til starten og slutningen af den tekst, der er tilpasset Azure søgningen returnerer.
