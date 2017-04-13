<properties
    pageTitle="Oversigt over Azure lagerplads ressourcer med Microsoft Azure-lager klient-bibliotek til C++ | Microsoft Azure"
    description="Lær at bruge listen API'er i Microsoft Azure-lager klientbibliotek for C++ optælles beholdere, blob, køer, tabeller og objekter."
    documentationCenter=".net"
    services="storage"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="list-azure-storage-resources-in-c"></a>Listen Azure lagerplads ressourcer i C++

Listen handlinger er vigtige i mange udvikling scenarier med Azure-lager. I denne artikel beskrives, hvordan mest effektivt optælles objekter i Azure-lager ved hjælp af på listen API'er fastsat i biblioteket Microsoft Azure-lager klient C++.

>[AZURE.NOTE] Denne vejledning er beregnet til Azure-lager klientbibliotek til C++ version 2.x, som er tilgængelige via [NuGet](http://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp).

Biblioteket lagerplads klienten indeholder en række forskellige metoder til listen eller forespørgsel objekter i Azure-lager. I denne artikel omhandler følgende scenarier:

-   Listen beholdere på en konto
-   Listen BLOB i en objektbeholder eller virtuelle blob mappe
-   Listen køer på en konto
-   Liste over tabeller i en konto
-   Forespørgsel objekter i en tabel

Hver af disse metoder vises med forskellige overloads til forskellige scenarier.

## <a name="asynchronous-versus-synchronous"></a>Asynkron eller synkron

Da biblioteket lagerplads klient for C++ er bygget oven på [C++ RESTEN bibliotek](https://github.com/Microsoft/cpprestsdk), understøtter vi uden videre asynkrone handlinger ved hjælp af [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Eksempel:

    pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;

Synkron handlinger ombrydes de tilsvarende asynkrone handlinger:

    list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
    {
        return list_blobs_segmented_async(token).get();
    }

Hvis du arbejder med flere tråde programmer eller tjenester, anbefaler vi, at du bruger asynkron API'er direkte i stedet for oprettelse af en tråd til at ringe til Synkroniser API'er, som betydeligt påvirker din ydeevne.

## <a name="segmented-listing"></a>Segmenteret oversigt

Skalaen på skylagring kræver segmenteret posten. Du kan for eksempel have via en million BLOB i en objektbeholder med Azure blob- eller en milliarder objekter i en Azure-tabel. Dette er ikke teoretiske tal, men kunders brugen sager.

Det er derfor upraktisk til at få vist alle objekter i et enkelt svar. I stedet kan du angive objekter ved hjælp af sideinddeling. Hver af på listen API'er har en *opdelt* overbelastning.

Svar til en handling af segmenteret listen indeholder:

-   <i>_segment</i>, som indeholder sæt af resultater, der returneres til et enkelt opkald til listen over API.
-   *continuation_token*, som der overføres til det næste opkald for at få den næste side med resultater. Når der er ingen flere resultater til at returnere, er det fortsættelse token er null.

Et typisk opkald til at få vist alle BLOB i en objektbeholder kan for eksempel se ud som følgende kodestykke. Koden er tilgængelig i vores [eksempler](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

    // List blobs in the blob container
    azure::storage::continuation_token token;
    do
    {
        azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_diretory(it->as_directory());
        }
    }

        token = segment.continuation_token();
    }
    while (!token.empty());

Bemærk, at antallet af resultater returneret på en side for eksempel kan styres af parameter *max_results* i overbelastning af hver API:

    list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
        blob_listing_details::values includes, int max_results, const continuation_token& token,
        const blob_request_options& options, operation_context context)

Hvis du ikke angiver parameteren *max_results* , returneres den maksimale standardværdien for op til 5000 resultater i en enkelt side.

Bemærk også, at en forespørgsel til Azure Table storage kan returnere nogen poster eller færre poster end værdien af parameteren *max_results* , du har angivet, selvom fortsættelse tokenet ikke er tom. En grund kan være, at forespørgslen ikke kan udføres i fem sekunder. Så længe fortsættelse tokenet ikke er tomme, forespørgslen skal fortsætte, og din kode bør ikke antages størrelsen på segmentet resultater.

Anbefalede koder mønsteret for de fleste scenarier er opdelt liste, som indeholder eksplicitte statussen for listen eller forespørgsler, og hvordan tjenesten reagerer på hver enkelt anmodning. Særligt for C++ programmer eller tjenester, kan underniveau kontrollen over på listen status hjælpe kontrolelement hukommelse og ydeevne.

