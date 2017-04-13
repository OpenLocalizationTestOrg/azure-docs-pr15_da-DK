<properties 
    pageTitle="Sådan gør du direkte streaming bruge Azure Media Services til at oprette flere bithastighed streams ved hjælp af Azure portal | Microsoft Azure" 
    description="Dette selvstudium vejleder dig gennem trinnene til at oprette en kanal, der modtager en enkelt bithastighed live stream og koder til flere bithastighed stream ved hjælp af portalen Azure." 
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
    ms.topic="get-started-article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-the-azure-portal"></a>Sådan gør du direkte streaming bruge Azure Media Services til at oprette flere bithastighed streams ved hjælp af Azure portal

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST-API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

Dette selvstudium vejleder dig gennem trinnene til at oprette en **kanal** , der modtager en enkelt bithastighed live stream og koder til flere bithastighed stream.

>[AZURE.NOTE]Se [Live streaming ved hjælp af Azure Media Services for at oprette flere bithastighed streams](media-services-manage-live-encoder-enabled-channels.md)yderligere grundlæggende oplysninger, der er relateret til kanaler, der er aktiveret til direkte kodning.

##<a name="common-live-streaming-scenario"></a>Almindelige direkte Streaming scenarie

Følgende er generelle trin til oprettelse af almindelige direkte streaming programmer.

>[AZURE.NOTE] I øjeblikket, er den maksimale anbefalede varighed for en direkte begivenhed otte timer. Kontakt amslived på Microsoft.com, hvis du vil køre en kanal i længere tid.

