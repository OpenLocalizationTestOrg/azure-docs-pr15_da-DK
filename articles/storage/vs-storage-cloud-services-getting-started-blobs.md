<properties
    pageTitle="Komme i gang med blob-lager og Visual Studio forbundne tjenester (skytjenester) | Microsoft Azure"
    description="Sådan Introduktion til brug af Azure Blob-lager i en skybaseret tjeneste projekt i Visual Studio, når forbindelsen til en lagerplads konto ved hjælp af Visual Studio forbundne tjenester"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introduktion til Azure Blob-lager og Visual Studio forbundne tjenester (cloud services projekter)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Oversigt

I denne artikel beskrives, hvordan Introduktion til Azure Blob-lager, når du har oprettet eller refererer til en Azure-lager-konto ved hjælp af dialogboksen Visual Studio **Tilføje forbundne tjenester** i et Visual Studio cloud services-projekt. Vi viser dig, hvordan du kan få adgang til og oprette blob beholdere, og hvordan du kan udføre almindelige opgaver som at sende, vise og hente BLOB. Eksemplerne er skrevet i C\# og bruge [Microsoft Azure lagerplads klientbibliotek til .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Azure Blob-lager er en tjeneste til lagring af store mængder ustrukturerede data, der kan åbnes fra et vilkårligt sted i verden via HTTP eller HTTPS. En enkelt blob kan være en hvilken som helst størrelse. BLOB kan være ting som billeder, lyd- og videofiler, rækkedata og dokumentfiler.

Ligesom filer direkte i mapper, direkte BLOB storage i beholdere. Når du har oprettet en lagerplads, kan du oprette en eller flere beholdere i opbevaring. For eksempel på et lager, kaldet "Scrapbog", kan du oprette beholdere i opbevaring kaldet "billeder" for at gemme billeder, og en anden kaldet "lyd" til at gemme lydfiler. Når du opretter beholderne, kan du overføre individuelle blob filer hertil.

