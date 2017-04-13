<properties 
    pageTitle="Fejlfinding i forbindelse med vejledning til direkte streaming | Microsoft Azure" 
    description="Dette emne giver forslag til, hvordan du direkte streaming fejlfindingsproblemer." 
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
    ms.date="10/12/2016"  
    ms.author="juliako"/>

#<a name="troubleshooting-guide-for-live-streaming"></a>Fejlfinding i forbindelse med vejledning til direkte streaming

Dette emne får forslag for at foretage fejlfinding af nogle direkte streaming problemer.

## <a name="issues-related-to-on-premises-encoders"></a>Problemer i forbindelse med lokale kodere 

Dette afsnit giver forslag til, hvordan du udfører fejlfinding af problemer i forbindelse med lokale kodere, der er konfigureret til at sende en enkelt bithastighed stream til AMS kanaler, der er aktiveret til direkte kodning.

###<a name="problem-would-like-to-see-logs"></a>Problem: Gerne vil se logge 

- **Potentielle problem**: kan ikke finde encoder logfiler, der kan hjælpe i forbindelse med fejlfinding af problemer.
    
    - **Telestream Wirecast**: kan du normalt finde logfiler under C:\Users\{brugernavn} \AppData\Roaming\Wirecast\ 
    - **Frit Live**: Du kan finde indeholder links til logge på administrationsportalen. Klik på **Statistik**og derefter **logge**. På siden **Logfiler** , får du vist en liste over logfiler for alle elementerne, LiveEvent; Vælg den, der svarer til den aktuelle session. 
    - **Flash Media Live Encoder**: Du kan finde **Log Directory...** ved at gå til fanen **Kodning Log** .
    
###<a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problem: Der er ikke muligt til udskrivning af en løbende stream

- **Potentielle problem**: encoder bruges ikke automatisk deinterlace. 

    **Trin til fejlfinding**: søge efter en udjævning indstilling i grænsefladen encoder. Når deaktivere interlace er aktiveret, kan du kontrollere for gradvist outputindstillinger igen. 
 
###<a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problem: Prøvet flere encoder outputindstillinger og stadig ikke kan oprette forbindelse. 

- **Potentielle problem**: Azure kodning kanal blev ikke nulstillet korrekt. 

    **Trin til fejlfinding**: Sørg for, at encoder ikke længere skubbe til AMS, stoppe og nulstille kanalen. Når kører igen, skal du prøve at slutte din encoder med de nye indstillinger. Hvis dette stadig ikke løser problemet, prøv at oprette en ny kanal helt, nogle gange kanaler kan være beskadiget, når flere mislykkede forsøg på.  

- **Potentielle problem**: feltet GOP størrelse eller indstillinger for primære billede ikke er optimal. 

    **Trin til fejlfinding**: anbefalede GOP størrelse eller keyframe interval er to sekunder. Nogle kodere beregne denne indstilling i antal rammer, mens andre bruger sekunder. For eksempel: Når du skriver 30fps, GOP størrelsen ville være 60 rammer, som svarer til to sekunder.  
     
- **Potentielle problem**: lukket porte blokering af strømmen. 

    **Trin til fejlfinding**: Når streaming via RTMP, kontrollere firewall/proxy eller indstillinger for at bekræfte, at udgående porte 1935 og 1936 er åbne. Når du bruger RTP streaming, Bekræft, at udgående port 2010 er åbent. 


###<a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>Problem: Når du konfigurerer encoder til stream med RTP-protokollen, der er ingen sted til at angive et værtsnavn. 

- **Potentielle problem**: mange RTP kodere Tillad ikke for host navne og en IP-adresse skal hentes.  

    **Trin til fejlfinding**: for at finde IP-adressen skal du åbne en kommandoprompt på enhver computer. Åbn starteren Kør (GEVINST + R) for at gøre dette i Windows, og skriv "cmd" til at åbne.  

    Når kommandoprompten er åben, skal du skrive "Ping [AMS Host Name]". 

    Værtsnavnet kan være afledt ved at udelade portnummeret fra Azure indtager URL-adressen, som er fremhævet i følgende eksempel: 

    RTP://test2-amstest009.RTP.Channel.mediaservices.Windows.NET:2010 / 

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###<a name="problem-unable-to-playback-the-published-stream"></a>Problem: Kan ikke afspille publicerede strømmen.
 
- **Potentielle problem**: der er ingen Streaming slutpunkt, der kører, eller der er ingen streaming enheder (skalaenheder), der allokeres. 

    **Trin til fejlfinding**: gå til fanen "Streaming slutpunkt" i værktøjet AMSE, og Bekræft, at der er et Streaming slutpunkt, der kører med én streaming enhed. 
    


>[AZURE.NOTE] Sende en supportbilletter ved hjælp af portalen Azure Hvis når du har fulgt trinnene til fejlfinding du stadig ikke kan korrekt streame.

##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
