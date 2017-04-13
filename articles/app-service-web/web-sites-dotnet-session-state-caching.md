<properties 
    pageTitle="Sessionstilstand med Azure Redis cachen i Azure App Service" 
    description="Lær at bruge Cachetjeneste Azure til at understøtte cachelagring af ASP.NET session tilstand." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="Rick-Anderson" 
    manager="wpickett" 
    editor="none"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="get-started-article" 
    ms.date="06/27/2016" 
    ms.author="riande"/>


# <a name="session-state-with-azure-redis-cache-in-azure-app-service"></a>Sessionstilstand med Azure Redis cachen i Azure App Service


Dette emne forklares det, hvordan du bruger den Azure Redis Cachetjeneste til sessionstilstand.

Hvis din ASP.NET-WebApp bruger sessionstilstand, skal du konfigurere en ekstern session tilstand udbyder (Cachetjeneste Redis eller en udbyder af SQL Server-session tilstand). Hvis du bruger sessionstilstand og ikke bruger en ekstern udbyder, vil du være begrænset til én forekomst af din online. Cachetjeneste Redis er den hurtigste og nemmeste at aktivere.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

##<a id="createcache"></a>Oprette cachen
Følg [disse retningslinjer](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) for at oprette cachen.

##<a id="configureproject"></a>Føje RedisSessionStateProvider NuGet pakken til din online
Installere på NuGet `RedisSessionStateProvider` pakke.  Brug følgende kommando for at installere fra pakke manager-konsollen (**værktøjer** > **NuGet Package Manager** > **Pakke Manager-konsollen**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Installere **værktøjer** > **NuGet Package Manager** > **Administrere NugGet pakker til løsning**, søge efter `RedisSessionStateProvider`.

Finde flere oplysninger under [NuGet RedisSessionStateProvider siden](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) og [Konfigurer cache-klienten](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

##<a id="configurewebconfig"></a>Ændre filen Web.Config
Ud over at samling referencer til cachen, tilføjer pakken NuGet følgebrev poster i filen *web.config* . 

1. Åbn *web.config* , og find den elementet **sessionState** .

1. Angiv værdier for `host`, `accessKey`, `port` (SSL-porten skal være 6380), og angiv `SSL` til `true`. Disse værdier kan fås fra bladet [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715) for din cache forekomst. Få mere at vide under [oprette forbindelse til cachen](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Bemærk, at den ikke-SSL-port er deaktiveret som standard for nye cache. Du kan finde flere oplysninger om aktivering af ikke-SSL-porten, i afsnittet [Access porte](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) i emnet [konfigurere en cache i Azure Redis Cache](https://msdn.microsoft.com/library/azure/dn793612.aspx) . Den følgende markeringer viser ændringerne i filen *web.config* , specifikt ændres til *port*, *host*, accessKey*, og *ssl *.

          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;


##<a id="usesessionobject"></a>Brug af sessionsobjektet i kode
Det sidste trin er at begynde at bruge objektet Session i din ASP.NET-kode. Du kan føje objekter til sessionstilstand ved hjælp af metoden **Session.Add** . Denne metode bruger nøgle-værdi-par til at lagre elementer i cachen session tilstand.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

Følgende kode henter denne værdi fra sessionstilstand.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue; 

Du kan også bruge Redis cachen til cache objekter i WebApp. Du kan finde flere oplysninger om [MVC film app med Azure Redis Cache på 15 minutter](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Du kan finde flere oplysninger om, hvordan du bruger ASP.NET sessionstilstand, [ASP.NET Session tilstand oversigt][].

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="whats-changed"></a>Hvad er ændret
* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)

  *Ved [Rick Westermann](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net  
  [Oversigt over ASP.NET Session tilstand]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 
