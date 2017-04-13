<properties 
    pageTitle="Konfigurere Telestream Wirecast encoder for at sende en enkelt bithastighed live stream | Microsoft Azure" 
    description="Dette emne viser, hvordan du konfigurerer Wirecast direkte encoder for at sende en enkelt bithastighed stream til AMS kanaler, der er aktiveret til direkte kodning. " 
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

#<a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Brug Wirecast encoder til at sende en enkelt bithastighed live stream

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [Frit direkte](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Dette emne viser, hvordan du konfigurerer [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) direkte encoder for at sende en enkelt bithastighed stream til AMS kanaler, der er aktiveret til direkte kodning.  Få mere at vide under [arbejde med kanaler, der er aktiveret til at udføre Live kodning med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Dette selvstudium viser, hvordan du administrerer Azure Media Services (AMS) med værktøjet Azure Media Services Explorer (AMSE). Dette værktøj kan kun køre på Windows-PC. Hvis du er på Mac eller Linux, kan du bruge Azure portal til at oprette [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) og [programmer](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).


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

![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Angiv et navn, beskrivelsesfelt er valgfrit. Under Kanalindstillinger, Vælg **Standard** for indstillingen Live kodning med Input-protokollen, der er angivet til **RTMP**. Du kan lade alle andre indstillinger, som den er.


Kontrollér, at **starte den nye kanal nu** er markeret.

3. Klik på **Opret kanal**.
![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE] Kanalen kan tage op til 20 minutter om at starte.

Mens kanalen starter kan du [konfigurere encoder](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

>[AZURE.IMPORTANT] Bemærk, at fakturering starter, så snart kanal er i en klar. Du kan finde yderligere oplysninger finder [kanalen tilstand](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_wirecast_rtmp></a>Konfigurere Telestream Wirecast encoder

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

1. Åbn programmet Telestream Wirecast på den computer, der bruges, og Konfigurer til RTMP streaming.
2. Konfigurere output ved at gå til fanen **Output** og vælge **Outputindstillinger …**.
    
    Sørg for, at **Output Destination** er indstillet til **RTMP Server**.
3. Klik på **OK**.
4. Angive **destinationsfeltet være **Azure Media Services**** på indstillingssiden.
 
    Kodning profilen er valgt på forhånd til **Azure H.264 720 p 16:9 (1280 x 720)**. Vælg tandhjulsikonet til højre for slip ned for at tilpasse disse indstillinger, og derefter vælge **Ny forudindstillede**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. Konfigurere encoder forudindstillinger.

    Navngiv på forudindstillet, og kontrollere, om der følgende anbefalede indstillinger:

    **Video**
    
    - Encoder: MainConcept H.264
    - Rammer sekundet: 30
    - Gennemsnitlig bit rente: 5000 kbits/sec (kan tilpasses baseret på netværksbegrænsninger)
    - Profil: hoved
    - Vigtige ramme hver: 60 rammer

    **Lyd**

    - Target bit rente: 192 kbits/sec
    - Eksempel rente: 44.100 kHz
     
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. Tryk på **Gem**.

    Feltet kodning har nu den nyoprettede profil, der kan vælges. 

    Kontrollér, at den nye profil er markeret.

7. Få den kanal input URL-adresse for at tildele den til Wirecast **RTMP slutpunkt**.
    
    Gå tilbage til værktøjet AMSE, og se, om statusen for fuldførelse kanal. Når tilstanden er ændret fra **Start** til **kører**, kan du få input URL-adressen.
      
    Når kanalen kører, højreklik på kanalnavnet på skal gå ned for at holde markøren over **Kopi Input URL-adresse til Udklipsholder** og derefter vælge **Primære Input URL-adresse**.  
    
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. Indsæt oplysningerne i feltet **adresse** i afsnittet output i vinduet Wirecast **Outputindstillinger** , og tildele et stream navn. 


    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. Klik på **OK**.

10. Bekræft inputkilder til video og lyd er klar og trykke på **Stream** i øverste venstre hjørne på hovedskærmen i **Wirecast** .

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT]Før du klikker på **Stream**, sikre du **skal** , at kanalen, der er klar. 
>Du skal også sørge for ikke at lade kanalen i tilstanden klar uden et input bidrag feed i mere end > 15 minutter.

##<a name="test-playback"></a>Test afspilning
  
1. Gå til værktøjet AMSE, og højreklik på kanalen, der skal kontrolleres. Hold markøren over **afspilning af eksemplet** i menuen, og vælg **med Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Hvis strømmen vises i afspilleren, er klik derefter på encoder konfigureret korrekt for at oprette forbindelse til AMS. 

Hvis fejlen er modtaget, kanalen skal nulstilles og encoder indstillinger tilpasses. Se emnet [fejlfinding](media-services-troubleshooting-live-streaming.md) for at få vejledning.  

##<a name="create-a-program"></a>Oprette et program

1. Når kanal afspilning er bekræftet, kan du oprette et program. Højreklik på inden for området program under fanen **Live** i værktøjet AMSE, og vælg **Opret nyt Program**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

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
