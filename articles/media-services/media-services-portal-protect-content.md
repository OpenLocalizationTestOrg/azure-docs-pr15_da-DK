<properties 
    pageTitle="Konfiguration af indholdsbeskyttelse politikker ved hjælp af portalen Azure | Microsoft Azure" 
    description="I denne artikel viser, hvordan du bruger portalen Azure til at konfigurere politikker for indholdsbeskyttelse. I artiklen viser også, hvordan du aktiverer dynamisk kryptering for dine aktiver." 
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

# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Konfiguration af indholdsbeskyttelse politikker ved hjælp af portalen Azure

> [AZURE.NOTE] For at fuldføre dette selvstudium skal have du en Azure konto. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="overview"></a>Oversigt

Microsoft Azure Media Services (AMS) gør det muligt at sikre dine medier fra når den forlader computeren via lagerplads, behandling og levering. Media Services gør det muligt at levere dit indhold krypteret dynamisk med avancerede kryptering AES (Standard) (ved hjælp af 128-bit krypteringsnøgler), almindelige kryptering (CENC) ved hjælp af PlayReady og/eller Widevine DRM og Apple FairPlay. 

AMS indeholder en tjeneste til at levere DRM licenser og AES fjerne taster til autoriserede klienter. Portalen Azure gør det muligt at oprette en **nøgle/licens politik** for alle de typer af kryptering af administration.

Denne artikel beskrives, hvordan du konfigurerer indholdsbeskyttelse politikker ved hjælp af Azure portal. I artiklen viser også, hvordan du anvender dynamiske kryptering på dine aktiver.

> [AZURE.NOTE]  Hvis du har brugt Azure klassisk portalen til at oprette beskyttelse politikker, vises politikkerne, der muligvis ikke i [Azure-portalen](https://portal.azure.com/). Men alle gammelt politikker findes stadig. Kan du undersøge dem via Azure Media Services .NET SDK eller værktøjet [Azure-Media-tjenester-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases) (for at få vist politikkerne, højreklik på aktivet -> Vis oplysninger (F4) -> Klik på fanen indhold taster -> Klik på tasten). 
> 
> Hvis du vil kryptere din aktiv ved hjælp af nye politikker, konfigurere dem ved hjælp af Azure portal, skal du klikke på Gem og genanvende dynamiske kryptering. 

## <a name="start-configuring-content-protection"></a>Start konfiguration af indholdsbeskyttelse

Hvis du vil bruge på portalen til at starte konfiguration af indholdsbeskyttelse, globalt for kontoen AMS, skal du gøre følgende:

1. Vælg kontoen Azure Media Services på [Azure-portalen](https://portal.azure.com/).
2. Vælg **Indstillinger** > **indholdsbeskyttelse**.

![Beskytte indhold](./media/media-services-portal-content-protection/media-services-content-protection001.png)
 

## <a name="keylicense-authorization-policy"></a>Tasten/licens politik

AMS understøtter flere måder at godkendelse af brugere, der foretage nøgle eller licens anmodninger. Politikken indhold vigtige godkendelse skal konfigureres ved du og opfyldes af kunden i rækkefølge for den nøgle/licens til at være delived til klienten. Politikken indhold vigtige tilladelse kan har en eller flere godkendelse begrænsninger: **åbne** eller **token** begrænsning.

Portalen Azure gør det muligt at oprette en **nøgle/licens politik** for alle de typer af kryptering af administration.

###<a name="open"></a>Åbn 

Åbn begrænsning betyder, at systemet vil køre tasten for alle, der gør en anmodning om vigtige. Denne begrænsning kan være nyttige til testformål. 

### <a name="token"></a>Token

Politikken token begrænset skal være ledsaget af et token, der er udstedt af et Token STS (Secure Service). Media Services understøtter tokens i formatet enkel Web Tokens (SWT) og formatet JSON Web Token (JWT). Media Services giver ikke Secure Token Services. Du kan oprette en brugerdefineret STS eller udnytte Microsoft Azure ACS til problem tokens. STS skal konfigureres til at oprette et token, der er signeret med de angivne nøgle og problem krav, du har angivet i konfigurationen token begrænsning. Tjenesten Media Services vigtige levering returnerer det ønskede nøgle (eller den licens) til klienten Hvis Tokenet er gyldige og krav i token matcher dem, der er konfigureret for nøgle (eller licens).

Når konfigurere tokenet begrænsede politik, skal du angive den primære bekræftelse nøgle, udsteder og målgruppe parametre. Tasten primære bekræftelse indeholder nøglen, som er blevet signeret tokenet med, udsteder er det sikkert token tjeneste, som problemer tokenet. Deltagerne (også kaldet omfang) i denne artikel beskrives formålet med tokenet eller ressourcen tokenet tillader adgang til. Tjenesten Media Services vigtige levering valideret, at disse værdier i tokenet matcher værdierne i skabelonen.

![Beskytte indhold](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>PlayReady rettighedsskabelon

Du kan finde detaljerede oplysninger om PlayReady rettighedsskabelon [Media Services PlayReady licens skabelon oversigt](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>Ikke fast

Hvis du konfigurerer licens som ikke-fast, er den kun havde i hukommelsen, mens afspilleren bruger licensen.  

![Beskytte indhold](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Fast

Hvis du konfigurerer licensen som fast, gemmes det på klienten i fast lagerplads.

![Beskytte indhold](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Widevine rettighedsskabelon

Du kan finde detaljerede oplysninger om Widevine rettighedsskabelon [Widevine licens skabelon oversigt](media-services-widevine-license-template-overview.md).

### <a name="basic"></a>Grundlæggende

Når du vælger **grundlæggende**, oprettes skabelonen med alle standardindstillinger for værdier.

### <a name="advanced"></a>Avanceret

Detaljeret forklaring om avancerede indstilling af Widevine konfigurationer, skal du se [dette](media-services-widevine-license-template-overview.md) emne.

![Beskytte indhold](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay konfiguration

Hvis du vil aktivere FairPlay kryptering, skal du angive App certifikatet og programmet hemmeligt nøgle (Spørg) via indstillingen FairPlay konfiguration. Du kan finde detaljerede oplysninger om FairPlay konfiguration og krav til, [i denne](media-services-protect-hls-with-fairplay.md) artikel.

![Beskytte indhold](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Anvende dynamiske kryptering på dine aktiver

For at kunne udnytte dynamiske kryptering, skal du gøre følgende:

- Kod kildefilen i et sæt tilpasset bithastighed MP4 filer.
- Få mindst én efter behov streaming enhed for streaming slutpunktet, som du planlægger at levere dit indhold. Du kan finde flere oplysninger, se, [hvordan skalere streaming reserveret enheder efter behov](media-services-portal-manage-streaming-endpoints.md).

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Vælg et aktiv, du vil kryptere

Vælg **Indstillinger**for at se alle dine aktiver, > **Aktiver**.

![Beskytte indhold](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Krypter med AES eller DRM

Når du trykker på **Krypter** på et aktiv, får du vist med to indstillinger: **AES** eller **DRM**. 

#### <a name="aes"></a>AES

Ryd kryptering nøgle aktiveres på alle streaming protokoller AES: jævn Streaming, HLS og MPEG-STREG.

![Beskytte indhold](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM

Når du vælger fanen DRM, får du vist med forskellige indstillinger af indholdsbeskyttelse politikker (som du skal have konfigureret på nuværende tidspunkt) + et sæt af streaming protokoller.

- **PlayReady og Widevine med MPEG-STREG** -, dynamisk krypteres din MPEG-STREG værdistrøm med PlayReady og Widevine DRMs.
- **PlayReady og Widevine med MPEG-STREG + FairPlay med HLS** -, dynamisk krypteres du MPEG-STREG værdistrøm med PlayReady og Widevine DRMs. Din HLS streams med FairPlay, også krypteres.
- **PlayReady kun med jævne Streaming, HLS og MPEG-STREG** -, dynamisk krypteres udglattede Streaming, HLS, MPEG-STREG streams med PlayReady DRM.
- **Widevine kun med MPEG-STREG** - kryptere du MPEG-STREG med Widevine DRM dynamisk.
- **FairPlay kun med HLS** -, dynamisk krypteres din HLS stream med FairPlay.

For at aktivere FairPlay kryptering, skal du angive App certifikat og programmet hemmeligt nøgle (Spørg) via indstillingen FairPlay konfiguration af bladet indholdsbeskyttelse indstillinger.

![Beskytte indhold](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Når du foretager kryptering markering, skal du trykke på **Anvend**.

##<a name="next-steps"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