## <a name="greedy-listing"></a>Grådige oversigt

Tidligere versioner af biblioteket lagerplads klient for C++ (versioner 0.5.0 få vist og tidligere) medtages ikke opdelt posten API'er for tabeller og køer, som i følgende eksempel:

    std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
    std::vector<table_entity> execute_query(const table_query& query) const;
    std::vector<cloud_queue> list_queues() const;

Disse metoder er implementeret som indpakningsmaterialet segmenteret API'er. For hver enkelt respons segmenteret listes koden vedhæftes en vektor resultaterne og returneres alle resultater, når de fulde beholdere blev scannet.

Denne metode fungerer muligvis, når det lagerplads firma eller den tabel indeholder et lille antal objekter. Dog med et større antal objekter, kan den hukommelse, som kræves øge uden begrænsning, fordi alle resultater forblev i hukommelsen. Én liste handling kan tage meget lang tid, hvor brugeren havde ingen oplysninger om dens status.

Disse grådige oversigt API'er i SDK findes ikke i C#, Java eller JavaScript Node.js miljø. For at undgå de potentielle problemer ved at bruge disse grådige API'er skal vi fjernet dem i version 0.6.0 Preview.

Hvis din kode ringer disse grådige API'er:

    std::vector<azure::storage::table_entity> entities = table.execute_query(query);
    for (auto it = entities.cbegin(); it != entities.cend(); ++it)
    {
        process_entity(*it);
    }

Derefter skal du redigere koden for at bruge listen segmenteret API'er:

    azure::storage::continuation_token token;
    do
    {
        azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
        {
            process_entity(*it);
        }

        token = segment.continuation_token();
    } while (!token.empty());

Ved at angive parameteren *max_results* på målgruppen, kan du saldo mellem tal af anmodninger om og hukommelsesforbrug til at opfylde overvejelser for dit program.

Desuden, hvis du bruger segmenteret posten API'er, men dataene gemmes i en lokal af websteder i en "grådige" typografi, anbefaler også vi, at du refactor din kode for at håndtere lagring af data i en lokal samling omhyggeligt skaleres.

## <a name="lazy-listing"></a>Fløde oversigt

Selvom grådige posten opløftet potentielle problemer, er det praktisk, hvis der ikke er for mange objekter i objektbeholderen.

Hvis du også bruger C#- eller Oracle Java SDK'er, bør du have kendskab til ikke kan optælles programming modellen, som har en fløde-typografi liste, hvor data på en bestemt forskydning er kun hentet, hvis det er nødvendigt. I C++ indeholder skabelonen gentagelsesstreng-baserede også en lignende måde.

En typisk fløde oversigt API, ved hjælp af **list_blobs** som et eksempel ser sådan ud:

    list_blob_item_iterator list_blobs() const;

Et typisk kodestykke, der bruger fløde listen mønster kan se sådan ud:

    // List blobs in the blob container
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_directory(it->as_directory());
        }
    }

Bemærk, at fløde oversigt findes kun i synkron tilstand.

Sammenlignet med grådige oversigt, henter fløde oversigt data, når det er nødvendigt. Under dækker henter den data fra Azure-lager kun, når den næste gentagelsesstreng flyttes til næste segment. Derfor hukommelsesforbrug styres med en afgrænset størrelse, og handlingen er hurtig.

Fløde oversigt API'er er inkluderet i biblioteket lagerplads klient til C++ i version 2.2.0.

## <a name="conclusion"></a>Konklusion

I denne artikel gennemgås vi forskellige overloads til listen over API'er for forskellige objekter i biblioteket lagerplads klient for C++. Opsummering:

-   Async APIs anbefales under flere tråde scenarier.
-   Segmenteret oversigt anbefales til de fleste scenarier.
-   Fløde oversigt findes i biblioteket som en praktisk slikpapir i synkron scenarier.
-   Grådige posten kan ikke anbefales og er blevet fjernet fra biblioteket.

##<a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om Azure-lager og klientbibliotek for C++, i følgende ressourcer.

-   [Sådan bruger du Blob-lager fra C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Sådan bruger du Table Storage fra C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Sådan bruger du kø lagerplads fra C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Azure-lager-klient-bibliotek til C++ API dokumentation.](http://azure.github.io/azure-storage-cpp/)
-   [Azure-lager-teamets Blog](http://blogs.msdn.com/b/windowsazurestorage/)
-   [Azure-lager dokumentation](https://azure.microsoft.com/documentation/services/storage/)
