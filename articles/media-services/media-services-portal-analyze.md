<properties
    pageTitle="Analysere dine medier ved hjælp af portalen Azure | Microsoft Azure"
    description="Dette emne beskrives, hvordan til at behandle dine medier med medier Analytics medier processorer (MPs) ved hjælp af portalen Azure."
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


# <a name="analyze-your-media-using-the-azure-portal"></a>Analysere dine medier ved hjælp af portalen Azure

> [AZURE.NOTE] For at fuldføre dette selvstudium skal have du en Azure konto. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="overview"></a>Oversigt

Azure Media Services Analytics er en samling af tale og syn komponenter (i enterprise-skalering, overholdelse af regler, sikkerhed og global rækkevidde), som gør det nemmere for organisationer og virksomheder at udlede handlings indsigt fra deres videofiler. Du kan finde mere detaljeret overblik over Azure Media Services Analytics [dette](media-services-analytics-overview.md) emne. 

Dette emne beskrives, hvordan til at behandle dine medier med medier Analytics medier processorer (MPs) ved hjælp af portalen Azure. Medier Analytics MPs landbrugsprodukter MP4 filer eller JSON-filer. Hvis en medier processor produceret en MP4-fil, kan du gradvist hente filen. Hvis en medier processor produceret en JSON-fil, kan du hente filen fra Azure blob-lager. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Vælg et aktiv, du vil analysere 
 
