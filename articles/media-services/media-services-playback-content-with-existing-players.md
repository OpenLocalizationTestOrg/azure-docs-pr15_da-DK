<properties 
    pageTitle="Afspil dit indhold | Microsoft Azure" 
    description="I dette emne beskrives eksisterende spillere, du kan bruge til afspilning af dit indhold." 
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
    ms.date="10/12/2016" 
    ms.author="juliako"/>


#<a name="playing-your-content-with-existing-players"></a>Afspille dit indhold med eksisterende spillere

Azure Media Services understøtter mange populære streaming formater, som udglattede Streaming, HTTP Live Streaming og MPEG-streg. Dette emne får du henvisninger til eksisterende spillere, som du kan bruge til at teste din streams.

>[AZURE.NOTE]Afspil dynamisk pakket eller dynamisk krypteret indhold, skal du sørge for at få mindst én streaming enhed for streaming slutpunktet, som du planlægger at levere dit indhold. Finde oplysninger om skalering streaming enheder: [Sådan skalere streaming enheder](media-services-portal-manage-streaming-endpoints.md).

### <a name="the-azure-portal-media-services-content-player"></a>Azure portalen Media Services indhold afspilleren

**Azure** -portalen en indhold afspiller, som du kan bruge til at teste din video.

Klik på den ønskede video (Sørg for, at den er blevet [publiceret](media-services-portal-publish.md)), og klik på knappen **Afspil** i bunden af portalen.

Nogle overvejelser gælder:

- **MEDIA SERVICES indhold PLAYER** afspilles fra standard streaming slutpunkt. Hvis du vil afspille fra et ikke-standard streaming slutpunkt, skal du bruge en anden player. For eksempel [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![AMSPlayer][AMSPlayer]

###<a name="azure-media-player"></a>Azure Media Player

Brug [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) til afspilning af dit indhold (Ryd eller beskyttet) i en af følgende formater:

- Problemfri Streaming
- MPEG BINDESTREG
- HLS
- Gradvist MP4


###<a name="flash-player"></a>Flash Player

####<a name="aes-encrypted-with-token"></a>AES-krypteret med Token

[http://aestoken.azurewebsites.NET](http://aestoken.azurewebsites.net)

###<a name="silverlight-players"></a>Silverlight-afspillere

####<a name="monitoring"></a>Overvågning

[http://smf.cloudapp.NET/healthmonitor](http://smf.cloudapp.net/healthmonitor)

####<a name="playready-with-token"></a>PlayReady med Token

[http://sltoken.azurewebsites.NET](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>TANKESTREG afspillere

[http://dashplayer.azurewebsites.NET](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###<a name="other"></a>Andre

Teste HLS URL-adresser, du kan også bruge:

- **Safari** på en iOS-enhed eller
- **3ivx HLS Player** i Windows.

##<a name="developing-video-players"></a>Udvikling af video-afspillere

Oplysninger om, hvordan du udvikle dine egne spillere finder du [udvikle video afspillere](media-services-develop-video-players.md)




##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
