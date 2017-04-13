<properties 
    pageTitle="Sådan bruger du Azure Redis Cache | Microsoft Azure" 
    description="Lær at forbedre ydeevnen for dine Azure programmer med Azure Redis Cache" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache"></a>Sådan bruger du Azure Redis Cache

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Denne vejledning viser, hvordan du kommer i gang med **Azure Redis Cache**. Microsoft Azure Redis Cache er baseret på populære Åbn kilden Redis Cache. Det giver dig adgang til en sikker, dedikeret Redis cache, administreres af Microsoft. En cache, der er oprettet ved hjælp af Azure Redis Cache er tilgængelige fra et program i Microsoft Azure.

Microsoft Azure Redis Cache findes i de følgende lag:

-   **Grundlæggende** – enkelt node. Flere størrelse op til 53 GB.
-   **Standard** – to noder primær/replika. Flere størrelse op til 53 GB. 99,9% SERVICENIVEAUAFTALE.
-   **Premium** – to noder primær/replika med op til 10 shards. Flere størrelser fra 6 GB til 530 GB (Kontakt os med at få mere at vide). Alle Standard niveau funktioner og mere herunder understøttelse af [Redis klynge](cache-how-to-premium-clustering.md), [Redis brugerdata](cache-how-to-premium-persistence.md)og [Azure virtuelt netværk](cache-how-to-premium-vnet.md). 99,9% SERVICENIVEAUAFTALE.

Hvert niveau varierer i forhold til funktioner og priser. Du kan finde oplysninger om priser, [Cachen priser detaljer][].

Denne vejledning viser, hvordan du bruger [StackExchange.Redis][] -klienten ved hjælp af C\# kode. De scenarier, der er omfattet omfatter **oprette og konfigurere en cache**, **konfigurere cache-klienter**og **tilføje og fjerne objekter fra cachen**. Du kan finde flere oplysninger om brug af Azure Redis Cache referere til afsnittet [Næste trin][] . Se, [hvordan du opretter en Web App med Redis Cache](cache-web-app-howto.md)for et trinvist selvstudium i at opbygge en ASP.NET MVC online med Redis Cache.

<a name="getting-started-cache-service"></a>
## <a name="get-started-with-azure-redis-cache"></a>Introduktion til Azure Redis Cache

Det er nemt at komme i gang med Azure Redis Cache. For at komme i gang skal du klargøre og konfigurere en cache. Derefter skal konfigurere du cache klienter så de kan få adgang til cachen. Når cache-klienter er konfigureret, kan du begynder at arbejde med dem.

-   [Oprette cachen][]
-   [Konfigurere cache-klienter][]

<a name="create-cache"></a>
## <a name="create-a-cache"></a>Oprette en cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>For at få adgang til din cache efter den er oprettet

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Du kan finde flere oplysninger om at konfigurere din cache, se, [hvordan du konfigurerer Azure Redis Cache](cache-configure.md).

<a name="NuGet"></a>
## <a name="configure-the-cache-clients"></a>Konfigurere cache-klienter

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Når klientprojektet på computeren er konfigureret til cachelagring, kan du bruge de teknikker, der er beskrevet i de følgende afsnit til at arbejde med din cache.

<a name="working-with-caches"></a>
## <a name="working-with-caches"></a>Arbejde med cachelagre

Trinnene i dette afsnit beskriver, hvordan at udføre almindelige opgaver med Cache.

