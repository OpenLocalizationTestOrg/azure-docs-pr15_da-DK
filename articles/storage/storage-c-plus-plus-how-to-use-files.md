<properties
    pageTitle="Sådan bruger du fillagring fra C++ | Microsoft Azure"
    description="Gemme data fra en fil i skyen med Azure fillagring."
    services="storage"
    documentationCenter=".net"
    authors="seguler"
    manager="jahogg"
    editor="tysonn" />

<tags ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="seguler" />

# <a name="how-to-use-file-storage-from-c"></a>Sådan bruger du fillagring fra C++

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]
[AZURE.INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

## <a name="about-this-tutorial"></a>Om dette selvstudium

I dette selvstudium lærer du, hvordan til at udføre grundlæggende handlinger på tjenesten Microsoft Azure fil storage. Eksempler, der er skrevet i C++, lærer du, hvordan du oprette aktier og mapper, overføre, liste og slette filer. Hvis du er ny bruger af Microsoft Azure fillagring service, vil det være meget nyttige eksemplerne ved at følge begreberne i de følgende afsnit.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Oprette en C++-program

For at opbygge eksemplerne, skal du installere biblioteket Azure lagerplads klient 2.4.0 for C++. Du skal også har oprettet en Azure-lager-konto.

For at installere Azure lagerplads klienten 2.4.0 for C++, kan du bruge en af følgende fremgangsmåder:

-   **Linux:** Følg vejledningen på siden [Azure lagerplads klientbibliotek til C++ vigtigt](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .

-   **Windows:** I Visual Studio, skal du klikke på **værktøjer &gt; NuGet Package Manager &gt; pakke Manager-konsollen**. Skriv følgende kommando i [NuGet pakke Manager-konsollen](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) , og tryk på **ENTER**.

    Installer-pakke wastorage

## <a name="set-up-your-application-to-use-file-storage"></a>Konfigurere dit program tilladelse til at bruge fillagring

Tilføj følgende medtage sætninger til toppen af filen C++, hvor du vil bruge Azure opbevaring API'er til at få adgang til filer:

    #include "was/storage_account.h"
    #include "was/file.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurere en forbindelsesstreng Azure-lager

Hvis du vil bruge fillagring, skal du oprette forbindelse til kontoen Azure-lager. Det første trin er at konfigurere en forbindelsesstreng, som vi skal bruge til at oprette forbindelse til kontoen lagerplads. Lad os oprette en statisk variabel for at gøre dette.

    // Define the connection-string with your values.
    const utility::string_t 
    storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

## <a name="connecting-to-an-azure-storage-account"></a>Oprette forbindelse til en Azure-lager-konto

Du kan bruge klassen **cloud_storage_account** til at repræsentere kontooplysningerne lagerplads. For at hente kontooplysningerne lagerplads fra forbindelsesstrengen lagerplads, kan du bruge metoden **Fortolk** .

    // Retrieve storage account from connection string. 
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-share"></a>Sådan: oprette en del

Alle filer og mapper i fillagring er placeret i en objektbeholder, kaldet en **del**. Kontoen lagerplads kan have så mange aktier, som giver mulighed for din konto kapacitet. For at få adgang til en del og dens indhold, skal du bruge en fil lagerplads klient.

    // Create the file storage client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();

Du kan derefter ved hjælp af filen lagerplads klienten, til at få en reference til en del.

    // Get a reference to the file share
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));

For at oprette del, skal du bruge metoden **create_if_not_exists** for objektet **cloud_file_share** .

    if (share.create_if_not_exists()) { 
        std::wcout << U("New share created") << std::endl;  
    }

På dette tidspunkt indeholder **dele** en reference til et share med navnet **Mine-eksempel-del**.

## <a name="how-to-upload-a-file"></a>Sådan: overføre en fil

I det mindste indeholder en Azure filshare lagerplads en rodmappe, hvor filer kan være placeret. I dette afsnit lærer du, hvordan du overføre en fil fra lokale lager til en del rodmappe.

