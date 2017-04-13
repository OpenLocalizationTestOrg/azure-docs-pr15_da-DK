<properties
    pageTitle="Introduktion til Azure Blob-lager (objekt storage) ved hjælp af .NET | Microsoft Azure"
    description="Gemme ustrukturerede data i skyen med Azure Blob-lager (objekt storage)."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-blob-storage-using-net"></a>Introduktion til Azure Blob-lager ved hjælp af .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Oversigt

Azure Blob-lager er en tjeneste, der gemmer ustrukturerede data i skyen som objekter/BLOB. BLOB-lager kan gemme alle typer tekst eller binære data, som et dokument, mediefil eller program installer. BLOB-lager kaldes også objekt lagerplads.

### <a name="about-this-tutorial"></a>Om dette selvstudium

Dette selvstudium viser, hvordan du kan skrive .NET-koden for nogle almindelige scenarier, ved hjælp af Azure Blob-lager. Scenarier, der er dækket omfatter overførsel, listen over, hente og slette BLOB.

**Anslået tid at gennemføre:** 45 minutter

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Azure-lager klientbibliotek til .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Azure Konfigurationsstyring til .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- En [Azure lagerplads konto](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Flere eksempler

For flere eksempler på brug af Blob-lager, kan du se [Introduktion til Azure Blob-lager i .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). Du kan hente Northwind og køre den, eller du kan gennemse koden på GitHub.


[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Tilføje erklæringer af navneområder

Tilføj følgende `using` sætninger til toppen af den `program.cs` fil:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### <a name="parse-the-connection-string"></a>Fortolke forbindelsesstrengen

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Oprette Blob service-klienten

Klassen **CloudBlobClient** gør det muligt at hente beholdere og blob, der er gemt i Blob-lager. Her er en metode til at oprette service klienten:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Nu er du klar til at skrive programkode, der læser data fra og skriver data til Blob-lager.

## <a name="create-a-container"></a>Oprette en objektbeholder

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

I dette eksempel viser, hvordan du opretter en objektbeholder, hvis den ikke allerede findes:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Ny objektbeholderen er privat, hvilket betyder, skal du angive produktnøglen lagerplads adgang for at hente BLOB fra denne beholder som standard. Hvis du vil gøre filerne i beholderen tilgængelig for alle, kan du angive objektbeholderen skal være offentlig ved hjælp af følgende kode:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

Alle på internettet kan se BLOB i en offentlig beholder, men du kan ændre eller slette dem kun, hvis du har hurtigtast relevante konto eller en delt adgang signatur.

## <a name="upload-a-blob-into-a-container"></a>Overføre en blob til en objektbeholder

Azure Blob-lager understøtter Bloker BLOB og siden BLOB.  I fleste tilfælde skal er Bloker blob typen anbefales at bruge.

Hent en objektbeholder reference for at overføre en fil til en blok blob, og bruge det til at få en blok blob reference. Når du har en blob reference, kan du overføre en hvilken som helst strøm af data til den ved at ringe til metoden **UploadFromStream** . Handlingen Opret blob, hvis det ikke tidligere findes, eller overskrive den, hvis den findes.

I følgende eksempel viser, hvordan du kan overføre en blob til en objektbeholder og antages det, at objektbeholderen allerede blev oprettet.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Liste over BLOB i en objektbeholder

Du kan få vist BLOB i en objektbeholder, først få en objektbeholder reference. Derefter kan du bruge den beholder **ListBlobs** metode til at hente BLOB og/eller mapper i postkassen. For at få adgang til den bredt udvalg af egenskaber og metoder til en returnerede **IListBlobItem**, skal du konverteret den til et **CloudBlockBlob**, **CloudPageBlob**eller **CloudBlobDirectory** objekt.  Hvis typen er ukendt, kan du bruge en type kontrol til at bestemme, kan det skal.  Følgende kode demonstrerer, hvordan du kan hente og output URI for hvert element på den `photos` objektbeholder:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Som vist ovenfor, kan du navngive BLOB med oplysninger om printersti i deres navne. Dette opretter en virtuel mappe-struktur, som du kan organisere og gennemgang, som du ville gøre et traditionelle filsystem. Bemærk, at mappestrukturen er kun virtuelle – de kun ressourcer, der er tilgængelige i Blob-lager er beholdere og BLOB. Biblioteket lagerplads klienten tilbyder imidlertid en **CloudBlobDirectory** objektet for at referere til en virtuel mappe og forenkle processen med at arbejde med blob, der er organiseret på denne måde.

For eksempel kan du overveje følgende sæt af Bloker BLOB i en objektbeholder med navnet `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Når du ringer **ListBlobs** på objektbeholderen 'fotos' (som i eksemplet ovenfor), returneres en hierarkisk liste. Den indeholder både **CloudBlobDirectory** og **CloudBlockBlob** objekter, der repræsenterer kataloger og BLOB i beholderen, henholdsvis. Resultatet ser sådan ud:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Du kan eventuelt angive parameteren **UseFlatBlobListing** for af metoden **ListBlobs** til **Sand**. I dette tilfælde skal returneres alle blob i beholderen som et **CloudBlockBlob** objekt. Opkaldet til **ListBlobs** til at returnere en flad oversigt ser sådan ud:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

og resultatet ser sådan ud:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## <a name="download-blobs"></a>Hente BLOB

For at hente BLOB skal først hente en blob reference og derefter kalde metoden **DownloadToStream** . I følgende eksempel bruges metoden **DownloadToStream** til at overføre blob indholdet til en stream-objekt, som du kan derefter fastholdes til en lokal fil.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Du kan også bruge metoden **DownloadToStream** for at hente indholdet af en blob som en tekststreng.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Slette BLOB

Slette en blob skal først hente en blob reference, og kald derefter metoden **slette** på den.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Listen BLOB i sider asynkront

Hvis du listen over et stort antal BLOB, eller du vil styre antallet resultater, du vender tilbage på én gang på listen, kan du angive BLOB på sider i resultaterne. I dette eksempel viser, hvordan til at returnere resultater i sider asynkront, så udførelse af ikke er blokeret under venter på at returnere et stort antal resultater.

I dette eksempel vises en flad blob listen over, men du kan også udføre en hierarkisk liste, ved at angive den `useFlatBlobListing` parameter i metoden **ListBlobsSegmentedAsync** til `false`.

Fordi metoden eksempel kalder en asynkron metode, den skal indledes med den `async` nøgleord, og den skal returnere en **opgave** -objekt. Nøgleordet await angivet for metoden **ListBlobsSegmentedAsync** afbryder udførelse af metoden eksempel, indtil listen opgaven er fuldført.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="writing-to-an-append-blob"></a>Skrive til en Føj blob

En Føj blob er en ny type blob, introduceret med version 5.x af biblioteket Azure lagerplads klient til .NET. En Føj blob er optimeret til Føj handlinger, som logføring. En Føj blob består af blokke ligesom en blok blob, men når du føjer en ny blok til en Føj blob, er det altid vedhæftes i slutningen af blob. Du kan ikke opdatere eller slette en eksisterende blok i en Føj blob. Blokering id'er for en Føj blob vises ikke, som de er til en blok blob.

Hver blok i en Føj blob kan være en anden størrelse, op til maksimalt 4 MB, og en Føj blob kan indeholde op til 50.000 dokumentkomponenter. Den maksimale størrelse på en Føj blob er derfor lidt mere end 195 GB (4 MB X 50.000 blokerer).

Eksemplet herunder opretter en ny Tilføj blob og tilføjer nogle data, simulere en simpel logføring handling.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Se [forstå Bloker blob, siden blob, og Føj BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx) kan finde flere oplysninger om forskellene mellem de tre typer BLOB.

## <a name="managing-security-for-blobs"></a>Administrere sikkerhed i forbindelse med BLOB

Beholder som standard Azure-lager dine data sikre ved at begrænse adgangen til konto ejer, som er i besiddelse af hurtigtasterne konto. Når du har brug for til at dele blob-data i din lagerplads konto, er det er vigtigt at gøre dette uden bringe sikkerheden for din konto access-taster. Du kan desuden kryptere blob-data for at sikre, at det er sikkert gå over en netværksforbindelse og i Azure-lager.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Kontrol af adgang til blob-data

Blob-data i din lagerplads konto er kun tilgængelig for lagerplads kontoejeren som standard. Godkendelse af anmodninger om mod Blob-lager kræver hurtigtast konto som standard. Dog kan du gøre visse blob-data tilgængelige for andre brugere. Har du to muligheder:

- **Anonym adgang:** Du kan gøre en objektbeholder eller dens BLOB offentligt tilgængeligt for anonym adgang. Du kan få flere oplysninger i [Administrer anonym adgang til beholdere og BLOB](storage-manage-access-to-resources.md) .
- **Delt access signaturer:** Du kan angive klienter med en delt access-signatur (SAS), som leverer delegeret adgang til en ressource i din lagerplads konto med tilladelser, du angiver med over et interval, du angiver. Du kan få flere oplysninger i [Brug af delte Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md) .

### <a name="encrypting-blob-data"></a>Kryptere blob-data

Azure-lager understøtter kryptering af blob data både på klienten og på serveren:

- **Klientsiden kryptering:** Biblioteket lagerplads klient til .NET understøtter kryptering af data i klientprogrammer før overførsel til Azure-lager og dekryptere data under hentning af klienten. Biblioteket understøtter også integration med Azure-tasten samling til Lagerstyring for konto nøgle. Du kan få flere oplysninger i [Klientsiden kryptering med .NET til Microsoft Azure-lager](storage-client-side-encryption.md) . Se også [Selvstudium: kryptere og dekryptere BLOB i Microsoft Azure-lager, med Azure-tasten samling](storage-encrypt-decrypt-blobs-key-vault.md).
- **Serversiden kryptering**: Azure-lager understøtter nu serversiden kryptering. Se [Azure lagerplads Service kryptering efter Data på resten (Preview)](storage-service-encryption.md).

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært det grundlæggende omkring af Blob-lager, skal du følge disse links for at få mere at vide.

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure-lager Explorer
- [Microsoft Azure lagerplads Explorer (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) er en gratis, enkeltstående app fra Microsoft, der gør det muligt at arbejde visuelt med Azure-lager data i Windows, OS X og Linux.

### <a name="blob-storage-samples"></a>BLOB storage eksempler

- [Introduktion til Azure Blob-lager i .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>BLOB storage reference

- [Lagerplads klientbibliotek til .NET reference](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [REST-API reference](http://msdn.microsoft.com/library/azure/dd179355)

### <a name="conceptual-guides"></a>Grundlæggende hjælpelinjer

- [Overføre data sammen med værktøjet AzCopy kommandolinjeparametre](storage-use-azcopy.md)
- [Introduktion til fillagring til .NET](storage-dotnet-how-to-use-files.md)
- [Sådan bruger du Azure blob-lager med WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