1. Oprette forbindelse et videokamera til en computer. Start og konfigurere en lokal direkte koder, der kan udskrive en enkelt bithastighed stream i en af følgende protokoller: RTMP, bløde Streaming eller RTP (MPEG-TS). Se [Azure Media Services RTMP Support og Live kodere](http://go.microsoft.com/fwlink/?LinkId=532824)kan finde flere oplysninger.
    
    Dette trin kan også foretages, når du har oprettet din kanal.

1. Oprette og starte en kanal. 

1. Hent kanalen indtager URL-adresse. 

    Ingest URL-adresse bruges af live encoder til at sende strømmen til kanalen.
1. Hente URL-adressen med kanal preview. 

    Brug denne URL-adresse til at kontrollere, at din kanal korrekt modtager direkte strømmen.

3. Oprette en begivenhed/program (som du kan også oprette et aktiv). 
1. Publicere hændelsen (der oprettes en anmodet arbejdsproces locator for den tilknyttede aktiv).  

    Sørg for at have mindst én streaming reserveret enhed på streaming slutpunktet hvorfra du vil at streame indhold.
1. Starte hændelsen, når du er klar til at starte streaming og arkivering.
2. Du kan også direkte encoder kan have en angivelse af at starte en annonce. Annoncen indsættes i output strømmen.
1. Stoppe begivenheden, når du vil stoppe streaming og arkivering begivenheden.
1. Slette begivenheden (og du kan også slette aktivet).   

##<a name="in-this-tutorial"></a>I dette selvstudium

I dette selvstudium bruges portalen Azure til at udføre følgende opgaver: 

2.  Konfigurere streaming slutpunkter.
3.  Oprette en kanal, der er aktiveret til at udføre direkte kodning.
1.  Få indtager URL-adressen for at angive den for at live encoder. Direkte encoder vil bruge denne URL-adresse til indtager strømmen til kanalen. .
1.  Oprette en begivenhed/program (og et aktiv)
1.  Publicere aktivet og få streaming URL-adresser  
1.  Afspille dit indhold 
2.  Rydde op

##<a name="prerequisites"></a>Forudsætninger

Følgende er påkrævet til at fuldføre selvstudiet.

- For at fuldføre dette selvstudium skal have du en Azure konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
- En Media Services-konto. Hvis du vil oprette en Media Services-konto, skal du se [Oprette konto](media-services-portal-create-account.md).
- Et webcam og en koder, der kan sende en enkelt bithastighed live stream.

##<a name="configure-streaming-endpoints"></a>Konfigurere streaming slutpunkter 

Media Services indeholder dynamiske emballagen, som du kan bruge til at fremvise din multi-bithastighed MP4s i følgende streaming formater: MPEG TANKESTREG, HLS, bløde Streaming eller HD'ER, uden at du kan igen pakkes i disse streaming formater. Med dynamisk emballagen du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format og Media Services, opbygge og fungere relevante svaret baseret på anmodninger fra en klient.

For at kunne udnytte dynamiske emballagen, skal du hente mindst én streaming enhed for streaming slutpunktet, som du planlægger at levering af dit indhold.  

For at oprette og ændre antallet af streaming reserveret enheder skal du gøre følgende:

1. Log på på [Azure-portalen](https://portal.azure.com/) , og Vælg kontoen AMS.
1. Klik på **Streaming slutpunkter**i vinduet **Indstillinger** . 

2. Klik på standard streaming slutpunkt. 

    Vinduet **Standard STREAMING SLUTPUNKT oplysninger** vises.

3. Skubbe skyderen **Streaming enheder** for at angive antallet streaming enheder.

    ![Streaming enheder](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-streaming-units.png)

4. Klik på knappen **Gem** for at gemme ændringerne.

    >[AZURE.NOTE]Tildeling af en hvilken som helst nye enheder kan tage op til 20 minutter at gennemføre.

##<a name="create-a-channel"></a>Oprette en kanal

1. Vælg Media Services på [Azure-portalen](https://portal.azure.com/), og klik derefter på på dit kontonavn Media Services.
2. Vælg **Live Streaming**.
3. Vælg **brugerdefineret oprette**. Denne indstilling, kan du oprette en kanal, der er aktiveret til direkte kodning.

    ![Oprette en kanal](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
    
4. Klik på **Indstillinger**.
    
    1.  Vælg typen **Live kodning** kanal. Denne type angiver, at du vil oprette en kanal, der er aktiveret til direkte kodning. Det betyder, at indgående enkelt bithastighed stream sendes til kanalen, og kodet i en multi-bithastighed stream ved hjælp af angivne direkte encoder indstillinger. Se [Live streaming ved hjælp af Azure Media Services for at oprette flere bithastighed streams](media-services-manage-live-encoder-enabled-channels.md)kan finde flere oplysninger. Klik på OK.
    2. Angiv navn, en kanal.
    3. Klik på OK nederst på skærmen.
    
5. Vælg fanen **Ingest** .

    1. På denne side, kan du vælge en streaming protokol. Type **Live kodning** kanal er gyldig protokolindstillinger:
        
        - Enkelt bithastighed Fragmented MP4 (udglattede Streaming)
        - Enkelt bithastighed RTMP
        - RTP (MPEG-TS): MPEG-2 transportstream over RTP.
        
        Detaljeret forklaring om hver protokol, skal du se [Live streaming ved hjælp af Azure Media Services for at oprette flere bithastighed streams](media-services-manage-live-encoder-enabled-channels.md).
    
        Du kan ikke ændre indstillingen protocol mens kanalen eller dens tilknyttede begivenheder/programmer kører. Hvis du kræver forskellige protokoller, skal du oprette separate kanaler for hver streaming protokol.  

    2. Du kan anvende IP-begrænsning på ingest. 
    
        Du kan angive de IP-adresser, der har tilladelse til at indtager en video til denne kanal. Tilladte IP-adresser kan angives som en enkelt IP-adresse (f.eks. ' 10.0.0.1'), en IP-område, ved hjælp af en IP-adresse og en CIDR undernetmaske (fx ' 10.0.0.1/22') eller en IP-område, ved hjælp af en IP-adresse og en stiplet decimal undernetmaske (fx ' 10.0.0.1(255.255.252.0)').

        Hvis ingen IP-adresser er angivet, og der findes ingen regel definition tillades ingen IP-adresse. Oprette en regel for at tillade en hvilken som helst IP-adresse, og Angiv 0.0.0.0/0.

6. Anvende IP-begrænsning på eksempel på fanen **Vis udskrift** .
7. Angiv den kodning forudindstillet under fanen **kodning** . 

    På nuværende tidspunkt kun systemet forudindstillet, kan du vælge er **standard 720 p**. Åbn en Microsoft-supportbilletter for at angive et brugerdefineret format. Indtast navnet på det format, der er oprettet for dig. 

>[AZURE.NOTE] I øjeblikket kan kanal start tage op til 30 minutter. Kanal Nulstil kan tage op til 5 minutter.

Når du har oprettet kanalen, kan du klikke på kanalen og vælge **Indstillinger** , hvor du kan få vist dine kanaler konfigurationer. 

Se [Live streaming ved hjælp af Azure Media Services for at oprette flere bithastighed streams](media-services-manage-live-encoder-enabled-channels.md)kan finde flere oplysninger.


##<a name="get-ingest-urls"></a>Hent indtager URL-adresser

Når kanalen, der er oprettet, kan du få indtager URL-adresser, du vil give direkte encoder. Encoder bruger disse URL-adresser til at skrive en live stream.

![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##<a name="create-and-manage-events"></a>Oprette og administrere begivenheder

###<a name="overview"></a>Oversigt

En kanal er knyttet til begivenheder/programmer, der gør det muligt at styre publicering og opbevaring af målgrupper i en live stream. Kanaler administrere begivenheder/programmer. Kanal og Program relationen minder meget om traditionelle medier, hvor en kanal har en konstant strøm af indhold og et program, er fastsat til nogle tidsindstillede begivenhed på denne kanal.

Du kan angive antallet timer, du vil bevare det registrerede indhold for begivenheden ved at definere længden **Arkiv-vinduet** . Denne værdi kan angives fra et minimum af 5 minutter til maksimalt 25 timer. Arkiv vinduet længde bestemmer også den maksimale mængde tid klienter kan søge efter tilbage i tiden fra den aktuelle direkte position. Begivenheder kan køre over det angivne antal timer, men indhold, der falder bag vinduet længden kasseres løbende. Denne værdi af denne egenskab også bestemmer, hvor lang tid klient manifester kan vokse.

Hver enkelt hændelse er knyttet til et aktiv. Hvis du vil publicere begivenheden skal du oprette en anmodet arbejdsproces locator for den tilknyttede aktiv. Har du denne locator gør det muligt at opbygge en streaming URL-adresse, som du kan give dine kunder.

En kanal understøtter op til tre samtidig, der kører begivenheder, så du kan oprette flere Arkiver af samme indgående strømmen. Dette giver dig mulighed at udgive og arkivere forskellige dele af en begivenhed, efter behov. Din forretningsbehov er for eksempel at arkivere 6 timer for en begivenhed, men at transmittere kun sidste 10 minutter. Hvis du vil gøre dette, skal du oprette to samtidigt kørende begivenheden. En begivenhed er indstillet til at arkivere 6 timer for begivenheden, men programmet ikke er blevet publiceret. Anden hændelsen er indstillet til at arkivere til 10 minutter, og dette program er blevet publiceret.

Du skal ikke genbruge eksisterende programmer for nye begivenheder. I stedet, oprette og starte et nyt program for hver enkelt hændelse.

Starte en begivenhed/program, når du er klar til at starte streaming og arkivering. Stoppe begivenheden, når du vil stoppe streaming og arkivering begivenheden. 

Hvis du vil slette arkiveret indhold, stoppe og slette begivenheden og slet derefter den tilknyttede aktiv. Et aktiv kan ikke slettes, hvis de anvendes hændelsen; begivenheden skal slettes først. 

Selvom du stoppe og slette begivenheden, skal brugerne kunne streame dine arkiveret indhold som en video efter behov, så længe du ikke Slet aktivet.

Hvis du vil bevare det arkiverede indhold, men ikke har den tilgængelig for streaming, kan du slette den streaming locator.

###<a name="createstartstop-events"></a>Oprette/start/stop begivenheder

Når du har strømmen der flyder i kanalen kan du begynde at streaming begivenheden ved at oprette en aktiv, Program og Streaming Locator. Dette vil arkivere strømmen og gøre det tilgængeligt for brugere via Streaming slutpunktet. 

Der er to måder at starte begivenhed: 

1. Siden **Kanalindstillinger** tryk **Live begivenhed** til at tilføje en ny begivenhed.

    Angiv: navn på denne begivenhed, aktiv navn, arkiv vindue og krypteringsindstilling.
    
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
    
    Hvis du ikke udfyldte **Udgiv direkte begivenheden nu** markeret, vil få oprettet hændelsen PUBLICERING URL-adresser.
    
    Du kan trykke på **Start**, når du er klar til at streame begivenheden.

    Når du starter begivenheden, kan du trykke på **værdikontrol** til at begynde afspilning indholdet.

2. Du kan også bruge en genvej og tryk på knappen **Gå direkte** på siden **kanal** . Dette vil oprette en standard aktiv, Program og Streaming Locator.

    Hændelsen hedder **standard** og vinduet arkiv er indstillet til otte timer.

Du kan se den publicerede begivenhed fra siden **Live begivenhed** . 

Hvis du klikker på **Fra luften**, stopper alle direkte begivenheder. 


##<a name="watch-the-event"></a>Se begivenheden

Hvis du vil se begivenheden, skal du klikke på **se** i portalen Azure eller kopiere streaming URL-adressen og bruge en afspiller efter eget valg. 
 
![Oprettet](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Direkte begivenhed konverterer automatisk begivenheder til efter behov indhold, når ikke længere.

##<a name="clean-up"></a>Rydde op

Hvis du er færdig med streaming begivenheder og vil rydde op i de ressourcer, der er klargjort tidligere, skal du følge følgende procedure.

- Stop med at trykke på strømmen fra encoder.
- Stop kanalen. Når kanalen er standset, vil det ikke betale eventuelle gebyrer. Når du har brug at starte det igen, kan det have samme indtager URL-adresse, så du ikke behøver at omkonfigurere dine encoder.
- Du kan stoppe din Streaming slutpunkt, medmindre du vil fortsætte med at angive arkiv med live begivenheden som en stream efter behov. Hvis kanalen er stoppet midlertidigt, vil det ikke betale eventuelle gebyrer.
  
##<a name="view-archived-content"></a>Få vist arkiveret indhold

Selvom du stoppe og slette begivenheden, skal brugerne kunne streame dine arkiveret indhold som en video efter behov, så længe du ikke Slet aktivet. Et aktiv kan ikke slettes, hvis den bruges af en begivenhed begivenheden skal slettes først. 

Vælg **indstilling** for at administrere dine aktiver, og klik på **Aktiver**.

![Aktiver](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

##<a name="considerations"></a>Overvejelser i forbindelse med

- I øjeblikket, er den maksimale anbefalede varighed for en direkte begivenhed otte timer. Kontakt amslived på Microsoft.com, hvis du vil køre en kanal i længere tid.
- Sørg for at have mindst én streaming reserveret enhed på streaming slutpunktet hvorfra du vil at streame indhold.


##<a name="next-step"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
