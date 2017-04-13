<properties 
    pageTitle="Sådan oprettes en medier Processor | Microsoft Azure" 
    description="Lær, hvordan du opretter en medier processor komponent for at kode, konvertere format, kryptere eller dekryptere medieindhold til Azure Media Services." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
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

##<a name="get-mediaprocessor"></a>Få MediaProcessor

>[AZURE.NOTE] Når du arbejder med Media Services REST-API, gælder følgende overvejelser:
>
>Når du åbner objekter i Media Services, skal du angive bestemte sidehoved felter og værdier i HTTP-anmodninger. Se [konfiguration for Media Services RESTEN API udvikling](media-services-rest-how-to-use.md)kan finde flere oplysninger.

>Når du har oprettet forbindelse til https://media.windows.net, får du en 301 Omdiriger, der angiver en anden Media Services URI. Du skal foretage efterfølgende opkald til den nye URI som beskrevet i [tilslutning til Media Services ved hjælp af REST-API](media-services-rest-connect-programmatically.md). 


Følgende RESTEN opkaldet viser, hvordan du får en forekomst af medier processor ved navn (i dette tilfælde **Media Encoder Standard**). 



    
Anmode om:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net
    
Svar:
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Næste trin

Nu hvor du, hvordan du får en forekomst af medier processor, gå til [Sådan kode et aktiv](media-services-rest-get-started.md) emnet som viser dig, hvordan du bruger Media Encoder Standard til at kode et aktiv.
