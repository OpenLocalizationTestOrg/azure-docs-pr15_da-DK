<properties
    pageTitle="Cache ASP.NET Output Cache udbyder"
    description="Lær, hvordan du cache ASP.NET siden outputtet ved hjælp af Azure Redis Cache"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>ASP.NET Output Cache-Provider til Azure Redis Cache

Provideren Redis Output Cache er en uden for proces lagerplads metode til output cachedata. Disse data er specifikt for fuld HTTP-svar (siden outputcachelagring). Udbyderen, der tilsluttes det nye output cache udbyder udvidelsesmuligheder af punkt, der blev introduceret i ASP.NET 4.

Hvis du vil bruge provideren Redis Output cachen først konfigurere din cache og derefter konfigurere din ASP.NET-program ved hjælp af pakken Redis Output Cache udbyder NuGet. Dette emne indeholder vejledning om konfiguration af dit program at bruge Redis Output Cache Provider. Se [oprette en cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)kan finde flere oplysninger om oprettelse og konfiguration af en Azure Redis Cache forekomst.

## <a name="store-aspnet-page-output-in-the-cache"></a>Gemme ASP.NET siden output i cachen

Konfigurere en klientprogrammet i Visual Studio ved hjælp af pakken Redis Output Cache udbyder NuGet, højreklik på projektet i **Solution Explorer** , og vælg **Administrer NuGet pakker**.

![Azure Redis Cache administrere NuGet-pakker](./media/cache-aspnet-output-cache-provider/redis-cache-manage-nuget-menu.png)

Skriv **RedisOutputCacheProvider** i søgetekstfeltet, vælge den fra resultaterne, og klik på **Installer**.

![Azure Redis Cache Output Cache udbyder](./media/cache-aspnet-output-cache-provider/redis-cache-page-output-provider.png)

Pakken Redis Output Cache udbyder NuGet har en afhængighed på StackExchange.Redis.StrongName-pakke. Hvis pakken StackExchange.Redis.StrongName ikke findes i dit projekt, der skal installeres. Bemærk, at ud over pakken stærke med navnet StackExchange.Redis.StrongName der også StackExchange.Redis ikke-sikkert navn version. Hvis dit projekt bruger StackExchange.Redis ikke-sikkert navn version skal du fjerne den, enten før eller efter installation af Redis Output Cache udbyder NuGet-pakken, kan ellers du få navngivning af konflikter i projektet. Du kan finde flere oplysninger om disse pakker, [konfigurere .NET cache klienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Pakken NuGet-overførsler og tilføjer de nødvendige samling referencer og tilføjer følgende afsnit i en web.config-fil, der indeholder den nødvendige konfiguration for ASP.NET-program at bruge Redis Output Cache Provider.

    <caching>
      <outputCachedefault Provider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

Afsnittet kommenterede indeholder et eksempel på attributter og eksempler på Indstillinger for hver attribut.

Konfigurere attributterne med værdierne fra din cache blade i Microsoft Azure-portalen, og Konfigurer de andre værdier efter behov. Du kan finde instruktioner til at få adgang til din cache egenskaber, [konfigurere Redis cacheindstillinger](cache-configure.md#configure-redis-cache-settings).

-   **host** – angive din cache slutpunkt.
-   **port** – Brug en ikke-SSL-port eller SSL port, afhængigt af indstillingerne for ssl.
-   **accessKey** – Brug den primære eller sekundære nøgle til din cache.
-   **ssl** -SAND, hvis du vil sikre cache/klienten kommunikation med ssl; Ellers false. Sørg for at angive den korrekte port.
    -   Ikke-SSL-porten er deaktiveret som standard for nye cache. Angiv sand for denne indstilling til at bruge SSL-port. Du kan finde flere oplysninger om aktivering af ikke-SSL-porten, i afsnittet [Access porte](cache-configure.md#access-ports) i emnet [konfigurere en cache](cache-configure.md) .
-   **databaseId** – angivet hvilken database bruge til cachen outputdata. Hvis ikke angives, bruges af standardværdien for 0.
-   **applicationName** -taster er gemt i redis som <AppName>_<SessionId>_Data. Dette gør det muligt for flere programmer til at dele den samme nøgle. Denne parameter er valgfri, og hvis du ikke angiver den bruges en standardværdi.
-   **connectionTimeoutInMilliseconds** – denne indstilling giver dig mulighed at tilsidesætte indstillingen connectTimeout i StackExchange.Redis-klienten. Hvis ikke angives, bruges standardindstillingen for connectTimeout 5000. Du kan finde yderligere oplysninger finder [StackExchange.Redis konfiguration model](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** – denne indstilling giver dig mulighed at tilsidesætte indstillingen syncTimeout i StackExchange.Redis-klienten. Hvis ikke angives, bruges standardindstillingen for syncTimeout 1000. Du kan finde yderligere oplysninger finder [StackExchange.Redis konfiguration model](http://go.microsoft.com/fwlink/?LinkId=398705).

Tilføj en OutputCache-direktivet på hver side, hvor du vil cache output.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

I dette eksempel cachelagrede siden dataene forbliver i cachen i 60 sekunder, og en anden version af siden cachelagres for hver parameter kombination. Finde flere oplysninger om OutputCache-direktivet [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Når disse trin udføres, er dit program konfigureret til at bruge provideren Redis Output Cache.

## <a name="next-steps"></a>Næste trin

Se [ASP.NET Session tilstand-Provider til Azure Redis Cache](cache-aspnet-session-state-provider.md).
