<properties 
    pageTitle="Media Encoder standardformater og codecs" 
    description="Dette emne er en oversigt over Media Encoder standardformater og codecs." 
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
    ms.date="10/10/2016"
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder standardformater og Codecs


Dette dokument indeholder en liste over de mest almindelige import og eksport-filformater, der kan bruges med Media Encoder Standard.


##<a name="input-containerfile-formats"></a>Skriv objektbeholder /-filformater

Filformater (filtyper)|Understøttes
---|---|---|---
FLV (med H.264 og AAC codecs) (.flv)          |Ja 
MXF (.mxf)                  |Ja 
GXF (.gxf)                  |Ja 
MPEG2-PS, MPEG2-TS, 3GP (.ts .ps .3gp, .3gpp, og .mpg)   |Ja 
Windows Media Video (WMV) / afrikansk (.wmv, .asf) |Ja 
AVI (dekomprimerede 8-bit/10 bit) (.avi)|Ja 
MP4 (.mp4, .m4a, .m4v) / ISMV (.isma, .ismv)|Ja 
[Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Ja 
Matroska/WBEM (.mkv)        |Ja 
BØLGE/WAV (.wav) |Ja 
QuickTime (.mov) |Ja

>[AZURE.NOTE] Nyere er en liste over mest fundet filtypenavnene. Media Encoder Standard understøtter mange andre (for eksempel: .m2ts, .mpeg2video, .qt). Hvis du forsøger at kode en fil, og du får en fejlmeddelelse om det format, der ikke understøttes, skal du give feedback [her](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).

###<a name="audio-formats-in-input-containers"></a>Lydformater i input beholdere 

Media Encoder Standard understøtter udførelse af de følgende lydformater i input beholdere:

- MXF, GXF og QuickTime filer, som har lydspor med interleaved stereo eller 5.1 eksempler

eller

- MXF GXF QuickTime-filer, og hvor lyden er medtaget som separat PCM spor, men tilknytningen kanal (til stereo eller 5.1) kan udledes fra filens metadata

Bemærk, der understøtter til eksplicitte/brugerangivet kanaltilknytning ydes inden for kort tid.


##<a name="input-video-codecs"></a>Skriv Codecs til videoenheder

Skriv Codecs til videoenheder|Understøttes
---|---|---|---
AVC 8-bit/10-bit, op til 4:2:2, herunder AVCIntra   |8 bit 4:2:0 og 4:2:2 
Entusiastiske DNxHD (i MXF)                                 |Ja 
DVCPro/DVCProHD (i MXF)                            |Ja 
Digitalt videokamera (DV) (i AVI-filer)                   |Ja
JPEG 2000                                           |Ja 
MPEG-2 (op til 422 profil og højt niveau, herunder varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® og D10)|Op til 422 profil 
MPEG-1                                              |Ja 
VC-1/WMV9                                           |Ja 
Canopus HQ/HQX                                      |Nej 
MPEG-4-del 2                                       |Ja 
[Theora](https://en.wikipedia.org/wiki/Theora)      |Ja 
YUV420 ikke-komprimeret format eller mezzanine                   |Ja
Apple ProRes 422                                    |Ja
Apple ProRes 422 LT |Ja
Apple ProRes 422 HQ |Ja
Apple ProRes Proxy|Ja
Apple ProRes 4444 |Ja
Apple ProRes 4444 XQ |Ja



##<a name="input-audio-codecs"></a>Skriv Codecs til lydenheder

Skriv Codecs til lydenheder|Understøttes
---|---|---|---
AAC (AAC-LC, AAC-HE og AAC-HEv2; op til 5.1)|Ja 
MPEG lag 2|Ja 
MP3 (MPEG-1 Audio Layer 3)|Ja 
Windows Media Audio|Ja 
WAV/PCM|Ja 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Ja 
[Opus](http://go.microsoft.com/fwlink/?LinkId=822667) |Ja 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Ja 
AMR (tilpasset flere rente)|Ja
AES (SMPTE 331 M og 302 M, AES3-2003)        |Nej 
Dolby® E                                    |Nej 
Dolby® Digital (AC3)                        |Nej 
Dolby® Digital Plus (E-AC3)                 |Nej 


##<a name="output-formats-and-codecs"></a>Outputformater og codecs

I følgende tabel vises de codecs og filformater, der understøttes til eksport.


Filformat|Video-Codec|Lyd-Codec
---|---|---
MP4 <br/><br/>(herunder flere bithastighed MP4 beholdere) |H.264 (høj, Main og oprindelige profiler)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2 TS |H.264 (høj, Main og oprindelige profiler)|AAC-LC, HE-AAC v1, HE-AAC v2 



##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Se også

[Kodning On Demand-indhold med Azure Media Services](media-services-encode-asset.md)

[Sådan kode med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)
