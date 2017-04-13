<properties 
    pageTitle="Overføre filer til en Media Services-konto ved hjælp af .NET | Microsoft Azure" 
    description="Lære at få medieindhold til Media Services ved at oprette og overføre aktiver." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="juliako"/>



# <a name="upload-files-into-a-media-services-account-using-net"></a>Overføre filer til en Media Services-konto ved hjælp af .NET

 > [AZURE.SELECTOR]
 - [.NET](media-services-dotnet-upload-files.md)
 - [RESTEN](media-services-rest-upload-files.md)
 - [Portal](media-services-portal-upload-files.md)

I Media Services, du overfører (eller indtager) dine digitale filer til et aktiv. Objektet **aktiv** kan indeholde video, lyd, billeder, miniaturebilleder af websteder, tekst spor og undertekster filer (og metadata om filerne.)  Når filerne er overført, er dit indhold gemt sikkert i skyen til yderligere behandling og streaming.

Filerne i aktivet kaldes **Aktiv filer**. Forekomsten **AssetFile** og den faktiske mediefil er to forskellige objekter. Forekomsten AssetFile indeholder metadata om mediefil, mens mediefilen indeholder det faktiske indhold.

>[AZURE.NOTE]Overvej følgende gælder, når du vælger en aktiv filnavn:
>
>- Media Services bruger værdien i egenskaben IAssetFile.Name, når du opbygger en URL-adresser for streaming indholdet (for eksempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Derfor er procent-kodning ikke tilladt. Værdien af egenskaben **navn** må ikke være følgende [procent-kodning-reserveret tegn](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Desuden kan kun der en '.' for filtypenavnet.
>
>- Længden af navnet skal ikke være større end 260 tegn.

Når du opretter aktiver, kan du angive følgende krypteringsindstillinger for. 

- **Ingen** - kryptering ikke bruges. Dette er standardværdien. Bemærk, at når du bruger denne indstilling, hvis indholdet ikke er beskyttet undervejs eller i resten i lagerplads.
Hvis du planlægger at levere en MP4 ved hjælp af løbende overførsel Brug denne indstilling. 
- **CommonEncryption** - Brug denne indstilling, hvis du sender indhold, der allerede er blevet krypteret og beskyttet med almindelige kryptering eller PlayReady DRM (for eksempel udglattede Streaming beskyttet med PlayReady DRM).
- **EnvelopeEncrypted** – Brug denne indstilling, hvis du sender HLS krypteret med AES. Bemærk, at filerne skal er blevet kodet og krypteret af transformere Manager.
- **StorageEncrypted** - krypterer Ryd indholdet lokalt Hjælp AES-256 bit kryptering og derefter uploads det til Azure-lager, hvor det er gemt krypteret på resten. Aktiver, der er beskyttet med lagerplads kryptering er automatisk uden kryptering og placeret i en krypteret filsystem før kodning, og du kan også igen krypteret før uploade tilbage som et nyt output aktiv. Den primære use case for lagerplads kryptering er, når du vil sikre høj kvalitet input mediefilerne med stærk kryptering på resten på disk.

    Media Services indeholder på disken lagerplads kryptering for dine aktiver, ikke over via som Manager DRM (Digital Rights).

    Hvis din aktiv er krypteret lagerplads, skal du konfigurere aktiv levering politik. Finde flere oplysninger under [konfiguration af aktiv levering politik](media-services-dotnet-configure-asset-delivery-policy.md).

Hvis du angiver for dine aktiver skal krypteres med en indstilling for **CommonEncrypted** eller en **EnvelopeEncypted** -indstilling, skal du knytte dine aktiver med en **ContentKey**. Se, [hvordan du opretter en ContentKey](media-services-dotnet-create-contentkey.md)kan finde flere oplysninger. 

Hvis du angiver for din aktiv som skal krypteres med en indstilling for **StorageEncrypted** , oprette Media Services SDK til .NET **StorateEncrypted** **ContentKey** for din aktiv.


Dette emne viser, hvordan du bruger Media Services .NET SDK samt Media Services .NET SDK udvidelser til at overføre filer til et Media Services aktiv.

 
## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Overføre en enkelt fil med Media Services .NET SDK 

Nedenstående eksempelkode bruger .NET SDK til at udføre følgende opgaver: 

- Opretter et tomt aktiv.
- Opretter en AssetFile-forekomst, vi vil knytte til aktivet.
- Opretter en AccessPolicy-forekomst, der definerer de tilladelser og varigheden af adgang til aktivet.
- Opretter en forekomst af Locator, der giver adgang til aktivet.
- Overfører en enkelt mediefil til Media Services. 

        
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
        }

##<a name="upload-multiple-files-with-media-services-net-sdk"></a>Overføre flere filer med Media Services .NET SDK 

Følgende kode viser, hvordan du opretter et aktiv, og Overfør flere filer.

Koden gør følgende:
    
-   Opretter en tom aktiv ved hjælp af metoden CreateEmptyAsset, der er defineret i det forrige trin.
    
-   Opretter en **AccessPolicy** -forekomst, der definerer de tilladelser og varigheden af adgang til aktivet.
    
-   Opretter en forekomst af **Locator** , der giver adgang til aktivet.
    
-   Opretter en forekomst af **BlobTransferClient** . Denne type repræsenterer en klient, der fungerer på Azure BLOB. I dette eksempel bruger vi klienten til at overvåge statussen Overfør. 
    
-   Optælles gennem filer i den angivne mappe og opretter en **AssetFile** forekomst for hver fil.
    
-   Overfører filerne til Media Services ved hjælp af metoden **UploadAsync** . 
    
>[AZURE.NOTE] Brug metoden UploadAsync til at sikre, at ikke blokerer for opkaldene, og filerne, der overføres parallelt.
    
    
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }
    
    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }



