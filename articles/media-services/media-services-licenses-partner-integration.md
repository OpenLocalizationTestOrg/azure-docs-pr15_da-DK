<properties 
    pageTitle="Brug af partnere til at levere Widevine licenser til Azure Media Services | Microsoft Azure" 
    description="I denne artikel beskrives, hvordan du kan bruge Azure Media Services (AMS) til at levere en stream, krypteres dynamisk ved AMS med både PlayReady og Widevine DRMs. PlayReady licens stammer fra Media Services PlayReady licensserveren og Widevine licens er leveret af castLabs licensserver." 
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

#<a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>Brug af partnere til at levere Widevine licenser til Azure Media Services

##<a name="overview"></a>Oversigt

Microsoft Azure Media Services gør det muligt at levere MPEG-STREG beskyttet med Widevine DRM, som er krypteret per specifikationen almindelige kryptering (CENC).

Media Services, der starter med Media Services .NET SDK version 3.5.2, kan du konfigurere Widevine licens skabelon og få Widevine licenser. Du kan også bruge de følgende AMS partnere kan hjælpe dig med at levere Widevine licenser: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) [castLabs](http://castlabs.com/company/partners/azure/).

##<a name="castlabs"></a>castLabs

Du kan bruge [castLabs](http://castlabs.com/company/partners/azure/) til at levere Widevine licenser. Kan finde flere oplysninger under [Brug af castLabs til at levere DRM licenser til Azure Media Services](media-services-castlabs-integration.md)

##<a name="axinom"></a>Axinom

Du kan bruge [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) til at levere Widevine licenser. Yderligere oplysninger, du [Bruger Axinom til at levere DRM licenser til Azure Media Services](media-services-axinom-integration.md)


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Se også

[Ved hjælp af PlayReady og/eller Widevine dynamiske almindelige kryptering](media-services-protect-with-drm.md)

[Mingfei's blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

