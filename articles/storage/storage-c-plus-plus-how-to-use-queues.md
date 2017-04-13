<properties
    pageTitle="Sådan bruger du kø lagerplads (C++) | Microsoft Azure"
    description="Lær at bruge tjenesten kø storage i Azure. Eksempler er skrevet i C++."
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

# <a name="how-to-use-queue-storage-from-c"></a>Sådan bruger du kø lagerplads fra C++  

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Oversigt
Denne vejledning viser dig, hvordan til at udføre almindelige scenarier ved hjælp af tjenesten Azure kø lagerplads. Eksemplerne er skrevet i C++ og bruge [Azure lagerplads klientbibliotek til C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). De scenarier, der er omfattet omfatter **indsættelse**, **visning**, **hente**og **slette** beskeder i kø samt **oprettelse og sletning af køer**.

>[AZURE.NOTE] Denne vejledning er beregnet til biblioteket Azure lagerplads klient for C++ version 1.0.0 og derover. Den anbefalede version er lagerplads klientbibliotek 2.2.0, som er tilgængelig via [NuGet](http://www.nuget.org/packages/wastorage) eller [GitHub](http://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Oprette en C++-program  
I denne vejledning, skal bruge du lagerplads funktioner, der kan afspilles i en C++-program.

For at gøre det, skal du installere biblioteket Azure-lager-klienten til C++ og oprette en Azure-lager-konto i abonnementet Azure.

For at installere biblioteket Azure-lager-klienten til C++, kan du bruge følgende metoder:

-   **Linux:** Følg vejledningen på siden [Azure lagerplads klientbibliotek til C++ vigtigt](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
-   **Windows:** I Visual Studio, skal du klikke på **Værktøjer > NuGet Package Manager > pakke Manager-konsollen**. Skriv følgende kommando i [NuGet pakke Manager-konsollen](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) , og tryk på **ENTER**.

        Install-Package wastorage

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurere dit program tilladelse til at få adgang til kø lagerplads
Tilføj følgende medtage sætninger til toppen af filen C++, hvor du vil bruge Azure opbevaring API'er til at få adgang til køer:  

    #include "was/storage_account.h"
    #include "was/queue.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurere en forbindelsesstreng Azure-lager

En Azure-lager-klient bruges en forbindelsesstreng lager til lagring slutpunkter og legitimationsoplysninger for at få adgang til data management services. Når du kører i et klienten på computeren, skal du angive forbindelsesstrengen lagerplads i følgende format med navnet på kontoen lager og tasten lagerplads adgang til kontoen lagerplads, der er angivet i [Azure Portal](https://portal.azure.com) for værdierne *kontonavn* og *AccountKey* . Du kan finde oplysninger om lagerplads firmaer og hurtigtaster [Om Azure lagerplads konti](storage-create-storage-account.md). I dette eksempel vises, hvordan du kan definere et statisk felt til at holde forbindelsesstrengen:  

    // Define the connection-string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Hvis du vil teste dit program i din lokale Windows-computer, kan du bruge Microsoft Azure [lagerplads emulator](storage-use-emulator.md) , der er installeret med [Azure SDK](https://azure.microsoft.com/downloads/). Lagerplads emulatoren er et værktøj, der simulerer de Blob, kø og tabel tjenester, der er tilgængelige i Azure på din lokale computer. I følgende eksempel viser, hvordan du kan definere et statisk felt til at holde forbindelsesstrengen til din lokale lager emulator:  

    // Define the connection-string with Azure Storage Emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Vælg knappen **Start** for at starte emulatoren Azure-lager, eller tryk på på **Windows** -tasten. Begynd at skrive **Azure lagerplads Emulator**, og vælg **Microsoft Azure lagerplads Emulator** fra listen over programmer.

I følgende eksempler forudsætter, at du har brugt en af disse to metoder til at få vist forbindelsesstreng lagerplads.

## <a name="retrieve-your-connection-string"></a>Hente din forbindelsesstreng
Du kan bruge klassen **cloud_storage_account** til at repræsentere kontooplysningerne lagerplads. For at hente kontooplysningerne lagerplads fra forbindelsesstrengen lagerplads, kan du bruge metoden **Fortolk** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-queue"></a>Sådan: oprette en kø
Et **cloud_queue_client** objekt kan du få reference objekter til køer. Følgende kode opretter et **cloud_queue_client** objekt.

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create a queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

Bruge objektet **cloud_queue_client** til at få en reference til den kø, du vil bruge. Du kan oprette køen, hvis den ikke findes.

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();  

## <a name="how-to-insert-a-message-into-a-queue"></a>Sådan: indsætte en meddelelse i en kø
Hvis du vil indsætte en meddelelse i en eksisterende kø, skal du først oprette en ny **cloud_queue_message**. Derefter skal du ringe til metoden **add_message** . Du kan oprette en **cloud_queue_message** fra en streng eller en matrix af **byte** . Her er kode, som opretter en kø (hvis den ikke findes) og indsætter meddelelsen 'Hej, store verden':

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();

    // Create a message and add it to the queue.
    azure::storage::cloud_queue_message message1(U("Hello, World"));
    queue.add_message(message1);  

## <a name="how-to-peek-at-the-next-message"></a>Sådan: Kig på den næste meddelelse
Du kan vise meddelelsen foran en kø uden at fjerne den fra køen ved at ringe til metoden **peek_message** .

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Peek at the next message.
    azure::storage::cloud_queue_message peeked_message = queue.peek_message();

    // Output the message content.
    std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Sådan: ændre indholdet af en meddelelse i køen
Du kan ændre indholdet af en meddelelse direkte i køen. Hvis meddelelsen repræsenterer en arbejdsopgave, kan du bruge denne funktion til at opdatere status for arbejdsopgaven. Følgende kode opdaterer kø meddelelsen med ny oplysningerne, og angiver synlighed timeout for at udvide en anden 60 sekunder. Dette gemmer tilstanden for arbejde, der er knyttet til meddelelsen, og giver klienten på en anden minut for at fortsætte med at arbejde i meddelelsesboksen. Du kan bruge denne teknik til at spore flere trin arbejdsprocesser på kø meddelelser uden at starte forfra fra begyndelsen, hvis et behandlingstrin mislykkes på grund af hardware og software fejl. Typisk, du vil beholde antal et forsøg samt, og hvis meddelelsen er forsøgt igen mere end n gange, du vil slette den. Dette beskytter mod en meddelelse, der udløser en programfejl, hver gang behandles.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the message from the queue and update the message contents.
    // The visibility timeout "0" means make it visible immediately.
    // The visibility timeout "60" means the client can get another minute to continue
    // working on the message.
    azure::storage::cloud_queue_message changed_message = queue.get_message();

    changed_message.set_content(U("Changed message"));
    queue.update_message(changed_message, std::chrono::seconds(60), true);

    // Output the message content.
    std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  

## <a name="how-to-de-queue-the-next-message"></a>Sådan: Fjern kø den næste meddelelse
Din kode kø deaktivere en meddelelse fra en kø i to trin. Når du ringer **get_message**, får du den næste meddelelse i en kø. En meddelelse, der returneres fra **get_message** ikke er synligt for andre kode, der læser meddelelser fra denne kø. For at afslutte fjerner meddelelsen fra køen, skal du også kontakte **delete_message**. Denne proces for at fjerne en meddelelse sikrer, at hvis din kode ikke behandle en meddelelse på grund af hardware og software fejl, kan en anden forekomst af din kode få den samme meddelelse og prøve igen. Din kode kalder **delete_message** højre, når meddelelsen er blevet behandlet.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the next message.
    azure::storage::cloud_queue_message dequeued_message = queue.get_message();
    std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

    // Delete the message.
    queue.delete_message(dequeued_message);

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Sådan: udnytte yderligere indstillinger for meddelelser i deaktivere kø
Der er to måder, du kan tilpasse hentning af meddelelse fra en kø. Først, kan du få en mængde meddelelser (op til 32). Andet, kan du angive timeout for en længere eller kortere invisibility, så din kode, mere eller mindre tid til at behandle fuldt hver meddelelse. Følgende kodeeksempel bruger **get_messages** metoden til at få 20 meddelelser i et opkald. Det behandler derefter hver meddelelse ved hjælp af en **til** en løkke. Invisibility timeouten angives også til fem minutter for hver meddelelse. Bemærk, at 5 minutter starter for alle meddelelser på samme tid, så når 5 minutter er gået, siden opkaldet til **get_messages**, de meddelelser, som ikke er blevet slettet bliver synlige igen.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
    // 5 minutes (300 seconds).
    azure::storage::queue_request_options options;
    azure::storage::operation_context context;

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

    for (auto it = messages.cbegin(); it != messages.cend(); ++it)
    {
        // Display the contents of the message.
        std::wcout << U("Get: ") << it->content_as_string() << std::endl;
    }

## <a name="how-to-get-the-queue-length"></a>Sådan: få længde kø
Du kan få en estimering af antallet af beskeder i en kø. Metoden **download_attributes** beder tjenesten kø til at hente de kø attributter, herunder antallet meddelelser. Metoden **approximate_message_count** får tilnærmet antallet af beskeder i køen.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Fetch the queue attributes.
    queue.download_attributes();

    // Retrieve the cached approximate message count.
    int cachedMessageCount = queue.approximate_message_count();

    // Display number of messages.
    std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  

## <a name="how-to-delete-a-queue"></a>Sådan: slette en kø
Hvis du vil slette en kø og alle de meddelelser, der er indeholdt i den, skal ringe til metoden **delete_queue_if_exists** på objektet kø.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // If the queue exists and delete it.
    queue.delete_queue_if_exists();  

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende kø lagerplads, skal du følge disse links for at få flere oplysninger om Azure-lager.

-   [Sådan bruger du Blob-lager fra C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Sådan bruger du Table Storage fra C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Listen Azure lagerplads ressourcer i C++](storage-c-plus-plus-enumeration.md)
-   [Lagerplads klientbibliotek til C++ Reference](http://azure.github.io/azure-storage-cpp)
-   [Azure-lager dokumentation](https://azure.microsoft.com/documentation/services/storage/)

