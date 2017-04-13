<properties
    pageTitle=" Skala streaming slutpunkter ved hjælp af Azure portal | Microsoft Azure"
    description="Dette selvstudium vejleder dig gennem trinnene til skalering streaming slutpunkter ved hjælp af Azure portal."
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


# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Skalere streaming slutpunkter ved hjælp af Azure portal

##<a name="overview"></a>Oversigt

> [AZURE.NOTE] For at fuldføre dette selvstudium skal have du en Azure konto. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). 

Når du arbejder med Azure Media Services en af de mest almindelige scenarier levere video via tilpasset bithastighed streaming på dine kunder. Media Services understøtter følgende tilpasset bithastigheden streaming teknologier: HTTP Live Streaming (HLS), bløde Streaming, MPEG TANKESTREG og HD'ER (for kun Adobe PrimeTime/adgang til indehavere).

Media Services indeholder dynamiske emballagen, som du kan bruge til at fremvise din tilpassede bithastighed MP4-kodet indhold i streaming formater, der understøttes af Media Services (MPEG TANKESTREG, HLS, bløde Streaming, HD'ER) just-in-time, uden at du gemme allerede pakkede versioner af hver af disse streaming formater.

For at kunne udnytte dynamiske emballagen, skal du gøre følgende:

- Kod filen mezzanine (kilde) i et sæt tilpasset bithastighed MP4-filer (kodning trinnene vises senere i dette selvstudium).  
- Oprette mindst én streaming enhed til den *streaming slutpunkt* , som du planlægger at levering af dit indhold. Nedenstående trin viser, hvordan du kan ændre antallet af streaming enheder.

Med dynamisk emballagen du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format og Media Services, oprette og udskrive relevante svaret baseret på anmodninger fra en klient.

Desuden kan du styre kapaciteten af tjenesten Streaming slutpunkt til at håndtere voksende båndbredde behov ved at justere streaming enheder. Det anbefales at tildele en eller flere skalaenheder til programmer i produktionsmiljø. Streaming enheder giver dig både dedikeret udgangspunkt kapacitet, der kan købes i tidsintervaller 200 Mbps og yderligere funktionalitet hvilken funktionalitet, som omfatter: [dynamiske emballagen](media-services-dynamic-packaging-overview.md), CDN integration og Avanceret konfiguration. Du kan finde yderligere oplysninger finder [Administrer streaming slutpunkter ved hjælp af Azure portal](media-services-portal-manage-streaming-endpoints.md).

## <a name="scale-streaming-endpoints"></a>Skalere streaming slutpunkter

For at oprette og ændre antallet af streaming reserveret enheder skal du gøre følgende:

1. Vælg kontoen Azure Media Services på [Azure-portalen](https://portal.azure.com/).
2. Vælg **Streaming slutpunkter**i vinduet **Indstillinger** .
3. Klik på det streaming slutpunkt, du vil skalere. 
4. Flyt skyderen for at angive antallet streaming enheder
 
![Streaming slutpunkt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

Overvej følgende gælder:

- Tildeling af en hvilken som helst nye streaming enheder kan tage omkring 20 minutter for at gennemføre. 
- I øjeblikket, kan går fra en hvilken som helst positive værdi af streaming enheder tilbage til ingen, deaktivere op til en time streaming efter behov.
- Det højeste antal enheder, der er angivet for 24-timers periode bruges til beregning af omkostninger. Du kan finde oplysninger om priser detaljer [Media Services priser detaljer](http://go.microsoft.com/fwlink/?LinkId=275107).

##<a name="next-steps"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