Det første trin i overføre en fil er at opnå en reference til mappen, hvor den skal placeres. Du kan gøre dette ved at ringe til metoden **get_root_directory_reference** for objektet del.

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();

Nu hvor du har en reference til rodmappen på del, kan du overføre en fil på den. I dette eksempel overfører fra en fil, fra tekst og fra en stream.

    // Upload a file from a stream.
    concurrency::streams::istream input_stream = 
      concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

    azure::storage::cloud_file file1 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
    file1.upload_from_stream(input_stream);

    // Upload some files from text.
    azure::storage::cloud_file file2 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    file2.upload_text(_XPLATSTR("more text"));

    // Upload a file from a file.
    azure::storage::cloud_file file4 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    file4.upload_from_file(_XPLATSTR("DataFile.txt"));  

## <a name="how-to-create-a-directory"></a>Sådan: oprette en mappe

Du kan også organisere lager ved at placere filer i undermapper i stedet for få dem alle i rodmappen. Tjenesten Azure fil storage kan du oprette den ønskede mapper, som din konto tillader. Nedenstående kode opretter en mappe med navnet **mit-eksempel-mappe** under rodmappen samt en undermappe med navnet **mit-eksempel-undermappe**.
    
    // Retrieve a reference to a directory
    azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Return value is true if the share did not exist and was successfully created.
    directory.create_if_not_exists();
    
    // Create a subdirectory.
    azure::storage::cloud_file_directory subdirectory = 
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    subdirectory.create_if_not_exists();

## <a name="how-to-list-files-and-directories-in-a-share"></a>Sådan: liste over filer og mapper i en del

Få vist en liste over filer og mapper i en del gøres nemt ved at ringe til **list_files_and_directories** på en **cloud_file_directory** reference. For at få adgang til den bredt udvalg af egenskaber og metoder til en returnerede **list_file_and_directory_item**, skal du ringe til **list_file_and_directory_item.as_file** metode til at få et **cloud_file** objekt eller **list_file_and_directory_item.as_directory** metoden til at få et **cloud_file_directory** objekt.

