
<properties 
    pageTitle="Administrere aktiver og relaterede objekter med Media Services .NET SDK" 
    description="Lær at administrere aktiver og relaterede objekter med Media Services SDK til .NET." 
    authors="juliako" 
    manager="dwrede" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016"
    ms.author="juliako"/>


#<a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Administrere aktiver og relaterede objekter med Media Services .NET SDK


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md)
- [RESTEN](media-services-rest-manage-entities.md)


Dette emne viser, hvordan du udfører følgende Media Services management opgaver:

- Få en aktiv reference 
- Få en sag reference 
- Liste over alle aktiver 
- Listen opgaver og aktiver 
- Liste over alle access-politikker 
- Liste over alle Locators
- Optælling af store samlinger af objekter
- Slette et aktiv 
- Slette et job 
- Slette en access-politik 

##<a name="prerequisites"></a>Forudsætninger 

Se [konfigurere dit miljø](media-services-set-up-computer.md)

##<a name="get-an-asset-reference"></a>Få en aktiv Reference

En hyppige opgave er at få en reference til et eksisterende aktiv i Media Services. Følgende kodeeksempel viser, hvordan du kan få en aktiv reference fra samlingen Aktiver på serveren kontekst objektet, baseret på et aktiv Id.
Følgende kodeeksempel bruger en Linq forespørgsel til at få en reference til et eksisterende IAsset objekt.

    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();
    
        return asset;
    }

##<a name="get-a-job-reference"></a>Få en sag Reference

Når du arbejder med behandling af opgaver i Media Services kode, du ofte har brug at få en reference til et eksisterende job baseret på et id. Følgende kodeeksempel viser hvordan du får en reference til et IJob objekt fra samlingen job.
WarningWarning du muligvis at få en sag reference, når du starter et længerevarende kodning job og har brug for at kontrollere jobstatus på en tråd. Når metoden returnerer fra en tråd i er tilfældet skal du hente en opdaterede reference til et job.

    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();
    
        return job;
    }

##<a name="list-all-assets"></a>Liste over alle aktiver

Efterhånden som antallet af aktiver, du har i lagerplads vokser, er det praktisk til at få vist dine aktiver. Følgende kodeeksempel viser hvordan du kan navigere gennem samlingen Aktiver på objektet server kontekst. Med de enkelte aktiver skriver kodeeksemplet nogle af dens egenskabsværdier til konsollen. Hver aktiv kan for eksempel indeholder mange mediefiler. Kodeeksemplet skriver alle filer, der er knyttet til hver enkelt aktiv ud.



    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");
    
            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-jobs-and-assets"></a>Listen opgaver og aktiver

En vigtige relaterede opgave er at liste Aktiver med deres tilknyttede job i Media Services. Følgende kodeeksempel viser, hvordan du liste over de enkelte IJob objekter, og derefter til hver sag vises egenskaber om jobbet, alle relaterede opgaver, alle input aktiver, og alle output aktiver. Koden i dette eksempel kan være nyttige til mange andre opgaver. Hvis du vil have vist output aktiverne fra en eller flere kodning job, som du kørte tidligere, viser denne kode for eksempel, hvordan du kan få adgang til output aktiverne. Når du har en reference til et output aktiv, kan du derefter levere indholdet til andre brugere eller programmer ved at hente den eller angive URL-adresser. 

Du kan finde flere oplysninger om indstillinger til at levere aktiver, [Levere Aktiver med Media Services SDK til .NET](media-services-deliver-streaming-content.md).

    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");
    
    
            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }
    
            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {
    
                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-all-access-policies"></a>Liste over alle Access-politikker

Du kan angive en access-politik på et aktiv eller filerne i Media Services. En access-politik definerer tilladelser for en fil eller et aktiv (hvilken type adgang og varigheden). I din Media Services-kode, skal definerer du typisk en access-politik ved at oprette et IAccessPolicy objekt og knytte den til et eksisterende aktiv. Derefter opretter du en ILocator objekt, hvor du kan angive direkte adgang til aktiver i Media Services. Visual Studio-projekt, der følger med serien dokumentation indeholder flere kodeeksempler, der viser, hvordan du oprette og tildele adgang politikker og locators til aktiver.

Følgende kodeeksempel viser, hvordan til at få vist alle access-politikker på serveren, og viser typer tilladelser, der er knyttet til hver. Er en anden nyttig måde at få vist politikker for access til at få vist alle ILocator objekter på serveren, og derefter til hver locator, du kan få vist dets tilknyttede access-politik ved hjælp af egenskaben AccessPolicy.

    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");
    
        }
    }

##<a name="list-all-locators"></a>Liste over alle Locators

En locator er en URL-adresse, der indeholder en direkte sti for at få adgang til et aktiv, sammen med tilladelser til aktivet, som defineret af den locator tilknyttede access-politik. Hver aktiv kan have en samling af ILocator objekter, der er knyttet til den i egenskaben Locators. Serverkonteksten har også en Locators af websteder, der indeholder alle locators.

Følgende kodeeksempel viser alle locators på serveren. For hver locator vises Id for den relaterede aktiv og access-politik. Typen af tilladelser, udløbsdatoen og den fulde sti vises også til aktivet.

Bemærk, at en sti til et aktiv er kun en grundlæggende URL-adressen til aktivet. Hvis du vil oprette en direkte sti til individuelle filer, som en bruger eller et program kunne gå til, skal din kode føje bestemte filstien til locator stien. Du kan finde flere oplysninger om, hvordan du gør dette, skal du se emnet [Levere Aktiver med Media Services SDK til .NET](media-services-deliver-streaming-content.md).

    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }

## <a name="enumerating-through-large-collections-of-entities"></a>Optælling af store samlinger af objekter

Når forespørgsler enheder, er der en grænse på 1000 objekter, der returneres ad gangen, fordi offentlige RESTEN v2 begrænser forespørgselsresultater 1000 resultater. Du skal bruge Spring over, og tag under optælling af store samlinger af enheder. 
    
Følgende funktion løkker gennem alle job i den angivne Media Services-konto. Media Services returnerer 1000 job i job af websteder. Funktionen gør brug af Spring over, og tag at sikre dig, at alle job optælles (Hvis du har mere end 1000 job på din konto).
    
    static void ProcessJobs()
    {
        try
        {
    
            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;
    
            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }
    
                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

##<a name="delete-an-asset"></a>Slette et aktiv

I følgende eksempel sletter et aktiv.

    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();
    
        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");
    
    }

##<a name="delete-a-job"></a>Slette et Job

Hvis du vil slette et job, skal du kontrollere status for jobbet som angivet i egenskaben State. Job, der er færdige eller annulleret kan slettes, mens job, der er i visse andre tilstande i kø, planlagt eller behandler, annulleres først, og klik derefter de kan slettes.
Følgende kodeeksempel viser en metode til at slette en sag ved at markere jobbet stater og derefter slette når tilstanden er færdig eller annulleret. Denne kode afhænger af det forrige afsnit i dette emne for at komme i en reference til en sag: få en sag reference.

    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;
    
        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);
    
            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }
    
        }
    }


##<a name="delete-an-access-policy"></a>Slette en Access-politik

Følgende kodeeksempel viser, hvordan du får en reference til en access-politik ud fra en politik-Id, og derefter slette politikken.

    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();
    
        policy.Delete();
    
    }
    


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
