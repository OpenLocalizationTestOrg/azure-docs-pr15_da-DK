<properties
    pageTitle="Sådan bruger du blob-lager (objekt storage) fra PHP | Microsoft Azure"
    description="Gemme ustrukturerede data i skyen med Azure Blob-lager (objekt storage)."
    documentationCenter="php"
    services="storage"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="how-to-use-blob-storage-from-php"></a>Sådan bruger du blob-lager fra PHP

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Oversigt

Azure Blob-lager er en tjeneste, der gemmer ustrukturerede data i skyen som objekter/BLOB. BLOB-lager kan gemme alle typer tekst eller binære data, som et dokument, mediefil eller program installationsprogrammet. BLOB-lager kaldes også objekt lagerplads.

Denne vejledning viser, hvordan du udfører almindelige scenarier ved hjælp af tjenesten Azure blob. Eksemplerne er skrevet i PHP og bruge [Azure SDK til PHP] [download]. De scenarier, der er dækket omfatter **overførsel af** **listen over**, **hente**og **slette** BLOB. Du kan finde flere oplysninger om blob, afsnittet [Næste trin](#next-steps) .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Oprette et PHP tjenesteprogram

Det eneste krav til oprettelse af et PHP-program, der har adgang til tjenesten Azure blob er referering klasser i Azure SDK til PHP fra i din kode. Du kan bruge en hvilken som helst udviklingsværktøjer til at oprette dit program, herunder Notesblok.

I denne vejledning, skal bruge du service-funktioner, som kan kaldes i et PHP program lokalt eller i kode, der kører en Azure web rolle, arbejder rolle eller websted.

## <a name="get-the-azure-client-libraries"></a>Få Azure klientbiblioteker

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-blob-service"></a>Konfigurere dit program tilladelse til at få adgang til tjenesten blob

Hvis du vil bruge tjenesten Azure blob API'er, skal du:

1. Reference til automatisk indlæser filen ved hjælp af sætningen [require_once] og
2. Henvise til en hvilken som helst klasser, du kan bruge.

I følgende eksempel viser, hvordan du medtage filen automatisk indlæser og henviser til klassen **ServicesBuilder** .

> [AZURE.NOTE] I dette eksempel (og andre eksemplerne i denne artikel) forudsætter, at du har installeret PHP klient billedbiblioteker til Azure via komponist. Hvis du har installeret bibliotekerne manuelt, skal du henvise til den `WindowsAzure.php` automatisk indlæser fil.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


I eksemplerne nedenfor, den `require_once` sætningen vises altid, men kun de klasser, der er nødvendige for eksempel at udføre der refereres til.

## <a name="set-up-an-azure-storage-connection"></a>Konfigurere en forbindelse til Azure-lager

For at oprette en Azure blob-tjenesteklient, skal du først har en gyldig forbindelsesstreng. Formatet for forbindelsesstrengen blob-tjenesten er:

For at få adgang til en direkte tjeneste:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

For at få adgang til lagerplads emulatoren:

    UseDevelopmentStorage=true


Hvis du vil oprette en hvilken som helst Azure service-klienten, skal du bruge klassen **ServicesBuilder** . Du kan:

* Overføre forbindelsesstrengen direkte til den eller
* Bruge **CloudConfigurationManager (CCM)** til at kontrollere flere eksterne kilder til forbindelsesstrengen:
    * Som standard leveres den med understøttelse af en ekstern kilde - miljøet variabler.
    * Du kan tilføje nye kilder ved at forlænge klassen **ConnectionStringSource** .

Eksempler, der er beskrevet her, sendes forbindelsesstrengen direkte.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## <a name="create-a-container"></a>Oprette en objektbeholder

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Et **BlobRestProxy** objekt kan du oprette en blob beholder med metoden **createContainer** . Når du opretter en objektbeholder, du kan angive indstillinger for objektbeholderen, men gøre så er ikke påkrævet. (I eksemplet nedenfor viser, hvordan til at angive objektbeholder kontrolelement adgangslisten (ACL) og objektbeholder metadata).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
    use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    // OPTIONAL: Set public access policy and metadata.
    // Create container options object.
    $createContainerOptions = new CreateContainerOptions();

    // Set public access policy. Possible values are
    // PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
    // CONTAINER_AND_BLOBS:
    // Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous
    // request, but cannot enumerate containers within the storage account.
    //
    // BLOBS_ONLY:
    // Specifies public read access for blobs. Blob data within this
    // container can be read via anonymous request, but container data is not
    // available. proxys cannot enumerate blobs within the container via
    // anonymous request.
    // If this value is not specified in the request, container data is
    // private to the account owner.
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    try {
        // Create container.
        $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Ringe til **setPublicAccess (PublicAccessType::CONTAINER\_og\_BLOB)** gør objektbeholder og blob-data tilgængelige via anonyme anmodninger. Ringe til **setPublicAccess(PublicAccessType::BLOBS_ONLY)** gør kun blob-data tilgængelige via anonyme anmodninger. Se [konfigurere objektbeholder ACL (REST-API)]kan finde flere oplysninger om objektbeholder ACLs[container-acl].

Du kan finde flere oplysninger om Blob service fejlkoder se [Blob Service fejlkoder][error-codes].

## <a name="upload-a-blob-into-a-container"></a>Overføre en blob til en objektbeholder

Hvis du vil overføre en fil som en blob, skal du bruge metoden **BlobRestProxy -> createBlockBlob** . Denne handling opretter blob, hvis det ikke findes, eller overskriver den, hvis det er tilfældet. Kodeeksemplet herunder antager, at beholderen er allerede blevet oprettet og bruger [fopen] [ fopen] til at åbne filen som en stream.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
        //Upload blob
        $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Bemærk, at det forrige sample overfører et blob som en stream. Men en blob kan også overføres som en streng ved hjælp af, for eksempel den [fil\_få\_indholdet] [ file_get_contents] funktionen. Hvis du vil gøre dette ved hjælp af det forrige eksempel, ændre `$content = fopen("c:\myfile.txt", "r");` til `$content = file_get_contents("c:\myfile.txt");`.

## <a name="list-the-blobs-in-a-container"></a>Liste over BLOB i en objektbeholder

Du kan få vist BLOB i en objektbeholder, kan du bruge metoden **BlobRestProxy -> listBlobs** med en **foreach** løkke til løkke på resultatet. Følgende kode viser navnet på hver blob som output i en objektbeholder og viser dets URI til browseren.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // List blobs.
        $blob_list = $blobRestProxy->listBlobs("mycontainer");
        $blobs = $blob_list->getBlobs();

        foreach($blobs as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="download-a-blob"></a>Hente en blob

Kalde metoden **BlobRestProxy -> getBlob** for at hente en blob og derefter kalde metoden **getContentStream** på resulterende **GetBlobResult** objektet.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Get blob.
        $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
        fpassthru($blob->getContentStream());
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Bemærk, at det foregående eksempel henter en blob som en stream ressource (standard). Men du kan bruge den [stream\_få\_indholdet] [ stream-get-contents] funktionen til at konvertere den returnerede stream til en streng.

## <a name="delete-a-blob"></a>Slette en blob

Hvis du vil slette en blob, du overføre objektbeholdernavnet på og blob til **BlobRestProxy -> deleteBlob**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete blob.
        $blobRestProxy->deleteBlob("mycontainer", "myblob");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-a-blob-container"></a>Slette en blob objektbeholder

Til sidst skal for at slette en blob objektbeholder skal du overføre objektbeholdernavnet til **BlobRestProxy -> deleteContainer**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteContainer("mycontainer");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende til Azure blob-tjenesten, skal du følge disse links for at lære mere komplekse lagerplads opgaver.

- Gå til [Azure-lager-teamets blog](http://blogs.msdn.com/b/windowsazurestorage/)
- Se [PHP Bloker blob eksempel](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php).
- Se [PHP siden blob eksempel](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php).
- [Overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md)
 
Du kan finde flere oplysninger, se også [PHP Developer Center](/develop/php/).


[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