Følgende kode viser, hvordan til at hente og output URI for hvert element i rodmappen på del på.

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    // Output URI of each item.
    azure::storage::list_file_and_diretory_result_iterator end_of_results;
    
    for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
    {
        if(it->is_directory())
        {
            ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
        else if (it->is_file())
        {
            ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
        }       
    }
    

## <a name="how-to-download-a-file"></a>Sådan: hente en fil

Først hente en filreference for at hente filer, og kald derefter metoden **download_to_stream** for at overføre indholdet af filen til en stream-objekt, du kan derefter fastholdes til en lokal fil. Du kan også bruge metoden **download_to_file** til at hente indholdet af en fil til en lokal fil. Du kan bruge metoden **download_text** til at hente indholdet af en fil som en tekststreng.

I følgende eksempel bruges metoderne **download_to_stream** og **download_text** til at vise hente de filer, som er oprettet i ovenstående afsnit.

    // Download as text
    azure::storage::cloud_file text_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    utility::string_t text = text_file.download_text();
    ucout << "File Text: " << text << std::endl;
    
    // Download as a stream.
    azure::storage::cloud_file stream_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    stream_file.download_to_stream(output_stream);
    std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();
    outfile.write((char *)&data[0], buffer.size());
    outfile.close();

## <a name="how-to-delete-a-file"></a>Sådan: slette en fil

En anden almindelige lagerplads filhandling er sletning af filer. Følgende kode sletter en fil med navnet Mine-eksempel-fil-3 gemt under rodmappen.

    // Get a reference to the root directory for the share. 
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    file.delete_file_if_exists();

## <a name="how-to-delete-a-directory"></a>Sådan: slette en mappe

Slette en mappe er en enkelt opgave, men Bemærk, at du ikke kan slette en mappe, der stadig indeholder filer eller andre mapper.
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // Get a reference to the directory.
    azure::storage::cloud_file_directory directory = 
      share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Get a reference to the subdirectory you want to delete.
    azure::storage::cloud_file_directory sub_directory =
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    
    // Delete the subdirectory and the sample directory.
    sub_directory.delete_directory_if_exists();
    
    directory.delete_directory_if_exists();

## <a name="how-to-delete-a-share"></a>Sådan: slette en del

Hvis du sletter en del gøres ved at ringe til metoden **delete_if_exists** i et cloud_file_share objekt. Her er eksempelkode, der gør.
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // delete the share if exists
    share.delete_share_if_exists();

## <a name="set-the-maximum-size-for-a-file-share"></a>Angive den maksimale størrelse for et filshare

Du kan angive kvote (eller maksimumstørrelsen) for et filshare i gigabyte. Du kan også kontrollere for at se, hvor meget data gemmes i øjeblikket delt.

Ved at angive kvoten for en del, kan du begrænse den samlede størrelse af de filer, der er gemt på dialogboksen del. Hvis den samlede størrelse af filer på dialogboksen del overstiger den angivne kvote delt, vil derefter klienter ikke kunne øge størrelsen af eksisterende filer eller oprette nye filer, medmindre filerne, som er tomme.

Eksemplet herunder viser, hvor der skal kontrolleres det aktuelle forbrug for en del, og hvordan du opretter kvoten for del.

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    if (share.exists())
    {
        std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();
    
        //This line sets the quota to 2560GB
        share.resize(2560);
    
        std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();
    
    }

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Oprette en delt adgang signatur til en fil eller et filshare

Du kan oprette en delt adgang signatur (SAS) for et filshare eller for individuelle filer. Du kan også oprette en delt access-politik på et filshare til at administrere delt adgang signaturer. Oprette en delt access-politik anbefales, da det giver mulighed for at ophæve Sikkerhedstilknytningerne, hvis den skal være kompromitteret.

I følgende eksempel oprettes en delt access-politik på en del, og derefter bruger denne politik til at give begrænsningerne for en Sikkerhedstilknytninger på en fil i dialogboksen del.

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client and get a reference to the share
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    if (share.exists())
    {
        // Create and assign a policy
        utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

        azure::storage::file_shared_access_policy sharedPolicy = 
          azure::storage::file_shared_access_policy();

        //set permissions to expire in 90 minutes
        sharedPolicy.set_expiry(utility::datetime::utc_now() + 
          utility::datetime::from_minutes(90));

        //give read and write permissions
        sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

        //set permissions for the share
        azure::storage::file_share_permissions permissions; 

        //retrieve the current list of shared access policies
        azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;
        
        //add the new shared policy
        policies.insert(std::make_pair(policy_name, sharedPolicy));

        //save the updated policy list
        permissions.set_policies(policies);
        share.upload_permissions(permissions);

        //Retrieve the root directory and file references
        azure::storage::cloud_file_directory root_dir = 
          share.get_root_directory_reference();
        azure::storage::cloud_file file = 
          root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

        // Generate a SAS for a file in the share 
        //  and associate this access policy with it.       
        utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);
        
        // Create a new CloudFile object from the SAS, and write some text to the file.     
        azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
        utility::string_t text = _XPLATSTR("My sample content");        
        file_with_sas.upload_text(text);        
        
        //Download and print URL with SAS.
        utility::string_t downloaded_text = file_with_sas.download_text();      
        ucout << downloaded_text << std::endl;      
        ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;
    
    }

Du kan finde flere oplysninger om oprettelse og bruge delte access signaturer, [Ved hjælp af delte Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om Azure-lager, kan du gennemse disse ressourcer:

-   [Lagerplads klientbibliotek til C++](https://github.com/Azure/azure-storage-cpp)

-   [Azure-lager Explorer](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)

-   [Azure-lager dokumentation](https://azure.microsoft.com/documentation/services/storage/)