1. Vælg kontoen Azure Media Services på [Azure-portalen](https://portal.azure.com/).
2. Vælg **Aktiver**i vinduet **Indstillinger** .  
.
    ![Analysere videoer](./media/media-services-portal-analyze/media-services-portal-analyze001.png)

2. Vælg den aktiv, du vil analysere, og tryk på knappen **analyser** .
        
    ![Analysere videoer](./media/media-services-portal-analyze/media-services-portal-analyze002.png)

3. Vælg processoren i **processen medier aktiv med medier Analytics** -vinduet. 

    Resten af i artiklen forklares, hvorfor og hvordan du bruger hver processor. 
   
4. Tryk på **Opret** til starten et job.

## <a name="azure-media-indexer"></a>Azure Media indekseringsprogram

**Azure Media indekseringsprogram** medier processor gør det muligt at foretage mediefiler og indhold søgbar samt generere lukket undertekster numre. Dette afsnit giver nogle oplysninger om indstillinger, som du kan angive for denne MP.

![Analysere videoer](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Sprog

Naturligt sprog til genkendes i multimediefilen. Engelsk eller spansk. 

### <a name="captions"></a>Billedtekster

Du kan vælge et billedtekst format, der skal oprettes fra dit indhold. Et indeksering job kan generere undertekster filer i følgende formater:  

- **samisk**
- **TTML**
- **WebVTT**

Lukket billedtekst (CC) filer i disse formater kan bruges til at foretage lyd- og videofiler tilgængelige for personer med nedsat handicap.

### <a name="aib-file"></a>AIB fil

Vælg denne indstilling, hvis du vil have til at generere lyd indeks Blob-fil til brug sammen med brugerdefinerede SQL Server IFilter. Du kan finde yderligere oplysninger finder [denne](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blog.

### <a name="keywords"></a>Nøgleord

Vælg denne indstilling, hvis du vil have til at oprette en nøgleord XML-fil. Denne fil indeholder nøgleord ud fra indholdet tale med frekvens og forskydning oplysninger.

### <a name="job-name"></a>Jobbet navn

Et fuldt navn, hvor du kan identificere jobbet. [I denne](media-services-portal-check-job-progress.md) artikel beskrives, hvordan du kan overvåge statussen for en sag. 

### <a name="output-file"></a>Outputfil

Et fuldt navn, hvor du kan identificere output indholdet. 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse

Azure Media Hyperlapse er en MP, der opretter jævn tid udløb videoer fra første person eller handling kamera indhold.  Se [dette](media-services-hyperlapse-content.md) emne kan finde flere oplysninger. Dette afsnit giver nogle oplysninger om indstillinger, som du kan angive for denne MP.

![Analysere videoer](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Hastighed 

Angive hastigheden, som at øge hastigheden af input videoen. Output er en konstante og tid udløb gengivelse af input video.

### <a name="job-name"></a>Jobbet navn

Et fuldt navn, hvor du kan identificere jobbet. [I denne](media-services-portal-check-job-progress.md) artikel beskrives, hvordan du kan overvåge statussen for en sag. 

### <a name="output-file"></a>Outputfil

Et fuldt navn, hvor du kan identificere output indholdet. 

## <a name="azure-media-face-detector"></a>Azure Media ansigt registrering ressourcer

**Azure Media ansigt registrering ressourcer** medier processor (MP) gør det muligt at tælle, spore bevægelser og endda måle publikum deltage og reaktionsprodukter via ansigtsudtryk. Denne tjeneste indeholder to funktioner: 

- **Registrering af ansigt**

    Ansigt registrering finder og registrerer human flader i en video. Flere flader kan findes og efterfølgende spores, når de flyttes rundt, med de klokkeslæt og sted metadata, der returneres i en JSON-fil. Under registrering forsøge det at give et ensartet ID til den samme ansigt, mens personen, der bevæge dig rundt på skærmen, selvom de er blokeres eller forlade kortvarigt rammen.

    >[AZURE.NOTE]Denne services udfører ikke facial talegenkendelse. En person, der forlader rammen eller bliver blokeres længe får et nyt ID når de vender tilbage.

- **Følelser registrering**
    
    Følelser registrering er en valgfri komponent i ansigt registrering medier Processor, der returnerer analyse på flere følelsesmæssige attributter fra de flader registreres, herunder lykke, sadness, frygt, anger og meget mere. 

![Analysere videoer](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Tilstanden registrering

En af følgende tilstande kan bruges med processor:

- registrering af ansigt
- per ansigt følelse registrering
- Sammenlæg følelse registrering

### <a name="job-name"></a>Jobbet navn

Et fuldt navn, hvor du kan identificere jobbet. [I denne](media-services-portal-check-job-progress.md) artikel beskrives, hvordan du kan overvåge statussen for en sag. 

### <a name="output-file"></a>Outputfil

Et fuldt navn, hvor du kan identificere output indholdet. 

## <a name="azure-media-motion-detector"></a>Azure Media Bevægelsesdetektor

**Azure Media Bevægelsesdetektor** medier processor (MP) gør det muligt at identificere effektivt sektioner af interesse i en anden måde lange og uneventful video. Bevægelsessti registrering kan bruges på statisk kamera optagelser til at identificere sektioner af videoen, hvor bevægelsessti opstår. Den genererer en JSON-fil, der indeholder en metadata med tidsstempler og den omgivende område, hvor hændelsen indtraf.

Rettet mod sikkerhed video feeds, er denne teknologi i stand til at kategorisere bevægelse i relevante begivenheder og falsk positive såsom skygger og lyssætning ændringer. Her kan du oprette sikkerhedsadvarsler fra kamera feeds uden plejer med uendelig irrelevante begivenheder, at bruge samtidig kan udtrække tid af interesse fra meget lang overvågning videoer.

![Analysere videoer](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video miniaturer

Denne processor kan hjælpe dig med at oprette oversigter over lange videoer ved automatisk at vælge interessante kodestykker fra kilde videoen. Dette er nyttigt, når du vil give et hurtigt overblik over, hvad du kan forvente i en lang video. Detaljerede oplysninger og eksempler under [Brug Azure Media Video miniaturer til at oprette en Video opsummering](media-services-video-summarization.md)

![Analysere videoer](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Jobbet navn

Et fuldt navn, hvor du kan identificere jobbet. [I denne](media-services-portal-check-job-progress.md) artikel beskrives, hvordan du kan overvåge statussen for en sag. 

### <a name="output-file"></a>Outputfil

Et fuldt navn, hvor du kan identificere output indholdet. 


##<a name="next-steps"></a>Næste trin

Få vist Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


