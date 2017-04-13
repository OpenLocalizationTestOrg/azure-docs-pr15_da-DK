<properties 
    pageTitle="Problemfri Streaming-plug-in til Open Source Media Framework" 
    description="Lær at bruge Azure Media Services udglattede Streaming plug-in'et til Adobe Åbn kilde medier Framework." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Hvordan du kan bruge den Microsoft-jævne Streaming plug-in til Adobe Open Source Media Framework

##<a name="overview"></a>Oversigt


Microsoft udglattede Streaming plug-in'et til Åbn kilde medier Framework 2.0 (SS for OSMF) udvider standard funktionerne i OSMF og tilføjer Microsoft udglattede Streaming indhold afspilning til nye og eksisterende OSMF spillere. Plug-in'et tilføjes også udglattede Streaming afspilning funktioner til Strobe medier afspilning (SMP).

SS for OSMF indeholder to versioner af plug-in:

- Statisk udglattede Streaming plug-in til OSMF (.swc)

- Dynamisk udglattede Streaming plug-in til OSMF (.swf)

Dette dokument forudsætter, at læseren har en generel forståelse af OSMF og OSMF plug-ins. Du kan finde flere oplysninger om OSMF, skal du se i dokumentationen på [officielt OSMF websted](http://osmf.org/).

###<a name="smooth-streaming-plugin-for-osmf-20"></a>Udjævne Streaming-plug-in til OSMF 2.0

Plug-in'et understøtter indlæsning og afspilning af efter behov udglattede Streaming indhold med følgende funktioner:

- Efter behov udglattede Streaming afspilning (Afspil, Pause, Seek, Stop)
- Direkte udglattede Streaming afspilning af (Afspil)
- Live DVR funktioner (Pause, Seek, DVR afspilning, gå-to-Live)
- Understøttelse af codecs til videoenheder - H.264
- Understøttelse af lyd codecs - AAC
- Flere sprog for lyd skifte med OSMF indbyggede API'er
- Maks afspilning kvalitet markering med OSMF indbyggede API'er
- Sidevogn lukket billedtekster med OSMF billedtekster plug-in
- Adobe&reg; Flash&reg; Player 11.4 eller nyere.
- Denne version understøtter kun OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Understøttede funktioner og kendte problemer

Se [dette dokument](http://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf)for en komplet liste over understøttede funktioner, ikke-understøttede funktioner og kendte problemer.


## <a name="loading-the-plugin"></a>Indlæser plug-in'en
OSMF-plug-ins kan indlæses statisk (under kompilering) eller dynamisk (på kørselstidspunktet). Problemfri Streaming plug-in'et OSMF hentes omfatter både dynamiske og statiske versioner.

- Statisk indlæsning: for at indlæse statisk, en statisk biblioteksfil (SWC) er påkrævet. Statisk plug-ins tilføjes som en reference til projekter og flette i den endelige outputfilen kompilering samtidig.

- Dynamisk indlæsning: for at indlæse dynamisk, en forudkompileret (SWF)-fil er påkrævet. Dynamiske plug-ins er indlæst i runtime og ikke medtaget i outputtet project. (Kompileret output) Dynamiske plug-ins kan indlæses ved hjælp af HTTP og fil protokoller.

Se den officielle [OSMF plug-in siden](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)kan finde flere oplysninger om statiske og dynamiske indlæsning.

###<a name="ss-for-osmf-static-loading"></a>SS til OSMF statisk indlæsning
Kodestykket nedenfor viser, hvordan du indlæse SS plug-in'et til OSMF statisk og afspille grundlæggende video ved hjælp af OSMF MediaFactory klasse. Før herunder SS for OSMF kode, skal du sikre dig, at projektreferencen til indeholder statisk plug-in'et "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

```
package 
{
    
    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    
    
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        

        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }
    
        private function initMediaPlayer():void
        {
        
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            
            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}
```


###<a name="ss-for-osmf-dynamic-loading"></a>SS til OSMF dynamiske indlæsning

Kodestykket nedenfor viser, hvordan du indlæse SS plug-in'et til OSMF dynamisk og afspille et basic video ved hjælp af klassen OSMF MediaFactory. Kopiere dynamiske plug-in'et "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" til projektmappen, hvis du vil indlæse med FILE protocol før herunder SS for OSMF kode, eller kopiere under en webserver for HTTP belastning. Det er ikke nødvendigt at medtage "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" i projektreferencer.

 
pakke {}
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;

    
    //Sets the size of the SWF
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        
        
        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }
        
        private function initMediaPlayer():void
        {
            
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}

##<a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Strobe afspilning af medier med dynamiske plug-in'et SS ODMF

Problemfri Streaming for OSMF dynamiske plug-in er kompatibelt med [Strobe medier afspilning (SMP)](http://osmf.org/strobe_mediaplayback.html). Du kan bruge SS for OSMF plug-in til at føje udglattede Streaming indhold afspilning til SMP. Kopiér "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" for at gøre dette, under en webserver for HTTP belastning at benytte følgende fremgangsmåde:

1.  Gennemse [Strobe medieafspilning konfigurationssiden](http://osmf.org/dev/2.0gm/setup.html). 
2.  Angive src til en jævn Streaming kilde, (fx http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3.  Foretag de ønskede ændringer, og klik på Vis og Opdater.
 
    **Bemærk!** Webserveren indhold skal have en gyldig crossdomain.xml. 
4.  Kopier og Indsæt koden til en enkelt HTML-side ved hjælp af din foretrukne teksteditor, f.eks, i følgende eksempel:



        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



5. Føje udglattede Streaming OSMF plug-in til den integrerede kode, og Gem.

        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>


6.  Gem din HTML-side og Publicer til en webserver. Gå til den publicerede webside ved hjælp af din foretrukne Flash&reg; Player aktiveret internetbrowser (Internet Explorer, Chrome, Firefox, osv.).
7.  Få glæde af udglattede Streaming indholdet i Adobe&reg; Flash&reg; Player.

Du kan finde flere oplysninger om generelle OSMF udvikling, officielle [OSMF udvikling side](http://osmf.org/resources.html).

##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Se også

[Microsoft tilpasset Streaming plug-in til OSMF-opdatering](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 