- Se [Introduktion til Azure Blob-lager ved hjælp af .NET](storage-dotnet-how-to-use-blobs.md)kan finde flere oplysninger om ved hjælp af programmering manipulere BLOB.
- Du kan finde generelle oplysninger om Azure-lager, [lagerplads dokumentation](https://azure.microsoft.com/documentation/services/storage/).
- Du kan finde generelle oplysninger om Azure Cloud Services, [Cloud Services dokumentation](https://azure.microsoft.com/documentation/services/cloud-services/).
- Du kan finde flere oplysninger om programmering ASP.NET-programmer, [ASP.NET](http://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Access blob beholdere i kode

Adgang fra et program til BLOB i skyen service projekter, skal du tilføje følgende elementer, hvis de ikke allerede findes.

1. Tilføj følgende kode navneområdeerklæringer til toppen af enhver C#-fil, du ønsker fra et program adgang til Azure-lager.

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Få et **CloudStorageAccount** objekt, der repræsenterer kontooplysningerne lagerplads. Brug følgende kode til at få den din lagerplads forbindelsesstreng og lagerplads kontooplysninger fra Azure service konfigurationen.

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. Få et **CloudBlobClient** objekt til at referere til en eksisterende objektbeholder i kontoen lagerplads.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

4. Få et **CloudBlobContainer** objekt til at referere til en bestemt blob objektbeholder.

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE] Du kan bruge alle den kode, der vises i den foregående fremgangsmåde foran den kode, der er vist i følgende afsnit.

## <a name="create-a-container-in-code"></a>Oprette en objektbeholder i kode

> [AZURE.NOTE] Nogle API'er, udfører opkald ud til Azure-lager i ASP.NET er asynkron. Yderligere oplysninger finder du i [asynkron programming med asynkron og Await](http://msdn.microsoft.com/library/hh191443.aspx) . Koden i følgende eksempel antages, at du bruger asynkron programmering.

For at oprette en objektbeholder i kontoen lagerplads, skal du gøre blot tilføje et opkald til **CreateIfNotExistsAsync** som i følgende kode:

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


Hvis du vil gøre filerne i beholderen tilgængelig for alle, kan du angive objektbeholderen skal være offentlig ved hjælp af følgende kode.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Alle på internettet kan se BLOB i en offentlig beholder, men du kan ændre eller slette dem kun, hvis du har den relevante hurtigtast.

## <a name="upload-a-blob-into-a-container"></a>Overføre en blob til en objektbeholder

Azure-lager understøtter Bloker BLOB og siden BLOB. I fleste tilfælde skal er Bloker blob typen anbefales at bruge.

Hent en objektbeholder reference for at overføre en fil til en blok blob, og bruge det til at få en blok blob reference. Når du har en blob reference, kan du overføre en hvilken som helst strøm af data til den ved at ringe til metoden **UploadFromStream** . Denne handling opretter blob, hvis det ikke fandtes i forvejen, eller overskriver den, hvis den findes. I følgende eksempel viser, hvordan du kan overføre en blob til en objektbeholder og antages det, at beholderen allerede blev oprettet.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Liste over BLOB i en objektbeholder

Du kan få vist BLOB i en objektbeholder, først få en objektbeholder reference. Derefter kan du bruge den beholder **ListBlobs** metode til at hente BLOB og/eller mapper i postkassen. For at få adgang til den bredt udvalg af egenskaber og metoder til en returnerede **IListBlobItem**, skal du konverteret den til et **CloudBlockBlob**, **CloudPageBlob**eller **CloudBlobDirectory** objekt. Hvis typen er ukendt, kan du bruge en type kontrol til at bestemme, kan det skal. Følgende kode demonstrerer, hvordan du kan hente og output URI for hvert element i beholderen **fotos** :

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

Som vist i det forrige eksempel-kode, har tjenesten blob begrebet undermapper i beholdere, samt. Dette er, så du kan organisere dine BLOB i en mere mappe-lignende struktur. For eksempel kan du overveje følgende sæt af Bloker BLOB i en objektbeholder med navnet **fotos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Når du ringer **ListBlobs** på objektbeholderen (som i forrige eksempel), indeholder returneres samlingen **CloudBlobDirectory** og **CloudBlockBlob** objekter, der repræsenterer kataloger og BLOB indeholdt på øverste niveau. Her er resultatet:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Du kan eventuelt angive parameteren **UseFlatBlobListing** for af metoden **ListBlobs** til **Sand**. Dette resulterer i hver blob, returneres som en **CloudBlockBlob**, uanset directory. Her er et opkald til **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

og her er resultaterne:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Du kan finde flere oplysninger [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Hente BLOB

For at hente BLOB skal først hente en blob reference og derefter kalde metoden **DownloadToStream** . I følgende eksempel bruges metoden **DownloadToStream** til at overføre blob indholdet til en stream-objekt, du kan derefter fastholdes til en lokal fil.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Du kan også bruge metoden **DownloadToStream** for at hente indholdet af en blob som en tekststreng.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Slette BLOB

Slette en blob skal først hente en blob reference, og kald derefter metoden **slette** .

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Listen BLOB i sider asynkront

Hvis du listen over et stort antal BLOB, eller du vil styre antallet resultater, du vender tilbage på én gang på listen, kan du angive BLOB på sider i resultaterne. I dette eksempel viser, hvordan til at returnere resultater i sider asynkront, så udførelse af ikke er blokeret under venter på at returnere et stort antal resultater.

I dette eksempel vises en flad blob listen over, men du kan også udføre en hierarkisk liste, ved at angive parameteren **useFlatBlobListing** af metoden **ListBlobsSegmentedAsync** til **Falsk**.

Fordi metoden eksempel kalder en asynkron metode, den skal indledes med nøgleordet **asynkron** , og den skal returnere en **opgave** -objekt. Nøgleordet await angivet for metoden **ListBlobsSegmentedAsync** afbryder udførelse af metoden eksempel, indtil listen opgaven er fuldført.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
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

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
