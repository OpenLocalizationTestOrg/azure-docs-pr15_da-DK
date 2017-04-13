<properties
    pageTitle="Oprette en Azure søgeindekset | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Hvad er et indeks i Azure Søg, og hvordan bruges det?"
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

# <a name="create-an-azure-search-index"></a>Oprette en Azure søgeindekset
> [AZURE.SELECTOR]
- [Oversigt](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTEN](search-create-index-rest-api.md)

## <a name="what-is-an-index"></a>Hvad er et indeks?

Et *indeks* er en fast store af *dokumenter* og andre konstruktioner, der bruges af en Azure Search-tjenesten. Et dokument er én portion kan søges efter data i indekset. For eksempel en e-handel forhandler kan have et dokument for hvert element, de sælger, en nyheder organisation kan have et dokument for hver artikel osv. Tilknytte disse begreber til mere velkendte database tilsvarende: et *indeks* er lignende til en *tabel*, og *dokumenter* er stort set svarer til *rækker* i en tabel.

Når du tilføje/Overfør dokumenter og sender søgeforespørgsler til Azure søgning, kan du sende dine anmodninger til et bestemt indeks i search-tjenesten.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Felttyper og attributter i en Azure søgeindekset

Når du definerer dit skema, skal du angive navn og type på attributter for hvert felt i indekset. Felttypen klassificerer de data, der er gemt i det pågældende felt. Attributter er angivet for individuelle felter til at angive, hvordan feltet skal bruges. De følgende tabeller Optæl filtyper og attributter, du kan angive.


### <a name="field-types"></a>Felttyper
|Type|Beskrivelse|
|------------|-----------|
|*Edm.String*|Tekst, der kan eventuelt tokenized til søgning i hele teksten (word seneste, stammer osv).|
|*Collection(EDM.String)*|En liste over strenge, som du kan også være tokenized til søgning i hele teksten. Der er ingen teoretiske øvre grænse på antallet af elementer i en samling, men den 16 MB øvre grænse på datastørrelse gælder for af websteder.|
|*Edm.Boolean*|Indeholder værdier, Sand/falsk.|
|*Edm.Int32*|32-bit heltalsværdier.|
|*Edm.Int64*|64-bit heltalsværdier.|
|*Edm.Double*|Dobbelt præcision numeriske data.|
|*Edm.DateTimeOffset*|Dato, klokkeslæt-værdier, der er repræsenteret i formatet OData V4 (fx `yyyy-MM-ddTHH:mm:ss.fffZ` eller `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`).|
|*Edm.GeographyPoint*|Et punkt, der repræsenterer en geografiske placering på kuglen.|

Du kan finde flere oplysninger om Azure Søg [understøttede datatyper på MSDN](https://msdn.microsoft.com/library/azure/dn798938.aspx).



### <a name="field-attributes"></a>Feltattributter
|Attribut|Beskrivelse|
|------------|-----------|
|*Nøgle*|En streng, der indeholder det entydige ID for hvert dokument, der bruges til dokument slå op. Alle indeks skal have en nøgle. Kun ét felt kan være tasten, og typen skal være indstillet til Edm.String.|
|*Størrelse*|Angiver, om et felt kan returneres i et søgeresultat.|
|*Filtrerbare*|Giver mulighed for feltet, der skal bruges i filterforespørgsler.|
|*Kan sorteres*|Gør det muligt for en forespørgsel til at sortere søgeresultaterne ved hjælp af dette felt.|
|*Facetable*|Gør det muligt for et felt, der skal bruges i en [facetteret navigation](search-faceted-navigation.md) struktur til bruger selv-dirigeret filtrering. Felter, der indeholder gentagne værdier, som du kan bruge til at gruppere flere dokumenter (for eksempel flere dokumenter, der falder under en enkelt brand eller tjenestekategori) fungerer normalt bedst som facetter.|
|*Søgbar*|Markerer feltet som hele teksten kan søges i.|

Du kan finde flere oplysninger om Azure Søg [indeksattributter på MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx).



## <a name="guidance-for-defining-an-index-schema"></a>Vejledning til at definere et indeks-skema

Når du designer dit indeks, kan du tage dine tid i fasen planlægning til at gennemgå hver beslutning. Det er vigtigt, at du holder din søgning brugernes oplevelse og business behov huske, når du designer dit indeks, som hvert felt skal være tildelt de [korrekte attributter](https://msdn.microsoft.com/library/azure/dn798941.aspx). Ændre et indeks, når den er installeret omfatter genopbygge og genindlæse dataene.


Hvis data lagerplads krav ændrer sig med tiden, kan du øge eller mindske kapacitet med at tilføje eller fjerne partitioner. Yderligere oplysninger finder du [administrere dine søgetjenesten i Azure](search-manage.md) eller [Begrænsninger for tjenesten](search-limits-quotas-capacity.md).
