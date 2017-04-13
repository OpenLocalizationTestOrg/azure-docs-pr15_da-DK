<properties
    pageTitle="Oversigt over dynamiske emballagen | Microsoft Azure"
    description="Emne giver og oversigt over dynamiske emballagen."
    authors="Juliako"
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
    ms.date="10/24/2016" 
    ms.author="juliako"/>


# <a name="dynamic-packaging"></a>Dynamisk emballagen

##<a name="overview"></a>Oversigt

Microsoft Azure Media Services kan bruges til at levere mange medier kilde filformater medier streaming formater og indholdsbeskyttelse filformaterne til en række forskellige klient teknologier (for eksempel iOS XBOX, Silverlight, Windows 8). Disse klienter forstå forskellige protokoller, for eksempel iOS kræver en HTTP Live Streaming (HLS) V4-format og Silverlight og Xbox kræver udglattede Streaming. Hvis du har et sæt af tilpassede bithastighed (multi-bithastighed) MP4 (ISO Base 14496-12) mediefiler eller et sæt tilpasset bithastighed udglattede Streaming filer, du vil gøre det muligt at klienter, der forstå MPEG TANKESTREG, HLS eller udglattet Streaming, bør du drage fordel af Media Services dynamiske emballagen.

Alt, du skal er at oprette et aktiv, der indeholder et sæt af tilpassede bithastighed MP4 filer eller tilpasset bithastighed udglattede Streaming filer med dynamiske emballagen. Derefter sikrer baseret på det angivne format i manifest eller fragment anmodningen, On Demand Streaming server, at du modtager strømmen i den protokol, du har valgt. Som et resultat, du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format Media Services-tjenesten, så og opbygge fungere relevante svaret baseret på anmodninger fra en klient.

I det følgende diagram viser traditionelle kodning og statisk emballagen arbejdsproces.

![Statisk kodning](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

I følgende diagram vises dynamisk emballagen arbejdsprocessen.

![Dynamisk kodning](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]For at kunne udnytte dynamiske emballagen, skal du først få mindst én efter behov streaming enhed for streaming slutpunktet, som du planlægger at levering af dit indhold. Se, [hvordan du skala Media Services](media-services-portal-manage-streaming-endpoints.md)kan finde flere oplysninger.

##<a name="common-scenario"></a>Almindelige scenarie

1. Overføre en input-fil (kaldet en mezzanine-fil). H.264, MP4 eller WMV (til listen over understøttede formater se [Formater understøttes af Media Encoder Standard](media-services-media-encoder-standard-formats.md).

1. Kod mezzanine filen til H.264 MP4 tilpasset bithastighed sæt.

1. Publicere det aktiv, der indeholder tilpassede bithastigheden MP4 angive ved at oprette On Demand Locator.

1. Opbygge streaming URL-adresser for at få adgang til og streame dit indhold.


##<a name="preparing-assets-for-dynamic-streaming"></a>Forberede Aktiver til dynamiske streaming

For at forberede dig på dine aktiver dynamisk streaming har du to muligheder:

1. [Overføre en master fil](media-services-dotnet-upload-files.md).
2. Du kan [bruge Media Encoder Standard encoder for at frembringe H.264 MP4 tilpasset bithastighed sæt](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Stream dit indhold](media-services-deliver-content-overview.md).


##<a id="unsupported_formats"></a>Formater, der ikke understøttes af dynamiske emballagen

Følgende kilde-filformater, der ikke understøttes af dynamiske emballagen.

- Dolby digital mp4-filer.
- Dolby digital udglattede filer.

##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
