<properties 
    pageTitle="Media Encoder Premium arbejdsproces formater og codecs | Microsoft Azure" 
    description="Dette emne giver et overblik over Media Encoder Premium arbejdsproces filformater formater og codecs" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erik43" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"    
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-premium-workflow-formats-and-codecs"></a>Media Encoder Premium arbejdsproces formater og codecs


>[AZURE.NOTE]Mail mepd på Microsoft.com premium encoder spørgsmål.
>
>Media Encoder Premium arbejdsproces medier processor beskrevet i dette emne er ikke tilgængelig i Kina. 

Dette dokument indeholder en liste over input og output-filformater og codecs, der understøttes af offentlige prøveversionen af **Media Encoder Premium arbejdsproces** encoder.

[Media Encoder Premium Worflow Input formater og Codecs](#input_formats)

[Media Encoder Premium Worflow outputformater og Codecs](#output_formats)

**Media Encoder Premium arbejdsproces** understøtter undertekster er beskrevet i [dette](#closed_captioning) afsnit. 


##<a id="input_formats"></a>Media Encoder Premium arbejdsproces Input formater og Codecs

Følgende afsnit viser de codecs og filformater, der understøtter denne medier processor som input.

###<a name="input-containerfile-formats"></a>Skriv objektbeholder /-filformater

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- MPEG-2 Transport-Streams
- MPEG-2-programstreams
- MPEG-4/MP4
- Windows Media/afrikansk
- AVI (dekomprimerede 8-bit/10 bit)

###<a name="input-video-codecs"></a>Skriv Codecs til videoenheder

- AVC 8-bit/10-bit, op til 4:2:2, herunder AVCIntra
- Entusiastiske DNxHD (i MXF)
- DVCPro/DVCProHD (i MXF)
- JPEG2000
- MPEG-2 (op til 422 profil og højt niveau, herunder varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® og D10)
- MPEG-1
- Windows Media Video/VC-1

###<a name="input-audio-codecs"></a>Skriv Codecs til lydenheder

- AES (SMPTE 331 M og 302 M, AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC, AAC-HE og AAC-HEv2; op til 5.1)
- MPEG lag 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio
- WAV/PCM
 
##<a id="output_format"></a>Media Encoder Premium arbejdsproces outputformater og Codecs

Følgende afsnit viser de codecs og filformater, der understøttes som output fra denne medier processor.

###<a name="output-containerfile-formats"></a>Output objektbeholder /-filformater

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM og AS02)
- DPP (herunder AS11)
- GXF
- MPEG-4/MP4
- Windows Media/afrikansk
- AVI (dekomprimerede 8-bit/10 bit)
- Problemfri Streaming-filformat (PIFF 1.3)
- MPEG-TS 


###<a name="output-video-codecs"></a>Output Codecs til videoenheder

- AVC (H.264; 8-bit; op til høj profil, niveau 5.2; 4 K meget HD; AVC handel)
- Entusiastiske DNxHD (i MXF)
- DVCPro/DVCProHD (i MXF)
- MPEG-2 (op til 422 profil og højt niveau, herunder varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® og D10)
- MPEG-1
- Windows Media Video/VC-1
- JPEG-oprettelse af miniaturer

###<a name="output-audio-codecs"></a>Output Codecs til lydenheder

- AES (SMPTE 331 M og 302 M, AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus (E-AC3) op til 7.1
- AAC (AAC-LC, AAC-HE og AAC-HEv2; op til 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio

##<a id="closed_captioning"></a>Understøttelse af undertekster

Indtager på, **Media Encoder Premium arbejdsproces** understøtter:

1. SCC filer
1. SMPTE TT filer
1. CEA-608/CEA-708 – medtaget som brugerdata (SFI meddelelser på H.264 grundlæggende streams, ATSC/53, SCTE20) eller medtaget som hjælpefunktioner data i MXF/GXF filer
1. STL undertitel filer

Under output er følgende indstillinger tilgængelige:

1. CEA-608 til CEA 708 oversættelse
1. CEA-608/CEA-708 passerer gennem (integreret i SFI meddelelser på H.264 grundlæggende streams eller medtaget som hjælpefunktioner data i MXF filer)
1. SCC
1. SMPTE for tekst (fra kilde CEA 608 per SMPTE RP2052, herunder oprettelse af DFXP-fil)
1. SRT undertitel fil
1. DVB undertitel streams

Bemærk: ikke alle ovenstående outputformaterne der understøttes til levering af via streaming i Azure Media Services.

##<a name="known-issues"></a>Kendte problemer

Hvis videoen input ikke indeholder indeholder undertekster, output aktiv stadig en tom TTML-fil. 


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
