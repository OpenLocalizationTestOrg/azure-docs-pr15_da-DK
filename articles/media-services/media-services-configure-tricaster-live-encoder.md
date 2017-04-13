<properties 
    pageTitle="Konfigurere NewTek TriCaster encoder for at sende en enkelt bithastighed live stream | Microsoft Azure" 
    description="Dette emne viser, hvordan du konfigurerer Tricaster direkte encoder for at sende en enkelt bithastighed stream til AMS kanaler, der er aktiveret til direkte kodning." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="juliako;cenkd;anilmur"/>

#<a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Brug NewTek TriCaster encoder til at sende en enkelt bithastighed live stream

> [AZURE.SELECTOR]
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Frit direkte](media-services-configure-elemental-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Dette emne viser, hvordan du konfigurerer [Til NewTek TriCaster](http://newtek.com/products/tricaster-40.html) direkte encoder for at sende en enkelt bithastighed stream til AMS kanaler, der er aktiveret til direkte kodning. Få mere at vide under [arbejde med kanaler, der er aktiveret til at udføre Live kodning med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Dette selvstudium viser, hvordan du administrerer Azure Media Services (AMS) med værktøjet Azure Media Services Explorer (AMSE). Dette værktøj kan kun køre på Windows-PC. Hvis du er på Mac eller Linux, kan du bruge Azure portal til at oprette [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) og [programmer](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

>[AZURE.NOTE]Når du bruger Tricaster til at sende i et feed til AMS kanaler, der er aktiveret til direkte kodning bidrag, kan der være video-eller-fejl i direkte begivenheden Hvis du bruger visse funktioner i Tricaster, som hurtig skærer mellem feeds eller skifte til/fra slates. AMS teamet arbejder på at løse disse problemer, indtil da, er det ikke anbefales at bruge disse funktioner.


##<a name="prerequisites"></a>Forudsætninger

- [Oprette en Azure Media Services-konto](media-services-portal-create-account.md)
- Sikre, at der er et Streaming slutpunkt, der kører med mindst én streaming enhed, der er allokeret. Kan finde flere oplysninger under [Administrere Streaming slutpunkter på en Media Services-konto](media-services-portal-manage-streaming-endpoints.md)
- Installere den nyeste version af værktøjet [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
- Start værktøjet og oprette forbindelse til kontoen AMS.

##<a name="tips"></a>Tip

- Når det er muligt, kan du bruge en kabelforbundet Internetforbindelse.
- En god tommelfingerregel ved bestemmelse af krav til båndbredde på er ved at dobbeltklikke på streaming bitrates. Mens det ikke er et obligatorisk krav, kan reducere virkning på overbelastning af netværket.
- Når ved hjælp af software baseret på kodere, og Luk alle unødvendige programmer.

## <a name="create-a-channel"></a>Oprette en kanal

1.  Gå til fanen **Live** i værktøjet AMSE, og højreklik inden for området kanal. Vælg **Opret kanal...** i menuen.

![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Angiv et navn, beskrivelsesfelt er valgfrit. Under Kanalindstillinger, Vælg **Standard** for indstillingen Live kodning med Input-protokollen, der er angivet til **RTMP**. Du kan lade alle andre indstillinger, som den er.


Kontrollér, at **starte den nye kanal nu** er markeret.

3. Klik på **Opret kanal**.
![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

>[AZURE.NOTE] Kanalen kan tage op til 20 minutter om at starte.


Mens kanalen starter kan du [konfigurere encoder](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

>[AZURE.IMPORTANT] Bemærk, at fakturering starter, så snart kanal er i en klar. Du kan finde yderligere oplysninger finder [kanalen tilstand](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_tricaster_rtmp></a>Konfigurere NewTek TriCaster encoder

I dette selvstudium bruges følgende output indstillinger. I resten af dette afsnit beskrives konfigurationstrinnene mere detaljeret. 

**Video**:
 
- Codec: H.264 
- Profil: Høj (niveau 4.0) 
- Bithastighed: 5000 kbps 
- Nøglebillede: to sekunder (60 sekunder) 
- Ramme rente: 30
 
**Lyd**:

- Codec: AAC (LC) 
- Bithastighed: 192 kbps 
- Eksempel rente: 44.1 kHz


###<a name="configuration-steps"></a>Trin til konfiguration

1. Oprette et nyt projekt **Til NewTek TriCaster** afhængigt af hvilken video inputkilden bruges. 
2. Find knappen **Stream** én gang inden for projektet, og klik på tandhjulsikonet ud for den for at få adgang til menuen stream konfiguration.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Når menuen har åbnet, skal du klikke på **Ny** under overskriften forbindelse. Når du bliver bedt om forbindelsestypen, kan du vælge **Adobe Flash**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)

4. Klik på **OK**.

5. En FMLE profil kan nu importeres ved at klikke på pil ned under **Streaming profil** og navigere til **Gennemse**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)

6. Gå til det sted, hvor konfigureret FMLE profil blev gemt.
7. Markere det, og tryk på **OK**.

    Når profilen, der er overført, kan du fortsætte til næste trin.

6. Få den kanal input URL-adresse for at tildele den til Tricaster **RTMP slutpunkt**.
    
    Gå tilbage til værktøjet AMSE, og se, om statusen for fuldførelse kanal. Når tilstanden er ændret fra **Start** til **kører**, kan du få input URL-adressen.
      
    Når kanalen kører, højreklik på kanalnavnet på skal gå ned for at holde markøren over **Kopi Input URL-adresse til Udklipsholder** og derefter vælge **Primære Input URL-adresse**.  
    
    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)

7. Indsæt oplysningerne i feltet **placering** under **Flash Server** i Tricaster projektet. Også tildele et stream navn i feltet **Stream-ID** . 

    Hvis oplysninger om stream blev føjet til FMLE profilen, kan det også importeres til denne sektion ved at klikke på **Indstillinger for profilimport**, navigere til den gemte FMLE profil og klikke på **OK**. De relevante felter i Flash Server skal udfylde med oplysninger fra FMLE.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)

9. Klik på **OK** nederst på skærmen, når du er færdig. Når video og lyd input i Tricaster er klar, kan du begynde at streaming til AMS ved at klikke på knappen **Stream** .

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

>[AZURE.IMPORTANT]Før du klikker på **Stream**, sikre du **skal** , at kanalen, der er klar. 
>Du skal også sørge for ikke at lade kanalen i tilstanden klar uden et input bidrag feed i mere end > 15 minutter. 

##<a name="test-playback"></a>Test afspilning
  
1. Gå til værktøjet AMSE, og højreklik på kanalen, der skal kontrolleres. Hold markøren over **afspilning af eksemplet** i menuen, og vælg **med Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Hvis strømmen vises i afspilleren, er klik derefter på encoder konfigureret korrekt for at oprette forbindelse til AMS. 

Hvis fejlen er modtaget, kanalen skal nulstilles og encoder indstillinger tilpasses. Se emnet [fejlfinding](media-services-troubleshooting-live-streaming.md) for at få vejledning.  

##<a name="create-a-program"></a>Oprette et program

1. Når kanal afspilning er bekræftet, kan du oprette et program. Højreklik på inden for området program under fanen **Live** i værktøjet AMSE, og vælg **Opret nyt Program**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)

2. Navngiv programmet, og hvis det er nødvendigt, kan du justere **Arkiv vinduet længden** (som standard er 4 timer). Du kan også angive en lagerplacering eller forlade som standard.  
3. Markér feltet **starter programmet nu** .
4. Klik på **Opret Program**.  
  
    Bemærk: Oprettelse af programmet kræver mindre tid end oprettelse af kanal.    
 
5. Når programmet kører, Bekræft afspilning ved at højreklikke på program og navigere til **afspilning af det eller programmer** og derefter vælge **med Azure Media Player**.  
6. Når bekræftet, højreklik på programmet igen og vælge **Kopiér Output URL-adressen til Udklipsholder** (eller hente disse oplysninger fra indstillingen **programoplysninger og indstillinger** i menuen). 

Strømmen er nu klar til at være integreret i en afspiller, eller distribueret til et publikum til direkte visning.  


## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

Se emnet [fejlfinding](media-services-troubleshooting-live-streaming.md) for at få vejledning. 


##<a name="next-step"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
