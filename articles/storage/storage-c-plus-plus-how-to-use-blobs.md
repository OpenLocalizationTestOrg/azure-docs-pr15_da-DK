<properties
    pageTitle="Sådan bruger du blob-lager (objekt storage) fra C++ | Microsoft Azure"
    description="Gemme ustrukturerede data i skyen med Azure Blob-lager (objekt storage)."
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

# <a name="how-to-use-blob-storage-from-c"></a>Sådan bruger du Blob-lager fra C++  

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Oversigt

Azure Blob-lager er en tjeneste, der gemmer ustrukturerede data i skyen som objekter/BLOB. BLOB-lager kan gemme alle typer tekst eller binære data, som et dokument, mediefil eller program installer. BLOB-lager kaldes også objekt lagerplads.

Denne vejledning gennemgås, hvordan du udfører almindelige scenarier ved hjælp af tjenesten Azure Blob-lager. Eksemplerne er skrevet i C++ og bruge [Azure lagerplads klientbibliotek til C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). De scenarier, der er dækket omfatter **overførsel af** **listen over**, **hente**og **slette** BLOB.  

>[AZURE.NOTE] Denne vejledning er beregnet til biblioteket Azure lagerplads klient for C++ version 1.0.0 og derover. Den anbefalede version er lagerplads klientbibliotek 2.2.0, som er tilgængelig via [NuGet](http://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Oprette en C++-program
I denne vejledning, skal bruge du lagerplads funktioner, der kan afspilles i en C++-program.  

For at gøre det, skal du installere biblioteket Azure-lager-klienten til C++ og oprette en Azure-lager-konto i abonnementet Azure.   

For at installere biblioteket Azure-lager-klienten til C++, kan du bruge følgende metoder:

-   **Linux:** Følg vejledningen på siden [Azure lagerplads klientbibliotek til C++ vigtigt](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows:** I Visual Studio, skal du klikke på **Værktøjer > NuGet Package Manager > pakke Manager-konsollen**. Skriv følgende kommando i [NuGet pakke Manager-konsollen](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) , og tryk på **ENTER**.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurere dit program tilladelse til at få adgang til Blob-lager  
Tilføj følgende medtage sætninger til toppen af filen C++, hvor du vil bruge Azure opbevaring API'er til at få adgang til BLOB:  

    #include "was/storage_account.h"
    #include "was/blob.h"

## <a name="setup-an-azure-storage-connection-string"></a>Konfiguration af en forbindelsesstreng Azure-lager
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

Få derefter en reference til en **cloud_blob_client** klasse, som du kan hente objekter, der repræsenterer beholdere og blob, der er gemt i tjenesten Blob Storage. Følgende kode opretter et **cloud_blob_client** objekt ved hjælp af objektet lagerplads konto vi hentet ovenfor:  

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## <a name="how-to-create-a-container"></a>Sådan: oprette en objektbeholder

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

I dette eksempel viser, hvordan du opretter en objektbeholder, hvis den ikke allerede findes:  

    try
    {
        // Retrieve storage account from connection string.
        azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

        // Create the blob client.
        azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

        // Retrieve a reference to a container.
        azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

        // Create the container if it doesn't already exist.
        container.create_if_not_exists();
    }
    catch (const std::exception& e)
    {
        std::wcout << U("Error: ") << e.what() << std::endl;
    }  

Ny objektbeholder er privat, og skal du angive produktnøglen lagerplads adgang for at hente BLOB fra denne beholder som standard. Hvis du vil gøre filer (BLOB) inde i beholderen tilgængelig for alle, kan du angive objektbeholderen skal være offentlig ved hjælp af følgende kode:  

    // Make the blob container publicly accessible.
    azure::storage::blob_container_permissions permissions;
    permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
    container.upload_permissions(permissions);  

Alle på internettet kan se BLOB i en offentlig beholder, men du kan ændre eller slette dem kun, hvis du har den relevante hurtigtast.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Sådan: overføre en blob til en objektbeholder
Azure Blob-lager understøtter Bloker BLOB og siden BLOB. I fleste tilfælde skal er Bloker blob typen anbefales at bruge.  

Hent en objektbeholder reference for at overføre en fil til en blok blob, og bruge det til at få en blok blob reference. Når du har en blob reference, kan du overføre en hvilken som helst strøm af data til den ved at ringe til metoden **upload_from_stream** . Handlingen Opret blob, hvis det ikke tidligere findes, eller overskrive den, hvis den findes. I følgende eksempel viser, hvordan du kan overføre en blob til en objektbeholder og antages det, at beholderen allerede blev oprettet.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Create or overwrite the "my-blob-1" blob with contents from a local file.
    concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
    blockBlob.upload_from_stream(input_stream);
    input_stream.close().wait();

    // Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
    // Retrieve a reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
    blob2.upload_text(U("more text"));

    // Retrieve a reference to a blob named "my-blob-3".
    azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
    blob3.upload_text(U("other text"));  

Du kan også bruge metoden **upload_from_file** til at overføre en fil til en blok blob.

## <a name="how-to-list-the-blobs-in-a-container"></a>Sådan: listen BLOB i en objektbeholder
Du kan få vist BLOB i en objektbeholder, først få en objektbeholder reference. Derefter kan du bruge den beholder **list_blobs** metode til at hente BLOB og/eller mapper i postkassen. For at få adgang til den bredt udvalg af egenskaber og metoder til en returnerede **list_blob_item**, skal du ringe til **list_blob_item.as_blob** metode til at få et **cloud_blob** objekt eller **list_blob.as_directory** metoden til at få et cloud_blob_directory objekt. Følgende kode demonstrerer, hvordan du kan hente og output URI for hvert element i beholderen **mit-eksempel-objektbeholder** :

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Output URI of each item.
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
        }
        else
        {
            std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
    }

Du kan finde flere oplysninger om listen over handlinger, [Listen Azure lagerplads ressourcer i C++](storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Sådan: hente BLOB
For at hente BLOB skal først hente en blob reference og derefter kalde metoden **download_to_stream** . I følgende eksempel bruges metoden **download_to_stream** til at overføre blob indholdet til en stream-objekt, du kan derefter fastholdes til en lokal fil.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Save blob contents to a file.
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    blockBlob.download_to_stream(output_stream);

    std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();

    outfile.write((char *)&data[0], buffer.size());
    outfile.close();  

Du kan også bruge metoden **download_to_file** til at hente indholdet af en blob til en fil.
Desuden kan du også bruge metoden **download_text** for at hente indholdet af en blob som en tekststreng.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

    // Download the contents of a blog as a text string.
    utility::string_t text = text_blob.download_text();

## <a name="how-to-delete-blobs"></a>Sådan: slette BLOB
Slette en blob skal først hente en blob reference, og kald derefter metoden **delete_blob** på den.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Delete the blob.
    blockBlob.delete_blob();

## <a name="next-steps"></a>Næste trin
Nu hvor du har lært det grundlæggende omkring af blob-lager, skal du følge disse links for at få flere oplysninger om Azure-lager.  

-   [Sådan bruger du kø lagerplads fra C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Sådan bruger du Table Storage fra C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Listen Azure lagerplads ressourcer i C++](storage-c-plus-plus-enumeration.md)
-   [Lagerplads klientbibliotek til C++ Reference](http://azure.github.io/azure-storage-cpp)
-   [Azure-lager dokumentation](https://azure.microsoft.com/documentation/services/storage/)
- [Overføre data sammen med værktøjet AzCopy kommandolinjen](storage-use-azcopy.md)
