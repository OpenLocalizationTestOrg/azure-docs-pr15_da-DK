<properties
    pageTitle="Oprette og bruge en SAS med Blob-lager | Microsoft Azure"
    description="Dette selvstudium viser, hvordan du opretter delt adgang signaturer til brug med Blob-lager, og hvordan du kan bruge dem fra din klientprogrammer."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Delte signaturer Access, del 2: Oprette og bruge en SAS med Blob-lager

## <a name="overview"></a>Oversigt

[Del 1](storage-dotnet-shared-access-signature-part-1.md) i dette selvstudium set delte access signaturer (SAS) og forklaring bedste fremgangsmåder til brug af dem. Del 2 viser, hvordan du opretter og bruger derefter delt adgang signaturer med Blob-lager. Eksemplerne er skrevet i C# og bruge biblioteket Azure-lager-klienten til .NET. De scenarier, der er omfattet omfatter disse aspekter af arbejde med delt adgang signaturer:

- Oprettelse af en delt adgang signatur i en objektbeholder
- Oprettelse af en delt adgang signatur i en blob
- Oprette en lagrede access-politik for at administrere signaturer på en objektbeholder ressourcer
- Test signaturer delt adgang fra en klientprogrammet

## <a name="about-this-tutorial"></a>Om dette selvstudium

I dette selvstudium fokuserer vi på at oprette delt adgang signaturer til beholdere og BLOB ved at oprette to console-programmer. Det første console program genererer delt adgang signaturer på en objektbeholder og en blob. Dette program har kendskab til tasterne lagerplads konto. Det andet console-program, der skal fungere som et klientprogrammet, får adgang til objektbeholder og blob ressourcer ved hjælp af de delte access signaturer, der er oprettet med det første program. Dette program bruger signaturer delt adgang kun til at godkende adgangen til objektbeholderen og blob ressourcer – den har ikke kendskab til tasterne konto.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Del 1: Oprette en Console program tilladelse til at generere delt adgang signaturer

