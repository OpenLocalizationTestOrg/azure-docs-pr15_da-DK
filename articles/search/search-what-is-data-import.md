<properties
    pageTitle="Afsendelse af data i Azure Søg | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Lær at overføre data til et indeks i Azure Søg."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search"></a>Overføre data til Azure søgning
> [AZURE.SELECTOR]
- [Oversigt](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTEN](search-import-data-rest-api.md)


## <a name="data-upload-models-in-azure-search"></a>Data overføre modeller i Azure Søg
Der er to måder til at udfylde dit Azure søgeindeks med dine data. Den første indstilling skubber manuelt dine data i indekset ved hjælp af Azure Search [REST-API](search-import-data-rest-api.md) eller [.NET SDK](search-import-data-dotnet.md). Den anden mulighed er at [pege en understøttet datakilde](search-indexer-overview.md) på dit Azure søgeindeks og lade Azure Søg automatisk trække dine data til søgetjenesten.

Denne vejledning kun dækker vejledning til brug af opslagsnål objektmodellen af data Overfør (som understøttes kun i [REST-API](search-import-data-rest-api.md) og [.NET SDK](search-import-data-dotnet.md)), men du kan stadig få mere at vide om at hente modellen nedenfor.

### <a name="push-data-to-an-index"></a>Push-data til et indeks

Denne metode refererer til automatisk at sende dataene til Azure søgning for at gøre det tilgængeligt til søgning. For programmer, der har meget lav ventetid krav (f.eks. Hvis du skal søge handlinger for at være synkroniseret med dynamisk lager databaser), modellen, push er kun.

Du kan bruge [REST-API](https://msdn.microsoft.com/library/azure/dn798930.aspx) eller [.NET SDK](search-import-data-dotnet.md) opslagsnål data til et indeks. Der findes i øjeblikket ingen understøttelse af værktøjet for have anbragt data via portalen.

Denne metode er mere fleksibel end hente modellen, fordi du kan overføre dokumenter, enkeltvis eller i batches (op til 1000 per batchen eller 16 MB, uanset hvilken grænse der kommer først). Push-model kan du også overføre dokumenter til Azure Søg uanset hvor dataene er.

### <a name="pull-data-into-an-index"></a>Hente data ind i et indeks

Hente modellen gennemsøgninger en understøttet datakilde og overfører automatisk dataene til du Azure søgeindekset for dig. Ved at spore ændringer og sletninger til eksisterende dokumenter ud over at genkende nye dokumenter, fjerne indeks at skulle administrere aktivt dataene i indekset.

Denne funktion er implementeret gennem *indeks*, tilgængelige for øjeblikket til [Blob-lager (preview)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [Azure SQL-database og SQL Server på Azure FOS](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)i Azure Søg.

Funktionen indekseringsprogram der vises i [Azure Portal](search-import-data-portal.md) samt som [REST-API](https://msdn.microsoft.com/library/azure/dn946891.aspx).
