<properties 
    pageTitle="Konfigurere FMLE encoder for at sende en enkelt bithastighed live stream | Microsoft Azure" 
    description="Dette emne viser, hvordan du konfigurerer Flash Media Live Encoder (FMLE) encoder for at sende en enkelt bithastighed stream til AMS kanaler, der er aktiveret til direkte kodning." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako;cenkdin;anilmur"/>

#<a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Brug FMLE encoder til at sende en enkelt bithastighed live stream

> [AZURE.SELECTOR]
- [FMLE](media-services-configure-fmle-live-encoder.md)
- [Frit direkte](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)

Dette emne viser, hvordan du konfigurerer [Flash Media Live Encoder](http://www.adobe.com/products/flash-media-encoder.html) (FMLE) encoder for at sende en enkelt bithastighed stream til AMS kanaler, der er aktiveret til direkte kodning. Få mere at vide under [arbejde med kanaler, der er aktiveret til at udføre Live kodning med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Dette selvstudium viser, hvordan du administrerer Azure Media Services (AMS) med værktøjet Azure Media Services Explorer (AMSE). Dette værktøj kan kun køre på Windows-PC. Hvis du er på Mac eller Linux, kan du bruge Azure portal til at oprette [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) og [programmer](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

Bemærk, at dette selvstudium beskriver ved hjælp af AAC. Dog understøtter ikke FMLE AAC som standard. Du har brug at købe et plug-in til AAC kodning sådanne fra MainConcept: [AAC-plug-in](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

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

![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Angiv et navn, beskrivelsesfelt er valgfrit. Under Kanalindstillinger, Vælg **Standard** for indstillingen Live kodning med Input-protokollen, der er angivet til **RTMP**. Du kan lade alle andre indstillinger, som den er.


Kontrollér, at **starte den nye kanal nu** er markeret.

3. Klik på **Opret kanal**.
![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

>[AZURE.NOTE] Kanalen kan tage op til 20 minutter om at starte.


Mens kanalen starter kan du [konfigurere encoder](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

>[AZURE.IMPORTANT] Bemærk, at fakturering starter, så snart kanal er i en klar. Du kan finde yderligere oplysninger finder [kanalen tilstand](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_fmle_rtmp></a>Konfigurere FMLE encoder

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

1. Gå til den Flash Media Live Encoder's (FMLE) sproggrænseflade på den computer, der bruges.

    Grænsefladen er én primære side med indstillinger. Skal du lægge mærke til følgende anbefalede indstillinger for at komme i gang med streaming ved hjælp af FMLE.
    
    - Format: H.264 ramme rente: 30,00 
    - Indtast størrelse: 1280 x 720 
    - Bit rente: 5000 Kbps (kan tilpasses baseret på netværksbegrænsninger)  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

    Når ved hjælp af interlaced kilder, skal du markering indstillingen "Fjern skærminterlacing"

2. Vælg skruenøgleikonet ud for Format, skal være disse yderligere indstillinger:

    - Profil: hoved
    - Niveau: 4.0
    - Keyframe frekvens: to sekunder 
    
    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)

3. Angive følgende vigtige lyd:
    
    - Format: AAC 
    - Eksempel rente: 44100 Hz
    - Bithastighed: 192 Kbps
    
    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)

6. Få den kanal input URL-adresse for at tildele den til den FMLE **RTMP slutpunkt**.
    
    Gå tilbage til værktøjet AMSE, og se, om statusen for fuldførelse kanal. Når tilstanden er ændret fra **Start** til **kører**, kan du få input URL-adressen.
      
    Når kanalen kører, højreklik på kanalnavnet på skal gå ned for at holde markøren over **Kopi Input URL-adresse til Udklipsholder** og derefter vælge **Primære Input URL-adresse**.  
    
    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)

7. Indsæt oplysningerne i feltet **FMS URL-adresse** i afsnittet output, og tildele et stream navn. 

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Gentag disse trin med sekundær Input URL-adressen for ekstra redundans.
8. Vælge **Opret forbindelse**.

>[AZURE.IMPORTANT]Før du klikker på **Opret forbindelse**, sikre du **skal** , at kanalen, der er klar. 
>Du skal også sørge for ikke at lade kanalen i tilstanden klar uden et input bidrag feed i mere end > 15 minutter.

##<a name="test-playback"></a>Test afspilning
  
1. Gå til værktøjet AMSE, og højreklik på kanalen, der skal kontrolleres. Hold markøren over **afspilning af eksemplet** i menuen, og vælg **med Azure Media Player**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Hvis strømmen vises i afspilleren, er klik derefter på encoder konfigureret korrekt for at oprette forbindelse til AMS. 

Hvis fejlen er modtaget, kanalen skal nulstilles og encoder indstillinger tilpasses. Se emnet [fejlfinding](media-services-troubleshooting-live-streaming.md) for at få vejledning.  

##<a name="create-a-program"></a>Oprette et program

1. Når kanal afspilning er bekræftet, kan du oprette et program. Højreklik på inden for området program under fanen **Live** i værktøjet AMSE, og vælg **Opret nyt Program**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)

2. Navngiv programmet, og hvis det er nødvendigt, kan du justere **Arkiv vinduet længden** (som standard er 4 timer). Du kan også angive en lagerplacering eller forlade som standard.  
3. Markér feltet **starter programmet nu** .
4. Klik på **Opret Program**.  
  
    Bemærk: Oprettelse af programmet kræver mindre tid end oprettelse af kanal.    
 
5. Når programmet kører, Bekræft afspilning ved at højreklikke på program og navigere til **afspilning af det eller programmer** og derefter vælge **med Azure Media Player**.  
6. Når bekræftet, højreklik på programmet igen og vælge **Kopiér Output URL-adressen til Udklipsholder** (eller hente disse oplysninger fra indstillingen **programoplysninger og indstillinger** i menuen). 

Strømmen er nu klar til at være integreret i en afspiller, eller distribueret til et publikum til direkte visning.  


## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

Se emnet [fejlfinding](media-services-troubleshooting-live-streaming.md) for at få vejledning. 


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