Først, sikre dig, at du har biblioteket Azure-lager-klienten til .NET er installeret. Du kan installere den [NuGet pakke](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet pakke") , der indeholder de mest opdaterede assemblies for biblioteket klient; Dette er den anbefalede metode til at sikre, at du har de seneste rettelser. Du kan også hente biblioteket klienten på computeren som en del af den seneste version af [Azure SDK til .NET](https://azure.microsoft.com/downloads/).

Opret en ny Windows console-program i Visual Studio, og kald den **GenerateSharedAccessSignatures**. Føje referencer til **Microsoft.WindowsAzure.Configuration.dll** og **Microsoft.WindowsAzure.Storage.dll**, ved hjælp af en af følgende fremgangsmåder:

-   Hvis du vil installere pakken NuGet, skal du først installere [NuGet klient](https://docs.nuget.org/consume/installing-nuget). I Visual Studio, skal du vælge **Project | Administrere NuGet pakker**, Søg online efter **Azure-lager**, og følg vejledningen for at installere.
-   Alternativt kan du finde assemblies i din installation af Azure SDK og føje referencer til dem.

Øverst i filen Program.cs skal du tilføje sætningerne **ved hjælp af** følgende:

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Redigere app.config-filen, så den indeholder en indstilling for søgekonfiguration med en forbindelsesstreng, der peger på kontoen lagerplads. Filen app.config skal ligne dette:

    <configuration>
      <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings>
    </configuration>

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Oprette en delt adgang signatur URI for en objektbeholder

Til at begynde med, tilføjer vi en metode til at oprette en delt adgang signatur i en ny objektbeholder. Signaturen er i dette tilfælde ikke knyttet til en politik for lagrede access, så det driver URI de oplysninger, der angiver dens udløbstid og de tilladelser, den giver.

Først skal du tilføje kode til **Main()** metoden til at godkende adgang til kontoen lager og oprette en ny objektbeholder:

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Insert calls to the methods created below here...

        //Require user input before closing the console window.
        Console.ReadLine();
    }

Derefter skal du tilføje en ny metode, der genererer delt adgang signatur til objektbeholderen og returnerer signaturen URI:

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Set the expiry time and permissions for the container.
        //In this case no start time is specified, so the shared access signature becomes valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;

        //Generate the shared access signature on the container, setting the constraints directly on the signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Tilføj følgende linjer i bunden af metoden **Main()** , før opkaldet til **Console.ReadLine()**til at ringe **GetContainerSasUri()** og skrive signatur URI til vinduet console:

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Samle og køre for at sende den delte access signatur URI for objektbeholderen nye. URI bliver svarende til følgende URI:       

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Når du har kørt koden, gælder den delte access signatur, du oprettede på objektbeholderen kun for de næste 24 timer. Signaturen giver en klient tilladelse til at liste BLOB i beholderen og skrive en ny blob i objektbeholderen.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Oprette en delt adgang signatur URI for en Blob

Derefter skal vi skrive lignende kode for at oprette en ny blob inde i beholderen og oprette en signatur, som delt adgang til den. Denne delt adgang signatur er ikke knyttet til en politik for lagrede access, så den indeholder starttidspunkt, udløbstid og oplysninger om tilladelser på URI.

Tilføje en ny metode, der opretter en ny blob, og skriv noget tekst, derefter genererer en delt adgang signatur og returnerer signaturen URI:

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Set the expiry time and permissions for the blob.
        //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
        //The shared access signature will be valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

        //Generate the shared access signature on the blob, setting the constraints directly on the signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

Tilføj følgende linjer for at ringe **GetBlobSasUri()**, før opkaldet til **Console.ReadLine()**i bunden af metoden **Main()** , og skrive delt adgang signatur URI til vinduet console:    

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();


Samle og køre for at sende delt adgang signaturen URI til den nye blob. URI bliver svarende til følgende URI:

    https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### <a name="create-a-stored-access-policy-on-the-container"></a>Oprette en lagrede Access-politik på objektbeholderen

Nu Lad os oprette en lagrede access-politik på beholder, der vil definere begrænsninger til en hvilken som helst delt adgang signaturer, der er knyttet til den.

I de tidligere eksempler angivet vi starttidspunktet (implicit eller eksplicit), udløbstid, og tilladelserne på den delte access signatur URI selve. I eksemplerne nedenfor vil vi angive disse den lagrede access-politik og ikke af den delte access-signatur. Hvis du gør det giver mulighed at ændre disse begrænsninger uden genudgive delt adgang signatur.

Det er muligt at have en eller flere af betingelserne for den delte access-signatur, og resten på den lagrede access-politik. Men du kan kun angive starttidspunkt, udløbstid og tilladelser i ét sted eller den anden side Du kan for eksempel angive tilladelser på den delte access signatur og også angive dem på den lagrede access-politik.

Bemærk, at når du føjer en adgangspolitik for til en objektbeholder, skal du på objektbeholderen eksisterende tilladelser, tilføje den nye access-politik og derefter angive den objektbeholder tilladelser.

Tilføje en ny metode, der opretter en ny politik for lagrede access i en objektbeholder og returnerer navnet på politikken:

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
        string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

Tilføj følgende linjer til første Ryd alle eksisterende politikker for access i bunden af metoden **Main()** , før opkaldet til **Console.ReadLine()**, og kald derefter metoden **CreateSharedAccessPolicy()** :    

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Bemærk, at når du rydder politikkerne for af access i en objektbeholder, du skal først beholderen er eksisterende tilladelser, og derefter Slet tilladelserne, og angiv derefter tilladelserne igen.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Oprette en delt adgang signatur URI på objektbeholderen, der bruger en Access-politik

Dernæst skal oprette vi en anden delt adgang signatur på objektbeholderen, du oprettede tidligere, men denne gang skal vi knytte signaturen til den access-politik, vi oprettede i forrige eksempel.

Tilføje en ny metode til at oprette en anden delt adgang signatur på objektbeholderen:

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Generate the shared access signature on the container. In this case, all of the constraints for the
        //shared access signature are specified on the stored access policy.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Tilføj følgende linjer for at ringe metoden **GetContainerSasUriWithPolicy** i bunden af metoden **Main()** , før opkaldet til **Console.ReadLine()**:

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Oprette en delt adgang signatur URI på Blob, der bruger en Access-politik

Til sidst skal tilføjer vi en lignende metode til at oprette en anden blob og oprette en delt adgang signatur, der er knyttet til en access-politik.

Tilføje en ny metode til at oprette en blob og oprette en delt adgang signatur:

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " +
        "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Generate the shared access signature on the blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

Tilføj følgende linjer for at ringe metoden **GetBlobSasUriWithPolicy** i bunden af metoden **Main()** , før opkaldet til **Console.ReadLine()**:    

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

Metoden **Main()** ser nu sådan ud i sin helhed. Kør den for at skrive delt adgang signatur URI'er til console-vinduet, og derefter kopiere og indsætte dem i en tekstfil til brug i den anden del af dette selvstudium.

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Generate a SAS URI for the container, without a stored access policy.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, without a stored access policy.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();

        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for
        //shared access signatures on the container and the blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

        //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        Console.ReadLine();
    }

