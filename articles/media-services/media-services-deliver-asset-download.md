<properties 
    pageTitle="Hente medieaktiver" 
    description="Få mere at vide om at hente Aktiver til din computer. Kodeeksempler, der er skrevet i C# og brug af Media Services SDK til .NET." 
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
    ms.date="09/26/2016"
    ms.author="juliako"/>

#<a name="how-to-deliver-an-asset-by-download"></a>Sådan: afholde et aktiv som overførsel

Dette emne beskrives indstillingerne til at levere medieaktiver, der er overført til Media Services. Du kan afholde Media Services indhold i flere programmer scenarier. Du kan hente medieaktiver, eller du kan få adgang til dem ved hjælp af en locator. Du kan sende medieindhold til et andet program eller en anden udbyder. Forbedret ydeevne og skalerbarhed, kan du også levere indhold ved hjælp af en indhold levering netværk (CDN).

I dette eksempel vises, hvordan du henter medieaktiver fra Media Services til din lokale computer. Koden forespørgsler de job, der er knyttet til kontoen Media Services ved job-ID og får adgang til dens **OutputMediaAssets** af websteder (som er angivet af en eller flere output medieaktiver, der resulterer i at køre et job). I dette eksempel vises, hvordan du henter medieaktiver output fra et job, men du kan anvende den samme fremgangsmåde for at hente andre aktiver.

    
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 
    
        // Get a reference to the job. 
        IJob job = GetJob(jobId);
    
        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);
    
        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };
    
        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;
    
            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);
    
            Console.WriteLine("File download path:  " + localDownloadPath);
    
            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));
    
            outputFile.DownloadProgressChanged -= DownloadProgress;
        }
    
        Task.WaitAll(downloadTasks.ToArray());
    
        return outputAsset;
    }
    
    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }



##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

   
##<a name="see-also"></a>Se også 

[Levere streaming indhold](media-services-deliver-streaming-content.md)

