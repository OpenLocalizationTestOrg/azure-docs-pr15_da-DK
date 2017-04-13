<properties 
    pageTitle="Kode et aktiv med Media Encoder Standard ved hjælp af .NET | Microsoft Azure" 
    description="Dette emne viser, hvordan du bruger .NET til at kode en aktiv ved hjælp af Media Encoder Strandard." 
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
    ms.author="juliako;anilmur"/>


# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Kode et aktiv med Media Encoder Standard ved hjælp af .NET

Kodning job er en af de mest almindelige behandling handlinger i Media Services. Du opretter kodning job for at konvertere mediefiler fra én kodning til en anden. Når du koder, kan du bruge Media Services indbyggede Media Encoder. Du kan også bruge en koder, der leveres af en Media Services partner; fra tredjepart kodere er tilgængelige via Azure Marketplace. 

Dette emne viser, hvordan du bruger .NET til at kode dine aktiver med Media Encoder Standard (MAER). Media Encoder Standard er konfigureret ved hjælp af en af de encoder forudindstillinger beskrevet [her](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Det anbefales at altid kode din mezzanine-filer i en tilpasset bithastighed MP4 angive og Konverter derefter sættet til det ønskede format ved hjælp af [Dynamiske emballagen](media-services-dynamic-packaging-overview.md). For at kunne udnytte dynamiske emballagen, skal du først få mindst én efter behov streaming enhed for streaming slutpunktet, som du planlægger at levering af dit indhold. Se, [hvordan du skala Media Services](media-services-portal-manage-streaming-endpoints.md)kan finde flere oplysninger.

Hvis din output aktiv er krypteret lagerplads, skal du konfigurere aktiv levering politik. Finde flere oplysninger under [konfiguration af aktiv levering politik](media-services-dotnet-configure-asset-delivery-policy.md).

>[AZURE.NOTE]MAER giver en outputfil med et navn, der indeholder de første 32 tegn af input filnavnet. Navnet er baseret på Hvad er angivet i filen forudindstillede. For eksempel "filnavn": "{Basename} _" Index "{udvidelsen}". {Basename} erstattes af de første 32 tegn af input filnavnet.

###<a name="mes-formats"></a>MES formater

[Formater og codecs](media-services-media-encoder-standard-formats.md)

###<a name="mes-presets"></a>MES forudindstillinger

Media Encoder Standard er konfigureret ved hjælp af en af de encoder forudindstillinger beskrevet [her](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Input- og metadata

Når du koder et input aktiv (eller aktiver) ved hjælp af MES-system, får du et output aktiv på det vellykket gennemførelse af dette kode opgave. Output aktivet indeholder video, lyd, miniaturer, manifest, osv., der er baseret på den kodning forudindstillet, du bruger.

Output aktivet indeholder også en fil med metadata om input aktivet. Navnet på metadata XML-filen har følgende format: < asset_id > _metadata.xml (for eksempel 41114ad3-eb5e - 4c 57-8d 92-5354e2b7d4a4_metadata.xml), hvor < asset_id > er emneid værdien af input aktiv. Skemaet for denne input metadata XML er beskrevet [her](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Output aktivet indeholder også en fil med metadata om output aktiv. Navnet på metadata XML-filen har følgende format: < source_file_name > _manifest.xml (for eksempel BigBuckBunny_manifest.xml). Skemaet for disse output metadata XML er beskrevet [her](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Hvis du vil undersøge en af de to metadatafiler, kan du oprette en SAS locator og hente filen til din lokale computer. Du kan finde et eksempel på at oprette en SAS locator og hente en fil ved hjælp af Media Services .NET SDK udvidelser.

##<a name="download-sample"></a>Hente eksempel

Hente og køre en stikprøve fra [her](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##<a name="example"></a>Eksempel

Følgende kodeeksempel bruger Media Services .NET SDK til at udføre følgende opgaver:

- Oprette et kodning job.
- Få en reference til Media Encoder Standard encoder.
- Angive, at den "H264 flere bithastighed 720p" forudindstillede. Du kan se alle forudindstillingerne [her](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). Du kan også bruge skemaet som disse forudindstillinger skal opfylde [her](https://msdn.microsoft.com/library/mt269962.aspx) emne.
- Føje en enkelt kodning opgave til jobbet. 
- Angiv input aktivet kodes.
- Oprette et output aktiv, der indeholder den kodet aktiv.
- Tilføje en hændelseshandler for at kontrollere jobstatus status.
- Sende jobbet.
        
        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        

            // Create a task with the encoding details, using a string preset.
            // In this case "H264 Multiple Bitrate 720p" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Multiple Bitrate 720p",
                TaskOptions.None);
        
            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);
        
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
            return job.OutputMediaAssets[0];
        }
        
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
        
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
        
                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }
        
        
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
            return processor;
        }


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Se også 

[Hvordan du kan generere miniature ved hjælp af Media Encoder Standard med .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Media Services kodning oversigt](media-services-encode-asset.md)