Når du kører GenerateSharedAccessSignatures console-program, kan du se output, der svarer til følgende i vinduet console. Dette er de delte adgang signaturer, du skal bruge i del 2 i selvstudiet.

![SAS-console-output-1][sas-console-output-1]

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Del 2: Oprette en Console program tilladelse til at teste delt adgang signaturer

Hvis du vil teste delt adgang signaturer, der er oprettet i de tidligere eksempler, skal vi oprette en anden console-program, der bruger signaturer til at udføre handlinger på objektbeholderen og en blob.

> [AZURE.NOTE] Hvis mere end 24 timer er gået, fordi du har fuldført den første del af selvstudiet, vil de signaturer, du oprettede ikke længere være gyldige. I dette tilfælde skal du køre koden i det første console-program til at oprette nyt delt adgang signaturer til brug i den anden del af selvstudiet.

Opret en ny Windows console-program i Visual Studio, og kald den **ConsumeSharedAccessSignatures**. Føje referencer til **Microsoft.WindowsAzure.Configuration.dll** og **Microsoft.WindowsAzure.Storage.dll**, som du gjorde tidligere.

Øverst i filen Program.cs skal du tilføje sætningerne **ved hjælp af** følgende:

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Tilføj følgende konstanter i brødteksten i metoden **Main()** , og opdatere deres værdier til de delte access signaturer, du har oprettet i en del 1 af selvstudiet.

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Tilføje en metode til at prøve objektbeholder handlinger ved hjælp af en delt adgang signatur

Dernæst skal tilføjer vi en metode, der tester nogle repræsentant objektbeholder handlinger ved hjælp af en delt adgang signatur på objektbeholderen. Bemærk, at delt adgang signatur bruges til at returnere en reference til objektbeholderen, godkendelse af adgang til objektbeholderen baseret på den signatur, der er alene.

Føje følgende metode til Program.cs:

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Get a reference to one of the blobs in the container and read it.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Opdatere metoden **Main()** for at ringe **UseContainerSAS()** med begge af delt adgang signaturer, du har oprettet på objektbeholderen:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        Console.ReadLine();
    }


### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Tilføje en metode til at prøve Blob handlinger ved hjælp af en delt adgang signatur

Til sidst skal tilføjer vi en metode, der tester nogle repræsentant blob handlinger ved hjælp af en delt adgang signatur på blob. I dette tilfælde bruger vi parametre **CloudBlockBlob(String)**, der passerer i signaturen delt adgang til at returnere en reference til blob. Ingen anden godkendelse er påkrævet. det er baseret på den signatur, der er alene.

Føje følgende metode til Program.cs:

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container.
        try
        {
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Read the contents of the blob.
        try
        {
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Delete operation: Delete the blob.
        try
        {
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Opdatere metoden **Main()** for at ringe **UseBlobSAS()** med begge af delt adgang signaturer, du oprettede på blob:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Kør programmet console og overhold output til at se, hvilke handlinger er tilladt for hvilke signaturer. Output i vinduet console vil se ud som følger:

![SAS-console-output-2][sas-console-output-2]

## <a name="next-steps"></a>Næste trin

[Delt adgang signaturer, del 1: Om SAS datamodellen](storage-dotnet-shared-access-signature-part-1.md)

[Administrere anonym adgang til beholdere og BLOB](storage-manage-access-to-resources.md)

[Delegering af Access med en delt adgang signatur (REST-API)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[Introduktion til tabeller og kø SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG
