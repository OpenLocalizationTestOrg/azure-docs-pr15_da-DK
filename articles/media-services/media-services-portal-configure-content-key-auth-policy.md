<properties 
    pageTitle="Konfigurere indhold nøgle politik ved hjælp af portalen Azure | Microsoft Azure" 
    description="Lær, hvordan du konfigurerer en politik for en indhold nøgle." 
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



#<a name="configure-content-key-authorization-policy"></a>Konfigurere indhold vigtige politik
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##<a name="overview"></a>Oversigt

Microsoft Azure Media Services gør det muligt at levere MPEG-STREG, bløde Streaming og HTTP Live Streaming (HLS) streams, der er beskyttet med avancerede kryptering AES (Standard) (ved hjælp af 128-bit krypteringsnøgler) eller [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS gør det også muligt at levere TANKESTREG streams krypteret med Widevine DRM. Både PlayReady og Widevine krypteres per specifikationen almindelige kryptering (ISO/IEC 23001-7 CENC).

Media Services indeholder også en **Tjeneste til levering af tasten/licens** , som kan kunder få AES-nøgler eller PlayReady/Widevine licenser til at afspille den krypterede indhold.

Dette emne viser, hvordan bruge Azure-portalen til at konfigurere politikken indhold vigtige godkendelse. Tasten kan senere bruges til at kryptere dynamisk indhold. Bemærk, at aktuelt du kan kryptere følgende streaming formater: HLS, MPEG TANKESTREG og jævn Streaming. Du kan ikke kryptere HD'ER streaming format, eller progressive henter.

Når en afspiller anmoder om en stream, der er angivet til krypteres dynamisk, bruger Media Services tasten konfigureret til at kryptere dynamisk indholdet ved hjælp af AES eller DRM kryptering. Hvis du vil dekryptere strømmen, afspilleren anmoder om tasten fra de vigtigste leveringstjenesten. For at afgøre, om brugeren har tilladelse til at hente nøglen, evaluerer tjenesten de politikker, der tilladelse, du har angivet for nøglen.


Hvis du planlægger at har flere indhold taster eller vil angive en URL-adresse til en **Tjeneste til levering af tasten/licens** end den vigtige leveringstjenesten Media Services, kan du bruge Media Services .NET SDK eller REST API'er.

[Konfigurere indhold nøgle godkendelse politik ved hjælp af Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)

[Konfigurere indhold nøgle godkendelse politik ved hjælp af Media Services REST-API](media-services-rest-configure-content-key-auth-policy.md)

###<a name="some-considerations-apply"></a>Nogle overvejelser gælder:

- Hvis du vil kunne bruge dynamiske emballagen og dynamiske kryptering, skal du sikre dig at have mindst én streaming reserveret enhed. Du kan finde flere oplysninger, se, [hvordan skalere en medier-tjeneste](media-services-portal-manage-streaming-endpoints.md).
- Din aktiv skal indeholde et sæt af tilpassede bithastighed MP4s eller tilpasset bithastighed udglattede Streaming filer. Du kan finde flere oplysninger [kodes et aktiv](media-services-encode-asset.md).
- Tjenesten nøgle gemmer ContentKeyAuthorizationPolicy og dens relaterede objekter (indstillinger for politik og begrænsninger) i 15 minutter.  Hvis du opretter en ContentKeyAuthorizationPolicy og angive, at en "Token" begrænsning, test, og derefter opdatere politikken "Åbn" begrænsning, tager det omkring 15 minutter, før politikken skifter til den "Åbn" version af politikken.


##<a name="how-to-configure-the-key-authorization-policy"></a>Sådan: Konfigurere politikken vigtige godkendelse

Vælg den **INDHOLDSBESKYTTELSE** side for at konfigurere politikken vigtige godkendelse.

Media Services understøtter flere måder at godkendelse af brugere, der foretage vigtige anmodninger. Politikken indhold vigtige tilladelse kan have **åbne**, **token**eller begrænsninger i **IP-** godkendelse (**IP** kan konfigureres med RESTEN eller .NET SDK).

###<a name="open-restriction"></a>Åbn begrænsning

**Åbn** begrænsningen betyder, at systemet vil køre tasten for alle, der gør en anmodning om vigtige. Denne begrænsning kan være nyttige til testformål.

![Systemkald][open_policy]

###<a name="token-restriction"></a>Token begrænsning

Tryk på knappen **TOKEN** for at vælge token begrænset politikken.

Politikken **token** begrænsede skal være ledsaget af et token, der er udstedt af en **Secure Token Service** (STS). Media Services understøtter tokens i formatet **Enkel Web Tokens** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) og formatet **JSON Web Token** (JWT). Du kan finde oplysninger [JWT token godkendelse](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services giver ikke **Secure Token Services**. Du kan oprette en brugerdefineret STS eller udnytte Microsoft Azure ACS til problem tokens. STS skal konfigureres til at oprette et token, der er signeret med de angivne nøgle og problem krav, du har angivet i konfigurationen token begrænsning. Tjenesten Media Services vigtige levering returnerer krypteringsnøglen til klienten, hvis Tokenet er gyldig og krav i tokenet til dem, der er konfigureret til tasten indhold. Du kan finde yderligere oplysninger finder [Brug Azure ACS til problem tokens](http://mingfeiy.com/acs-with-key-services).

Når du konfigurerer **TOKEN** begrænsede politik, skal du angive værdier for **bekræftelse nøgle**, **udsteder** og **Målgruppe**. Tasten primære bekræftelse indeholder nøglen, som er blevet signeret tokenet med, udsteder er det sikkert token tjeneste, som problemer tokenet. Deltagerne (også kaldet omfang) i denne artikel beskrives formålet med tokenet eller ressourcen tokenet tillader adgang til. Tjenesten Media Services vigtige levering valideret, at disse værdier i tokenet matcher værdierne i skabelonen.

###<a name="playready"></a>PlayReady

Når du beskytter dit indhold med **PlayReady**, er en af de ting, du skal angive i din politik en XML-streng, der definerer skabelonen PlayReady licens. Følgende politik er angivet som standard:

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <LicenseType>Nonpersistent</LicenseType>
          <PlayRight>
            <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
          </PlayRight>
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

Du kan klikke på knappen **importere politik xml** og give en anden XML, som er i overensstemmelse med XML-skemaet defineret [her](https://msdn.microsoft.com/library/azure/dn783459.aspx).


##<a name="next-step"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

