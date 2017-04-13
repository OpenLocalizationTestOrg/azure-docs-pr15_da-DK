<properties
    pageTitle="Sådan bruger du Table storage (C++) | Microsoft Azure"
    description="Gem strukturerede data i skyen ved hjælp af Azure Table storage, et NoSQL datalager."
    services="storage"
    documentationCenter=".net"
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

# <a name="how-to-use-table-storage-from-c"></a>Sådan bruger du Table storage fra C++

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Oversigt  
Denne vejledning viser dig, hvordan til at udføre almindelige scenarier ved hjælp af lagringstjeneste Azure-tabel. Eksemplerne er skrevet i C++ og bruge [Azure lagerplads klientbibliotek til C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). De scenarier, der er omfattet omfatter **oprettelse og sletning af en tabel** og **arbejde med tabel enheder**.

>[AZURE.NOTE] Denne vejledning er beregnet til biblioteket Azure lagerplads klient for C++ version 1.0.0 og derover. Den anbefalede version er lagerplads klientbibliotek 2.2.0, som er tilgængelig via [NuGet](http://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="create-a-c-application"></a>Oprette en C++-program  
I denne vejledning, skal bruge du storage-funktioner, der kan afspilles i en C++-program. For at gøre det, skal du installere biblioteket Azure-lager-klienten til C++ og oprette en Azure-lager-konto i abonnementet Azure.  

For at installere biblioteket Azure-lager-klienten til C++, kan du bruge følgende metoder:

-   **Linux:** Følg vejledningen på siden [Azure lagerplads klientbibliotek til C++ vigtigt](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows:** I Visual Studio, skal du klikke på **Værktøjer > NuGet Package Manager > pakke Manager-konsollen**. Skriv følgende kommando i [NuGet pakke Manager-konsollen](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) , og tryk på Enter.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-table-storage"></a>Konfigurere dit program tilladelse til at få adgang til Table storage  
Tilføj følgende medtage sætninger til toppen af filen C++, hvor du vil bruge Azure opbevaring API'er til at få adgang til tabeller:  

    #include "was/storage_account.h"
    #include "was/table.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurere en forbindelsesstreng Azure-lager  
En Azure-lager-klient bruges en forbindelsesstreng lager til lagring slutpunkter og legitimationsoplysninger for at få adgang til data management services. Når du kører en klientprogrammet, skal du angive forbindelsesstrengen lagerplads i følgende format. Brug navnet på kontoen lager og hurtigtast lagerplads for kontoen lagerplads, der er angivet i [Azure Portal](https://portal.azure.com) for *kontonavn* og *AccountKey* værdier. Du kan finde oplysninger om lagerplads firmaer og hurtigtaster [om Azure lagerplads konti](storage-create-storage-account.md). I dette eksempel vises, hvordan du kan definere et statisk felt til at holde forbindelsesstrengen:  

    // Define the connection string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Hvis du vil teste dit program i din lokale Windows-baseret computer, kan du bruge Azure [lagerplads emulator](storage-use-emulator.md) , der er installeret med [Azure SDK](https://azure.microsoft.com/downloads/). Lagerplads emulatoren er et værktøj, der simulerer de Azure Blob, kø og tabel tjenester, der er tilgængelige på din lokale computer. I følgende eksempel viser, hvordan du kan definere et statisk felt til at holde forbindelsesstrengen til din lokale lager emulator:  

    // Define the connection string with Azure storage emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Klik på knappen **Start** for at starte emulatoren Azure-lager, eller tryk på på Windows-tasten. Begynd at skrive **Azure lagerplads Emulator**, og vælg derefter **Microsoft Azure lagerplads Emulator** fra listen over programmer.  

I følgende eksempler forudsætter, at du har brugt en af disse to metoder til at få vist forbindelsesstreng lagerplads.  

## <a name="retrieve-your-connection-string"></a>Hente din forbindelsesstreng  
Du kan bruge klassen **cloud_storage_account** til at repræsentere kontooplysningerne lagerplads. For at hente kontooplysningerne lagerplads fra forbindelsesstrengen lagerplads, kan du bruge metoden analysér.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Få derefter en reference til en **cloud_table_client** klasse, som det kan du få reference objekter til tabeller og objekter, der er gemt i lagringstjeneste tabel. Følgende kode opretter et **cloud_table_client** objekt ved hjælp af objektet lagerplads konto vi hentet ovenfor:  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## <a name="create-a-table"></a>Oprette en tabel
Et **cloud_table_client** objekt kan du få reference objekter til tabeller og objekter. Følgende kode opretter et **cloud_table_client** objekt og bruges til at oprette en ny tabel.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();  

## <a name="add-an-entity-to-a-table"></a>Føje et objekt til en tabel
Hvis du vil tilføje et objekt til en tabel, skal du oprette et nyt **table_entity** objekt og sende dem til **table_operation::insert_entity**. Følgende kode bruger kundens fornavn som række nøgle og efternavn som tasten partition. Sammen, identificerer et objekt partition og række nøgle entydigt objektet i tabellen. Objekter med den samme Partitionsnøgle kan forespørges hurtigere end dem med forskellige partition taster, men med forskellige partition taster giver mulighed for større parallel handling skalerbarhed. Se [Microsoft Azure-lager ydeevne og skalerbarhed tjekliste](storage-performance-checklist.md)kan finde flere oplysninger.

Følgende kode opretter en ny forekomst af **table_entity** med nogle kundedata skal gemmes. Koden næste ringer **table_operation::insert_entity** for at oprette en **table_operation** objekt for at indsætte et objekt i en tabel, og knytter den nye tabel-enhed til den. Til sidst skal opkald koden metoden execute på objektet **cloud_table** . Og den nye **table_operation** sender en anmodning til tjenesten tabel til at indsætte objektet ny kunde i tabellen "personer".  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();

    // Create a new customer entity.
    azure::storage::table_entity customer1(U("Harp"), U("Walter"));

    azure::storage::table_entity::properties_type& properties = customer1.properties();
    properties.reserve(2);
    properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

    properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

    // Create the table operation that inserts the customer entity.
    azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

    // Execute the insert operation.
    azure::storage::table_result insert_result = table.execute(insert_operation);

## <a name="insert-a-batch-of-entities"></a>Indsætte en gruppe objekter
Du kan indsætte en mængde enheder til tjenesten tabel i én skrivehandling. Følgende kode opretter et **table_batch_operation** objekt, og derefter tilføjer tre indsætte handlinger til den. Hver Indsæt operation tilføjes ved at oprette et nyt objekt-objekt, angive dens værdier og derefter kalder metoden Indsæt på objektet **table_batch_operation** knytte objektet til en ny Indsæt handling. Derefter kaldes **cloud_table.execute** for at udføre handlingen.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define a batch operation.
    azure::storage::table_batch_operation batch_operation;

    // Create a customer entity and add it to the table.
    azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

    azure::storage::table_entity::properties_type& properties1 = customer1.properties();
    properties1.reserve(2);
    properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
    properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

    // Create another customer entity and add it to the table.
    azure::storage::table_entity customer2(U("Smith"), U("Ben"));

    azure::storage::table_entity::properties_type& properties2 = customer2.properties();
    properties2.reserve(2);
    properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
    properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

    // Create a third customer entity to add to the table.
    azure::storage::table_entity customer3(U("Smith"), U("Denise"));

    azure::storage::table_entity::properties_type& properties3 = customer3.properties();
    properties3.reserve(2);
    properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
    properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

    // Add customer entities to the batch insert operation.
    batch_operation.insert_or_replace_entity(customer1);
    batch_operation.insert_or_replace_entity(customer2);
    batch_operation.insert_or_replace_entity(customer3);

    // Execute the batch operation.
    std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

Nogle ting, du skal være opmærksom på batchen handlinger:  

-   Du kan udføre op til 100 Indsæt Slet Flet, Erstat, og Indsæt eller Flet, og Indsæt eller Erstat i en hvilken som helst kombination i en enkelt gruppe.  
-   Batchhandlingen kan have en Hent handling, hvis det er den eneste operation i batchen.  
-   Alle objekter i en enkelt batchhandling skal have den samme Partitionsnøgle.  
-   Batchhandlingen er begrænset til en 4-MB dataene.  

## <a name="retrieve-all-entities-in-a-partition"></a>Hente alle objekter i en partition
Hvis du vil forespørge en tabel for alle objekter i en partition, skal du bruge et **table_query** objekt. Følgende kodeeksempel angiver et filter for enheder, hvor "Jensen" er tasten partition. I dette eksempel udskriver felterne for de enkelte objekter i forespørgselsresultaterne til konsollen.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Print the fields for each customer.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

Forespørgslen i dette eksempel viser alle de enheder, der opfylder filterkriterierne. Hvis du har store tabeller og har brug for at hente tabel enheder ofte, anbefaler vi, at gemmer du dine data i Azure lagerplads BLOB i stedet.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Hente et interval af objekter i en partition
Hvis du ikke vil forespørge alle objekterne i en partition, kan du angive et område ved at kombinere filteret partition vigtige med en række vigtige filter. Følgende kodeeksempel bruger to filtre til at få alle objekter i partition "Jensen", hvor tasten række (Fornavn) starter med et bogstav tidligere end "E" i alfabetet og derefter udskrives forespørgselsresultaterne.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table query.
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
        azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
        azure::storage::query_comparison_operator::equal, U("Smith")),
        azure::storage::query_logical_operator::op_and,
        azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Loop through the results, displaying information about the entity.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

## <a name="retrieve-a-single-entity"></a>Hente et enkelt objekt
Du kan skrive en forespørgsel for at hente en enkelt, specifik enhed. Følgende kode bruger **table_operation::retrieve_entity** til at angive kunden 'Jeff Smith'. Denne metode returnerer et enkelt objekt i stedet for en samling, og den returnerede værdi er i **table_result**. Angive både partition og række taster i en forespørgsel er den hurtigste måde at hente et enkelt objekt fra tjenesten tabel.  

    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Output the entity.
    azure::storage::table_entity entity = retrieve_result.entity();
    const azure::storage::table_entity::properties_type& properties = entity.properties();

    std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## <a name="replace-an-entity"></a>Erstatte et objekt
Hvis du vil erstatte et objekt, hente det fra tjenesten tabel, redigere objektet enhed og derefter gemme ændringerne tilbage til tjenesten tabel. Følgende kode ændres en eksisterende kunde telefon og mailadresse. I stedet for opkald **table_operation::insert_entity**, bruger denne kode **table_operation::replace_entity**. Derved objektet skal erstattes fuldt ud på serveren, medmindre objektet på serveren er ændret, siden det blev hentet, i hvilket tilfælde handlingen mislykkes. Denne fejl er at forhindre, at dit program fra ved et uheld overskrive en ændring, der er foretaget mellem hentning og opdatering af en anden komponent i dit program. Stort håndtering af denne fejl er at hente objektet igen, Foretag dine ændringer (hvis det er stadig er gyldig), og udfør derefter en anden **table_operation::replace_entity** handling. Næste afsnit viser dig, hvordan at tilsidesætte denne funktionsmåde.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Replace an entity.
    azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
    properties_to_replace.reserve(2);

    // Specify a new phone number.
    properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

    // Specify a new email address.
    properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

    // Create an operation to replace the entity.
    azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result replace_result = table.execute(replace_operation);

## <a name="insert-or-replace-an-entity"></a>Indsæt eller-Erstat et objekt
**table_operation::replace_entity** handlinger mislykkes, hvis objektet er blevet ændret siden den blev hentet fra serveren. Desuden skal du hente objektet fra serveren først i rækkefølge for **table_operation::replace_entity** skal lykkes. Nogle gange, men du ikke ved, hvis objektet, der findes på serveren, og de aktuelle værdier, der er gemt i den irrelevante – opdateringen skal overskrive dem alle. Hvis du vil gøre dette, vil du bruge en **table_operation::insert_or_replace_entity** handling. Denne handling indsætter enheden, hvis det ikke findes, eller erstatter den, hvis det er tilfældet, uanset hvor den seneste opdatering blev oprettet. I kodeeksemplet nedenfor objektet kunde for Jeff Smith hentes stadig, men gemmes det derefter tilbage til serveren via **table_operation::insert_or_replace_entity**. Opdateringer til objektet mellem hentning og opdateringshandling vil blive overskrevet.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Insert-or-replace an entity.
    azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

    properties_to_insert_or_replace.reserve(2);

    // Specify a phone number.
    properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

    // Specify an email address.
    properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

    // Create an operation to insert-or-replace the entity.
    azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## <a name="query-a-subset-of-entity-properties"></a>Forespørgsel et undersæt af egenskaber for enheder  
En forespørgsel til en tabel kan hente blot nogle få egenskaber fra et objekt. Forespørgslen i følgende kode bruger **table_query::set_select_columns** metoden til at returnere kun mailadresser af enheder i tabellen.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define the query, and select only the Email property.
    azure::storage::table_query query;
    std::vector<utility::string_t> columns;

    columns.push_back(U("Email"));
    query.set_select_columns(columns);

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Display the results.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

        const azure::storage::table_entity::properties_type& properties = it->properties();
        for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
        {
            std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
        }

        std::wcout << std::endl;
    }

>[AZURE.NOTE] Forespørgsler nogle egenskaber fra et objekt er en mere effektiv handling end at hente alle egenskaber.

## <a name="delete-an-entity"></a>Slette et objekt
Du kan nemt slette et objekt, når du har hentet den. Når objektet er hentet, ringe **table_operation::delete_entity** med objektet til at slette. Derefter kalde metoden **cloud_table.execute** . Følgende kode henter og sletter en enhed med en Partitionsnøgle af "Sørensen" og en række af "Jeff".  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);  

## <a name="delete-a-table"></a>Slette en tabel
Følgende kodeeksempel sletter endelig, en tabel fra en lagerplads konto. En tabel, der er blevet slettet udgår skal oprettes igen til et stykke tid, efter sletningen.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);

## <a name="next-steps"></a>Næste trin
Nu hvor du har lært grundlæggende tabel lagerplads, skal du følge disse links for at få flere oplysninger om Azure-lager:  

-   [Sådan bruger du Blob-lager fra C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Sådan bruger du kø lagerplads fra C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Liste over Azure lagerplads ressourcer i C++](storage-c-plus-plus-enumeration.md)
-   [Lagerplads klientbibliotek til C++ reference](http://azure.github.io/azure-storage-cpp)
-   [Azure lagerplads dokumentation](https://azure.microsoft.com/documentation/services/storage/)
