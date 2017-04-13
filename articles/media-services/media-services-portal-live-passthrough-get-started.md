<properties 
    pageTitle="Sådan gør du direkte streaming med lokalt kodere ved hjælp af portalen Azure | Microsoft Azure" 
    description="Dette selvstudium vejleder dig gennem trinnene til at oprette en kanal, der er konfigureret til en pass-through-levering." 
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
    ms.topic="get-started-article"
    ms.date="10/24/2016" 
    ms.author="juliako"/>


#<a name="how-to-perform-live-streaming-with-on-premise-encoders-using-the-azure-portal"></a>Sådan gør du direkte streaming med lokalt kodere ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Portal]( media-services-portal-live-passthrough-get-started.md)
- [.NET]( media-services-dotnet-live-encode-with-onpremises-encoders.md)
- [RESTEN]( https://msdn.microsoft.com/library/azure/dn783458.aspx)

Dette selvstudium vejleder dig gennem trinnene til portalen Azure til at oprette en **kanal** , der er konfigureret til en pass-through-levering. 

##<a name="prerequisites"></a>Forudsætninger

Følgende er påkrævet til at fuldføre selvstudiet:

- En Azure-konto. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). 
- En Media Services-konto. Se, [hvordan du opretter en Media Services-konto](media-services-portal-create-account.md)for at oprette en Media Services-konto.
- Et webcam. For eksempel [Telestream Wirecast encoder](http://www.telestream.net/wirecast/overview.htm).

Det anbefales at gennemgå følgende artikler:

- [Azure Media Services RTMP understøtter og Live kodere](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
- [Oversigt over Live vand ved hjælp af Azure Media Services](media-services-manage-channels-overview.md)
- [Live streaming med lokalt kodere, opretter flere bithastighed streams](media-services-live-streaming-with-onprem-encoders.md)


##<a id="scenario"></a>Almindelige direkte streaming scenarie

I følgende trin beskrives opgaver, der er involveret i oprettelse af almindelige direkte streaming programmer, der bruger kanaler, der er konfigureret til levering af pass-through. Dette selvstudium viser, hvordan du kan oprette og administrere en pass-through-kanal og direkte begivenheder.

1. Oprette forbindelse et videokamera til en computer. Start og konfigurere en lokal direkte koder, der skriver en multi-bithastighed RTMP eller fragmenteret MP4-stream. Se [Azure Media Services RTMP Support og Live kodere](http://go.microsoft.com/fwlink/?LinkId=532824)kan finde flere oplysninger.
    
    Dette trin kan også foretages, når du har oprettet din kanal.

1. Oprette og starte en pass-through-kanal.
1. Hent kanalen indtager URL-adresse. 

    Ingest URL-adresse bruges af live encoder til at sende strømmen til kanalen.
1. Hente URL-adressen med kanal preview. 

    Brug denne URL-adresse til at kontrollere, at din kanal korrekt modtager direkte strømmen.

3. Oprette et live begivenhed/program. 

    Når du bruger portalen Azure, opretter hvordan du opretter en direkte begivenhed også et aktiv. 
      
    >[AZURE.NOTE]Sørg for at have mindst én streaming reserveret enhed på streaming slutpunktet hvorfra du vil at streame indhold.
1. Starte begivenhed/program, når du er klar til at starte streaming og arkivering.
2. Du kan også direkte encoder kan have en angivelse af at starte en annonce. Annoncen indsættes i output strømmen.
1. Stop begivenhed/program, når du vil stoppe streaming og arkivering begivenheden.
1. Slette begivenheden/program (og du kan også slette aktivet).     

>[AZURE.IMPORTANT] Gennemgå [direkte streaming med lokalt kodere, oprette flere bithastighed streams](media-services-live-streaming-with-onprem-encoders.md) til at få mere at vide om begreber og overvejelser, der er relateret til direkte streaming med lokalt kodere og pass-through-kanaler.

##<a name="to-view-notifications-and-errors"></a>Sådan får du vist beskeder og fejl

Hvis du vil have vist meddelelser og fejl, der er oprettet med portalen Azure, klikke på ikonet i meddelelsesområdet.

![Beskeder](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

##<a name="configure-streaming-endpoints"></a>Konfigurere streaming slutpunkter 

Media Services indeholder dynamiske emballagen, som du kan bruge til at fremvise din multi-bithastighed MP4s i følgende streaming formater: MPEG TANKESTREG, HLS, bløde Streaming eller HD'ER, uden at du behøver at pakke til disse streaming formater. Med dynamisk emballagen du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format og Media Services opbygger og fungerer relevante svaret baseret på anmodninger fra en klient.

For at kunne udnytte dynamiske emballagen, skal du hente mindst én streaming enhed for streaming slutpunktet, som du planlægger at levering af dit indhold.  

For at oprette og ændre antallet af streaming reserveret enheder skal du gøre følgende:

1. Log på på [Azure-portalen](https://portal.azure.com/).
1. Klik på **Streaming slutpunkter**i vinduet **Indstillinger** . 

2. Klik på standard streaming slutpunkt. 

    Vinduet **Standard STREAMING SLUTPUNKT oplysninger** vises.

3. Skubbe skyderen **Streaming enheder** for at angive antallet streaming enheder.

    ![Streaming enheder](./media/media-services-portal-passthrough-get-started/media-services-streaming-units.png)

4. Klik på knappen **Gem** for at gemme ændringerne.

    >[AZURE.NOTE]Tildeling af en hvilken som helst nye enheder kan tage op til 20 minutter at gennemføre.
    
##<a name="create-and-start-pass-through-channels-and-events"></a>Oprette og starte pass-through-kanaler og begivenheder

En kanal er knyttet til begivenheder/programmer, der gør det muligt at styre publicering og opbevaring af målgrupper i en live stream. Kanaler administrere begivenheder. 
    
Du kan angive antallet timer, du vil bevare det registrerede indhold for programmet ved at definere længden **Arkiv-vinduet** . Denne værdi kan angives fra et minimum af 5 minutter til maksimalt 25 timer. Arkiv vinduet længde bestemmer også den maksimale mængde tid klienter kan søge efter tilbage i tiden fra den aktuelle direkte position. Begivenheder kan køre over det angivne antal timer, men indhold, der falder bag vinduet længden kasseres løbende. Denne værdi af denne egenskab også bestemmer, hvor lang tid klient manifester kan vokse.

Hver enkelt hændelse er knyttet til et aktiv. Hvis du vil publicere begivenheden, skal du oprette en anmodet arbejdsproces locator for den tilknyttede aktiv. Har du denne locator gør det muligt at opbygge en streaming URL-adresse, som du kan give dine kunder.

En kanal understøtter op til tre samtidig, der kører begivenheder, så du kan oprette flere Arkiver af samme indgående strømmen. Dette giver dig mulighed at udgive og arkivere forskellige dele af en begivenhed, efter behov. Din forretningsbehov er for eksempel at arkivere 6 timer i løbet af et program, men at transmittere kun sidste 10 minutter. Hvis du vil gøre dette, skal du oprette to samtidig programmer. Ét program er indstillet til at arkivere 6 timer for begivenheden, men programmet ikke er blevet publiceret. Andet programmet er indstillet til at arkivere til 10 minutter, og dette program er blevet publiceret.

Du skal ikke genbruge eksisterende direkte begivenheder. I stedet, oprette og starte en ny begivenhed for hver enkelt hændelse.

Starte hændelsen, når du er klar til at starte streaming og arkivering. Stoppe automatisk, hver gang du vil stoppe streaming og arkivering begivenheden. 

Hvis du vil slette arkiveret indhold, stoppe og slette begivenheden og slet derefter den tilknyttede aktiv. Et aktiv kan ikke slettes, hvis den bruges af en begivenhed begivenheden skal slettes først. 

Selvom du stoppe og slette begivenheden, skal brugerne kunne streame dine arkiveret indhold som en video efter behov, så længe du ikke Slet aktivet.

Hvis du vil bevare det arkiverede indhold, men ikke har den tilgængelig for streaming, kan du slette den streaming locator.

###<a name="to-use-the-portal-to-create-a-channel"></a>Bruge portalen til at oprette en kanal 

Dette afsnit viser, hvordan du bruger indstillingen **Hurtig oprettelse** til at oprette en pass-through-kanal.

Du kan finde flere oplysninger om pass-through-kanaler, [Live streaming med lokalt kodere, oprette flere bithastighed streams](media-services-live-streaming-with-onprem-encoders.md).

1. Vælg kontoen Azure Media Services på [Azure-portalen](https://portal.azure.com/).
2. Klik på **Live streaming**i vinduet **Indstillinger** . 

    ![Kom godt i gang](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
    
    Vinduet **direkte streaming** vises.

3. Klik på **Hurtig oprettelse** for at oprette en pass-through-kanal med RTMP indtager protocol.

    Vinduet **Opret en ny kanal** vises.
4. Giv den nye kanal et navn, og klik på **Opret**. 

    Dette opretter en pass-through-kanal med RTMP indtager protocol.

##<a name="create-events"></a>Oprette begivenheder.

1. Vælge en kanal, du vil tilføje en begivenhed.
2. Tryk på knappen **Live begivenhed** .

![Begivenhed](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)


##<a name="get-ingest-urls"></a>Hent indtager URL-adresser

Når kanalen, der er oprettet, kan du få indtager URL-adresser, du vil give direkte encoder. Encoder bruger disse URL-adresser til at skrive en live stream.

![Oprettet](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

##<a name="watch-the-event"></a>Se begivenheden

Hvis du vil se begivenheden, skal du klikke på **se** i portalen Azure eller kopiere streaming URL-adressen og bruge en afspiller efter eget valg. 
 
![Oprettet](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Direkte begivenhed automatisk bliver konverteret til efter behov indhold, når ikke længere.

##<a name="clean-up"></a>Rydde op

Du kan finde flere oplysninger om pass-through-kanaler, [Live streaming med lokalt kodere, oprette flere bithastighed streams](media-services-live-streaming-with-onprem-encoders.md).

- En kanal, kan den standses, kun, når alle begivenheder/programmer på kanalen er blevet stoppet.  Når kanalen er standset, det ikke betale eventuelle gebyrer. Når du har brug at starte det igen, kan det have samme indtager URL-adresse, så du ikke behøver at omkonfigurere dine encoder.
- En kanal kan slettes kun, når alle direkte begivenheder på kanalen er blevet slettet.

##<a name="view-archived-content"></a>Få vist arkiveret indhold

Selvom du stoppe og slette begivenheden, skal brugerne kunne streame dine arkiveret indhold som en video efter behov, så længe du ikke Slet aktivet. Et aktiv kan ikke slettes, hvis den bruges af en begivenhed begivenheden skal slettes først. 

Vælg **indstilling** for at administrere dine aktiver, og klik på **Aktiver**.

![Aktiver](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

##<a name="next-step"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
