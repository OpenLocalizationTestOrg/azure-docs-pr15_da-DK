<properties
    pageTitle=" Introduktion til levering af indhold efter behov ved hjælp af portalen Azure | Microsoft Azure"
    description="Dette selvstudium vejleder dig gennem trinnene til implementering af en grundlæggende Video-on-Demand (VoD) levering af indhold tjeneste med Azure Media Services (AMS) program ved hjælp af portalen Azure."
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
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Introduktion til levering af indhold efter behov ved hjælp af portalen Azure

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Dette selvstudium vejleder dig gennem trinnene til implementering af en grundlæggende Video-on-Demand (VoD) levering af indhold tjeneste med Azure Media Services (AMS) program ved hjælp af portalen Azure.

> [AZURE.NOTE] For at fuldføre dette selvstudium skal have du en Azure konto. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). 

Dette selvstudium indeholder følgende opgaver:

1.  Opret en Azure Media Services-konto.
2.  Konfigurer streaming slutpunkt.
1.  Overføre en videofil.
1.  Kod kildefilen i et sæt tilpasset bithastighed MP4 filer.
1.  Udgive aktiv og få streaming og løbende overførsel URL-adresser.  
1.  Afspil dit indhold.


## <a name="create-an-azure-media-services-account"></a>Oprette en Azure Media Services-konto

Trinnene i dette afsnit viser, hvordan du opretter en AMS-konto.

