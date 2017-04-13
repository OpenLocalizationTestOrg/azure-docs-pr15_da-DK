<properties
    pageTitle="  Udgive indhold ved hjælp af Azure portal | Microsoft Azure"
    description="Dette selvstudium vejleder dig gennem trinnene til publicering af dit indhold ved hjælp af Azure portal."
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

# <a name="publish-content-with-the-azure-portal"></a>Udgive indhold ved hjælp af Azure portal

> [AZURE.SELECTOR]
- [Portal](media-services-portal-publish.md)
- [.NET](media-services-deliver-streaming-content.md)
- [RESTEN](media-services-rest-deliver-streaming-content.md)

## <a name="overview"></a>Oversigt

> [AZURE.NOTE] For at fuldføre dette selvstudium skal have du en Azure konto. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). 

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

Du kan finde yderligere oplysninger finder [levere oversigt over celleindhold](media-services-deliver-content-overview.md).

>[AZURE.NOTE] Hvis du har brugt på portalen til at oprette locators før marts 2015, blev locators med en udløbsdato for to år oprettet.  

For at opdatere en udløbsdato på en locator, skal du bruge [RESTEN](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) eller [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API'er. Bemærk, at når du opdaterer udløbsdatoen for en SAS locator, ændres URL-adressen.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Bruge portalen til at publicere et aktiv

Bruge portalen til at publicere et aktiv skal du gøre følgende:

1. Vælg kontoen Azure Media Services på [Azure-portalen](https://portal.azure.com/).
1. Vælg **Indstillinger** > **Aktiver**.
1. Vælg den aktiv, du vil publicere.
1. Klik på knappen **Publicer** .
1. Vælg URL-typen.
2. Tryk på **Tilføj**.

    ![Publicere](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL-adressen, føjes til listen over **Publiceret URL-adresser**.

## <a name="play-content-from-the-portal"></a>Afspille indhold fra portalen

Azure-portalen indeholder en indhold afspiller, som du kan bruge til at teste din video.

Klik på den ønskede video, og klik derefter på knappen **Afspil** .

![Publicere](./media/media-services-portal-vod-get-started/media-services-play.png)

Nogle overvejelser gælder:

- Kontrollér, at videoen er blevet publiceret.
- Denne **medieafspiller** afspilles fra standard streaming slutpunkt. Hvis du vil afspille fra et ikke-standard streaming slutpunkt, skal du kopiere URL-adressen og bruge en anden player. For eksempel [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
- Streaming slutpunktet hvorfra du streamer skal køre.  
- Hvis du vil streame fra et streaming slutpunkt, skal du tilføje mindst én streaming enhed. Se [dette](media-services-portal-scale-streaming-endpoints.md) emne kan finde flere oplysninger.   

##<a name="next-steps"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


