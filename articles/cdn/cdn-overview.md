<properties
    pageTitle="Oversigt over Azure CDN | Microsoft Azure"
    description="Lær, hvad Azure indhold levering netværk (CDN) er, og hvordan du kan bruge det til at levere høj båndbredde indhold ved at cachelagre BLOB og statisk indhold."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/30/2016"
    ms.author="casoper"/>

# <a name="overview-of-the-azure-content-delivery-network-cdn"></a>Oversigt over Azure Content Delivery netværk (CDN)

> [AZURE.NOTE] Dette dokument forklarer, hvad Azure indhold levering netværk (CDN) er, hvordan det fungerer, og funktionerne i hvert Azure CDN produkt.  Hvis du vil springe over disse oplysninger og gå direkte til et selvstudium om, hvordan du opretter et CDN slutpunkt, skal du se [Ved hjælp af Azure CDN](cdn-create-new-endpoint.md).  Hvis du vil se en liste over aktuelle CDN node placeringer, skal du se [Azure CDN POP placeringer](cdn-pop-locations.md).

Azure indhold levering netværk (CDN) cachelagres statisk webindhold på strategisk indsatte placeringer til at levere maksimal hastighed til levering af indhold til brugere.  CDN giver udviklere en global løsning til at levere høj båndbredde indhold ved at cachelagre indholdet på fysisk noder på tværs af hele verden. 

Fordelene ved at bruge CDN til cache web webstedsaktiver omfatter:

- Bedre ydeevne og bruger oplevelse for slutbrugere, især hvis ved hjælp af programmer, hvor flere kørsler er nødvendige for at indlæse indhold.
- Stor skalering for bedre at kunne håndtere omgående høj belastning, ønsker i starten af et produkt-lanceringen.
- Ved at distribuere anmodninger og fungerer indhold fra edge-servere, der sendes mindre trafik til origin.


## <a name="how-it-works"></a>Sådan fungerer det

![LEVERANDØR(er) oversigt](./media/cdn-overview/cdn-overview.png)

1. En bruger (Bent) anmoder om en fil (også kaldet et aktiv) med en URL-adresse med en speciel domænenavn, som `<endpointname>.azureedge.net`.  DNS dirigerer anmodningen til den bedste ydeevne punkt af tilstedeværelse (POP) placering.  Dette er som regel den POP, som er geografisk er tættest på brugeren.

2. Hvis edge-servere i POP ikke har filen i deres cache, anmodninger edge server filen fra startpunkt.  Origin kan være en Azure-WebApp, Azure skybaseret tjeneste, Azure-lager-konto eller en offentligt tilgængelig webserver.

3. Origin returnerer filen til edge server, herunder valgfri HTTP overskrifter, der beskriver filens Time-to-Live (TTL).

4. Edge server gemmer filerne og returnerer filen til den oprindelige anmoder (Bent).  Forbliver cachelagrede på edge server fil, indtil TTL udløber.  Hvis origin ikke angiver en TTL, er standard TTL syv dage.

5. Flere brugere kan anmode om den samme fil ved hjælp af den samme URL-adresse, og også sendes til den samme POP.

6. Hvis TTL for filen ikke er udløbet, returnerer edge server filen fra cachen.  Dette resulterer i en hurtigere og hurtigere brugeroplevelse.


## <a name="azure-cdn-features"></a>Azure CDN funktioner

Der er tre Azure CDN produkter: **Azure CDN Standard fra Akamai**, **Azure CDN Standard fra Verizon**og **Azure CDN Premium fra Verizon**.  I følgende tabel vises de funktioner, der er tilgængelige til hvert enkelt produkt.

|       | Standard Akamai | Standard Verizon | Premium Verizon |
|-------|-----------------|------------------|-----------------|
| Nem integration med Azure tjenester som [lager](cdn-create-a-storage-account-with-cdn.md), [Cloud Services](cdn-cloud-service-with-cdn.md), [Web Apps](../app-service-web/cdn-websites-with-cdn.md)og [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;**|
| Administration af via [REST-API](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](./cdn-app-dev-net.md), [Node.js](./cdn-app-dev-node.md)eller [PowerShell](./cdn-manage-powershell.md). | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| HTTPS-support | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Justering af belastning | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [DDOS](https://www.us-cert.gov/ncas/tips/ST04-015) beskyttelse | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| IPv4/IPv6 dobbelt stak | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Understøttelse af brugerdefinerede domæne navn](cdn-map-content-to-custom-domain.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Forespørgsel streng cachelagring](cdn-query-string.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Geografisk-filtrering](cdn-restrict-access-by-country.md) |  | **& #x 2713;** | **& #x 2713;** |
| [Hurtigt fjerne](cdn-purge-endpoint.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Aktiver før indlæsning](cdn-preload-endpoint.md) |  | **& #x 2713;** | **& #x 2713;** |
| [Core analytics](cdn-analyze-usage-patterns.md) |  | **& #x 2713;** | **& #x 2713;** |
| [Understøttelse af HTTP/2](https://msdn.microsoft.com/library/mt762901.aspx) | **& #x 2713;**  |  |  |
| [Avancerede HTTP-rapporter](cdn-advanced-http-reports.md) | | | **& #x 2713;** |
| [Realtid statistik](cdn-real-time-stats.md) | | | **& #x 2713;** |
| [Alarmer i realtid](cdn-real-time-alerts.md) | | | **& #x 2713;** |
| [Kan tilpasses, baseret på regler levering af indhold program](cdn-rules-engine.md) | | | **& #x 2713;** |
| Indstillinger for cache/sidehoved (ved hjælp af [regler program](cdn-rules-engine.md))  | | | **& #x 2713;** |
| URL-omdirigering/omskrivning (ved hjælp af [regler program](cdn-rules-engine.md)) | | | **& #x 2713;** |
| Mobilenhed regler (ved hjælp af [regler program](cdn-rules-engine.md))  | | | **& #x 2713;** |

>[AZURE.TIP] Er der en funktion, du gerne vil se i Azure CDN?  [Give os feedback](https://feedback.azure.com/forums/169397-cdn)! 

## <a name="next-steps"></a>Næste trin

For at komme i gang med CDN skal du se [Ved hjælp af Azure CDN](./cdn-create-new-endpoint.md).

Hvis du er en eksisterende CDN-kunde, kan du nu administrere CDN slutpunkterne via [Microsoft Azure-portalen](https://portal.azure.com) eller med [PowerShell](cdn-manage-powershell.md).

For at se CDN i aktion skal se det [video af vores Build 2016 session](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Lær at automatisere Azure CDN med [.NET](./cdn-app-dev-net.md) eller [Node.js](./cdn-app-dev-node.md).

Prisoplysninger, se [CDN priser](https://azure.microsoft.com/pricing/details/cdn/).