1. Log på på [Azure-portalen](https://portal.azure.com/).
2. Klik på **+ Ny** > **Web + Mobile** > **Media Services**.

    ![Oprette Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Angiv obligatoriske værdier i **Opret MEDIA SERVICES konto** .

    ![Oprette Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Skriv navnet på den nye AMS-konto i **Kontonavn**. Et kontonavn Media Services er alle små bogstaver tal eller bogstaver, som ikke indeholder mellemrum, og er 3-24 tegn.
    2. Vælg mellem de forskellige Azure abonnementer, du har adgang til-abonnement.
    
    2. Vælg den nye eller eksisterende ressource i **Ressourcegruppe**.  En ressourcegruppe er en samling af ressourcer, der deler livscyklus, tilladelser og politikker. Få mere at vide [her](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. På den **placering**bruges Vælg geografisk område til at gemme de medie og metadata poster til kontoen Media Services. Dette område bruges til at behandle og streame medier. Kun de tilgængelige Media Services områder vises på rullelisten. 
    
    3. I **Lagerplads konto**, skal du vælge en lagerplads konto til at levere blob-lager af medieindhold fra kontoen Media Services. Du kan vælge en eksisterende lagerplads konto i det samme geografiske område som kontoen Media Services, eller du kan oprette en lagerplads konto. Der oprettes en ny konto lagerplads i samme område. Regler for lagerplads kontonavne er den samme som for Media Services konti.

        Lær mere om lagerplads [her](storage-introduction.md).

    4. Vælg **Fastgør til dashboard** for at se status for konto-installationen.
    
7. Klik på **Opret** nederst i formularen.

    Når kontoen er blevet oprettet, ændres status til at **køre**. 

    ![Indstillinger for Media Services](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Til at administrere kontoen AMS (for eksempel overføre videoer, kode aktiver, overvåge statussen job) bruge vinduet **Indstillinger** .

## <a name="manage-keys"></a>Administrere nøgler

Du har brug for kontonavnet og det primære vigtige oplysninger til automatisk at få adgang til kontoen Media Services.

1. Vælg din konto på Azure-portalen. 

    Vinduet **Indstillinger** vises til højre. 

2. Vælg **taster**i vinduet **Indstillinger** . 

    **Administrer nøgler** windows Viser kontonavnet og tasterne primære og sekundære vises. 
3. Tryk på knappen Kopiér til at kopiere værdierne.
    
    ![Media Services taster](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="configure-streaming-endpoints"></a>Konfigurere streaming slutpunkter

Når du arbejder med Azure Media Services en af de mest almindelige scenarier levere video via tilpasset bithastighed streaming på dine kunder. Media Services understøtter følgende tilpasset bithastigheden streaming teknologier: HTTP Live Streaming (HLS), bløde Streaming, MPEG TANKESTREG og HD'ER (for kun Adobe PrimeTime/adgang til indehavere).

Media Services indeholder dynamiske emballagen, som du kan bruge til at fremvise din tilpassede bithastighed MP4-kodet indhold i streaming formater, der understøttes af Media Services (MPEG TANKESTREG, HLS, bløde Streaming, HD'ER) just-in-time, uden at du gemme allerede pakkede versioner af hver af disse streaming formater.

For at kunne udnytte dynamiske emballagen, skal du gøre følgende:

- Kod filen mezzanine (kilde) i et sæt tilpasset bithastighed MP4-filer (kodning trinnene vises senere i dette selvstudium).  
- Oprette mindst én streaming enhed til den *streaming slutpunkt* , som du planlægger at levering af dit indhold. Nedenstående trin viser, hvordan du kan ændre antallet af streaming enheder.

Med dynamisk emballagen, du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format og Media Services opbygger og fungerer relevante svaret baseret på anmodninger fra en klient.

For at oprette og ændre antallet af streaming reserveret enheder skal du gøre følgende:


1. Klik på **Streaming slutpunkter**i vinduet **Indstillinger** . 

2. Klik på standard streaming slutpunkt. 

    Vinduet **Standard STREAMING SLUTPUNKT oplysninger** vises.

3. Skubbe skyderen **Streaming enheder** for at angive antallet streaming enheder.

    ![Streaming enheder](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Klik på knappen **Gem** for at gemme ændringerne.

    >[AZURE.NOTE]Tildeling af en hvilken som helst nye enheder kan tage op til 20 minutter at gennemføre.

## <a name="upload-files"></a>Overføre filer

Til stream videoer ved hjælp af Azure Media Services, du har brug at overføre kilde videoer, kode dem til flere bitrates og publicere resultatet. Det første trin er beskrevet i dette afsnit. 

1. Klik på **Aktiver**i vinduet **indstilling** .

    ![Overføre filer](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. Klik på knappen **Overfør** .

    Vinduet **Overfør en video aktiv** vises.

    >[AZURE.NOTE] Der er ingen fil størrelse begrænsninger.
    
4. Gå til den ønskede video på din computer, markere den, og klik på OK.  

    Upload starter, og du kan se fremdriften under filnavnet.  

Når overførslen er fuldført, kan du se det nye aktiv, vises i vinduet **Aktiver** . 

## <a name="encode-assets"></a>Kode Aktiver

Når du arbejder med Azure Media Services en af de mest almindelige scenarier leverer tilpasset bithastighed streaming til dine kunder. Media Services understøtter følgende tilpasset bithastigheden streaming teknologier: HTTP Live Streaming (HLS), bløde Streaming, MPEG TANKESTREG og HD'ER (for kun Adobe PrimeTime/adgang til indehavere). For at forberede dine videoer til tilpasset bithastighed streaming, skal du kode videoen kilde i flere bithastighed filer. Du skal bruge **Media Encoder Standard** encoder til at kode videoerne.  

Media Services indeholder også dynamisk emballagen, som du kan bruge til at fremvise din multi-bithastighed MP4s i følgende streaming formater: MPEG TANKESTREG, HLS, bløde Streaming eller HD'ER, uden at du behøver at pakke til disse streaming formater. Med dynamisk emballagen, du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format og Media Services opbygger og fungerer relevante svaret baseret på anmodninger fra en klient.

For at kunne udnytte dynamiske emballagen, skal du gøre følgende:

- Kod kildefilen til et sæt af multi-bithastighed MP4-filer (kodning trinnene vist senere i dette emne).
- Få mindst én streaming enhed for streaming slutpunktet, som du planlægger at levering af dit indhold. Yderligere oplysninger, du [konfigurerer streaming slutpunkter](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

### <a name="to-use-the-portal-to-encode"></a>Bruge portalen til at kode

I dette afsnit beskrives de trin, du kan bruge til at kode indholdet med Media Encoder Standard.

1.  Vælg **Aktiver**i vinduet **Indstillinger** .  
2.  Vælg den aktiv, som du gerne vil kode i vinduet **Aktiver** .
3.  Tryk på knappen **kodes** .
4.  Vælg den "Media Encoder almindelige" processor og en forudindstillet i vinduet **kodes et aktiv** . Eksempelvis hvis du kender din Skriv video har en opløsning på 1920 x 1080 pixel, du kan bruge den "H264 flere bithastighed 1080p" forudindstillede. Du kan finde flere oplysninger om forudindstillinger skal se [i denne](https://msdn.microsoft.com/library/azure/mt269960.aspx) artikel – er det vigtigt at vælge det format, der passer bedst til dit video input. Hvis du har en lav opløsning (640 x 360) video og derefter du skal ikke bruge standard "H264 flere bithastighed 1080p" forudindstillede.
    
    For lettere at administrere har du en mulighed for at redigere navnet på output aktiv, og navnet på jobbet.
        
    ![Kode Aktiver](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Tryk på **Opret**.

### <a name="monitor-encoding-job-progress"></a>Overvåge statussen for kodning job

Overvåge forløbet af kodning jobbet, klik på **Indstillinger** (øverst på siden), og vælg derefter **job**.

![Job](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Udgive indhold

For at give din bruger med en URL-adresse, der kan bruges til at streame eller hente dit indhold, skal du først "udgive" din aktiv ved at oprette en locator. Locators giver adgang til filer, der findes i aktivet. Media Services understøtter to typer locators: 

- Streaming (OnDemandOrigin) locators, bruges til tilpasset streaming (for eksempel til stream MPEG TANKESTREG, HLS eller udglattet Streaming). Hvis du vil oprette en streaming locator dine aktiver skal indeholde en .ism fil. 
- Gradvist (SAS) locators, bruges til levering af video via løbende overførsel.


En streaming URL-adresse har følgende format, og du kan bruge til at afspille udglattede Streaming aktiver.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

For at opbygge en HLS streaming URL-adresse skal du føje (format = m3u8 aapl) til URL-adressen.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

For at opbygge en MPEG TANKESTREG streaming URL-adresse skal du føje (format = mpd-klokkeslæt-csf) til URL-adressen.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


En SAS URL-adresse har følgende format.

    {blob container name}/{asset name}/{file name}/{SAS signature}

>[AZURE.NOTE] Hvis du har brugt på portalen til at oprette locators før marts 2015, blev locators med en udløbsdato for to år oprettet.  

For at opdatere en udløbsdato på en locator, skal du bruge [RESTEN](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) eller [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API'er. Når du opdaterer udløbsdatoen for en SAS locator, ændres URL-adressen.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Bruge portalen til at publicere et aktiv

Bruge portalen til at publicere et aktiv skal du gøre følgende:

1. Vælg **Indstillinger** > **Aktiver**.
1. Vælg den aktiv, du vil publicere.
1. Klik på knappen **Publicer** .
1. Vælg URL-typen.
2. Tryk på **Tilføj**.

    ![Publicere](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL-adressen er føjet til listen over **Publiceret URL-adresser**.

## <a name="play-content-from-the-portal"></a>Afspille indhold fra portalen

Azure-portalen indeholder en indhold afspiller, som du kan bruge til at teste din video.

Klik på den ønskede video, og klik derefter på knappen **Afspil** .

![Publicere](./media/media-services-portal-vod-get-started/media-services-play.png)

Nogle overvejelser gælder:

- Kontrollér, at videoen er blevet publiceret.
- Denne **medieafspiller** afspilles fra standard streaming slutpunkt. Hvis du vil afspille fra et ikke-standard streaming slutpunkt, skal du kopiere URL-adressen og bruge en anden player. For eksempel [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

##<a name="next-steps"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


