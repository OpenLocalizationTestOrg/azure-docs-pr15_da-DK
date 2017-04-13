<properties
    pageTitle="LEVERANDØR(er) cachelagring politik i Media Services udvidelse"
    description="Dette emne er en oversigt over et CDN cachelagring politik i Media Services filtypenavn."
    services="media-services,cdn"
    documentationCenter=".NET"
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="juliako"/>
 
#<a name="cdn-caching-policy-in-media-services-extension"></a>LEVERANDØR(er) cachelagring politik i Media Services udvidelse

Azure Media Services indeholder HTTP baseret tilpasset Streaming og løbende overførsel. HTTP baseret streaming er meget SVG med fordele for-cachelager i proxy- og CDN lag samt i cachen på klienten. Streaming slutpunkter indeholder generelle streaming funktioner, og konfiguration af HTTP-cachen headere. Streaming slutpunkter angiver HTTP Cache-Control: Maks alder og udløber sidehoveder. Du kan få flere oplysninger om HTTP-cachen headere fra [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

##<a name="default-caching-headers"></a>Standard cachelagring sidehoveder

Som standard anvende streaming slutpunkter 3 dag cache sidehoveder for efter behov streaming data (faktisk medier fragmenter/stykker) og manifest(playlist). Direkte streaming streaming slutpunkter anvende 3 dag cache brevhoveder for data (faktisk medier fragmenter/stykker) og to sekunder cache sidehoved manifest(playlist) anmodninger om. Når direkte program omdannes til (direkte arkiv) efter behov anvende efter behov streaming cache sidehoveder.

##<a name="azure-cdn-integration"></a>Azure CDN-integration

Azure Media Services indeholder [integreret CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) til streaming slutpunkter. Cache-styring sidehoveder gælder på samme måde, som streaming slutpunkter til CDN aktiveret streaming slutpunkter. Azure CDN bruger streaming slutpunkt konfigureret cache værdier for at definere levetid internt cachelagrede objekter og også bruger denne værdi til at angive leveringen cache sidehoveder. Når ved hjælp af CDN aktiveret streaming slutpunkter er det ikke anbefales til at angive værdier i små cache. Indstilling af små værdier mindske ydeevnen og reducere fordelen CDN. Det er ikke tilladt at angive cache sidehoveder mindre end 600 sekunder for CDN aktiveret streaming slutpunkter.

>[AZURE.IMPORTANT] Azure Media Services-integration med Azure CDN er implementeret på **Azure CDN fra Verizon**.  Hvis du vil bruge **Azure CDN fra Akamai** til Azure Media Services, skal du [konfigurere slutpunktet manuelt](cdn-create-new-endpoint.md).  Du kan finde flere oplysninger om Azure CDN funktioner, [CDN oversigt](cdn-overview.md).

##<a name="configuring-cache-headers-with-azure-media-services"></a>Konfiguration af cache sidehoveder med Azure Media Services

Du kan bruge Azure Management portal eller Azure Media Services API'er til at konfigurere cache sidehoved værdier.

1. Hvis du vil konfigurere cache sidehoveder ved hjælp af management portal se [Sådan administrere Streaming slutpunkter](../media-services/media-services-portal-manage-streaming-endpoints.md) sektionen konfiguration af slutpunktet Streaming.
2. Azure Media Services REST-API, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [StreamingEndpointCacheControl egenskaber](http://go.microsoft.com/fwlink/?LinkId=615302).

##<a name="cache-configuration-precedence-order"></a>Cache konfiguration prioritetsrækkefølge

1. Azure Media Services konfigureret cache værdi tilsidesætter standardværdien.
2. Hvis der er ingen manuel konfiguration, gælder standardværdier.
3. Sidehoveder anvendes som standard to sekunder cache til dynamiske streaming manifest(playlist) uanset konfigurationen Azure Media eller Azure-lager og tilsidesætte denne værdi er ikke tilgængelig.