-   [Oprette forbindelse til cachen][]
-   [Tilføje og hente objekter fra cachen][]
-   [Arbejde med .NET objekter i cachen](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## <a name="connect-to-the-cache"></a>Oprette forbindelse til cachen

For at kunne arbejde fra et program med en cache, skal have du en reference til cachen. Tilføj følgende til toppen af en fil, du vil bruge StackExchange.Redis-klienten til at få adgang til en Azure Redis Cache.

    using StackExchange.Redis;

>[AZURE.NOTE] StackExchange.Redis klienten kræver .NET Framework 4 eller nyere.

Forbindelsen til Azure Redis cachen administreres af den `ConnectionMultiplexer` klasse. Denne klasse er udviklet til at blive delt og genbruges i hele klientprogrammet og behøver ikke at oprettes på grundlag af hver handling. 

Oprette forbindelse til en Azure Redis Cache og returneres en forekomst af en forbundne `ConnectionMultiplexer`, ringe til statiske `Connect` metode og gang i cachen slutpunkt og nøgle som i følgende eksempel. Brug tasten dannet ud fra portalen Azure som parameteren password.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Advarsel: Aldrig store-legitimationsoplysninger i kildekode. Hvis du vil bevare dette eksempel enkel, jeg vise dem i koden. Se [hvordan programmet strenge og forbindelse strenge arbejde][] for oplysninger om, hvordan du gemmer legitimationsoplysninger.

Hvis du ikke vil bruge SSL, enten angive `ssl=false` eller udelader de `ssl` parameter.

>[AZURE.NOTE] Ikke-SSL-porten er deaktiveret som standard for nye cache. Du kan finde oplysninger om aktivering af ikke-SSL-porten, [Access porte](cache-configure.md#access-ports)..

En metode til deling af en `ConnectionMultiplexer` forekomst i dit program er at have en statisk egenskab, der returnerer en forbundne forekomst, svarende til følgende eksempel. Dette er en tråd sikker måde initialiseret kun en enkelt forbindelse `ConnectionMultiplexer` forekomst. I disse eksempler `abortConnect` er indstillet til falsk, hvilket betyder, at opkaldet lykkes, selvom der ikke oprettes forbindelse til Azure Redis cachen. En vigtige funktion af `ConnectionMultiplexer` er, at det vil automatisk genoprette forbindelsen til cachen én gang netværksproblem eller andre årsager er løst.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

Du kan finde flere oplysninger om avanceret forbindelse konfigurationsindstillinger, [StackExchange.Redis konfiguration model][].

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

Når forbindelsen er oprettet, returnerer en reference til redis cache-databasen ved at ringe på `ConnectionMultiplexer.GetDatabase` metode. Det objekt, der returneres fra den `GetDatabase` metode er et lette pass-through-objekt og behøver ikke at blive gemt.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Nu hvor du ved, hvordan du kan oprette forbindelse til en Azure Redis Cache forekomst og returnerer en reference til cache-databasen, Lad os se nærmere på arbejde med cachen.

<a name="add-object"></a>
## <a name="add-and-retrieve-objects-from-the-cache"></a>Tilføje og hente objekter fra cachen

Elementer kan være gemt i og hentet fra en cache ved hjælp af den `StringSet` og `StringGet` metoder.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis gemmer de fleste data som Redis strenge, men disse strenge kan indeholde mange typer data, herunder fortløbende binære data, som kan bruges, når lagring af .NET objekter i cachen.

Når du ringer til `StringGet`, hvis objektet findes, returneres, og hvis det ikke er tilfældet, `null` returneres. I dette tilfælde kan du hente værdien fra den ønskede datakilde og gemme dem i cachen til senere brug. Dette kaldes mønstret cache-side.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Angive udløb af et element i cachen, skal du bruge den `TimeSpan` -parameter for `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>Arbejde med .NET objekter i cachen

Azure Redis Cache kan cachelagre .NET objekter samt enkle datatyper, men før et .NET objekt kan cachelagres det skal serialiseres. Dette er ansvarlig for programmet udvikler, og giver mulighed for udvikler valg af serialiseringsfunktionen.

En nem måde at sekventielt objekter er at bruge den `JsonConvert` serialisering metoder i [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) og sekventielt til og fra JSON. I følgende eksempel viser en get og sæt ved hjælp af en `Employee` objektforekomst.


    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    
        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende, skal du følge disse links for at få flere oplysninger om Azure Redis Cache.

-   Se udbyderne ASP.NET til Azure Redis Cache.
    -   [Azure Redis Session tilstand udbyder](cache-aspnet-session-state-provider.md)
    -   [Azure Redis Cache ASP.NET Output Cache udbyder](cache-aspnet-output-cache-provider.md)
-   [Aktivere cachen diagnosticering](cache-how-to-monitor.md#enable-cache-diagnostics) , så du kan [overvåge](cache-how-to-monitor.md) tilstanden for din cache. Du kan få vist målene på portalen Azure, og du kan også [hente og gennemse](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) dem ved hjælp af værktøjerne efter eget valg.
-   Se [StackExchange.Redis cache klient dokumentation][].
    -   Azure Redis Cache kan åbnes fra mange Redis klienter og udviklingssprog. Du kan finde flere oplysninger [http://redis.io/clients][].
-   Azure Redis Cache kan også bruges med tredjepartstjenester og værktøjer som Redsmin og Redis skrivebord Manager.
    -   Se, [hvordan du henter en Azure Redis forbindelsesstreng og bruge det med Redsmin][]kan finde flere oplysninger om Redsmin.
    -   Få adgang til, og Undersøg dine data i Azure Redis Cache med en grafisk brugergrænseflade ved hjælp af [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager).
-   Se dokumentationen til [redis][] , og Læs om [redis datatyper][] og [en 15 minutters introduktion til Redis datatyper][].



<!-- INTRA-TOPIC LINKS -->
[Næste trin]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Oprette cachen]: #create-cache
[Configure the cache]: #enable-caching
[Konfigurere cache-klienter]: #NuGet
[Working with Caches]: #working-with-caches
[Oprette forbindelse til cachen]: #connect-to-cache
[Tilføje og hente objekter fra cachen]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png






   
<!-- LINKS -->
[http://redis.IO/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Hvordan du kan hente en Azure Redis forbindelsesstreng og bruge det sammen med Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis konfiguration model]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cachelagre prisoplysninger]: http://www.windowsazure.com/pricing/details/cache/
[Azure Portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis cache klient dokumentation]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis-datatyper]: http://redis.io/topics/data-types
[en 15 minutters introduktion til Redis-datatyper]: http://redis.io/topics/data-types-intro

[Hvordan fungerer programmet strenge og forbindelsesstrenge af]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


