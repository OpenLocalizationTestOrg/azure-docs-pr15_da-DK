<properties 
    pageTitle="Udvikle videoafspiller programmer" 
    description="Emnet indeholder links til Player strukturer og plug-ins, som du kan bruge til at udvikle dine egne klientprogrammer, der kan forbruge streaming-medier fra Media Services." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="develop-video-player-applications"></a>Udvikle videoafspiller programmer

##<a name="overview"></a>Oversigt

Azure Media Services indeholder de værktøjer, du vil oprette avancerede, dynamiske player klientprogrammer for de fleste platforme, herunder: iOS-enheder, Android-enheder, Windows, Windows Phone, Xbox og Set-top felterne. Dette emne indeholder også links til SDK'er og Player strukturer, som du kan bruge til at udvikle dine egne klientprogrammer, der kan forbruge streaming-medier fra Azure Media Services.

##<a name="azure-media-player"></a>Azure Media Player

[Azure Media Player](http://aka.ms/ampinfo) er en web videoafspiller udviklet til at afspille medieindhold fra Microsoft Azure Media Services på en lang række browsere og enheder. Azure Media Player anvender branchestandarder som HTML5, Media kilde udvidelser (MSE) og krypteret medier udvidelser (EME) vil give en højberiget tilpasset streaming oplevelse. Når disse standarder ikke er tilgængelige på en enhed eller i en browser, bruger Azure Media Player Flash og Silverlight som fallback teknologi. Uanset den anvendte afspilning teknologi, har udviklere en samlet JavaScript-grænsefladen til at få adgang til API'er. Dette giver mulighed for indhold served ved Azure Media Services skal afspilles på tværs af en lang række enheder og browsere, der ikke eventuelle ekstra indsats.

Microsoft Azure Media Services giver mulighed for indhold, der skal være lægges med bindestreg, bløde Streaming og HLS streaming formater til afspiller indhold. Azure Media Player tager højde for disse forskellige formater og afspilles automatisk linket bedste baseret på platform/browser-funktioner. Microsoft Azure Media Services kan også til dynamiske kryptering af aktiver med PlayReady kryptering eller AES-128 bit konvolut kryptering. Azure Media Player giver mulighed for dekryptering af PlayReady og AES-128 bit krypteret indhold, når konfigureret korrekt. 

Du kan finde flere oplysninger:

- [Azure Media Player](http://aka.ms/ampinfo)
- [Azure Media Player dokumentation](http://aka.ms/ampdocs) 
- [Azure Media Player komme i gang Blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
- [Tilmeld dig for at holde dig opdateret med seneste fra Azure Media Player](http://aka.ms/ampsignup)
- [Tilføje nye anmode om funktioner, ideer, feedback](http://aka.ms/ampuservoice ) 


##<a name="other-tools-for-creating-player-applications"></a>Andre værktøjer til oprettelse af Player-programmer

Du kan også bruge en af følgende SDK'er:

- [Udjævne Streaming klient-SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Problemfri Streaming Windows Store-App](media-services-build-smooth-streaming-apps.md)
- [Microsoft Media Platform: Player Framework](http://playerframework.codeplex.com/) 
- [HTML5 Player Framework dokumentation](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Microsoft jævne Streaming plug-in til OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Licensering Microsoft® jævn Streaming klient konvertere Kit](http://aka.ms/sspk) 
- [XBOX Video Application Development](http://xbox.create.msdn.com/) 
 

##<a name="advertising"></a>Reklamer

Azure Media Services indeholder support til indsættelse af reklame gennem Windows Media-Platform: Player strukturer. Player strukturer med ad understøttelse er tilgængelige til Windows 8, Silverlight, Windows Phone 8 og iOS-enheder. Hver player framework indeholder eksempelkode, der viser, hvordan du kan implementere et program til afspilning. Der er tre forskellige typer af reklamer, du kan indsætte i dine medier:

Lineær – fuld ramme reklamer, afbryde primære videoen

Ikke-lineære – overlejring reklamer, der vises som den primære videoen afspilles, som regel et logo eller andre statisk billede, der er placeret i afspilleren

Companion – reklamer, der vises uden for afspilleren

Reklamer kan placeres på en hvilken som helst sted i den primære video tidslinje. Du skal kende afspilleren, hvornår der skal afspilles på ad og hvilke reklamer til afspilning. Dette gøres ved hjælp af et sæt standard XML-baserede filer: Video Ad Service-skabelon (VAST), Digital Video flere Ad afspilningsliste (VMAP), skabelon medier abstrakte rækkefølge (MAST) og Digital Video Player Ad Interface Definition (VPAID). STORE filer angive, hvilke reklamer skal vises. VMAP filer angive, hvornår til at afspille forskellige reklamer og indeholder omfattende XML. MAST filer er en anden måde at sekvens reklamer der også kan indeholde store XML. VPAID filer definere en grænseflade mellem videoafspilleren og ad eller ad-server. Du kan finde flere oplysninger, får du vist [Indsættelse af reklamer](https://msdn.microsoft.com/library/dn387398.aspx).

Se oplysninger om lukket for hørehæmmede og reklamer understøttelse i Live streaming videoer, [understøttes undertekster for hørehæmmede og Ad indsætningspunktet standarder](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Se også

[Integrere en tilpasset MPEG-STREG-videostreaming i et HTML5-program med DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[GitHub dash.js lager](https://github.com/Dash-Industry-Forum/dash.js)
 
