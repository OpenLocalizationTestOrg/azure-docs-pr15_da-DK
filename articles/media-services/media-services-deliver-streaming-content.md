<properties 
    pageTitle="Publicere Azure Media Services indhold ved hjælp af .NET" 
    description="Få mere at vide, hvordan du opretter en locator, der bruges til at opbygge en streaming URL-adresse. Kodeeksempler, der er skrevet i C# og brug af Media Services SDK til .NET." 
    authors="juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016"
    ms.author="juliako"/>


# <a name="publish-azure-media-services-content-using-net"></a>Publicere Azure Media Services indhold ved hjælp af .NET
 
> [AZURE.SELECTOR]
- [RESTEN](media-services-rest-deliver-streaming-content.md)
- [.NET](media-services-deliver-streaming-content.md)
- [Portal](media-services-portal-publish.md)

##<a name="overview"></a>Oversigt

Du kan streame en tilpasset bithastighed MP4 angive ved at oprette en anmodet arbejdsproces streaming locator og opbygning af en streaming URL-adresse. Emnet [kodning et aktiv](media-services-encode-asset.md) viser, hvordan du kode ind i en tilpasset bithastighed MP4 angive. 

>[AZURE.NOTE]Hvis dit indhold er krypteret, Konfigurer aktiv levering politik (som beskrevet i [dette](media-services-dotnet-configure-asset-delivery-policy.md) emne) før du opretter en locator. 

Du kan også bruge en anmodet arbejdsproces streaming locator til at oprette URL-adresser, der peger på MP4-filer, som kan downloades gradvist.  

Dette emne viser, hvordan du opretter en anmodet arbejdsproces streaming locator for at publicere din aktiv og opbygge en jævne, MPEG TANKESTREG og HLS streaming URL-adresser. Det viser også varmt til at opbygge løbende overførsel URL-adresser. 
     
##<a name="create-an-ondemand-streaming-locator"></a>Oprette en anmodet arbejdsproces streaming locator

Hvis du vil oprette den anmodet arbejdsproces streaming locator og få URL-adresser skal du gøre følgende:

   1. Hvis indholdet er krypteret, definere en access-politik.
   2. Oprette en anmodet arbejdsproces streaming locator.
   3. Hvis du planlægger at streame, kan du få streaming manifestfilen (.ism) i aktivet. 
        
    Hvis du vil hente gradvist, kan du få navnene på MP4-filer i aktivet.  
   4. Oprette URL-adresser til manifestfilen eller MP4-filer. 
   

###<a name="use-media-services-net-sdk"></a>Brug af Media Services .NET SDK 

Opbygge Streaming URL-adresser 

    private static void BuildStreamingURLs(IAsset asset)
    {
    
        // Create a 30-day readonly access policy. 
        // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);
    
        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));
    
        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();
    
        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();
        
        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }

Kode output:
    
    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)
    

>[AZURE.NOTE]Du kan også streame indholdet via en SSL-forbindelse. Kontrollér, at din streaming URL-adresser begynder med HTTPS for at gøre dette. 

Opbygge løbende overførsel URL-adresser 

    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);
    
        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));
    
        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();
    
        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));
                
        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }

Kode output:
    
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4
    
    . . . 

###<a name="use-media-services-net-sdk-extensions"></a>Brug af Media Services .NET SDK filtypenavne

Følgende kode kalder .NET SDK filtypenavne metoder, oprette en locator og generere udglattede Streaming, HLS og MPEG-STREG URL-adresser til tilpasset streaming.

    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));
    
    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();
    
    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Se også

[Hente Aktiver](media-services-deliver-asset-download.md)
[Konfigurer aktiv levering politik](media-services-dotnet-configure-asset-delivery-policy.md)
