<properties 
    pageTitle="Sådan oprettes en medier Processor | Microsoft Azure" 
    description="Lær, hvordan du opretter en medier processor komponent for at kode, konvertere format, kryptere eller dekryptere medieindhold til Azure Media Services. Kodeeksempler, der er skrevet i C# og brug af Media Services SDK til .NET." 
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


#<a name="how-to-get-a-media-processor-instance"></a>Sådan: få en forekomst af medier Processor

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [RESTEN](media-services-rest-get-media-processor.md)


##<a name="overview"></a>Oversigt

Formatere konvertering, kryptere eller dekryptere medieindhold i Media Services en medier processor er en komponent, der håndterer en bestemt behandlingsopgave, som kodning. Du opretter typisk en medier processor, når du opretter en opgave til kode, kryptere eller konvertere formatet af medieindhold.

Den følgende tabel indeholder navn og beskrivelse af hver tilgængelige medier processor.

Navnet på medie Processor|Beskrivelse|Få mere at vide
---|---|---
Media Encoder Standard|Indeholder standard funktioner til kodning efter behov. |[Oversigt og sammenligning af Azure på Demand medier kodere](media-services-encode-asset.md)
Media Encoder Premium arbejdsproces|Kan du køre kodning opgaver ved hjælp af Media Encoder Premium arbejdsproces.|[Oversigt og sammenligning af Azure på Demand medier kodere](media-services-encode-asset.md)
Azure Media indekseringsprogram| Gør det muligt at foretage mediefiler og indhold søgbare samt generere lukket undertekster spor og nøgleord.|[Azure Media indekseringsprogram](media-services-index-content.md)
Azure Media Hyperlapse (preview)|Gør det muligt at udjævne "stød" i din video med video konstante. Også giver dig mulighed at øge hastigheden af dit indhold i et forbrugsvarer klip.|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Azure Media Encoder|Afskrivning
Lagerplads dekryptering| Afskrivning|
Azure Media Packager|Afskrivning|
Azure Media kryptering|Afskrivning|

##<a name="get-media-processor"></a>Få medier Processor

Følgende metode viser, hvordan du får en forekomst af medier processor. Det kode antages brug af en modul niveau variabel med navnet **_context** refereres serverkonteksten, som beskrevet i afsnittet til [Sådan: oprette forbindelse til Media Services automatisk](media-services-dotnet-connect-programmatically.md).

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

##<a name="next-steps"></a>Næste trin

Nu hvor du, hvordan du får en forekomst af medier processor, gå til [Sådan kode et aktiv](media-services-dotnet-encode-with-media-encoder-standard.md) emnet som viser dig, hvordan du bruger Media Encoder Standard til at kode et aktiv.


