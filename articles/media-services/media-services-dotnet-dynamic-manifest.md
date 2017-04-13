<properties 
    pageTitle="Oprette filtre med Azure Media Services .NET SDK" 
    description="Dette emne beskrives det, hvordan du opretter filtre, så kunden kan bruge dem i stream bestemte dele af en stream. Media Services opretter dynamiske manifester for at opnå denne selektiv streaming." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="07/18/2016"
    ms.author="juliako;cenkdin"/>


#<a name="creating-filters-with-azure-media-services-net-sdk"></a>Oprette filtre med Azure Media Services .NET SDK

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [RESTEN](media-services-rest-dynamic-manifest.md)

Media Services, der starter med 2.11 udgave, kan du angive filtre til dine aktiver. Disse filtre er server side regler, der gør dine kunder til vælger at gøre ting som: Afspil kun en del af en video (i stedet for afspilning af hele video), eller angive kun et undersæt af lyd- og gengivelser, som din kundes enhed kan håndtere (i stedet for alle de gengivelser, der er knyttet til aktivet). Denne filtrering af dine aktiver opnås gennem **Dynamiske manifestet**s, der er oprettet på kundens anmodning til at streame en video, der er baseret på de angivne filtre.

Du kan finde mere detaljerede oplysninger om filtre og dynamisk manifestet, [dynamisk manifester oversigt](media-services-dynamic-manifest-overview.md).

Dette emne viser, hvordan du bruger Media Services .NET SDK til at oprette, opdatere og slette filtre. 


Bemærk Hvis du opdaterer et filter, det kan tage op til 2 minutter til streaming slutpunkt for at opdatere reglerne. Hvis indholdet blev served ved hjælp af dette filter (og cachelagret i proxyer og CDN cache), kan ved at opdatere dette filter medføre player mislykkede forsøg. Det er anbefalet til at rydde cachen efter opdatering af filteret. Hvis denne indstilling ikke er muligt, kan du overveje at bruge et andet filter. 

##<a name="types-used-to-create-filters"></a>Datatyper, der bruges til at oprette filtre

Følgende typer anvendes, når du opretter filtre: 

- **IStreamingFilter**.  Denne type er baseret på det følgende REST-API- [Filter](http://msdn.microsoft.com/library/azure/mt149056.aspx)
- **IStreamingAssetFilter**. Denne type er baseret på de følgende REST-API [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
- **PresentationTimeRange**. Denne type er baseret på de følgende REST-API [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
- **FilterTrackSelectStatement** og **IFilterTrackPropertyCondition**. Disse filtyper er baseret på følgende REST API'er [FilterTrackSelect og FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)


##<a name="createupdatereaddelete-global-filters"></a>Oprette/Opdater/læst/slette globale filtre

Følgende kode viser, hvordan du bruger .NET læse for at oprette, opdatere og slette aktiv filtre.
    
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();
                
    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();
    
    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);
    
    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);
    
    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();
    
    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();


##<a name="createupdatereaddelete-asset-filters"></a>Oprette/Opdater/læst/slette aktiv filtre

Følgende kode viser, hvordan du bruger .NET læse for at oprette, opdatere og slette aktiv filtre.

    
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
    
    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();
    
        
    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());
    
    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);
    
    filter.Update();
    
    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);
    
    // Delete
    filterUpdated.Delete();
    



##<a name="build-streaming-urls-that-use-filters"></a>Opbygge streaming URL-adresser, der bruger filtre

Oplysninger om, hvordan du publicerer og levere dine aktiver finder du under [Fremvisning af indhold til kunder oversigt](media-services-deliver-content-overview.md).


Følgende eksempler viser, hvordan du føjer filtre til din streaming URL-adresser.

**MPEG BINDESTREG** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Problemfri Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HD'ER**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Se også 

[Oversigt over dynamiske manifester](media-services-dynamic-manifest-overview.md)
 

