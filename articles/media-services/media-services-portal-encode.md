<properties
    pageTitle="Kode en aktiv, ved hjælp af Media Encoder Standard ved hjælp af Azure portal | Microsoft Azure"
    description="Dette selvstudium vejleder dig gennem trinnene til kodning en aktiv, ved hjælp af Media Encoder Standard ved hjælp af Azure portal."
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
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>Kode en aktiv, ved hjælp af Media Encoder Standard ved hjælp af Azure portal

> [AZURE.NOTE] For at fuldføre dette selvstudium skal have du en Azure konto. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). 

Når du arbejder med Azure Media Services en af de mest almindelige scenarier leverer tilpasset bithastighed streaming til dine kunder. Media Services understøtter følgende tilpasset bithastigheden streaming teknologier: HTTP Live Streaming (HLS), bløde Streaming, MPEG TANKESTREG og HD'ER (for kun Adobe PrimeTime/adgang til indehavere). For at forberede dine videoer til tilpasset bithastighed streaming, skal du kode videoen kilde i flere bithastighed filer. Du skal bruge **Media Encoder Standard** encoder til at kode videoerne.  

Media Services indeholder også dynamisk emballagen, som du kan bruge til at fremvise din multi-bithastighed MP4s i følgende streaming formater: MPEG TANKESTREG, HLS, bløde Streaming eller HD'ER, uden at du kan igen pakkes i disse streaming formater. Med dynamisk emballagen du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format og Media Services, oprette og udskrive relevante svaret baseret på anmodninger fra en klient.

For at kunne udnytte dynamiske emballagen, skal du gøre følgende:

- Kod kildefilen til et sæt af multi-bithastighed MP4-filer (kodning trinnene vist senere i dette emne).
- Få mindst én streaming enhed for streaming slutpunktet, som du planlægger at levering af dit indhold. Yderligere oplysninger, du [konfigurerer streaming slutpunkter](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

Hvis du vil skalere medier behandling, skal du se [dette](media-services-portal-scale-media-processing.md) emne.

## <a name="encode-with-the-azure-portal"></a>Kode ved hjælp af Azure portal

I dette afsnit beskrives de trin, du kan bruge til at kode indholdet med Media Encoder Standard.

1.  Vælg kontoen Azure Media Services på [Azure-portalen](https://portal.azure.com/).
2.  Vælg **Aktiver**i vinduet **Indstillinger** .  
2.  Vælg den aktiv, som du gerne vil kode i vinduet **Aktiver** .
3.  Tryk på knappen **kodes** .
4.  Vælg den "Media Encoder almindelige" processor og en forudindstillet i vinduet **kodes et aktiv** . Eksempelvis hvis du kender din Skriv video har en opløsning på 1920 x 1080 pixel, du kan bruge den "H264 flere bithastighed 1080p" forudindstillede. Du kan finde flere oplysninger om forudindstillinger skal se [i denne](https://msdn.microsoft.com/library/azure/mt269960.aspx) artikel – er det vigtigt at vælge det format, der passer bedst til dit video input. Hvis du har en lav opløsning (640 x 360) video og derefter du skal ikke bruge standard "H264 flere bithastighed 1080p" forudindstillede.
    
    For lettere at administrere har du en mulighed for at redigere navnet på output aktiv, og navnet på jobbet.
        
    ![Kode Aktiver](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Tryk på **Opret**.


##<a name="next-step"></a>Næste trin

Du kan overvåge kodning job fremskridt med portalen Azure, som beskrevet i [denne](media-services-portal-check-job-progress.md) artikel.  

##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


