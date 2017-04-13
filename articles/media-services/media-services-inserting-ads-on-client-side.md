<properties 
    pageTitle="Indsættelse af reklamer på klientsiden | Microsoft Azure" 
    description="Dette emne viser, hvordan du indsætter reklamer på klientsiden." 
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
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="inserting-ads-on-the-client-side"></a>Indsættelse af reklamer på klientsiden

Dette emne indeholder oplysninger om, hvordan du indsætter forskellige typer af reklamer på klientsiden.

Se oplysninger om lukket for hørehæmmede og ad understøttelse i Live streaming videoer, [understøttes undertekster for hørehæmmede og Ad indsætningspunktet standarder](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

>[AZURE.NOTE] Azure Media Player understøtter i øjeblikket ikke reklamer.

##<a id="insert_ads_into_media"></a>Indsætning af reklamer i dine medier

Azure Media Services indeholder support til indsættelse af reklame gennem Windows Media-Platform: Player strukturer. Player strukturer med ad understøttelse er tilgængelige til Windows 8, Silverlight, Windows Phone 8 og iOS-enheder. Hver player framework indeholder eksempelkode, der viser, hvordan du kan implementere et program til afspilning. Der findes tre forskellige typer af reklamer, du kan indsætte i: medielisten.

- **Lineær** – fuld ramme reklamer, afbryde primære videoen.
- **Nonlinear** – overlejring reklamer, der vises som den primære videoen afspilles, som regel et logo eller andre statisk billede, der er placeret i afspilleren.
- **Companion** – reklamer, der vises uden for afspilleren.

Reklamer kan placeres på en hvilken som helst sted i den primære video tidslinje. Du skal kende afspilleren, hvornår der skal afspilles på ad og hvilke reklamer til afspilning. Dette gøres ved hjælp af et sæt standard XML-baserede filer: Video Ad Service-skabelon (VAST), Digital Video flere Ad afspilningsliste (VMAP), skabelon medier abstrakte rækkefølge (MAST) og Digital Video Player Ad Interface Definition (VPAID). STORE filer angive, hvilke reklamer skal vises. VMAP filer angive, hvornår til at afspille forskellige reklamer og indeholder omfattende XML. MAST filer er en anden måde at sekvens reklamer der også kan indeholde store XML. VPAID filer definere en grænseflade mellem videoafspilleren og ad eller ad-server.

Hver player framework fungerer anderledes, og hver behandles i et nyt emne. Dette emne beskrives de grundlæggende systemer, der bruges til at indsætte reklamer. Reklamer videoafspiller programmer anmode om fra en ad-server. Ad-server kan svare på flere forskellige måder:

- Returnere en langt fil
- Returnere en VMAP fil (med integrerede VAST)
- Returnere en MAST fil (med integrerede VAST)
- Returnere en langt fil med VPAID reklamer

 
###<a name="using-a-video-ad-service-template-vast-file"></a>Ved hjælp af en Video Ad Service (VAST) skabelonfil

En omfattende fil angiver, hvilke ad eller reklamer skal vises. Følgende XML er et eksempel på en omfattende fil for en lineær ad:
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
    
Lineær ad er beskrevet i de **<Linear>** element. Det angiver varigheden af ad, registrering af hændelser, skal du klikke på igennem, klik på registrering, og et antal **<MediaFile>** elementer. Sporing hændelser er angivet i den **<TrackingEvents>** element og tillade en ad-server til at registrere forskellige hændelser, der opstår, mens du får vist ad. I dette tilfælde starten, midten, fuldført, og udvid begivenheder, registreres. Start begivenheden opstår, når ad vises. Hændelsen midtpunkt opstår, når mindst 50% af den ad tidslinje er blevet vist. Hændelsen fuldført opstår, når ad har kørt til slutningen. Udvid begivenheden opstår, når brugeren udvider videoafspilleren til fuld skærm. Clickthroughs er angivet med en **<ClickThrough>** element i en **<VideoClicks>** element og angiver en URI til en ressource skal vises, når brugeren klikker på ad. ClickTracking er angivet i en **<ClickTracking>** element, også inden for den **<VideoClicks>** element og angiver en sporing ressource til afspilleren til at anmode om når brugeren klikker på ad. Den **<MediaFile>** elementer angive oplysninger om en bestemt kodning af en annonce. Når der er mere end ét **<MediaFile>** element, kan videoafspilleren vælge den bedste kodning til platformen. 

Lineær reklamer kan vises i en bestemt rækkefølge. Du kan tilføje yderligere <Ad> elementer til VAST fil og angive den rækkefølge, ved hjælp af attributten sekvens. I følgende eksempel illustrerer dette:
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
    
Ikke-lineære reklamer er angivet i en <Creative> samt element. Følgende eksempel viser en <Creative> element, der beskriver en ikke-lineære ad.

    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>

 
Den **<NonLinearAds>** element kan indeholde et eller flere **<NonLinear>** elementer, hver især kan beskrive en ikke-lineære ad. Den **<NonLinear>** element angiver ressourcen for ikke-lineære ad. Ressourcen kan være en **<StaticResouce>**, en **<IFrameResource>**, eller en **<HTMLResouce>**.**<StaticResource>** i denne artikel beskrives en ikke-HTML-ressource og definerer en creativeType attribut, der angiver, hvordan ressourcen, der vises:

Billede/gif, billede/jpeg, billede/png – ressourcen vises i en HTML- **<img>** mærke.

Program/x-javascript-ressourcen vises i en HTML-<**script**> kode.

Program/x-shockwave-flash-ressourcen vises i en Flash player.

**<IFrameResource>**Beskriver en HTML-ressource, der kan vises i en IFrame. **<HTMLResource>**i denne artikel beskrives en del af en HTML-kode, der kan indsættes i en webside. **<TrackingEvents>**Angiv sporing begivenheder og URI til at anmode om når hændelsen indtræffer. I dette eksempel registreres acceptInvitation og Skjul begivenheder. Yderligere oplysninger om den **<NonLinearAds>** element og dets underordnede websteder, se IAB.NET/VAST. Bemærk, at den **<TrackingEvents>** element er placeret i den** <NonLinearAds> ** element frem for **<NonLinear>** element.

Companion reklamer er defineret i en <CompanionAds> element. Den <CompanionAds> element kan indeholde et eller flere <Companion> elementer. Hver <Companion> element i denne artikel beskrives et companion ad og kan indeholde et <StaticResource>, <IFrameResource>, eller <HTMLResource> som er angivet på samme måde som i en ikke-lineære ad. En omfattende fil kan indeholde flere companion reklamer og player programmet kan vælge den mest relevante annonce skal vises. Du kan finde flere oplysninger om VAST, [store 3.0](http://www.iab.net/media/file/VASTv3.0.pdf).

###<a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Brug af en Digital Video flere Ad afspilningslistefil (VMAP)

En VMAP fil kan du angive, hvornår ad sideskift, hvor lang tid hver sideskift er, hvor mange reklamer kan vises i et sideskift, og hvad typer reklamer muligvis vises under et sideskift. Følgende i en eksempelfil VMAP, der definerer et enkelt ad sideskift:
    
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
     
Filen VMAP begynder med et <VMAP> element, der indeholder en eller flere <AdBreak> elementer, der definerer en ad sideskift. Hver ad sideskift angiver en type sektionsskift, sideskift-ID og tidsforskydning. Attributten breakType angiver typen af ad, der kan afspilles under pausen: lineær, ikke-lineære, eller få vist. Vise reklamer kort til langt companion reklamer. Mere end én ad type kan angives en kommasepareret (uden mellemrum) på listen. BreakID er en valgfri id for ad. TimeOffset angiver, når ad skal vises. Det kan angives i en af følgende måder:

1. Tid – i hh:mm:ss eller hh:mm:ss.mmm format, hvor .mmm er millisekunder. Værdien af denne attribut angiver tidspunkt fra begyndelsen af video tidslinjen til starten af ad sideskift.
1. Procent – i n % format er hvor n procentdelen af video tidslinjen til at afspille, før du afspille ad
1. Start/slut – angiver, at en annonce skal vises før eller efter videoen har været vist
1. Placere – angiver rækkefølgen af ad sideskift, når tidsindstilling for ad sideskift er ukendt, sådan som direkte streaming. Rækkefølgen af hver ad sideskift er angivet i formatet #n, hvor n er et heltal, 1 eller større. 1 angiver ad skal afspilles på den første mulighed 2 angiver ad skal afspilles på den anden mulighed og så videre.

I elementet <**AdBreak**> kan der være et <**AdSource**> element. Elementet <**AdSource**> indeholder følgende attributter:

1. Id-Angiver et id for ad kilden
1. allowMultipleAds – en boolesk værdi, der angiver, om flere reklamer kan vises under ad sideskift
1. followRedirects – en valgfri boolesk værdi, der angiver, hvis den videoafspiller skal imødekomme omdirigerer i et ad-svar

Elementet <**AdSource**> indeholder afspilleren et indbygget ad svar eller en reference til et ad svar. Det kan indeholde et af følgende elementer:

- <VASTAdData>Angiver et langt ad svar er integreret i filen VMAP
- <AdTagURI>en URI, der refererer til et ad-svar fra et andet system
- <CustomAdData>-en vilkårlig streng, der respresents et ikke-store svar

I dette eksempel er ikke angivet et integrerede ad svar med en <VASTAdData> element, der indeholder et BREDT ad svar. Du kan finde flere oplysninger om de andre elementer, [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

Elementet <**AdBreak**> kan også indeholde et <**TrackingEvents**> element. <**TrackingEvents**> element gør det muligt at spore starten eller slutningen af en ad pause eller om der opstod en fejl under ad sideskift. Elementet <**TrackingEvents**> indeholder et eller flere <**sporing**> elementer, hver især angiver en sporing begivenhed og en sporing URI. De mulige sporing hændelser er:

1. breakStart – registrerer starten af et ad sideskift
1. breakEnd – spore fuldførelse af en ad sideskift
1. fejl – registrerer en fejl, der opstod under ad sideskift

I følgende eksempel viser en VMAP-fil, der angiver sporing begivenheder

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Du kan finde flere oplysninger om elementet <**TrackingEvents**> og dets underordnede websteder, under http://iab.org/VMAP.pdf

###<a name="using-a-media-abstract-sequencing-template-mast-file"></a>Ved hjælp af en medier resume rækkefølge skabelonfilen (MAST)

En MAST fil kan du angive udløsere, der definerer, når en annonce vises. Følgende er et eksempel MAST fil, der indeholder udløsere til en pre filmrulle ad, en midt filmrulle ad og en efter filmrulle ad.

    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>

 

Filen MAST begynder med et **<MAST>** element, der indeholder en **<triggers>** element. Den <triggers> element indeholder et eller flere **<trigger>** elementer, der definerer, når en annonce skal afspilles. 

Den **<trigger>** element indeholder en **<startConditions>** element, der angiver, når en annonce skal begynde at afspille. Den **<startConditions>** element indeholder et eller flere <condition> elementer. Når hver <condition> evalueres til sand en udløser startes eller tilbagekaldes, afhængigt af om den <condition> er indeholdt i en **< startConditions**> eller **<endConditions>** element henholdsvis. Når flere <condition> elementer er til stede, er blevet behandlet som implicit OR, de betingelser, der evaluerer til true medfører, at udløse at starte. <condition>elementer kan indlejres. Når underordnede <condition> er forudindstillede elementer, de behandles som en implicit og, alle betingelser skal evalueres til Sand for udløser at starte. Den <condition> element indeholder følgende attributter, der definerer betingelsen: 

1. **type** – angiver typen af betingelse, begivenhed eller egenskab
1. **navn** – navnet på den egenskab eller en begivenhed skal anvendes under beregning
1. **værdi** – den værdi, som en egenskab skal evalueres mod
1. **operatoren** – handlingen skal bruges under beregning: EQ (lighedstegn) NEQ (ikke lig med) GTR (større), GEQ (større eller lig med), og LT (mindre end), LEQ (mindre end eller lig med), rest (modulus)

**<endConditions>**indeholder også <condition> elementer. Når en betingelse evalueres til sand på udløser nulstilles. Den <trigger> element indeholder også en <sources> element, der indeholder en eller flere <source> elementer. Den <source> elementer definerer URI til ad svaret og typen af ad svar. I dette eksempel gives en URI til et langt svar. 


    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
 

###<a name="using-video-player-ad-interface-definition-vpaid"></a>Brug af Video Player-Ad interfacedefinition (VPAID)

VPAID er et API til at aktivere eksekverbare ad enheder til at kommunikere med en video. Det gør det meget interaktive ad oplevelser. Brugeren kan interagere med ad og ad kan svare på Handlinger, der udføres af Fremviser. En annonce kan for eksempel vises knapper, som brugeren mulighed for at få vist yderligere oplysninger eller en længere version af ad. Videoafspilleren skal understøtte VPAID API og den eksekverbare ad skal implementere API. Når en afspiller, som en annonce fra en ad-server serveren kan svare med en omfattende svar, der indeholder en VPAID ad.

En eksekverbare ad oprettes i kode, der skal udføres i et runtime-miljø som Adobe Flash™ eller JavaScript, der kan udføres i en webbrowser. Når en server til ad returnerer et langt svar, der indeholder en VPAID ad, værdien af apiFramework attribut i den <MediaFile> element skal være "VPAID". Denne attribut angiver, at de indeholdte ad er en VPAID eksekverbare ad. Typeattributten skal være angivet til MIME-typen på den eksekverbare fil, som "program/x-shockwave-flash" eller "program/x-javascript". Følgende XML-kodestykke viser den <MediaFile> element fra en omfattende svar, der indeholder en VPAID eksekverbare ad. 

    
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
 

En eksekverbare ad kan initialiseret ved hjælp af den <AdParameters> element i den <Linear> eller <NonLinear> elementer i et langt svar. Yderligere oplysninger om den <AdParameters> element, se [langt 3.0](http://www.iab.net/media/file/VASTv3.0.pdf). Du kan finde flere oplysninger om VPAID API, [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

##<a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementere en Windows eller Windows Phone 8 afspiller med Ad Support

Microsoft Media Platform: Player Framework for Windows 8 og Windows Phone 8 indeholder en samling af eksempler på programmer, der viser, hvordan du kan implementere en videoafspiller program, ved hjælp af en ramme. Du kan hente Player Framework og eksemplerne fra [Player Framework for Windows 8 og Windows Phone 8](https://playerframework.codeplex.com).

Når du åbner Microsoft.PlayerFramework.Xaml.Samples løsningen vises et antal mapper i projektet. Mappen reklame indeholder den eksempelkode, der er relevante for oprettelsen af en videoafspiller med ad support. I reklame-mappe er et antal XAML/cs filer som viser, hvordan du indsætter reklamer i en anden måde. Følgende liste beskrives hver:

- AdPodPage.xaml viser, hvordan du får vist en ad styreanordning.
- AdSchedulingPage.xaml viser, hvordan du planlægger reklamer.
- FreeWheelPage.xaml viser, hvordan du bruger FreeWheel plug-in'et til at planlægge reklamer.
- MastPage.xaml viser, hvordan du planlægger reklamer med en MAST fil.
- ProgrammaticAdPage.xaml viser, hvordan du planlægger reklamer til en video fra et program.
- ScheduleClipPage.xaml viser, hvordan du planlægger en annonce uden en omfattende fil.
- VastLinearCompanionPage.xaml viser, hvordan du indsætter en lineær og companion ad.
- VastNonLinearPage.xaml viser, hvordan du indsætter en ikke-lineær ad.
- VmapPage.xaml viser, hvordan du angiver reklamer med en VMAP fil.

Hver af disse eksempler bruger klassen MediaPlayer defineret af player framework. Eksempler på de fleste Brug plug-ins, tilføjer understøttelse af forskellige ad svar formater. Eksemplet ProgrammaticAdPage arbejde fra et program sammen med en forekomst af MediaPlayer.

###<a name="adpodpage-sample"></a>Eksempel på AdPodPage

Dette eksempel bruger AdSchedulerPlugin til at definere, hvornår du skal have vist en annonce. I dette eksempel er en midt filmrulle annonce planlagt skal afspilles efter 5 sekunder. Ad styreanordning (en gruppe af reklamer skal vises i rækkefølge) er angivet i filen store returneres fra en ad-server. URI til den store fil er angivet i den <RemoteAdSource> element.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
    
        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>
    
                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>
    
                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>

Du kan finde flere oplysninger om AdSchedulerPlugin, se [reklamer i Player Framework på Windows 8 og Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

###<a name="adschedulingpage"></a>AdSchedulingPage

Dette eksempel bruger også AdSchedulerPlugin. Det planlægger tre reklamer, en foreløbig filmrulle ad, en midt filmrulle ad og en efter filmrulle ad. URI, der VAST for hver annonce er angivet i en <RemoteAdSource> element.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>
    
                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>


###<a name="freewheelpage"></a>FreeWheelPage

Dette eksempel bruger FreeWheelPlugin, som angiver en kilde-attribut, der angiver en URI, der peger på en SmartXML-fil, der angiver ad indhold samt ad planlægningsoplysninger.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="mastpage"></a>MastPage

Dette eksempel bruger MastSchedulerPlugin, hvor du kan bruge en MAST fil. Attributten kilde Angiver placeringen af filen MAST.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="programmaticadpage"></a>ProgrammaticAdPage

Dette eksempel skal fra et program arbejde sammen med MediaPlayer. Filen ProgrammaticAdPage.xaml starter MediaPlayer:

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>

Filen ProgrammaticAdPage.xaml.cs opretter en AdHandlerPlugin tilføjer en TimelineMarker til at angive, når en annonce skal vises, og derefter tilføjer en handler for hændelsen MarkerReached der indlæser en RemoteAdSource, der angiver en URI til en omfattende fil og derefter afspilles på ad.
    
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;
    
            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }
    
            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }

###<a name="scheduleclippage"></a>ScheduleClipPage

Dette eksempel bruger AdSchedulerPlugin til at planlægge en midt filmrulle ad ved at angive en .wmv-fil, der indeholder ad.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearcompanionpage"></a>VastLinearCompanionPage

Dette eksempel viser, hvordan du bruger AdSchedulerPlugin til at planlægge en midt filmrulle lineær ad med en companion ad. Den <RemoteAdSource> element angiver placeringen af store filer.
    
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage

Dette eksempel bruger AdSchedulerPlugin til at planlægge en lineær og en ikke-lineær ad. STORE filens placering er angivet med den <RemoteAdSource> element.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
                            
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vmappage"></a>VMAPPage

Denne eksempler bruger VmapSchedulerPlugin til at planlægge reklamer ved hjælp af en VMAP fil. URI til filen VMAP er angivet i attributten kilde for den <VmapSchedulerPlugin> element.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

##<a name="implementing-an-ios-video-player-with-ad-support"></a>Implementere en iOS Video Player med Ad Support


Microsoft Media Platform: Player ramme for iOS indeholder en samling af eksempler på programmer, der viser, hvordan du kan implementere en videoafspiller program, ved hjælp af en ramme. Du kan hente Player Framework og eksemplerne fra [Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). Siden github indeholder et link til en Wiki, der indeholder yderligere oplysninger om player framework og en introduktion til player eksempel: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).


###<a name="scheduling-ads-with-vmap"></a>Planlægge reklamer med VMAP

I følgende eksempel viser, hvordan du planlægger reklamer ved hjælp af en VMAP fil.

    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest
    
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }          
            }

###<a name="scheduling-ads-with-vast"></a>Planlægge reklamer med VAST

I følgende eksempel viser, hvordan du planlægger en sen binding store ad.
    
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
         
   I følgende eksempel viser, hvordan du planlægger en tidlig binding store ad.
Eksempel 4: Planlæg en tidlig binding store ad //Download VAST filen, hvis (! [ framework.adResolver downloadManifest: & manifest withURL: [NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]]) {[selv logFrameworkError];} mere {adLinearTime.startTime = 7; adLinearTime.duration = 0;
        
        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }

I følgende eksempel viser, hvordan du indsætter en annonce, ved hjælp af grov klippe redigering (RCE)

    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

I følgende eksempel viser, hvordan du planlægger en ad styreanordning.

    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;
    
    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

I følgende eksempel viser, hvordan du planlægger en ikke-sticky midt filmrulle ad. En ikke-sticky ad er kun afspilles, når uanset eventuelle ønsker fremviseren udfører.
    
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

I følgende eksempel viser, hvordan du planlægger en sticky midt filmrulle ad. En sticky ad vises hver gang den angivne punkt på tidslinjen, video er nået.

    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }


I følgende eksempel viser, hvordan du planlægger et efter filmrulle ad.

    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

I følgende eksempel viser, hvordan du planlægger en foreløbig filmrulle ad.
    
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

I følgende eksempel viser, hvordan du planlægger en midt filmrulle overlejring ad.
    
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }



##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
##<a name="see-also"></a>Se også

[Udvikle videoafspiller programmer](media-services-develop-video-players.md)
