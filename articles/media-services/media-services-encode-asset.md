<properties 
    pageTitle="Oversigt og sammenligning af Azure på demand medier kodere | Microsoft Azure" 
    description="Dette emne er en oversigt over og sammenligning af Azure på demand medier kodere." 
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
    ms.date="09/19/2016" 
    ms.author="juliako"/>

#<a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Oversigt og sammenligning af Azure på demand medier kodere

##<a name="encoding-overview"></a>Kodning oversigt

Azure Media Services er der flere muligheder for kodningen af medier i skyen.

Når du starter med Media Services, er det vigtigt at forstå forskellen mellem codecs og filformater.
Codecs er den software, der implementerer komprimering/kompression algoritmer, mens filformater er objektbeholdere, hold komprimeret videoen.

Media Services indeholder dynamiske emballagen, som du kan bruge til at fremvise din tilpassede bithastighed MP4 eller udglattet Streaming kodet indhold i streaming formater, der understøttes af Media Services (MPEG TANKESTREG, HLS, bløde Streaming, HD'ER) uden at du kan igen pakkes i disse streaming formater.

For at kunne udnytte [dynamiske emballagen](media-services-dynamic-packaging-overview.md), skal du gøre følgende:

- Kod filen mezzanine (kilde) i et sæt af tilpassede bithastighed MP4 filer eller tilpasset bithastighed udglattede Streaming filer (kodning trinnene vises senere i dette selvstudium).
- Få mindst én efter behov streaming enhed for streaming slutpunktet, som du planlægger at levering af dit indhold. Se, [hvordan du skala On Demand Streaming reserveret enheder](media-services-portal-manage-streaming-endpoints.md)kan finde flere oplysninger.

Media Services understøtter følgende på demand kodere, der er beskrevet i denne artikel:

- [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
- [Media Encoder Premium arbejdsproces](media-services-encode-asset.md#media-encoder-premium-workflow)

I denne artikel giver en kort oversigt over efter behov medier kodere og indeholder links til artikler, som giver mere detaljerede oplysninger. Emnet indeholder også sammenligning af kodere.

Bemærk, at som standard hver Media Services-konto kan have én aktiv kodning opgave ad gangen. Du kan reservere kodning enheder, hvor du kan have flere kodning opgaver, der kører samtidig, en for hver kodning reserveret enhed, du har købt. For at vide, skal du se [Skalering kodning enheder](media-services-scale-media-processing-overview.md).

##<a name="media-encoder-standard"></a>Media Encoder Standard

###<a name="how-to-use"></a>Sådan bruges

[Sådan kode med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

###<a name="formats"></a>Formater

[Formater og codecs](media-services-media-encoder-standard-formats.md)

###<a name="presets"></a>Forudindstillinger

Media Encoder Standard er konfigureret ved hjælp af en af de encoder forudindstillinger beskrevet [her](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Input- og metadata

Kodere input metadataene er beskrevet [her](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Kodere output metadata er beskrevet [her](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###<a name="generate-thumbnails"></a>Generere miniaturer

Du kan finde oplysninger [hvordan du kan generere miniaturer ved hjælp af Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#thumbnails).

###<a name="trim-videos-clipping"></a>Trimme videoer (skærmklip)

Oplysninger, se, [hvordan du trimme videoer ved hjælp af Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#trim_video).

###<a name="create-overlays"></a>Oprette masker

Du kan finde oplysninger [Sådan opretter du overlejrer ved hjælp af Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#overlay).

###<a name="see-also"></a>Se også

[Media Services-blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##<a name="media-encoder-premium-workflow"></a>Media Encoder Premium arbejdsproces

###<a name="overview"></a>Oversigt

[Introduktion til Premium-kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###<a name="how-to-use"></a>Sådan bruges

Media Encoder Premium arbejdsprocessen er konfigureret til brug af komplekse arbejdsprocesser. Arbejdsproces filer blev oprettet og opdateret ved hjælp af værktøjet [Workflow Designer](media-services-workflow-designer.md) .

[Sådan bruger du Premium kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###<a name="known-issues"></a>Kendte problemer

Hvis videoen input ikke indeholder indeholder undertekster, output aktiv stadig en tom TTML-fil. 


##<a id="compare_encoders"></a>Sammenligne kodere

###<a id="billing"></a>Fakturering dokumentmåler anvendes af hver encoder

Navnet på medie Processor|Det er relevant priser|Noter
---|---|---
**Media Encoder Standard** |ENCODER|Kodning opgaver skal betale efter størrelsen på output aktiv i GBytes på den rente, der er angivet [her][1], under kolonnen ENCODER.
**Media Encoder Premium arbejdsproces** |PREMIUM ENCODER|Kodning opgaver skal betale efter størrelsen på output aktiv i GBytes på den rente, der er angivet [her][1], under kolonnen PREMIUM ENCODER.


Dette afsnit sammenlignes funktionerne kodning af **Media Encoder Standard** og **Media Encoder Premium arbejdsproces**.


###<a name="input-containerfile-formats"></a>Skriv objektbeholder /-filformater

Skriv objektbeholder /-filformater|Media Encoder Standard|Media Encoder Premium arbejdsproces
---|---|---
Adobe® Flash® F4V           |Ja|Ja
MXF/SMPTE 377M              |Ja|Ja
GXF                         |Ja|Ja
MPEG-2 Transport-Streams    |Ja|Ja
MPEG-2-programstreams      |Ja|Ja
MPEG-4/MP4                  |Ja|Ja
Windows Media/afrikansk           |Ja|Ja
AVI (dekomprimerede 8-bit/10 bit)|Ja|Ja
3GPP/3GPP2                  |Ja|Nej
Problemfri Streaming-filformat (PIFF 1.3)|Ja|Nej
[Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Ja|Nej
Matroska/WBEM               |Ja|Nej
QuickTime (.mov) |Ja|Nej

###<a name="input-video-codecs"></a>Skriv Codecs til videoenheder

Skriv Codecs til videoenheder|Media Encoder Standard|Media Encoder Premium arbejdsproces
---|---|---
AVC 8-bit/10-bit, op til 4:2:2, herunder AVCIntra   |8 bit 4:2:0 og 4:2:2|Ja
Entusiastiske DNxHD (i MXF)                                 |Ja|Ja
DVCPro/DVCProHD (i MXF)                            |Ja|Ja
JPEG2000                                            |Ja|Ja
MPEG-2 (op til 422 profil og højt niveau, herunder varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® og D10)|Op til 422 profil|Ja
MPEG-1                                              |Ja|Ja
Windows Media Video/VC-1                            |Ja|Ja
Canopus HQ/HQX                                      |Nej|Nej
MPEG-4-del 2                                       |Ja|Nej
[Theora](https://en.wikipedia.org/wiki/Theora)      |Ja|Nej
Apple ProRes 422    |Ja|Nej
Apple ProRes 422 LT |Ja|Nej
Apple ProRes 422 HQ |Ja|Nej
Apple ProRes Proxy|Ja|Nej
Apple ProRes 4444 |Ja|Nej
Apple ProRes 4444 XQ |Ja|Nej

###<a name="input-audio-codecs"></a>Skriv Codecs til lydenheder

Skriv Codecs til lydenheder|Media Encoder Standard|Media Encoder Premium arbejdsproces
---|---|---
AES (SMPTE 331 M og 302 M, AES3-2003)        |Nej|Ja
Dolby® E                                    |Nej|Ja
Dolby® Digital (AC3)                        |Nej|Ja
Dolby® Digital Plus (E-AC3)                 |Nej|Ja
AAC (AAC-LC, AAC-HE og AAC-HEv2; op til 5.1)|Ja|Ja
MPEG lag 2|Ja|Ja
MP3 (MPEG-1 Audio Layer 3)|Ja|Ja
Windows Media Audio|Ja|Ja
WAV/PCM|Ja|Ja
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Ja|Nej
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format)) |Ja|Nej
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Ja|Nej


###<a name="output-containerfile-formats"></a>Output objektbeholder /-filformater

Output objektbeholder /-filformater|Media Encoder Standard|Media Encoder Premium arbejdsproces
---|---|---
Adobe® Flash® F4V|Nej|Ja
MXF (OP1a, XDCAM og AS02)|Nej|Ja
DPP (herunder AS11)|Nej|Ja
GXF|Nej|Ja
MPEG-4/MP4|Ja|Ja
MPEG-TS|Ja|Ja
Windows Media/afrikansk|Nej|Ja
AVI (dekomprimerede 8-bit/10 bit)|Nej|Ja
Problemfri Streaming-filformat (PIFF 1.3)|Nej|Ja

###<a name="output-video-codecs"></a>Output Codecs til videoenheder

Output Codecs til videoenheder|Media Encoder Standard|Media Encoder Premium arbejdsproces
---|---|---
AVC (H.264; 8-bit; op til høj profil, niveau 5.2; 4 K meget HD; AVC handel)|Kun 8 bit 4:2:0|Ja
Entusiastiske DNxHD (i MXF)|Nej|Ja
MPEG-2 (op til 422 profil og højt niveau, herunder varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® og D10)|Nej|Ja
MPEG-1|Nej|Ja
Windows Media Video/VC-1|Nej|Ja
JPEG-oprettelse af miniaturer|Nej|Ja

###<a name="output-audio-codecs"></a>Output Codecs til lydenheder

Output Codecs til lydenheder|Media Encoder Standard|Media Encoder Premium arbejdsproces
---|---|---
AES (SMPTE 331 M og 302 M, AES3-2003)|Nej|Ja
Dolby® Digital (AC3)|Nej|Ja
Dolby® Digital Plus (E-AC3) op til 7.1|Nej|Ja
AAC (AAC-LC, AAC-HE og AAC-HEv2; op til 5.1)|Ja|Ja
MPEG lag 2|Nej|Ja
MP3 (MPEG-1 Audio Layer 3)|Nej|Ja
Windows Media Audio|Nej|Ja


##<a name="error-codes"></a>Fejlkoder  

I følgende tabel vises fejlkoder, der kan returneres i tilfælde af, at der opstod en fejl under udførelse kodning opgave.  For at få flere oplysninger om fejlen i .NET-kode skal du bruge klassen [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) . For at få flere oplysninger om fejlen i din RESTEN kode skal du bruge [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST-API.

ErrorDetail.Code|Mulige årsager til fejl
-----|-----------------------
Ukendt| Ukendt fejl under udførelse af opgaven
ErrorDownloadingInputAssetMalformedContent|Kategori af fejl, der dækker fejl i hente input aktiv som forkert filnavne skal nul længde-filer, der er forkerte formater og så videre.
ErrorDownloadingInputAssetServiceFailure|Kategori af fejl, der omhandler problemer med tjenesten side – for eksempel netværks- eller fejl under overførsel.
ErrorParsingConfiguration|Kategori af fejl hvor opgave <see cref="MediaTask.PrivateData"/> (konfiguration) ikke er gyldige, for eksempel konfigurationen er ikke et gyldigt system forudindstillede eller den indeholder ugyldige XML.
ErrorExecutingTaskMalformedContent|Kategori af fejl under udførelse af den opgave, hvor problemer i input mediefilerne forårsager fejl.
ErrorExecutingTaskUnsupportedFormat|Kategori af fejl, hvor medier processor kan ikke behandle de filer, der leveres - medier formatere ikke understøttes eller svarer ikke til konfigurationen. For eksempel forsøger at giver en ren output fra et aktiv, der indeholder kun video
ErrorProcessingTask|Kategori af andre fejl, der støder på medier processor under behandling af opgaven, der ikke er relateret til indhold.
ErrorUploadingOutputAsset|Kategori af fejl, når du overfører output aktiv
ErrorCancelingTask|Kategori af fejl dækker fejl, når du forsøger at annullere opgaven
TransientError|Kategori af fejl dækker midlertidige problemer (f.eks.) midlertidige problemer med Azure-lager)


For at få hjælp fra **Media Services** -teamet skal du åbne en [understøtter brugertilladelse](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).



##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-articles"></a>Relaterede artikler

- [Udføre avancerede kodning opgaver ved at tilpasse Media Encoder Standard forudindstillinger](media-services-custom-mes-presets-with-dotnet.md)
- [Kvoter og begrænsninger](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
