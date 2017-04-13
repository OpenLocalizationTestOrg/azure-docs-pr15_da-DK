<properties
    pageTitle="Administrere anonym adgang til beholdere og BLOB | Microsoft Azure"
    description="Lær, hvordan du kan gøre beholdere og BLOB tilgængelig for anonym adgang, og hvordan du kan få adgang til dem fra et program."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Administrere anonym adgang til beholdere og BLOB

## <a name="overview"></a>Oversigt

Som standard kan kun ejeren af kontoen lagerplads adgang til lagerplads ressourcer inden for kontoen. Du kan angive en objektbeholder tilladelser til at tillade anonyme læseadgang på beholderen og dens BLOB for kun Blob-lager, så du kan give adgang til disse ressourcer uden at dele din kontonøgle.

Anonym adgang er bedst for scenarier, hvor du vil visse BLOB skal altid være tilgængelige for anonyme læseadgang. Få mere finmaskede kontrol kan du oprette en delt adgang signatur, som gør det muligt at begrænsede Stedfortræderadgang ved hjælp af forskellige tilladelser og over et angivet tidsinterval. Se [Brug af delte Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md)kan finde flere oplysninger om oprettelse af delt adgang signaturer.

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Give anonyme brugertilladelser til at beholdere og BLOB

Som standard en objektbeholder og en hvilken som helst BLOB i den kan kun få adgang til ejeren af kontoen lagerplads. Hvis du vil give anonyme brugere læserettigheder til en objektbeholder og dens blob, kan du angive objektbeholder tilladelserne til at tillade offentlig adgang. Anonyme brugere kan læse BLOB i en offentligt tilgængelig objektbeholder uden godkendelse af anmodning.

Objektbeholdere indeholder følgende indstillinger til styring af objektbeholder adgang:

- **Fuld offentlige læseadgang:** Objektbeholder og blob data kan læses via anonyme anmodninger. Klienter kan optælle BLOB inde i beholderen via anonyme anmodningen, men kan ikke optælles objektbeholdere i kontoen lagerplads.

- **Offentlige læseadgang til BLOB kun:** BLOB-data i denne objektbeholder kan læses via anonyme anmodningen, men objektbeholder data er ikke tilgængelig. Klienter kan ikke optælles BLOB inde i beholderen via anonyme anmodninger.

- **Ingen offentlig læseadgang:** Objektbeholder og blob data kan læses af kontoejeren kun.

Du kan angive tilladelser til objektbeholderen på følgende måder:

