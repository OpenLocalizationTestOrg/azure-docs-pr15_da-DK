<properties
    pageTitle="Oprette et skrivebeskyttet øjebliksbillede af en blob | Microsoft Azure"
    description="Få mere at vide, hvordan du opretter et øjebliksbillede af en blob til at sikkerhedskopiere blob-data fra et bestemt tidspunkt i gang. Forstå, hvordan snapshots faktureres og hvordan du kan bruge dem til at minimere kapacitet gebyrer."
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

# <a name="create-a-blob-snapshot"></a>Oprette et snapshot af blob

## <a name="overview"></a>Oversigt

Et øjebliksbillede er en skrivebeskyttet version af en blob, der er taget på et sted i gang. Snapshots er nyttige til sikkerhedskopiering af BLOB. Når du opretter et øjebliksbillede, kan du læse, kopiere eller slette den, men du kan ikke redigere den.

Et øjebliksbillede af en blob er identisk med dens base blob, bortset fra at blob URI indeholder en **dato /** klokkeslætsværdi, der er føjet til blob URI til at angive det tidspunkt, hvor snapshot blev udført. Eksempelvis hvis en side blob URI er `http://storagesample.core.blob.windows.net/mydrives/myvhd`, snapshot URI ligner `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. 

> [AZURE.NOTE] Alle snapshots dele den grundlæggende blob URI. Kun forskellen mellem de grundlæggende blob og snapshot er den tilføjede **dato/klokkeslæt** -værdi.

En blob kan have en hvilken som helst antal snapshots. Snapshots fastholdes, indtil de slettes eksplicit. Et øjebliksbillede kan ikke outlive dens base blob. Du kan optælle de snapshots, der er knyttet til den grundlæggende blob til at registrere din aktuelle snapshots.

Når du opretter et øjebliksbillede af en blob, kopieres de blob egenskaber for system til snapshot med de samme værdier. Den grundlæggende blob metadata er også kopieret til et snapshot, medmindre du angiver separat metadata til snapshot, når du opretter den.

Alle rettigheder, der er knyttet til den grundlæggende blob påvirker ikke snapshot. Du kan ikke hente en rettighed på et øjebliksbillede.

## <a name="create-a-snapshot"></a>Oprette et øjebliksbillede

Følgende kodeeksempel viser, hvordan du opretter et øjebliksbillede i .NET. I dette eksempel angiver separat metadata til snapshot, når den er oprettet.

    private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
    {
        // Create a new block blob in the container.
        CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

        // Add blob metadata.
        baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

        try
        {
            // Upload the blob to create it, with its metadata.
            await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

            // Sleep 5 seconds.
            System.Threading.Thread.Sleep(5000);

            // Create a snapshot of the base blob.
            // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
            // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
            Dictionary<string, string> metadata = new Dictionary<string, string>();
            metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
            await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        }
        catch (StorageException e)
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
 

## <a name="copy-snapshots"></a>Kopiere snapshots

Kopiér handlinger, der involverer BLOB og snapshots skal du følge disse regler:

- Du kan kopiere et øjebliksbillede over dens base blob. Ved at fremme et øjebliksbillede til placeringen af den grundlæggende blob, kan du gendanne en tidligere version af en blob. Øjebliksbillede forbliver, men grundtallet blob overskrives med en skrivbar kopi af snapshot.

- Du kan kopiere et øjebliksbillede til en destination blob med et andet navn. Den resulterende destination blob er et skrivbart blob og ikke et øjebliksbillede.

- Når en kilde blob er kopieret, kopieres et snapshot af kilde blob ikke til destinationen. Når en destination blob overskrives med en kopi, bevares der er knyttet til den oprindelige destination blob øjebliksbilleder.

- Når du opretter et øjebliksbillede af en blok blob, kopieret den blob anvendt blokeringsliste også til et snapshot. En hvilken som helst tilbageskrivningshandlinger blokke kopieres ikke.

## <a name="specify-an-access-condition"></a>Angive en access-betingelse

Du kan angive en access-betingelse, så snapshot oprettes kun, hvis en betingelse opfyldes. Hvis du vil angive en access-betingelse, skal du bruge egenskaben **AccessCondition** . Hvis de angivne betingelser er opfyldt, snapshot er ikke oprettet, og returnerer Blob tjenesten statuskode HTTPStatusCode.PreconditionFailed.

## <a name="delete-snapshots"></a>Slette øjebliksbilleder

Du kan ikke slette en blob med snapshots, medmindre snapshots, slettes også. Du kan slette et øjebliksbillede enkeltvis eller angiver, at alle snapshots slettes, når kilde blob slettes. Hvis du forsøger at slette en blob, der er stadig snapshots, giver en fejl.

Følgende kodeeksempel viser, hvordan du sletter en blob og tilhørende snapshot i .NET, hvor `blockBlob` er en variabel af typen **CloudBlockBlob**:

    await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);

## <a name="snapshots-with-azure-premium-storage"></a>Snapshots med Azure Premium lagerplads

Ved hjælp af snapshots med Premium lagerplads Følg disse regler:

- Det maksimale antal snapshots per siden blob på en premium lagerplads konto er 100. Hvis denne grænse overskrides, returnerer handlingen øjebliksbillede Blob fejlkode 409 (**SnapshotCountExceeded**).

- Du kan tage et øjebliksbillede af en side blob på en premium lagerplads konto én gang hver 10 minutter. Hvis denne sats overskrides, returnerer handlingen øjebliksbillede Blob fejlkode 409 (**SnaphotOperationRateExceeded**).

- Du kan ikke kalde få Blob for at læse et øjebliksbillede af en side blob på en premium lagerplads konto. Ringe få Blob på et øjebliksbillede på en premium lagerplads konto returnerer fejlkode 400 (**InvalidOperation**). Dog kan du ringe Hent Blob egenskaber og få Blob Metadata mod et øjebliksbillede på en premium lagerplads konto.

- Hvis du vil læse et øjebliksbillede, kan du bruge handlingen Kopiér Blob til at kopiere et øjebliksbillede til en anden side blob i kontoen. Destination blob for kopieringen må ikke have en hvilken som helst eksisterende snapshots. Hvis destination blob har snapshots, returnerer handlingen Kopiér Blob fejlkode 409 (**SnapshotsPresent**).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Returnere den absolutte URI til et øjebliksbillede

Dette C#-kodeeksempel oprettes et øjebliksbillede og skriver ud på absolut URI-adressen for den primære placering.

    //Create the blob service client object.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## <a name="understand-how-snapshots-accrue-charges"></a>Forstå, hvordan snapshots periodiseres gebyrer

Oprette et øjebliksbillede, som er en skrivebeskyttet kopi af en blob, kan medføre yderligere data lagerplads gebyrer til din konto. Når du designer dit program, er det vigtigt at være opmærksom på, hvordan disse gebyrer kan periodiseres, så du kan minimere unødvendige omkostninger.

### <a name="important-billing-considerations"></a>Vigtige overvejelser om fakturering

Følgende liste indeholder vigtige punkter du bør overveje, når du opretter et øjebliksbillede.

- Du lagerplads konto medfører gebyrer for entydige blokke eller sider, uanset om de er i blob eller i snapshot. Din konto betale ikke yderligere gebyrer for snapshots, der er knyttet til en blob, indtil du opdaterer den blob, som de er baseret. Når du opdaterer den grundlæggende blob, divergerer den fra dets snapshots. Når dette sker, kan du betaler for entydige blokke eller sider i hver blob eller øjebliksbillede.

- Når du erstatter en blok i en blok blob, betale, der blokerer efterfølgende som en entydig blok. Dette er sand, hvis blokering har samme Bloker-ID og de samme data, som den har i snapshot. Når blokering er anvendt igen, den divergerer fra dens modstykke i et øjebliksbillede, og du vil betale for dataene. Det samme gælder for en side i en side blob, der er opdateret med samme data.

- Erstatte en blok blob ved at ringe til metoden **UploadFile**, **UploadText**, **UploadStream**eller **UploadByteArray** erstatter alle relevante blokke BLOB. Hvis du har et øjebliksbillede, der er knyttet til blob, alle dokumentkomponenter i grundlæggende blob og øjebliksbillede afvige nu, og du vil betale for alle blokke i begge BLOB. Dette er tilfældet, selvom dataene i de grundlæggende blob og snapshot forbliver identisk.

- Tjenesten Azure Blob har ikke mulighed for at afgøre, om to blokke indeholder identiske data. Hver blok, der er overført og anvendt behandles som entydige, selvom den har de samme data og den samme Bloker-ID. Fordi gebyrer periodisere for entydige blokke, er det vigtigt at overveje, ved at opdatere en blob, der har et øjebliksbillede medfører yderligere entydige dokumentkomponenter og yderligere gebyrer.

> [AZURE.NOTE] Bedste fremgangsmåder bestemmer, at du administrere snapshots omhyggeligt for at undgå ekstra gebyrer. Vi anbefaler, at du administrere snapshots på følgende måde:

> - Slette og genoprette snapshots, der er knyttet til en blob, hver gang du opdaterer blob, selvom du opdaterer med ens data, medmindre dit programdesign kræver, at du opretholder snapshots. Ved at slette og genoprette den blob snapshots, kan du sikre dig, at blob og snapshots ikke afviger.

> - Hvis du vedligeholder snapshots for en blob, kan du undgå at ringe til **UploadFile**, **UploadText**, **UploadStream**eller **UploadByteArray** for at opdatere blob. Disse metoder Erstat alle blokke i blob, så dine grundlæggende blob og snapshots afvige funktioner. I stedet opdatere det mindste antal blokke ved hjælp af metoderne **PutBlock** og **PutBlockList** .


### <a name="snapshot-billing-scenarios"></a>Øjebliksbillede fakturering scenarier


Følgende scenarier demonstrerer, hvordan omkostninger periodiseres til en blok blob og tilhørende snapshot.

I scenario 1 er base blob ikke blevet opdateret når snapshot blev udført, så gebyrer er påløbet kun for entydige blokke 1, 2 og 3.

![Azure lagerplads ressourcer](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

Den grundlæggende blob er blevet opdateret i scenario 2, men snapshot har ikke. Bloker 3 blev opdateret, og selvom den indeholder de samme data og samme-ID, det er ikke den samme som blokere 3 i snapshot. Derfor kan faktureres kontoen for fire dokumentkomponenter.

![Azure lagerplads ressourcer](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

Den grundlæggende blob er blevet opdateret i scenario 3, men snapshot har ikke. Bloker 3 er blevet erstattet med blokken 4 i grundlæggende blob, men snapshot afspejler stadig Bloker 3. Derfor kan faktureres kontoen for fire dokumentkomponenter.

![Azure lagerplads ressourcer](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

Grundlæggende blob er blevet opdateret helt i scenario 4 og indeholder ingen af dets oprindelige dokumentkomponenter. Som et resultat faktureres kontoen for alle otte entydige dokumentkomponenter. Dette scenarie kan opstå, hvis du bruger en update-metoden som **UploadFile**, **UploadText**, **UploadFromStream**eller **UploadByteArray**, fordi disse metoder Erstat alle for indholdet af en blob.

![Azure lagerplads ressourcer](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Næste trin

Du kan finde flere eksempler på brug af Blob-lager, [Azure kodeeksempler](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Du kan hente en Northwind og køre den, eller du kan gennemse koden på GitHub. 
