<properties
    pageTitle="Introduktion til levering af indhold efter behov ved hjælp af .NET | Azure"
    description="Dette selvstudium vejleder dig gennem trinnene til at implementere en on demand-program, levering af indhold med Azure Media Services ved hjælp af .NET."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/17/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Introduktion til levering af indhold efter behov ved hjælp af .NET SDK

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

>[AZURE.NOTE]
> For at fuldføre dette selvstudium skal have du en Azure konto. Yderligere oplysninger finder du [Azure gratis prøveversion](/pricing/free-trial/?WT.mc_id=A261C142F). 
 
##<a name="overview"></a>Oversigt 

Dette selvstudium vejleder dig gennem trinnene til implementering af et Video-on-Demand (VoD) levering af indhold-program ved hjælp af Azure Media Services (AMS) SDK til .NET.


Selvstudiet introducerer grundlæggende Media Services arbejdsprocessen og de mest almindelige programmering objekter og opgaver, der kræves til udvikling af Media Services. Ved afslutningen af selvstudiet, vil du kunne streame eller gradvist hente en eksempelfil medier, som du har overført, kodet og hentet.

## <a name="what-youll-learn"></a>Hvad du lærer

Selvstudiet viser, hvordan du udføre følgende opgaver:

1.  Oprette en Media Services-konto (ved hjælp af portalen Azure).
2.  Konfigurer streaming slutpunkt (ved hjælp af portalen Azure).
3.  Oprette og konfigurere et Visual Studio-projekt.
5.  Oprette forbindelse til kontoen Media Services.
6.  Oprette et nyt aktiv, og Overfør en videofil.
7.  Kod kildefilen i et sæt tilpasset bithastighed MP4 filer.
8.  Udgive aktivet og hente URL-adresser til streaming og gradvist download.
9.  Test ved at afspille dit indhold.

## <a name="prerequisites"></a>Forudsætninger

Følgende er påkrævet til at fuldføre selvstudiet.

- For at fuldføre dette selvstudium skal have du en Azure konto. 
    
    Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](/pricing/free-trial/?WT.mc_id=A261C142F). Du får kredit, der kan bruges til at teste betalt Azure tjenester. Selvom kreditterne bruges, kan du beholde kontoen og bruge gratis Azure tjenester og funktioner, som funktionen Web Apps i Azure App Service.
- Operativsystemer: Windows 8 eller nyere, Windows 2008 R2, Windows 7.
- .NET framework 4.0 eller nyere
- Visual Studio 2010 SP1 (professionel, Premium, Ultimate eller Express) eller nyere versioner.


##<a name="download-sample"></a>Hente eksempel

