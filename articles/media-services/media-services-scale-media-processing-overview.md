<properties
    pageTitle="Skalering behandling af medier oversigt | Microsoft Azure"
    description="Dette emne er en oversigt over skalering behandling af medier med Azure Media Services."
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
    ms.date="08/29/2016"
    ms.author="juliako"/>


# <a name="scaling-media-processing-overview"></a>Oversigt over behandling af medier skalering

Denne side giver en oversigt over, hvordan og hvorfor skalere medier behandling. 

## <a name="overview"></a>Oversigt

En Media Services-konto er knyttet til en reserveret enhedstype, som bestemmer hastigheden, som dine medier behandling opgaver behandles. Du kan vælge mellem følgende enhedstyper reserveret: **S1**, **S2**eller **S3**. For eksempel samme kodning jobbet køres hurtigere når du bruger indstillingen **S2** reserveret enhed type Sammenlign med typen **S1** . Du kan finde yderligere oplysninger finder [Reserveret enhedstyper](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Ud over at angive en reserveret enhedstype, kan du angive, at klargøre din konto med reserveret enheder. Antallet af klargjort reserveret enheder bestemmer antallet af medier opgaver, der kan behandles samtidigt på en given konto. Eksempelvis hvis din konto har fem reserveret enheder, og derefter fem medier opgaver skal køre samtidig så lang tid er, der opgaver skal behandles. De resterende opgaver skal vente i køen og vil få valgte til at behandle sekventielt, når en opgave, der kører afsluttes. Hvis en konto ikke har en hvilken som helst reserveret enheder, der er klargjort, bliver derefter opgaver fundet sekventielt. I dette tilfælde afhænger ventetiden mellem én opgave færdige og derefter et start tilgængeligheden af ressourcer i systemet.

## <a name="choosing-between-different-reserved-unit-types"></a>Vælge mellem forskellige reserveret enhed

Den følgende tabel kan du gøre beslutning, når du vælger mellem forskellige kodning hastigheder. Det også giver et par benchmark tilfælde og SAS URL-adresser, som du kan bruge til at hente videoer, som du kan udføre dine egne test:

Scenarier|**S1**|**S2**|**S3**|
----------|------------|----------|------------
Tilsigtede brug store og små bogstaver| Enkelt bithastighed kodning. <br/>Filer på SD eller under løsninger, tid ikke følsomme, lave omkostninger.|Enkelt bithastighed og flere bithastighed kodning.<br/>Normal brugen til både SD og HD kodning. |Enkelt bithastighed og flere bithastighed kodning.<br/>Fuld HD og 4K opløsning videoer. Tid, fordi følsomme, hurtigere kodning. 
Benchmark|[Inputfil: 5 minutter lange 640x360p på 29.97 rammer/andet](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>Kodning til en enkelt bithastighed MP4-fil med samme opløsning tager cirka 11 minutter.|[Input-fil: 5 minutter lange 1280x720p på 29.97 rammer/sekund](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>Kodning med "H264 enkelt bithastighed 720p" forudindstillede træder ca. 5 minutter.<br/><br/>Kodning med "H264 flere bithastighed 720p" forudindstillet tager omkring 11.5 minutter.|[Inputfil: 5 minutter lange 1920x1080p på 29.97 rammer/andet](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>Kodning med "H264 enkelt bithastighed 1080p" forudindstillede træder cirka 2,7 minutter.<br/><br/>Kodning med "H264 flere bithastighed 1080p" forudindstillet tager omkring 5,7 minutter.

##<a name="considerations"></a>Overvejelser i forbindelse med

>[AZURE.IMPORTANT] Gennemgå overvejelser, der er beskrevet i dette afsnit.  

- Reserverede enheder fungerer for parallelizing alle medier behandling, herunder indeksering job med Azure Media indekseringsprogram.  Imidlertid i modsætning til kodning, indekseringsjob mislykkedes ikke få behandlet hurtigere med hurtigere reserveret enheder.

- Hvis bruger delte puljen, det vil sige, har uden en hvilken som helst reserveret enheder, derefter kodes opgaverne den samme ydeevne som med S1 RUs. Men der er ingen øvre grænse til den tid, dine opgaver kan bruger i i kø tilstand, og til enhver tid, højst en opgave er køre.

- De følgende datacentre heller ikke enhedstype **S2** reserveret: Brasilien Syd, Indien vest, Indien Central og Indien syd.

- De følgende datacentre heller ikke enhedstype **S3** reserveret: Brasilien Syd, Indien Vest Indien Central.

- Det højeste antal enheder, der er angivet for 24-timers periode bruges til beregning af omkostninger.


##<a name="quotas-and-limitations"></a>Kvoter og begrænsninger

Se [kvoter og begrænsninger](media-services-quotas-and-limitations.md)for at få oplysninger om kvotaer og begrænsninger og hvordan du åbner en supportbilletter.

##<a name="next-step"></a>Næste trin

Opnå skalering medier behandlingsopgaven med en af disse teknologier: 

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portal](media-services-portal-scale-media-processing.md)
- [RESTEN](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
