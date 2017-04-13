<properties
    pageTitle="Hvad er Azure Søg | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Azure søgning er en fuldt administreret hostet skyen søgetjeneste. Yderligere oplysninger i denne funktionsoversigt."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="what-is-azure-search"></a>Hvad er Azure søgning?

Azure søgning er en løsning til skyen søgning som en tjeneste, der uddelegerer server og administration af infrastruktur til Microsoft, så du med en klar til brug tjeneste, som du kan udfylde med dine data og derefter bruge til at føje søgning til internettet eller mobile programmet. Azure søgning kan du nemt tilføje en stabil søgeoplevelse til dine programmer ved hjælp af en simpel [REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) eller [.NET SDK](search-howto-dotnet-sdk.md) uden administration af søgning infrastruktur eller blive ekspert i Søg.

## <a name="give-your-users-a-powerful-search-experience"></a>Give brugerne en effektiv søgeoplevelse

**Effektiv forespørgsler** kan udarbejdet ved hjælp af [syntaksen for Simpel forespørgsel](https://msdn.microsoft.com/library/azure/dn798920.aspx), som indeholder logiske operatorer, udtryk Søg operatorer, suffiks operatorer, rangorden for operatorer. Desuden kan [Lucene forespørgselssyntaksen](https://msdn.microsoft.com/library/azure/mt589323.aspx) aktivere uskarpt søgning, afstand søgning, øger ord og regulære udtryk. Azure søgning understøtter også brugerdefinerede Leksikalsk programmer til at tillade dit program til at håndtere komplekse forespørgsler ved hjælp af fonetisk tilsvarende og regulære udtryk analyse.

**Understøttelse af sprog** er [inkluderet for 56 forskellige sprog](https://msdn.microsoft.com/library/azure/dn879793.aspx). Bruge både Lucene programmer til analyse og programmer til Microsoft analyse (afgrænset af årstal i naturligt sprog i Office og Bing processing), kan Azure søgning analysere tekst i dit program søgefeltet til at håndtere intelligent sprogspecifikke linguistics herunder verbaltider, køn, uregelmæssige flertalsformerne navneord (fx ' mus' kontra 'mus'), word deaktivere sammensætning, word seneste (til sprog uden mellemrum), og meget mere.

**Søgeforslag** kan være aktiveret for autocompleted søgning søjler og til Autoudfyldning forespørgsler. [Faktisk dokumenter i indekset foreslås](https://msdn.microsoft.com/library/azure/dn798936.aspx) som brugere Angiv delsøgning input.

**Tryk på fremhævning** [gør det](https://msdn.microsoft.com/library/azure/dn798927.aspx) muligt at få vist udsnit af af tekst i hvert resultat, der indeholder ens for deres forespørgsel. Du kan frit vælge, hvilke felter returnere fremhævede kodestykker.

**Facetteret navigation** er let føjes til din side med søgeresultater med Azure søgning. Ved hjælp af [blot en enkelt forespørgselsparameter](https://msdn.microsoft.com/library/azure/dn798927.aspx)Azure, returnerer søgningen alle de nødvendige oplysninger for at oprette en facetteret søgeoplevelse i din app Brugergrænsefladen til at give dine brugere til at analysere ned og filtrere søgeresultaterne (fx filtrere katalog emner efter pris område eller brand).

Understøttelse af **geografisk geografiske** gør det muligt at intelligent at behandle, filtrere og vise geografiske placeringer. Azure søgning gør det muligt for brugerne at udforske data baseret på nærheden af et søgeresultat til et andet sted eller baseret på en bestemt geografisk område. I denne video forklares, hvordan det fungerer: [kanal 9: Azure søgning og geospatiale data](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

**Filtre** kan bruges til at inkorporere facetteret navigation i dit program brugergrænseflade, forbedre forespørgsel udformning nemt, og filter baseret på eller udvikler-brugerdefinerede kriterier. Oprette effektive filtre ved hjælp af [OData-syntaks](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>Klæde udviklerne med en nemt bruge tjeneste

**Høj tilgængelighed** sikrer, at en meget pålidelig service søgeoplevelse. Når skaleres korrekt, [Azure Søg tilbyder en 99,9% SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

**Fuldt administreret** som en til en komplet løsning, Azure Søg kræver absolut ingen styring af infrastruktur. Din tjeneste, kan nemt skræddersyet til dine behov ved at skalere todimensional til at håndtere flere lagring af dokumenter, højere forespørgsel indlæse eller begge.

**Integration af data** ved hjælp af [indeks](https://msdn.microsoft.com/library/azure/dn946891.aspx) gør det muligt for Azure søgning til automatisk at gennemsøge Azure SQL-Database, Azure DocumentDB eller [Azure Blob-lager](search-howto-indexing-azure-blob-storage.md) for at synkronisere dit søgeindeks indhold med din primære datalager.

**Dokument knække** er tilgængelig (i øjeblikket preview) [til at læse og indeksere overordnede filformater](search-howto-indexing-azure-blob-storage.md) herunder Microsoft Office samt PDF og HTML-dokumenter.

**Søg trafik analytics** er [nemt at indsamles og analysere](search-traffic-analytics.md) låse op indsigt fra hvad brugerne skriver i søgefeltet.

**Enkel pointmodel for kundeemner** er en fordel af Azure søgning. [Vundne profiler](https://msdn.microsoft.com/library/azure/dn798928.aspx) bruges til at tillade organisationer til model relevans som en funktion af værdier i selve dokumenterne. For eksempel du måske nyere produkter eller afviste produkter at blive vist højere i søgeresultaterne. Du kan også oprette vurdering profiler ved hjælp af mærker til tilpassede vundne baseret på kunde søgeindstillingerne, du har registrerede og gemt separat.

**Sortering** tilbydes for flere felter via indeks skemaet og derefter skifter på forespørgsel-tid med en enkelt søgning parameter.

**Sideopdeling** og (throttling) dine søgeresultater er [enkle med kontrolelementet perfekt](search-pagination-page-layout.md) , Azure søgning indeholder i forhold til dine søgeresultater.  

**Search explorer** kan du problem forespørgsler alle dine indeks højre fra din konto Azure portal så du nemt kan teste forespørgsler og justere vurdering profiler.

## <a name="how-it-works"></a>Sådan fungerer det

### <a name="1-provision-service"></a>1. klargøring service
Du kan dreje op en Azure Search-tjenesten ved hjælp af [Azure Portal](https://portal.azure.com/) eller [Azure ressource Management API](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Afhængigt af hvordan du konfigurerer search-tjenesten, skal du bruge enten den gratis niveau af tjeneste, der deles med andre Azure Søg abonnenter eller et [betalt lag](https://azure.microsoft.com/pricing/details/search/) , der helt helliger ressourcer, der skal bruges kun i din tjeneste. Når din tjeneste, kan du også vælge området af datacenter, vært for din tjeneste.

Afhængigt af hvilken niveau i tjeneste, du vælger, kan du tilpasse din tjeneste todimensional: 1) tilføje replikaer kan øge din kapacitet til at håndtere store forespørgsel indlæse og 2) tilføje partitioner for at tilføje lagerplads til flere dokumenter. Håndtering af dokument lager og forespørgsel overførselshastighed separat, kan du tilpasse search-tjenesten for din specifikke behov.

### <a name="2-create-index"></a>2. Opret indeks
Før du kan overføre dit indhold til din Azure Search-tjenesten, skal du først definere en Azure søgeindekset. Et indeks er som en databasetabel, der indeholder dine data og kan acceptere søgeforespørgsler. Du definerer indeks skemaet skal tilknyttes strukturen af de dokumenter, du vil søge, svarende til felter i en database.

Skemaet for disse indeks kan enten oprettes i portalen Azure eller fra et program, [ved hjælp af .NET SDK](search-howto-dotnet-sdk.md) eller [REST-API](https://msdn.microsoft.com/library/azure/dn798941.aspx). Når indekset er defineret, kan du derefter overføre dine data til Azure søgetjenesten hvor den efterfølgende er indekseret.

### <a name="3-index-data"></a>3. indeksere data
Når du har defineret felter og egenskaber for indekset, er du klar til at overføre indholdet til indekset. Du kan bruge en Skub eller træk model for at overføre data til indekset.

Hente modellen leveres via indeks, der kan konfigureres på demand eller planlagte opdateringer (se [indekseringsprogram handlinger (Azure Søg Service REST-API)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), så du nemt indtager data og dataændringer fra en Azure DocumentDB, Azure SQL-Database, Azure Blob-lager eller SQL Server, der er placeret i en Azure VM.

Push-modellen leveres via SDK eller REST API'er bruges til at sende opdaterede dokumenter til et indeks. Du kan skubbe data fra næsten alle datasæt ved hjælp af JSON-formatet. Se [tilføje, opdatere, eller slette dokumenter](https://msdn.microsoft.com/library/azure/dn798930.aspx) eller [Sådan bruger du .NET SDK)](search-howto-dotnet-sdk.md) vejledning i indlæsning af data.

### <a name="4-search"></a>4. søgning
Når du har udfyldt din Azure søgeindekset, kan du nu [søgeforespørgsler problem](https://msdn.microsoft.com/library/azure/dn798927.aspx) med din Tjenesteslutpunktet, ved hjælp af enkel HTTP-anmodninger med REST-API eller .NET SDK.

## <a name="try-it-now-for-free"></a>Prøv det nu (gratis!)
Du kan prøve Azure søgning i dag! Hvis du allerede har en Azure-konto, kan du [klargøring en tjeneste i det gratis niveau](search-create-service-portal.md).

Hvis du ikke har en Azure-konto kan du prøve en gratis, 60 minutters session uden du tilmelde dig påkrævet. Gå til [Prøve Azure App Service](http://go.microsoft.com/fwlink/p/?LinkId=618214) , og vælg "Web App." Vælg derefter skabelonen "ASP.NET + Azure Search" til at komme i gang.