- Fra [Azure-portalen](https://portal.azure.com).
- Fra et program, ved hjælp af biblioteket lagerplads klient eller REST-API.
- Ved hjælp af PowerShell. For at få mere for at vide om at angive tilladelser til objektbeholderen fra Azure PowerShell, se [Bruge Azure PowerShell med Azure-lager](storage-powershell-guide-full.md#how-to-manage-azure-blobs).

### <a name="setting-container-permissions-from-the-azure-portal"></a>Angive objektbeholder tilladelser fra Azure-portalen

Du kan angive tilladelser til objektbeholderen fra [Azure-portalen](https://portal.azure.com)ved at følge disse trin:

1. Gå til dashboard til kontoen lagerplads.
2. Vælg objektbeholdernavnet på listen. Klikke på navnet på Fremviser BLOB i den valgte objektbeholder
3. Vælg **Access-politik** på værktøjslinjen.
4. Vælg det ønskede niveau af tilladelser i feltet **type adgang** , som vist i skærmbilledet nedenfor.

    ![Redigere Metadata objektbeholder dialogboksen](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="setting-container-permissions-programmatically-using-net"></a>Angive objektbeholder tilladelser fra et program ved hjælp af .NET

For at angive tilladelser for en objektbeholder, ved hjælp af biblioteket .NET-klienten skal først hente den beholder eksisterende tilladelser ved at ringe til metoden **GetPermissions** . Angiv derefter egenskaben **PublicAccess** for objektet **BlobContainerPermissions** , der returneres af metoden **GetPermissions** . Til sidst skal kalde metoden **SetPermissions** med de opdaterede tilladelser.

I følgende eksempel angiver den beholder tilladelser til fuld offentlige læseadgang. For at angive tilladelser til angive offentlige læseadgang for BLOB kun egenskaben **PublicAccess** til **BlobContainerPublicAccessType.Blob**. Hvis du vil fjerne alle tilladelser for anonyme brugere, skal du angive egenskaben til **BlobContainerPublicAccessType.Off**.

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## <a name="access-containers-and-blobs-anonymously"></a>Få adgang til beholdere og BLOB anonymt

En klient, der har adgang til beholdere og BLOB anonymt kan bruge konstruktører, der ikke kræver legitimationsoplysninger. Følgende eksempler viser et par forskellige måder til at referere til Blob serviceressourcer anonymt.

### <a name="create-an-anonymous-client-object"></a>Oprette et anonymt klientobjekt

Du kan oprette et nyt tjenesten klientobjekt for anonym adgang ved at indsende Blob tjenesteslutpunkt for kontoen. Du skal også kende navnet på en objektbeholder i den pågældende konto, der er tilgængelige for anonym adgang.

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### <a name="reference-a-container-anonymously"></a>Henvise til en objektbeholder anonymt

Hvis du har en objektbeholder, der findes anonymt URL-adressen, kan du bruge det til at referere til objektbeholderen direkte.

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### <a name="reference-a-blob-anonymously"></a>Henvise til en blob anonymt

Hvis du har en blob, der er tilgængelig for anonym adgang URL-adressen, kan du referere til blob direkte ved hjælp af denne URL-adresse:

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## <a name="features-available-to-anonymous-users"></a>Funktioner, der er tilgængelige for anonyme brugere

De følgende tabel viser, hvilke handlinger kan kaldes ved anonyme brugere, når en objektbeholder ACL er indstillet til at tillade offentlig adgang.

| RESTEN handling                                         | Tilladelse med fuld offentlige læseadgang | Tilladelse med offentlige læseadgang for BLOB kun |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| Listen objektbeholdere                                        | Kun ejeren                              | Kun ejeren                                        |
| Oprette objektbeholder                                       | Kun ejeren                              | Kun ejeren                                        |
| Få objektbeholder egenskaber                               | Alle                                     | Kun ejeren                                        |
| Få objektbeholder Metadata                                 | Alle                                     | Kun ejeren                                        |
| Angive objektbeholder Metadata                                 | Kun ejeren                              | Kun ejeren                                        |
| Hent objektbeholder ACL                                      | Kun ejeren                              | Kun ejeren                                        |
| Angive objektbeholder ACL                                      | Kun ejeren                              | Kun ejeren                                        |
| Slette objektbeholder                                       | Kun ejeren                              | Kun ejeren                                        |
| Listen BLOB                                             | Alle                                     | Kun ejeren                                        |
| Placere Blob                                               | Kun ejeren                              | Kun ejeren                                        |
| Få Blob                                               | Alle                                     | Alle                                               |
| Få Blob egenskaber                                    | Alle                                     | Alle                                               |
| Angive Blob egenskaber                                    | Kun ejeren                              | Kun ejeren                                        |
| Få Blob Metadata                                      | Alle                                     | Alle                                               |
| Angive Blob Metadata                                      | Kun ejeren                              | Kun ejeren                                        |
| Placere blok                                              | Kun ejeren                              | Kun ejeren                                        |
| Få blokeringsliste (kun anvendt blokerer)                 | Alle                                     | Alle                                               |
| Få blokeringsliste (tilbageskrivningshandlinger blokke eller alle relevante blokke) | Kun ejeren                              | Kun ejeren                                        |
| Placere liste over blokerede afsendere                                         | Kun ejeren                              | Kun ejeren                                        |
| Slette Blob                                            | Kun ejeren                              | Kun ejeren                                        |
| Kopiere Blob                                              | Kun ejeren                              | Kun ejeren                                        |
| Øjebliksbillede Blob                                          | Kun ejeren                              | Kun ejeren                                        |
| Bortlease Blob                                             | Kun ejeren                              | Kun ejeren                                        |
| Anbringe siden                                               | Kun ejeren                              | Kun ejeren                                        |
| Hent siden områder                                        | Alle                                     | Alle                                                  |
| Tilføje Blob                                            | Kun ejeren                              | Kun ejeren                                                  |


## <a name="see-also"></a>Se også

- [Godkendelse for tjenesterne, Azure-lager](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Brug af delte Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md)
- [Delegering af Access med en delt adgang signatur](https://msdn.microsoft.com/library/azure/ee395415.aspx)
