<properties 
    pageTitle="Integrere en tilpasset MPEG-STREG-videostreaming i et HTML5-program med DASH.js | Microsoft Azure" 
    description="Dette emne beskrives, hvordan du kan integrere en MPEG-STREG tilpasset Streaming-Video i et HTML5-program med DASH.js." 
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


#<a name="embedding-a-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Integrere en tilpasset MPEG-STREG-videostreaming i et HTML5-program med DASH.js

##<a name="overview"></a>Oversigt

MPEG-STREG er en ISO-standarden for tilpasset streaming af videoindhold, som har betydelige fordele for dem, der skal levere høj kvalitet, tilpassede video streaming output. Med MPEG-STREG, bliver video strømmen automatisk slip til en lavere definition når netværket bliver belastet. Dette reducerer sandsynligheden for, at fremviseren får vist en "er stoppet midlertidigt" video, mens afspilleren henter de næste par sekunder for at afspille (også kendt bufferen). Som netværksoverbelastning reducerer, returnerer videoafspilleren tur. til en højere kvalitet stream. Denne mulighed for at tilpasse den båndbredde, der kræves også resulterer i et hurtigere starttidspunkt for video. Det betyder, at de første par sekunder kan afspilles i en fast – download lavere kvalitet segment og derefter har bufferen trin op til et højere kvalitet én gang tilstrækkelige indhold.

Dash.js er en Åbn kilde MPEG-STREG-videoafspiller skrevet på JavaScript. Dets mål er at tilvejebringe et robust, på tværs af platforme player, der kan genbruges frit i programmer, der kræver videoafspilning. Den indeholder afspilning af MPEG-STREG i enhver browser, der understøtter den W3C medier kilde udvidelser (MSE), i dag, der er Chrome, Microsoft Edge og IE11 (andre browsere har angivet deres formålet til at understøtte MSE). Du kan finde flere oplysninger om DASH.js se js GitHub dash.js lager.


##<a name="creating-a-browser-based-streaming-video-player"></a>Oprette en browser-baseret streaming videoafspiller

Hvis du vil oprette en enkel webside, der viser en videoafspiller med den forventede styrer sådanne en Afspil, pause, spole tilbage osv., skal du:

1. Oprette en HTML-side
1. Tilføje mærket video
1. Tilføje dash.js afspilleren
1. Initialiseret afspilleren
1. Tilføje typografi CSS
1. Få vist resultaterne i en browser, der implementerer MSE

Initialisering af afspilleren kan udføres i bare en håndfuld linjer af JavaScript-kode. Ved hjælp af dash.js, er det virkelig det nemt at integrere MPEG-STREG video i din baseret browserprogrammer.

##<a name="creating-the-html-page"></a>Oprettelse af HTML-siden

Det første trin er at oprette en standard HTML-side, der indeholder elementet **video** , Gem denne fil som basicPlayer.html, som i følgende eksempel viser:

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

##<a name="adding-the-dashjs-player"></a>Tilføje DASH.js afspilleren

Hvis du vil tilføje dash.js referenceimplementering til programmet, skal du tage filen dash.all.js fra version 1.0 af dash.js project. Dette bør gemmes i mappen JavaScript i dit program. Denne fil er en nemmere-fil, der henter den nødvendige dash.js kode til en enkelt fil. Hvis du har et kig omkring dash.js lager, du vil finde de individuelle filer, skal du teste kode og meget mere, men hvis du vil gøre er brug dash.js og derefter filen dash.all.js er, hvad du skal.

For at tilføje dash.js afspilleren til dine programmer skal du tilføje mærket script til afsnittet hoved i basicPlayer.html:

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


Dernæst skal du oprette en funktion for at initialiseret afspilleren, når siden indlæses. Tilføj følgende script efter den linje, hvor du har indlæst dash.all.js:

    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>

Denne funktion først opretter en DashContext. Det bruges til at konfigurere programmet for et bestemt runtime-miljø. Fra et teknisk synspunkt definerer den de klasser, der afhængighed indsættelse framework skal bruge, når bygning af programmet. I de fleste tilfælde vil du bruge Dash.di.DashContext.

Dernæst skal oprettes en forekomst af den primære klasse af dash.js framework, MediaPlayer. Denne klasse indeholder core metoder, der skal bruges som Afspil og pause, administrerer relationen med video element og også administrerer fortolkning af filen medier præsentation beskrivelse (MPD), som beskriver videoen skal afspilles.

Funktionen startup() i klassen MediaPlayer kaldes for at sikre, at afspilleren er klar til at afspille video. Denne funktion sikrer, at alle de nødvendige klasser (som defineret af konteksten) er blevet indlæst blandt andet. Når afspilleren er klar, kan du vedhæfte elementet video til den ved hjælp af funktionen attachView(). Dette gør det muligt for MediaPlayer til at indsætte video strømmen i elementet og også styre afspilning efter behov.

Overføre filen MPD URL-adressen til MediaPlayer, så det ved om videoen det forventes at afspille. Funktionen setupVideo() lige har oprettet skal udføres, når siden fuldt har indlæst. Gøre dette ved hjælp af hændelsen onload i elementet brødtekst. Ændre din <body> element til:

    <body onload="setupVideo()">

Til sidst skal angive størrelsen af elementet video ved hjælp af CSS. I en tilpasset streaming miljø er dette især vigtigt da kan ændre størrelsen på den video, der afspilles, som afspilning tilpasser sig efterhånden ændring af netværk betingelser. I denne enkle demo blot gennemtvinge elementet video skal være 80% af pladsen i browservinduet ved at tilføje følgende CSS i sektionen hoved på siden:
    
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

##<a name="playing-a-video"></a>Afspille en Video

Afspille en video, pege på browseren på basicPlayback.html filen, og klik på Afspil på videoafspilleren vises.


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Se også

[Udvikle videoafspiller programmer](media-services-develop-video-players.md)

[GitHub dash.js lager](https://github.com/Dash-Industry-Forum/dash.js) 
