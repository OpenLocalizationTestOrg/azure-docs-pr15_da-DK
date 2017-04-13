<properties
    pageTitle="Komme i gang med blob-lager og Visual Studio forbundne tjenester (ASP.NET 5) | Microsoft Azure"
    description="Sådan Introduktion til brug af Azure Blob-lager i Visual Studio ASP.NET-5-projekt, når du har oprettet en lagerplads konto ved hjælp af Visual Studio forbundne tjenester"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-5"></a>Introduktion til Azure Blob-lager og Visual Studio forbundne tjenester (ASP.NET 5)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

##<a name="overview"></a>Oversigt

I denne artikel beskrives, hvordan du kommer i gang med Azure Blob-lager i Visual Studio, når du har oprettet eller refererer til en Azure lagerplads konto i et ASP.NET 5 projekt ved hjælp af dialogboksen Visual Studio tilføje forbundne tjenester.

Azure Blob-lager er en tjeneste til lagring af store mængder ustrukturerede data, der kan åbnes fra et vilkårligt sted i verden via HTTP eller HTTPS. En enkelt blob kan være en hvilken som helst størrelse. BLOB kan være ting som billeder, lyd- og videofiler, rækkedata og dokumentfiler. I denne artikel beskrives, hvordan Introduktion til blob-lager, når du har oprettet en Azure-lager-konto ved hjælp af dialogboksen Visual Studio **Tilføje forbundne tjenester** i et ASP.NET-5-projekt.

Ligesom filer direkte i mapper, direkte BLOB storage i beholdere. Når du har oprettet en lagerplads, kan du oprette en eller flere beholdere i opbevaring. For eksempel på et lager, kaldet "Scrapbog", kan du oprette beholdere i opbevaring kaldet "billeder" for at gemme billeder, og en anden kaldet "lyd" til at gemme lydfiler. Når du opretter beholderne, kan du overføre individuelle blob filer hertil. Se [Introduktion til Azure Blob-lager ved hjælp af .NET](storage-dotnet-how-to-use-blobs.md) for flere oplysninger om ved hjælp af programmering manipulere BLOB.

##<a name="access-blob-containers-in-code"></a>Access blob beholdere i kode

Adgang fra et program til BLOB i ASP.NET 5 projekter, skal du tilføje følgende elementer, hvis de ikke allerede findes.

1. Tilføj følgende kode navneområdeerklæringer til toppen af enhver C#-fil, du vil have adgang fra et program til Azure-lager.

        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;

2. Få et **CloudStorageAccount** objekt, der repræsenterer kontooplysningerne lagerplads. Brug følgende kode til at få den din lagerplads forbindelsesstreng og lagerplads kontooplysninger fra Azure service konfigurationen.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **NOTE:** Bruge alle ovenstående kode foran koden i de følgende afsnit.


3. Bruge et **CloudBlobClient** objekt til at få en **CloudBlobContainer** reference til en eksisterende objektbeholder i kontoen lagerplads.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##<a name="create-a-container-in-code"></a>Oprette en objektbeholder i kode

Du kan også bruge **CloudBlobClient** til at oprette en objektbeholder i kontoen lagerplads. Alt, du skal gøre, er at tilføje et opkald til **CreateIfNotExistsAsync** som i følgende kode:

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**NOTE:** De API'er, udfører opkald til Azure-lager i ASP.NET 5 er asynkron. Yderligere oplysninger finder du i [asynkron programming med asynkron og Await](http://msdn.microsoft.com/library/hh191443.aspx) . Nedenstående kode antages det asynkron programmering bliver brugt.

Hvis du vil gøre filerne i beholderen tilgængelig for alle, kan du angive objektbeholderen skal være offentlig ved hjælp af følgende kode.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##<a name="upload-a-blob-into-a-container"></a>Overføre en blob til en objektbeholder

Hent en objektbeholder reference for at overføre en blob-fil til en objektbeholder, og bruge det til at få en blob reference. Når du har en blob reference, kan du overføre en hvilken som helst strøm af data til den ved at ringe til metoden **UploadFromStreamAsync** . Denne handling opretter blob, hvis det ikke allerede er der eller overskriver den, hvis den findes. I følgende eksempel viser, hvordan du kan overføre en blob til en objektbeholder og antages det, at beholderen allerede blev oprettet.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##<a name="list-the-blobs-in-a-container"></a>Liste over BLOB i en objektbeholder
Du kan få vist BLOB i en objektbeholder, først få en objektbeholder reference. Derefter kan du ringe på objektbeholderen **ListBlobsSegmentedAsync** metode til at hente BLOB og/eller mapper i postkassen. For at få adgang til den bredt udvalg af egenskaber og metoder til en returnerede **IListBlobItem**, skal du konverteret den til et **CloudBlockBlob**, **CloudPageBlob**eller **CloudBlobDirectory** objekt. Hvis du ikke kender typen blob, kan du bruge type Tjek til at bestemme, kan det skal. Følgende kode demonstrerer, hvordan du kan hente og output URI for hvert element i en beholder.

    BlobContinuationToken token = null;
    do
    {
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
        token = resultSegment.ContinuationToken;

        foreach (IListBlobItem item in resultSegment.Results)
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
    } while (token != null);

Der findes andre måder at få vist indholdet af en blob objektbeholder. Se [Introduktion til Azure Blob-lager ved hjælp af .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) kan finde flere oplysninger.

##<a name="download-a-blob"></a>Hente en blob
For at hente en blob skal først hente en reference til blob, og kald derefter metoden **DownloadToStreamAsync** . I følgende eksempel bruges metoden **DownloadToStreamAsync** til at overføre blob indholdet til en stream-objekt, du kan derefter gemme som en lokal fil.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

Der findes andre måder at gemme BLOB som filer. Se [Introduktion til Azure Blob-lager ved hjælp af .NET](storage-dotnet-how-to-use-blobs.md#download-blobs) kan finde flere oplysninger.

##<a name="delete-a-blob"></a>Slette en blob
For at slette en blob skal først hente en reference til blob, og kald derefter metoden **DeleteAsync** på den.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