Hente og køre en stikprøve fra [her](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Oprette en Azure Media Services-konto ved hjælp af portalen Azure

Trinnene i dette afsnit viser, hvordan du opretter en AMS-konto.

1. Log på på [Azure-portalen](https://portal.azure.com/).
2. Klik på **+ Ny** > **Media + CDN** > **Media Services**.

    ![Oprette Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Angiv obligatoriske værdier i **Opret MEDIA SERVICES konto** .

    ![Oprette Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Skriv navnet på den nye AMS-konto i **Kontonavn**. Et kontonavn Media Services er alle små bogstaver tal eller bogstaver, som ikke indeholder mellemrum, og er 3 til 24 tegn.
    2. Vælg mellem de forskellige Azure abonnementer, du har adgang til-abonnement.
    
    2. Vælg den nye eller eksisterende ressource i **Ressourcegruppe**.  En ressourcegruppe er en samling af ressourcer, der deler livscyklus, tilladelser og politikker. Få mere at vide [her](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. På den **placering**bruges Vælg geografisk område til at gemme de medie og metadata poster til kontoen Media Services. Dette område bruges til at behandle og streame medier. Kun de tilgængelige Media Services områder vises på rullelisten. 
    
    3. I **Lagerplads konto**, skal du vælge en lagerplads konto til at levere blob-lager af medieindhold fra kontoen Media Services. Du kan vælge en eksisterende lagerplads konto i det samme geografiske område som kontoen Media Services, eller du kan oprette en lagerplads konto. En ny lagerplads konto er oprettet i samme region. Regler for lagerplads kontonavne er den samme som for Media Services konti.

        Lær mere om lagerplads [her](storage-introduction.md).

    4. Vælg **Fastgør til dashboard** for at se status for konto-installationen.
    
7. Klik på **Opret** nederst i formularen.

    Når kontoen er blevet oprettet, ændres status til at **køre**. 

    ![Indstillinger for Media Services](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Til at administrere kontoen AMS (for eksempel overføre videoer, kode aktiver, overvåge statussen job) bruge vinduet **Indstillinger** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Konfigurere streaming slutpunkter ved hjælp af portalen Azure

Når du arbejder med Azure Media Services en af de mest almindelige scenarier levere video via tilpasset bithastighed streaming på dine kunder. Media Services understøtter følgende tilpasset bithastigheden streaming teknologier: HTTP Live Streaming (HLS), bløde Streaming, MPEG TANKESTREG og HD'ER (for kun Adobe PrimeTime/adgang til indehavere).

Media Services indeholder dynamiske emballagen, som du kan bruge til at fremvise din tilpassede bithastighed MP4-kodet indhold i streaming formater, der understøttes af Media Services (MPEG TANKESTREG, HLS, bløde Streaming, HD'ER) just-in-time, uden at du gemme allerede pakkede versioner af hver af disse streaming formater.

For at kunne udnytte dynamiske emballagen, skal du gøre følgende:

- Kod filen mezzanine (kilde) i et sæt tilpasset bithastighed MP4-filer (kodning trinnene vises senere i dette selvstudium).  
- Oprette mindst én streaming enhed til den *streaming slutpunkt* , som du planlægger at levering af dit indhold. Nedenstående trin viser, hvordan du kan ændre antallet af streaming enheder.

Med dynamisk emballagen, du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format og Media Services opbygger og fungerer relevante svaret baseret på anmodninger fra en klient.

For at oprette og ændre antallet af streaming reserveret enheder skal du gøre følgende:


1. Klik på **Streaming slutpunkter**i vinduet **Indstillinger** . 

2. Klik på standard streaming slutpunkt. 

    Vinduet **Standard STREAMING SLUTPUNKT oplysninger** vises.

3. Skubbe skyderen **Streaming enheder** for at angive antallet streaming enheder.

    ![Streaming enheder](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Klik på knappen **Gem** for at gemme ændringerne.

    >[AZURE.NOTE]Tildeling af en hvilken som helst nye enheder kan tage op til 20 minutter at gennemføre.

##<a name="create-and-configure-a-visual-studio-project"></a>Oprette og konfigurere et Visual Studio-projekt

1. Oprette et nyt C# Console-program i Visual Studio 2013, Visual Studio 2012 eller Visual Studio 2010 SP1. Angiv det **navn**, **placering**og **løsningsnavn**, og klik derefter på **OK**.

2. Brug [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) NuGet pakke til at installere **Azure Media Services .NET SDK filtypenavne**.  Media Services .NET SDK-udvidelser er et sæt af lokalnummer metoder og hjælpefunktioner, der kan forenkle din kode og gøre det nemmere at udvikle med Media Services. Installationen af denne pakke, også installerer **Media Services.NET SDK** og tilføjer alle andre nødvendige afhængigheder.

3. Tilføje en henvisning til system.Configuration i stor samling. Denne samling indeholder klassen **System.Configuration.ConfigurationManager** , som bruges til at få adgang til af konfigurationsfiler, for eksempel App.config.

4. Åbn filen App.config (Tilføj filen til dit projekt, hvis meddelelsen ikke blev tilføjet som standard) og tilføje en *appSettings* sektion til filen. Angiv værdier for din Azure Media Services navn og konto kontonøgle, som vist i følgende eksempel. Gå til [Azure portal](https://portal.azure.com/) for at få det kontonavn og vigtige oplysninger, og Vælg kontoen AMS. Vælg derefter **Indstillinger for** > **taster**. Administrer taster windows Viser kontonavnet og tasterne primære og sekundære vises.

        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
          
        </configuration>

5. Overskriv eksisterende **ved hjælp af** sætningerne i starten af filen Program.cs med følgende kode.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        

6. Oprette en ny mappe under mappen projekter, og Kopiér en .mp4- eller .wmv-fil, du vil kode og streame eller hente gradvist. I dette eksempel bruges stien "C:\VideoFiles".

##<a name="connect-to-the-media-services-account"></a>Oprette forbindelse til kontoen Media Services

Når du bruger Media Services med .NET, skal du bruge klassen **CloudMediaContext** for de fleste Media Services programming opgaver: oprette forbindelse til Media Services-konto oprette, opdatere, få adgang til og slette følgende objekter: aktiver, aktiv filer, job, access politikker, locators osv.

Overskrive klassen standard Program med følgende kode. Koden demonstrerer, hvordan du kan læse forbindelse værdierne fra App.config-filen, og hvordan du opretter **CloudMediaContext** objektet for at oprette forbindelse til Media Services. Du kan finde flere oplysninger om at oprette forbindelse til Media Services, i [tilslutning til Media Services med Media Services SDK til .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

Funktionen **Main** kalder metoder, der defineres yderligere i dette afsnit.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##<a name="create-a-new-asset-and-upload-a-video-file"></a>Oprette et nyt aktiv, og Overfør en videofil

I Media Services, du overfører (eller indtager) dine digitale filer til et aktiv. Objektet **aktiv** kan indeholde video, lyd, billeder, miniaturebilleder af websteder, tekst numre, og undertekster filer (og metadata om filerne.)  Når filerne er overført, er dit indhold gemt sikkert i skyen til yderligere behandling og streaming. Filerne i aktivet kaldes **Aktiv filer**.

Metoden **UploadFile** er defineret under opkald **CreateFromFile** (som defineret i .NET SDK Extensions). **CreateFromFile** opretter et nyt aktiv, hvor den angivne kildefilen er overført.

Metoden **CreateFromFile** tager **AssetCreationOptions** , hvor du kan angive en af følgende aktiv oprettelse af indstillinger:

- **Ingen** - kryptering ikke bruges. Dette er standardværdien. Bemærk, at når du bruger denne indstilling, indholdet ikke er beskyttet undervejs eller i resten i lagerplads.
Hvis du planlægger at levere en MP4 ved hjælp af løbende overførsel Brug denne indstilling.
- **StorageEncrypted** - Brug denne indstilling til at kryptere Ryd indholdet lokalt ved hjælp af avancerede kryptering AES (Standard) – 256 bit kryptering, der overfører det til Azure-lager, hvor det er gemt krypteret på resten. Aktiver, der er beskyttet med lagerplads kryptering er automatisk uden kryptering og placeret i et krypteret filsystem før kodning, og du kan også igen krypteret før uploade tilbage som et nyt output aktiv. Den primære use case for lagerplads kryptering er, når du vil sikre høj kvalitet input mediefilerne med stærk kryptering på resten på disk.
- **CommonEncryptionProtected** - Brug denne indstilling, hvis du sender indhold, der allerede er krypteret og beskyttet med almindelige kryptering eller PlayReady DRM (for eksempel udglattede Streaming beskyttet med PlayReady DRM).
- **EnvelopeEncryptionProtected** – Brug denne indstilling, hvis du sender HLS, der er krypteret med AES. Bemærk, at filerne skal er blevet kodet og krypteret af transformere Manager.

Metoden **CreateFromFile** kan du angive et tilbagekald for at rapportere fremdrift for overførsel af filen.

I eksemplet nedenfor Angiv vi **ingen** indstillingerne aktiv.

Tilføj følgende metode til klassen Program.

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


##<a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Kode kildefilen i et sæt tilpasset bithastighed MP4 filer

Efter ingesting aktiver i Media Services, medier kan være kodet transmuxed, vandmærke, og så videre, før den leveres til klienter. Disse aktiviteter er planlagt og kører i forhold til flere forekomster af baggrunden rolle at sikre høj ydeevne og tilgængelighed. Disse aktiviteter kaldes job, og hvert Job består af atomisk opgaver, der gør det faktiske arbejde på filen aktiv.

Som tidligere nævnt, når du arbejder med Azure Media Services, en af de mest almindelige scenarier levere tilpasset bithastighed streaming til dine kunder. Media Services kan dynamisk pakke en række tilpasset bithastighed MP4 filer til et af følgende formater: HTTP Live Streaming (HLS), bløde Streaming, MPEG TANKESTREG og HD'ER (for kun Adobe PrimeTime/adgang til indehavere).

For at kunne udnytte dynamiske emballagen, skal du gøre følgende:

- Kode eller kode et din mezzanine (kilde) fil i et sæt af tilpassede bithastighed MP4 filer eller tilpasset bithastighed udglattede Streaming filer.  
- Få mindst én streaming enhed for streaming slutpunktet, som du planlægger at levering af dit indhold.

Følgende kode viser, hvordan du sender en kodning job. Jobbet indeholder én opgave, der angiver kode et mezzanine-fil i et sæt af tilpassede bithastighed MP4s ved hjælp af **Media Encoder Standard**. Koden sender jobbet og vente, indtil den er fuldført.

Når jobbet er fuldført, vil du kunne streame dine aktiv eller gradvist hente MP4-filer, der er oprettet som et resultat af kodning.
Bemærk, at du ikke behøver være mere end 0 streaming enheder for at hente gradvist MP4-filer.

Tilføj følgende metode til klassen Program.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {
    
        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.
    
        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);
    
        Console.WriteLine("Submitting transcoding job...");
    
    
        // Submit the job and wait until it is completed.
        job.Submit();
    
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;
    
        Console.WriteLine("Transcoding job finished.");
    
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        return outputAsset;
    }

##<a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Udgive aktivet og hente URL-adresser til streaming og gradvist download

For at streame eller hente et aktiv, skal du først "udgive" ved at oprette en locator. Locators giver adgang til filer, der findes i aktivet. Media Services understøtter to typer locators: OnDemandOrigin locators, bruges til at streame medier (for eksempel MPEG TANKESTREG, HLS eller udglattet Streaming) og Access signatur (SAS) locators bruges til at hente mediefiler.

Når du opretter locators, kan du oprette de URL-adresser, der bruges til at streame eller hente filerne.


En streaming URL-adresse til jævn Streaming har følgende format:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

En streaming URL-adresse til HLS har følgende format:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

En streaming URL-adresse til MPEG TANKESTREG har følgende format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

En SAS URL-adresse, bruges til at hente filer har følgende format:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Media Services .NET SDK filtypenavne give praktisk hjælper metoder, der returnerer formateret URL-adresser til den publicerede aktiv.

Følgende kode bruger .NET SDK udvidelser til at oprette locators og få streaming og løbende overførsel URL-adresser. Koden viser også, hvordan du henter filer til en lokal mappe.

Tilføj følgende metode til klassen Program.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##<a name="test-by-playing-your-content"></a>Teste ved at afspille dit indhold  

Når du kører det program, der er defineret i forrige afsnit, vises URL-adresser stil med følgende i vinduet console.

Tilpasset streaming URL-adresser:

Problemfri Streaming

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG BINDESTREG

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

Løbende overførsel URL-adresser (lyd og video).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


For at streame video, skal du bruge [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Indsæt en URL-adresse i en browser (for eksempel Internet Explorer, Chrome eller Safari) for at teste løbende overførsel.


##<a name="next-steps-media-services-learning-paths"></a>Næste trin: Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### <a name="looking-for-something-else"></a>Leder du efter noget andet?

Hvis dette emne ikke indeholder hvad du forventede, der mangler noget eller i en anden måde opfylder ikke dine behov, kan du give os din feedback, ved hjælp af den Disqus tråd nedenfor.


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/
