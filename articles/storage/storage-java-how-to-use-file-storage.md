<properties
    pageTitle="Sådan bruger du fillagring fra Java | Microsoft Azure"
    description="Lær, hvordan du bruger tjenesten Azure-fil til at overføre, hente, liste, og Slet filer. Eksempler, der er skrevet i Java."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-file-storage-from-java"></a>Sådan bruger du fillagring fra Java

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Oversigt

I denne vejledning lærer du, hvordan du udfører grundlæggende handlinger på tjenesten Microsoft Azure fil storage. Eksempler, der er skrevet i Java lærer du, hvordan du oprette aktier og mapper, overføre, liste og slette filer. Hvis du er ny bruger af Microsoft Azure fillagring service, vil det være meget nyttige eksemplerne ved at følge begreberne i de følgende afsnit.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Oprette en Java-program

For at opbygge eksemplerne, skal du Java Development Kit (JDK) og [Azure lagerplads SDK til Java] []. Du skal også har oprettet en Azure-lager-konto.

## <a name="setup-your-application-to-use-file-storage"></a>Konfigurer dit program tilladelse til at bruge fillagring

For at bruge den Azure lagerplads API'er skal du tilføje følgende sætning til toppen af filen Java, hvor du vil have adgang til tjenesten storage fra.

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.file.*;

## <a name="setup-an-azure-storage-connection-string"></a>Konfiguration af en forbindelsesstreng Azure-lager

Hvis du vil bruge fillagring, skal du oprette forbindelse til kontoen Azure-lager. Det første trin er at konfigurere en forbindelsesstreng, som vi skal bruge til at oprette forbindelse til kontoen lagerplads. Lad os oprette en statisk variabel for at gøre dette.

    // Configure the connection-string with your values
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account_name;" +
        "AccountKey=your_storage_account_key";

> [AZURE.NOTE] Erstat your_storage_account_name og your_storage_account_key med de faktiske værdier for kontoen lagerplads.

## <a name="connecting-to-an-azure-storage-account"></a>Oprette forbindelse til en Azure-lager-konto

Hvis du vil oprette forbindelse til kontoen lagerplads, skal du bruge objektet **CloudStorageAccount** , der passerer en forbindelsesstreng til dens **Fortolk** metode.

    // Use the CloudStorageAccount object to connect to your storage account
    try {
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
    } catch (InvalidKeyException invalidKey) {
        // Handle the exception
    }

**CloudStorageAccount.parse** viser en InvalidKeyException, så du skal placere det i en prøve/produkter blok.

## <a name="how-to-create-a-share"></a>Sådan: oprette en del

Alle filer og mapper i fillagring er placeret i en objektbeholder, kaldet en **del**. Kontoen lagerplads kan have så mange aktier, som giver mulighed for din konto kapacitet. For at få adgang til en del og dens indhold, skal du bruge en fil lagerplads klient.

    // Create the file storage client.
    CloudFileClient fileClient = storageAccount.createCloudFileClient();

Du kan derefter ved hjælp af filen lagerplads klienten, til at få en reference til en del.

    // Get a reference to the file share
    CloudFileShare share = fileClient.getShareReference("sampleshare");

For at oprette faktisk del skal du bruge metoden **createIfNotExists** for objektet CloudFileShare.

    if (share.createIfNotExists()) {
        System.out.println("New share created");
    }

På dette tidspunkt indeholder **dele** en reference til et share med navnet **sampleshare**.

## <a name="how-to-upload-a-file"></a>Sådan: overføre en fil

Indeholder en Azure filshare lagring i det mindste, en rodmappe, hvor filer kan være placeret. I dette afsnit lærer du, hvordan du overføre en fil fra lokale lager til en del rodmappe.

Det første trin i overføre en fil er at opnå en reference til mappen, hvor den skal placeres. Du kan gøre dette ved at ringe til metoden **getRootDirectoryReference** for objektet del.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

Nu hvor du har en reference til rodmappen på del, kan du overføre en fil på den ved hjælp af følgende kode.

    // Define the path to a local file.
    final String filePath = "C:\\temp\\Readme.txt";

    CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
    cloudFile.uploadFromFile(filePath);

## <a name="how-to-create-a-directory"></a>Sådan: oprette en mappe

Du kan også organisere lager ved at placere filer i undermapper i stedet for få dem alle i rodmappen. Tjenesten Azure fil storage kan du oprette den ønskede mapper, som din konto tillader. Nedenstående kode opretter en underordnede mappe med navnet **sampledir** under rodmappen.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the sampledir directory
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    if (sampleDir.createIfNotExists()) {
        System.out.println("sampledir created");
    } else {
        System.out.println("sampledir already exists");
    }

## <a name="how-to-list-files-and-directories-in-a-share"></a>Sådan: liste over filer og mapper i en del

Få vist en liste over filer og mapper i en del gøres nemt ved at ringe til **listFilesAndDirectories** på en CloudFileDirectory reference. Metoden returnerer en liste over ListFileItem objekter, som du kan navigere på. Følgende kode vises som et eksempel, filer og mapper i rodmappen.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
        System.out.println(fileItem.getUri());
    }


## <a name="how-to-download-a-file"></a>Sådan: hente en fil

Én af de hyppigere handlinger, du udfører mod fillagring er at hente filer. I eksemplet nedenfor koden downloader SampleFile.txt og dens indhold vises.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the directory that contains the file
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    //Get a reference to the file you want to download
    CloudFile file = sampleDir.getFileReference("SampleFile.txt");

    //Write the contents of the file to the console.
    System.out.println(file.downloadText());

## <a name="how-to-delete-a-file"></a>Sådan: slette en fil

En anden almindelige lagerplads filhandling er sletning af filer. Følgende kode sletter en fil med navnet SampleFile.txt, der er gemt i en mappe med navnet **sampledir**.


    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory where the file to be deleted is in
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    String filename = "SampleFile.txt"
    CloudFile file;

    file = containerDir.getFileReference(filename)
    if ( file.deleteIfExists() ) {
        System.out.println(filename + " was deleted");
    }


## <a name="how-to-delete-a-directory"></a>Sådan: slette en mappe

Slette en mappe er en ganske enkelt opgave, selvom den skal være angivet, at du ikke kan slette en mappe, der stadig indeholder filer eller andre mapper.

    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory you want to delete
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    // Delete the directory
    if ( containerDir.deleteIfExists() ) {
        System.out.println("Directory deleted");
    }


## <a name="how-to-delete-a-share"></a>Sådan: slette en del

Hvis du sletter en del gøres ved at ringe til metoden **deleteIfExists** i et CloudFileShare objekt. Her er eksempelkode, der gør.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the file client.
       CloudFileClient fileClient = storageAccount.createCloudFileClient();

       // Get a reference to the file share
       CloudFileShare share = fileClient.getShareReference("sampleshare");

       if (share.deleteIfExists()) {
           System.out.println("sampleshare deleted");
       }
    } catch (Exception e) {
        e.printStackTrace();
    }

## <a name="next-steps"></a>Næste trin

Hvis du vil have mere at vide om andre Azure lagerplads API'er, skal du følge disse links.

- [Java Developer Center](http://azure.microsoft.com/develop/java/)
- [Azure-lager SDK til Java](https://github.com/azure/azure-storage-java)
- [Azure-lager SDK til Android](https://github.com/azure/azure-storage-android)
- [Azure-lager klient SDK Reference](http://dl.windowsazure.com/storage/javadoc/)
- [Azure-lager Services REST-API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Azure-lager-teamets Blog](http://blogs.msdn.com/b/windowsazurestorage/)
- [Overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md)
