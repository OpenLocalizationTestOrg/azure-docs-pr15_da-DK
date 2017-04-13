<properties 
    pageTitle="Ofte stillede spørgsmål | Microsoft Azure" 
    description="Ofte stillede spørgsmål (ofte stillede spørgsmål)" 
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
    ms.date="09/19/2016" 
    ms.author="juliako"/>


#<a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

##<a name="general-ams-faqs"></a>Generelt AMS ofte stillede spørgsmål

Sp: hvordan du tilpasse indeksering?

SV: reserveret enheder er de samme for kodning og indeksering opgaver. Følg vejledningen på [Sådan skala kodning reserveret enheder](media-services-scale-media-processing-overview.md). **Bemærk** , at ydeevne for indekseringsprogram ikke påvirkes af reserveret enhedstype.

Sp: Jeg har overført, kodet og publiceret en video. Hvad ville være årsagen til videoen afspilles ikke, når jeg forsøger at streame den?

SV: en af de mest almindelige årsager er du ikke har mindst én reserveret streaming enhed, der allokeres på streaming slutpunktet hvorfra du forsøger at afspilning.  Følg vejledningen på [Sådan skala Streaming reserveret enheder](media-services-portal-scale-streaming-endpoints.md).

Sp: kan jeg gøre i baggrunden på en live stream?

A: i baggrunden på direkte streams tilbydes i øjeblikket ikke i Azure Media Services, så du har brug for til at sammensætte allerede på din computer.

Sp: kan jeg bruge Azure CDN med Live Streaming?

SV: Media Services understøtter integration med Azure CDN (du kan finde flere oplysninger, se, [hvordan du administrerer Streaming slutpunkter på en Media Services-konto](media-services-portal-manage-streaming-endpoints.md)).  Du kan bruge Live streaming med CDN. Azure Media Services indeholder udglattede Streaming, HLS og MPEG-STREG output. Alle disse formater Brug HTTP til overførsel af data og få fordelene ved HTTP cachelagring. I direkte streaming faktisk deles video-eller-data til fragmenter og denne individuelle fragmenter få cachelagret i CDN. Kun data skal være opdaterede er manifest dataene. LEVERANDØR(er) opdaterer manifest data med jævne mellemrum.

Sp: betyder Azure Media services understøtter lagring af billeder?

SV: Hvis du bare vil gemme JPEG eller PNG-billeder, skal du holde dem fra Azure Blob-lager. Der er ingen grund til at lægge dem i kontoen Media Services, medmindre du vil beholde dem der er knyttet til din Video eller lyd aktiver. Eller hvis du har behov for at bruge billederne som overlejret i video encoder. Media Encoder Standard understøtter overlejring billeder oven på videoer, og det er, hvad den indeholder, JPEG og PNG som understøttet input formater. Se [Oprette overlejrer](media-services-custom-mes-presets-with-dotnet.md#overlay)kan finde flere oplysninger.

Sp: Hvordan kan jeg kopiere aktiver fra den ene Media Services-konto til en anden.

SV: for at kopiere aktiver fra én Media Services-konto til en anden ved hjælp af .NET, bruge [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) lokalnummer metode, der er tilgængelige i [Azure Media Services .NET SDK filtypenavne](https://github.com/Azure/azure-sdk-for-media-services-extensions/) lager. Se [denne](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) forum tråd kan finde flere oplysninger.

Sp: Hvad er de understøttede tegn til navngivning af filer, når du arbejder med AMS?

SV: Media Services bruger værdien i egenskaben IAssetFile.Name, når du opbygger en URL-adresser for streaming indholdet (for eksempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Derfor er procent-kodning ikke tilladt. Værdien af egenskaben **navn** må ikke være følgende [procent-kodning-reserveret tegn](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Desuden kan kun der en "." for filtypenavnet.


Sp: hvordan du opretter forbindelse ved hjælp af REST?

SV: Når du har oprettet forbindelse til https://media.windows.net, får du en 301 Omdiriger, der angiver en anden Media Services URI. Du skal foretage efterfølgende opkald til den nye URI som beskrevet i [tilslutning til Media Services ved hjælp af REST-API](media-services-rest-connect-programmatically.md). 


Sp: Hvordan kan jeg rotere en video under processen kodning.

SV: [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) understøtter rotationen af vinklerne af 90/180/270. Standardfunktionen er "Automatisk", hvor den forsøger at registrere rotation metadataene i filen indgående MP4/MOV og kompensation for den. Omfatte følgende **kilder** element til en af de json forudindstillinger defineret [her](http://msdn.microsoft.com/library/azure/mt269960.aspx):
    
    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...




##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
