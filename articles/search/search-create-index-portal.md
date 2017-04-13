<properties
    pageTitle="Oprette en Azure søgeindekset ved hjælp af portalen Azure | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Oprette et indeks ved hjælp af portalen Azure."
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

# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Oprette en Azure søgeindekset ved hjælp af portalen Azure
> [AZURE.SELECTOR]
- [Oversigt](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTEN](search-create-index-rest-api.md)

I denne artikel fører dig gennem processen med at oprette en Azure [indeks](search-what-is-an-index.md) ved hjælp af portalen Azure.

Før følge denne vejledning og oprette et indeks, skal bør du allerede har [oprettet en Azure Search-tjenesten](search-create-service-portal.md).


## <a name="i-go-to-your-azure-search-blade"></a>Jeg. Gå til din Azure Søg blade
1. Klik på "Alle de ressourcer" i menuen til venstre side af [Azure-portalen](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Vælg din Azure søgetjenesten

## <a name="ii-add-and-name-your-index"></a>II. Tilføj og navngiv indekset
1. Klik på knappen "Tilføj indeks"
2. Navngive dit Azure søgeindeks. Da vi opretter et indeks til at søge efter hoteller i denne vejledning, har vi navngivet vores indeks "hoteller".
  * Navnet på indekset skal starte med et bogstav og indeholder kun små bogstaver, tal eller stiplet ("-").
  * Svarer til tjenestenavnet på din, navnet på indekset, som du vælger også bliver en del af URL-adressen slutpunkt sted, hvor du vil sende din HTTP-anmodninger til Azure Søg API
3. Klik på posten "Felter" for at åbne en ny blade

![](./media/search-create-index-portal/add-index.png)


## <a name="iii-create-and-define-the-fields-of-your-index"></a>III. Oprette og definere felterne i dit indeks
1. Ved at vælge posten "Felter", åbnes en ny blade med en formular til at angive indeksdefinitionen for din.
2. Føje felter til dit indeks ved hjælp af formularen.

  * Et *nøgle* -felt af typen Edm.String er obligatorisk for hver Azure søgeindekset. Dette nøglefelt oprettes som standard til feltet "id". Vi har ændret det til "hotelId" i vores indeks.
  * Visse egenskaber i dit indeks skema kan kun angives én gang og kan ikke opdateres i fremtiden. Derfor-skemaopdateringer, der kræver reindeksere eksempelvis ændring af felttyper er ikke i øjeblikket mulige efter den indledende konfiguration.
  * Vi har omhyggeligt valgt egenskabsværdier for hvert felt, der er baseret på hvordan vi synes, at de skal bruges i et program. Husk på din søgning brugernes oplevelse og business behov, når du designer dit indeks, som hvert felt skal tildeles de [relevante egenskaber](https://msdn.microsoft.com/library/azure/dn798941.aspx). Disse egenskaber kontrollere, hvilke søge funktioner (filtrering, faceting, sortering, søgning i hele teksten, osv.) gælder for hvilke felter. Det er for eksempel sandsynligt, at personer søger efter hoteller er interesseret i nøgleord forekomster på feltet "beskrivelse", så vi aktivere søgning i hele teksten for det pågældende felt ved at angive egenskaben "Søgbar".
    * Du kan også angive [sprog analyzer](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx) for hvert felt ved at klikke på fanen "Analyzer" på øverst del af bladet. Du kan se under, vi har valgt en fransk analyzer for et felt i vores indeks, der er beregnet til fransk tekst.

3. Klik på **OK** på bladet "Felter" for at bekræfte dit felt definitioner
4. Klik på **OK** på "Tilføj indeks" blade at gemme og oprette det indeks, du lige har angivet.

I skærmbillederne nedenfor kan du se, hvordan vi har med navnet og defineret felterne til vores "hoteller" indeks.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next"></a>Næste
Når du har oprettet en Azure søgeindekset, vil du være klar til at [overføre dit indhold i indekset](search-what-is-data-import.md) så du kan begynde at søge efter dine data.
