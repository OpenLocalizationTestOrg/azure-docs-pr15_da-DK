<properties 
    pageTitle="Brug af castLabs til at levere Widevine licenser til Azure Media Services | Microsoft Azure" 
    description="I denne artikel beskrives, hvordan du kan bruge Azure Media Services (AMS) til at levere en stream, krypteres dynamisk ved AMS med både PlayReady og Widevine DRMs. PlayReady licens stammer fra Media Services PlayReady licensserveren og Widevine licens er leveret af castLabs licensserver." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="Mingfeiy;willzhan;Juliako"/>


#<a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Brug af castLabs til at levere Widevine licenser til Azure Media Services

> [AZURE.SELECTOR]
- [Axinom](media-services-axinom-integration.md)
- [castLabs](media-services-castlabs-integration.md)

##<a name="overview"></a>Oversigt

I denne artikel beskrives, hvordan du kan bruge Azure Media Services (AMS) til at levere en stream, krypteres dynamisk ved AMS med både PlayReady og Widevine DRMs. PlayReady licens stammer fra Media Services PlayReady licensserveren og Widevine licens er leveret af **castLabs** licensserver.

Du kan bruge [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)til afspilning af streaming indhold, der er beskyttet af CENC (PlayReady og/eller Widevine). Du kan finde oplysninger i [AMP dokument](http://amp.azure.net/libs/amp/latest/docs/) .

I det følgende diagram viser en overordnet Azure Media Services og arkitektur for integration med castLabs.

![Integration](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##<a name="typical-system-set-up"></a>Typisk system konfigurere

- Medieindhold er gemt i AMS.
- Tasten id'er af indhold taster er gemt i både castLabs og AMS.
- castLabs og AMS har token godkendelse, der er indbygget i. I følgende afsnit beskrives godkendelsestokens. 
- Når en klient-anmodninger til stream videoen indholdet dynamisk krypteres med **Almindelige kryptering** (CENC) og dynamisk pakket ved AMS til jævn Streaming og STREG. Vi leverer også PlayReady M2TS grundlæggende stream kryptering for HLS streaming protocol.
- PlayReady licens er hentet fra AMS licensserveren og Widevine licens er hentet fra castLabs licensserveren. 
- Media Player bestemmer automatisk, hvilke licens til at hente baseret på klient platform egenskaben. 

##<a name="authentication-token-generation-for-getting-a-license"></a>Godkendelse token generering af til at få en licens

Både castLabs og AMS understøtter JWT (JSON Web Token) token format, der bruges til at tillade en licens. 

###<a name="jwt-token-in-ams"></a>JWT token i AMS 

I følgende tabel beskrives JWT token i AMS. 

Udsteder|Udsteder streng fra valgt Service STS (Secure Token)
---|---
Målgruppen|Målgruppen streng fra de anvendte STS
Krav|Et sæt af krav
NotBefore|Start gyldighed tokenet
Udløber|Afslut gyldighed tokenet
SigningCredentials|Tasten, der deles af PlayReady licensserveren, castLabs licensserveren og STS, det kan være enten symmetriske eller asymmetrisk nøgle.

###<a name="jwt-token-in-castlabs"></a>JWT token i castLabs

I følgende tabel beskrives JWT token i castLabs. 

Navn|Beskrivelse
---|---
optData|JSON streng, der indeholder oplysninger om dig. 
CRT|JSON streng, der indeholder oplysninger om aktivet, dens oplysninger og afspille licensrettigheder.
IAT|Den aktuelle dato og klokkeslæt i epoke.
jti|Et entydigt id om dette token (hver token kan kun bruges én gang i castLabs system).

##<a name="sample-solution-set-up"></a>Eksempel løsning konfigurere 

[Eksempel løsning](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) består af to projekter:

-   En console-app, der kan bruges til at angive DRM begrænsninger på et allerede oralt aktiv til både PlayReady og Widevine.
-   Et webprogram, der hænder ud tokens, som kan ses som en meget forenklet version af en STS.


Bruge console programmet:

1.  Ændre app.config for at konfigurere AMS legitimationsoplysninger, castLabs legitimationsoplysninger, STS konfiguration og delte nøgle.
2.  Overføre et aktiv til AMS.
3.  Få UUID overførte aktivet og ændre linje 32 i filen Program.cs:

         var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.  Bruge en emneid til at navngive aktivet i castLabs systemet (linje 44 i filen Program.cs).

    Du skal indstille emneid for **castLabs**; det skal være en entydig alfanumerisk streng.

5.  Køre programmet.


For at bruge Web Application (STS):

1.  Ændre web.config til konfiguration af castlabs forhandler-ID, STS konfigurationen og den delte nøgle.
2.  Installer Azure websteder.
3.  Gå til webstedet.

##<a name="playing-back-a-video"></a>Afspille en video tilbage

Du kan bruge [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)til at afspille en video, der er krypteret med almindelige kryptering (PlayReady og/eller Widevine). Når du kører console-app, echoed indhold nøgle-ID og manifestet URL-adressen.

1.  Åbn en ny fane, og Start din STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2.  Gå til [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3.  Indsæt streaming URL-adressen.
4.  Klik på afkrydsningsfeltet **Avancerede indstillinger** .
5.  Vælg PlayReady og/eller Widevine i rullemenuen **beskyttelse** .
6.  Indsæt det token, du har fået fra din STS i tekstfeltet Token. 
    
    Licensserveren castLab behøver ikke den "bæreren =" præfiks foran tokenet. Så skal du fjerne, før du sender tokenet.
7.  Opdatere afspilleren.
8.  Videoen skal afspilles.


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
