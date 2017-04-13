<properties 
    pageTitle="Oprette avancerede kodning arbejdsprocesser med Workflow Designer | Microsoft Azure" 
    description="Få mere at vide om, hvordan du opretter avancerede kodning arbejdsprocesser med Workflow Designer." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;johndeu;anilmur"/>


#<a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Oprette avancerede kodning arbejdsprocesser med Workflow Designer

##<a name="overview"></a>Oversigt

**Workflow Designer** er et stationære Windows-værktøj, der bruges til at designe og opbygge brugerdefinerede arbejdsprocesser til kodning med **Media Encoder Premium arbejdsproces**.
Du kan designe og oprette komplekse arbejdsprocesser, der skal køre i **Media Encoder Premium**ved hjælp af en potens af værktøjet workflow designer.  

Arbejdsprocesser kan indeholde kunde beslutning logik og forgreninger baseret på inputkilde filegenskaber. Du kan oprette arbejdsprocesser med kan tilsidesættes egenskaber og dynamiske værdier, der gør det nemt at gentage og tilpasse i skyen selv de mest komplekse kodning opgaver.

Eksempel arbejdsprocesser, som du kan oprette omfatter:

- Beslutning baseret arbejdsprocesser, der undersøge kildeindholdet til opløsning og kode kun de ønskede output numre.  Dette er helfpul ved at fjerne de spilde numre, der ville være oprettet af upscaling på kilde indhold ved en fejltagelse.
- Flere input filer kan bruges til at understøtte billedtekster, masker og kombinere sammen indhold. 

Dette værktøj kan også bruges til at ændre nogen af vores [publiceret arbejdsprocesser](media-services-workflow-designer.md#existing_workflows). 

>[AZURE.NOTE]For at få din kopi af værktøjet Workflow Designer, skal du kontakte mepd@microsoft.com.


Når der oprettes en arbejdsproces-fil, kan overføres som et aktiv, og derefter bruges til kodning mediefiler. Oplysninger om, hvordan du kode med **Media Encoder Premium arbejdsprocessen** ved hjælp af **.NET**finder du i [Avanceret kodning med Media Encoder Premium arbejdsproces](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Ændre eksisterende arbejdsprocesser

Standard [publiceret arbejdsprocesser](media-services-workflow-designer.md#existing_workflows) kan redigeres ved hjælp af værktøjet Forespørgselsdesigner. Du kan få standard arbejdsproces filer [her](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Mappen indeholder også en beskrivelse af disse filer.

I de følgende videoer viser, hvordan du kan bruge designer.

###<a name="day-1--getting-started"></a>Dag 1 – Introduktion

Dag 1 video omhandler:

- Forespørgselsdesigner oversigt
- Grundlæggende arbejdsprocesser – "Hej verden"
- Oprette flere output MP4-filer til brug sammen med Azure Media Services streaming

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###<a name="day-2"></a>Dag 2

Dag 2 video omhandler:

- Varierende kilde fil scenarier – håndtering af lyd
- Arbejdsprocesser med avancerede logik
- Graph faser

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###<a name="day-3"></a>Dag 3

Dag 3 video omhandler:

- Scripting inde i arbejdsprocesser/perspektivplaner
- Begrænsninger i forbindelse med den aktuelle Encoder
- Q & A
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]


## <a name="next-step"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


Hvis du har brug for support eller har spørgsmål om oprettelse af brugerdefinerede arbejdsprocesser i værktøjet Workflow designer, bedes du sende mail til mepd@microsoft.com.

##<a name="see-also"></a>Se også

[Azure Premium Encoder Workflow Designer-videoer](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)