Når du overfører et stort antal aktiver, skal du overveje følgende.

- Oprette et nyt **CloudMediaContext** objekt per tråd. Klassen **CloudMediaContext** er ikke sikker tråd.
 
- Øge NumberOfConcurrentTransfers fra standardværdien på 2 til en højere værdi som 5. Angive denne egenskab påvirker alle forekomster af **CloudMediaContext**. 
 
- Beholde ParallelTransferThreadCount hos standardværdien af 10.
 
##<a id="ingest_in_bulk"></a>Ingesting Aktiver samlet ved hjælp af Media Services .NET SDK 

Overførsel af store aktiv filer kan være en flaskehals under oprettelse af aktiv. Ingesting aktiver i gangen eller "Flere Ingesting", omfatter afkobling aktiv oprettelse af overførslen. For at bruge en masse ingesting metode skal du oprette et manifest (IngestManifest), der beskriver aktivet og de tilknyttede filer. Derefter skal du bruge metoden Overfør efter eget valg til at overføre de tilknyttede filer til det manifest blob objektbeholder. Microsoft Azure Media Services ser objektbeholderen blob, der er knyttet til manifestet. Når en fil er overført til objektbeholderen blob, fuldfører Microsoft Azure Media Services aktiv oprettelse baseret på konfigurationen af aktivet i manifestet (IngestManifestAsset).


For at oprette et nyt IngestManifest opkald metoden Create vises af samlingen IngestManifests på CloudMediaContext. Denne metode opretter en ny IngestManifest med det manifest navn, du angiver.

    IIngestManifest manifest = context.IngestManifests.Create(name);

Oprette de aktiver, der er knyttet til flere IngestManifest. Konfigurere de ønskede krypteringsindstillinger på aktiv for flere ingesting.

    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

En IngestManifestAsset knytter et aktiv til en masse IngestManifest for flere ingesting. Det knytter også AssetFiles, der udgør hver aktiv. For at oprette en IngestManifestAsset skal du bruge metoden Create på serverkonteksten.

Følgende eksempel viser, at tilføje to nye IngestManifestAssets, der knytter de to Aktiver tidligere har oprettet til flere indtager manifest. Hver IngestManifestAsset knytter også et sæt af filer, der vil blive overført til hver enkelt aktiv under flere ingesting.  

    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;
    
    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
    
Du kan bruge en hvilken som helst høj hastighed klientprogrammet stand til at overføre aktiv filer til objektbeholderen til lagring af blob URI, der leveres af egenskaben **IIngestManifest.BlobStorageUriForUpload** for IngestManifest. Én væsentlige høj hastighed Overfør tjenesten er [Aspera On Demand for Azure-program](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). Du kan også skrive kode for at uploade Aktiver-filer som vist i følgende kodeeksempel.
    
    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
    
            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
    
            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);
    
            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });
    
        copytask.Start();
    }

Koden for overførsel af aktiv filer eksemplet bruges i dette emne vises i følgende kodeeksempel.
    
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
    

Du kan se statussen for flere ingesting for alle aktiver, der er knyttet til en **IngestManifest** ved at forespørge egenskaben statistik for **IngestManifest**. For at opdatere de oplysninger om status, skal du bruge en ny **CloudMediaContext** , hver gang du afstemning egenskaben statistik.

I følgende eksempel demonstrerer forespørgsel blandt en IngestManifest efter dens **Id**.
    
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
    
          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
    
                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }
    
             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
    


##<a name="upload-files-using-net-sdk-extensions"></a>Overføre filer ved hjælp af .NET SDK filtypenavne 

I eksemplet nedenfor viser, hvordan du overfører en enkelt fil ved hjælp af .NET SDK udvidelser. I dette tilfælde metoden **CreateFromFile** bruges, men den asynkrone version er også tilgængelig (**CreateFromFileAsync**). Metoden **CreateFromFile** kan du angive navnet på den fil, krypteringsindstilling og et tilbagekald for at rapportere fremdrift for overførsel af filen.


    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });
    
        Console.WriteLine("Asset {0} created.", inputAsset.Id);
    
        return inputAsset;
    }

I følgende eksempel kalder UploadFile funktion og angiver lagerplads kryptering som indstillingen for oprettelse af aktiv.  


    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-step"></a>Næste trin

Nu, hvor du har overført et aktiv til Media Services, skal du gå til [Sådan får du en Media Processor][] emnet.

[Sådan får du en Media-Processor]: media-services-get-media-processor.md
 
