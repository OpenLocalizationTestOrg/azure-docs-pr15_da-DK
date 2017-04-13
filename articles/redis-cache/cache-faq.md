<properties 
    pageTitle="Azure Redis Cache ofte stillede spørgsmål om | Microsoft Azure" 
    description="Lære om svarene på ofte stillede spørgsmål, mønstre og bedste praksis for Azure Redis Cache" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-faq"></a>Azure Redis Cache ofte stillede spørgsmål

Se svar på ofte stillede spørgsmål, mønstre og bedste fremgangsmåder til Azure Redis Cache. 


## <a name="what-if-my-question-isnt-answered-here"></a>Hvad nu, hvis mit spørgsmål ikke besvaret her?

Hvis dit spørgsmål ikke er angivet her, fortælle os, at og vil vi hjælpe dig med at finde et svar.

-   Du kan stille et spørgsmål i [Disqus tråd](#comments) i slutningen af disse ofte stillede spørgsmål og kommunikere med Azure Cache team og andre communitymedlemmer om i denne artikel.
-   Når et bredere publikum, kan du stil et spørgsmål på [Azure Cache MSDN-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) og kommunikere med Azure Cache team og andre medlemmer af gruppen.
-   Hvis du vil gøre anmode om en funktion, kan du sende dine anmodninger og ideer til [Azure Redis Cache bruger tale](https://feedback.azure.com/forums/169382-cache).
-   Du kan også sende en mail til os på [Azure Cache eksterne Feedback](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Grundlæggende om Azure Redis Cache

Ofte stillede spørgsmål i dette afsnit dækker nogle af de grundlæggende regler for Azure Redis Cache.

-    [Hvad er Azure Redis Cache?](#what-is-azure-redis-cache)
-    [Hvordan kan jeg komme i gang med Azure Redis Cache?](#how-can-i-get-started-with-azure-redis-cache)

De følgende ofte stillede spørgsmål dækker grundlæggende begreber og spørgsmål om Azure Redis Cache og besvares i en af de andre sektioner i ofte stillede spørgsmål.

-   [Hvilke Redis Cache tilbyder og størrelse skal jeg bruge?](#what-redis-cache-offering-and-size-should-i-use)
-   [Hvilke Redis cache klienter kan jeg bruge?](#what-redis-cache-clients-can-i-use)
-   [Er der en lokal emulator til Azure Redis Cache?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [Hvordan overvåge systemtilstand og ydeevnen af min cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)



## <a name="planning-faqs"></a>Planlægning af ofte stillede spørgsmål

-   [Hvilke Redis Cache tilbyder og størrelse skal jeg bruge?](#what-redis-cache-offering-and-size-should-i-use)
-   [Azure Redis Cache ydeevne](#azure-redis-cache-performance)
-   [I hvilket område skal jeg finde min cache?](#in-what-region-should-i-locate-my-cache)
-   [Hvordan får jeg faktureret til Azure Redis Cache?](#how-am-i-billed-for-azure-redis-cache)
-   [Kan jeg bruge Azure Redis Cache med Azure Government Cloud eller Azure Kina skyen?](#can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud)



## <a name="development-faqs"></a>Udvikling ofte stillede spørgsmål

-   [Hvad gør StackExchange.Redis konfigurationsindstillinger?](#what-do-the-stackexchangeredis-configuration-options-do)
-   [Hvilke Redis cache klienter kan jeg bruge?](#what-redis-cache-clients-can-i-use)
-   [Er der en lokal emulator til Azure Redis Cache?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [Hvordan kan jeg køre Redis kommandoer?](#how-can-i-run-redis-commands)
-   [Hvorfor Azure Redis Cache har ikke en MSDN klasse bibliotek reference som nogle af de andre Azure tjenester?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
-   [Kan jeg bruge Azure Redis Cache som en PHP session cache?](#can-i-use-azure-redis-cache-as-a-php-session-cache)


## <a name="security-faqs"></a>Ofte stillede spørgsmål om sikkerhed

-   [Hvornår skal jeg aktivere ikke SSL-porten til at oprette forbindelse til Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)


## <a name="production-faqs"></a>Fremstilling ofte stillede spørgsmål

-   [Hvad er nogle gode ideer, fremstilling?](#what-are-some-production-best-practices)
-   [Hvad er nogle af overvejelser, når du bruger almindelige Redis kommandoer?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
-   [Hvordan kan jeg benchmark- og teste ydeevnen for min cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Vigtige oplysninger om trådgruppe forøgelse](#important-details-about-threadpool-growth)
-   [Aktivere server globalt katalog at få flere overførselshastighed på klienten, når du bruger StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)


## <a name="monitoring-and-troubleshooting-faqs"></a>Overvågning og fejlfinding i forbindelse med ofte stillede spørgsmål

Ofte stillede spørgsmål i dette afsnit dækker almindelige overvågning og fejlfinding i forbindelse med spørgsmål. Du kan finde flere oplysninger om overvågning og fejlfinding i forbindelse med din Azure Redis Cache forekomster, se, [hvordan til at overvåge Azure Redis Cache](cache-how-to-monitor.md) og [fejlfinding i forbindelse med Azure Redis Cache](cache-how-to-troubleshoot.md).

-   [Hvordan overvåge systemtilstand og ydeevnen af min cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
-   [Min cache diagnosticering lagerplads kontoindstillinger ændres, Hvad skete der?](#my-cache-diagnostics-storage-account-settings-changed-what-happened)
-   [Hvorfor er diagnosticering aktiveret for nogle ny cache, men ikke andre?](#why-is-diagnostics-enabled-for-some-new-caches-but-not-others)
-   [Hvorfor får jeg vist timeout?](#why-am-i-seeing-timeouts)
-   [Hvorfor blev mine klienten blev afbrudt fra cachen?](#why-was-my-client-disconnected-from-the-cache)


## <a name="prior-cache-offering-faqs"></a>Forudgående Cache tilbyder ofte stillede spørgsmål

-   [Hvilke Azure Cache tilbyder er bedst for mig?](#which-azure-cache-offering-is-right-for-me)


### <a name="what-is-azure-redis-cache"></a>Hvad er Azure Redis Cache?

Azure Redis Cache er baseret på de populære open source- [Redis cache](http://redis.io). Det giver dig adgang til en sikker, dedikeret Redis cache, administreres af Microsoft og tilgængelig fra et program i Azure. Se siden [Azure Redis Cache](https://azure.microsoft.com/services/cache/) produkt på Azure.com for en mere detaljeret oversigt.


### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Hvordan kan jeg komme i gang med Azure Redis Cache?

Der er flere måder, du kan komme i gang med Azure Redis Cache.

-    Du kan se en af vores selvstudier, der er tilgængelige for [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)og [Python](cache-python-get-started.md). 
-    Du kan se, [hvordan du kan oprette høj ydeevne Apps ved hjælp af Microsoft Azure Redis-cachen](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
-    Du kan se i klienten dokumentationen til de klienter, der svarer til sproget i dit projekt for at se, hvordan du bruger Redis udvikling. Der findes mange Redis klienter, der kan bruges med Azure Redis Cache. Du kan finde en liste over Redis klienter [http://redis.io/clients](http://redis.io/clients).


Hvis du ikke allerede har en Azure-konto, kan du:

-    [Åbne en Azure-konto gratis](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Du får kredit, der kan bruges til at teste betalt Azure tjenester. Selvom kreditterne bruges, kan du beholde kontoen og bruge gratis Azure tjenester og funktioner.
-    [Aktivere Visual Studio abonnement fordele](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). MSDN-abonnement giver dig kredit hver måned, som du kan bruge til betalt Azure services.


<a name="cache-size"></a>
### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Hvilke Redis Cache tilbyder og størrelse skal jeg bruge?
Hvert Azure Redis Cache tilbud giver forskellige niveauer af **størrelse**, **båndbredde**, **høj tilgængelighed**og **SLA** indstillinger.

Følgende er overvejelser i forbindelse med valg af en Cache tilbud.

-   **Hukommelse**: den grundlæggende og Standard niveauer tilbyder 250 MB – 53 GB. Premium niveau indeholder op til 530 GB med flere tilgængelige [ønsker](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Se [Azure Redis Cache priser](https://azure.microsoft.com/pricing/details/cache/)kan finde flere oplysninger.
-   **Netværksydeevne**: Hvis du har en arbejdsbyrde, der kræver høj overførselshastighed Premium niveau giver mere båndbredde sammenlignet med Standard eller Basic. Inden for hvert niveau har større størrelser cache også flere båndbredde på grund af den underliggende VM, der er vært cachen. Se [følgende tabel](#cache-performance) kan finde flere oplysninger.
-   **Overførselshastighed**: feltet Premium niveau indeholder det maksimale antal tilgængelige gennemløb. Hvis cachen serveren eller klienten når båndbredde begrænsningerne, får du timeout på klientsiden. Du kan finde yderligere oplysninger i følgende tabel.
-   **Høj tilgængelighed/SLA**: Azure Redis Cache garanterer, at en Standard/Premium cache fås mindst 99,9% af tiden. Hvis du vil vide mere om vores SERVICENIVEAUAFTALE skal du se [Azure Redis Cache priser](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). SERVICENIVEAUAFTALEN dækker kun forbindelse til Cache slutpunkterne. SERVICENIVEAUAFTALEN omhandler ikke beskyttelse mod datatab. Det anbefales at bruge funktionen Redis data brugerdata i Premium lag til at øge fleksibilitet mod datatab.
-   **Redis Data brugerdata**: feltet Premium niveau gør det muligt at bevare cache dataene i en Azure-lager-konto. Alle dataene gemmes i en Basic/Standard cache kun i hukommelsen. I tilfælde af underliggende infrastruktur kan problemer der være muligt tab af data. Det anbefales at bruge funktionen Redis data brugerdata i Premium lag til at øge fleksibilitet mod datatab. Azure Redis Cache tilbyder RDB og AOF (kommer snart) indstillinger i Redis brugerdata. Se, [hvordan du konfigurerer brugerdata i en Premium Azure Redis Cache](cache-how-to-premium-persistence.md)kan finde flere oplysninger.
-   **Redis klynge**: oprette cachelagres større end 53 GB eller til shard data på tværs af flere Redis noder, kan du bruge Redis klynge, som er tilgængelig i Premium lag. Hver node består af et primær/replika cache par for høj tilgængelighed. Se, [hvordan du konfigurerer databaseserver for en Premium Azure Redis Cache](cache-how-to-premium-clustering.md)kan finde flere oplysninger.
-   **Udvidet sikkerhed og netværkets isolation**: Azure virtuelle netværk (VNET) installation indeholder øget sikkerhed og isolationsniveauet til Azure Redis Cache, samt undernet, politik for adgangskontrol, og andre funktioner til yderligere begrænse adgangen. Du kan finde flere oplysninger, se, [hvordan du konfigurere understøttelse af virtuelt netværk for en Premium Azure Redis Cache](cache-how-to-premium-vnet.md).
-   **Konfigurere Redis**: I Standard- og Premium lag, kan du konfigurere Redis til Keyspace meddelelser.
-   **Maksimalt antal klientforbindelser**: feltet Premium niveau indeholder det maksimale antal klienter, der kan oprette forbindelse til Redis med et stort antal forbindelser for større størrelse cache. , [Skal du henvise til siden priser for detaljer](https://azure.microsoft.com/pricing/details/cache/).
-   **Dedikeret Core for Redis Server**: I Premium lag alle cachestørrelser har en dedikeret kerne til Redis. I Basic-/ standarden niveauer i C1 størrelse og have derover en dedikeret kerne til Redis server.
-   **Redis er single-threaded** så har mere end to kerner giver ikke ekstra fordel over med blot to kerner, men VM større har typisk mere båndbredde end mindre størrelser. Hvis cachen serveren eller klienten når båndbredde begrænsningerne, du modtager timeout på klientsiden.
-   **Forbedring af ydeevnen**: cachelagre i Premium lag er installeret på hardware, som har hurtigere processorer og giver bedre ydeevne i forhold til det grundlæggende eller Standard niveau. Premium niveau cachelagre har højere overførselshastighed og nederste latenstider.

<a name="cache-performance"></a>
### <a name="azure-redis-cache-performance"></a>Azure Redis Cache ydeevne

I følgende tabel vises de registrerede under testen forskellige størrelser af Standard maksimale båndbredde værdier og Premium gemmer ved hjælp af `redis-benchmark.exe` fra en Iaas VM mod Azure Redis Cache slutpunkt. Bemærk, at disse værdier ingen er garanti for, og der er ingen SERVICENIVEAUAFTALE for disse tallene, men bør være typisk. Skal du indlæse din egen testprogram til at bestemme cachestørrelsen på højre til dit program.

Vi kan tegne følgende konklusioner fra denne tabel.

-   Overførsel for de cache, der har samme størrelse er højere i Premium lag i forhold til det Standard niveau. For eksempel med en 6 GB Cache er gennemløb af P1 140K RPS sammenlignet med 49 K til C3.
-   Med Redis klynge, øges overførselshastighed lineært, når du øger antallet af shards (noder) i klyngen. Eksempelvis hvis du opretter en P4 klynge af 10 shards, derefter tilgængelige overførselshastigheden er 250K * 10 = 2,5 millioner RPS.
-   Overførsel for større nøglestørrelser er højere i Premium lag i forhold til det Standard niveau.

| Priser niveau             | Størrelse   | CPU-kerner | Tilgængelige båndbredde                                    | 1 KB nøgle størrelse                            |
|--------------------------|--------|-----------|--------------------------------------------------------|------------------------------------------|
| **Standard cachestørrelser** |        |           | **Høj båndbredde per sec (Mb/s) / megabyte per sec (MB/s)** | **Anmodninger om sekundet (RPS)**            |
| C0                       | 250 MB | Delt    | 5 / 0.625                                              | 600                                      |
| C1                       | 1 GB   | 1         | 100 / 12,5                                             | 12200                                    |
| C2                       | 2,5 GB | 2         | 200 / 25                                               | 24000                                    |
| C3                       | 6 GB   | 4         | 400 / 50                                               | 49000                                    |
| C4                       | 13 GB  | 2         | 500 / 62.5                                             | 61000                                    |
| C5                       | 26 GB  | 4         | 1000 / 125                                             | 115000                                   |
| C6                       | 53 GB  | 8         | 2000 / 250                                             | 150000                                   |
| **Premium cachestørrelser**  |        | **CPU-kerner per shard**  |                                         | **Anmodninger om sekundet (RPS) per shard** |
| P1                       | 6 GB   | 2         | 1000 / 125                                             | 140000                                   |
| P2                       | 13 GB  | 4         | 2000 / 250                                             | 220000                                   |
| P3                       | 26 GB  | 4         | 2000 / 250                                             | 220000                                   |
| P4                       | 53 GB  | 8         | 4000 / 500                                             | 250000                                   |


Vejledning i at hente værktøjerne Redis såsom `redis-benchmark.exe`, se den [hvordan kan jeg køre Redis kommandoer?](#cache-commands) sektion.

<a name="cache-region"></a>
### <a name="in-what-region-should-i-locate-my-cache"></a>I hvilket område skal jeg finde min cache?

Find din Azure Redis Cache for bedste ydeevne og de laveste ventetid, i det samme område som klientprogrammet cache.

<a name="cache-billing"></a>
### <a name="how-am-i-billed-for-azure-redis-cache"></a>Hvordan får jeg faktureret til Azure Redis Cache?

Azure Redis Cache priser er [her](https://azure.microsoft.com/pricing/details/cache/). Siden priser viser priser som en hver time rente. Cachelagre faktureres på grundlag-minutters fra det tidspunkt, som oprettes cachen, indtil det tidspunkt, hvor en cache slettes. Der er ikke muligt for stoppe eller afbrydelsen fakturering af en cache.


## <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud"></a>Kan jeg bruge Azure Redis Cache med Azure Government Cloud eller Azure Kina skyen?

Ja, findes Azure Redis cachen i både Azure Government Cloud og Azure Kina skyen. Bemærk, at URL-adresser til at få adgang til og administration af Azure Redis Cache er forskellige i Azure Government Cloud og Azure Kina skyen sammenlignet med Azure offentlige skyen. Se [Azure Government databaser - Azure Redis Cache](../azure-government/documentation-government-services-database.md#azure-redis-cache) og [Azure Kina Cloud - Azure Redis Cache](https://www.azure.cn/documentation/services/redis-cache/)kan finde flere oplysninger om overvejelser i forbindelse med Azure Redis Cache med Azure Government Cloud og Azure Kina skyen.

Oplysninger om brug af Azure Redis Cache med PowerShell i Azure Government Cloud og Azure Kina skyen, se, [hvordan du opretter forbindelse til Azure Government Cloud eller Azure Kina skyen](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


<a name="cache-configuration"></a>
### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Hvad gør StackExchange.Redis konfigurationsindstillinger?

StackExchange.Redis har mange indstillinger. Dette afsnit taler om nogle af de almindelige indstillinger. Du kan finde flere oplysninger om indstillinger for StackExchange.Redis [StackExchange.Redis konfiguration](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

ConfigurationOptions|Beskrivelse|Anbefaling
---|---|---
AbortOnConnectFail|Når indstilles til sand, forbindelsen ikke igen efter en netværksfejl.|Indstillet til falsk, og lade StackExchange.Redis forbinde automatisk.
ConnectRetry|Antallet gange, skal gentages forsøg på forbindelser under indledende Opret forbindelse.| Se følgende noter for at få vejledning. |
ConnectTimeout|Timeout i ms for Forbind handlinger.| Se følgende noter for at få vejledning. |

I de fleste tilfælde standardværdierne af klienten tilstrækkelige. Du kan finjustere de indstillinger, der er baseret på din arbejdsbyrde.

-   **Nye forsøg**
    -   For ConnectRetry og ConnectTimeout er de generelle retningslinjer til mislykkes hurtigt og prøve igen. Dette er baseret på din arbejdsbyrde og hvor lang tid på middelværdien det tager for din klient til at udstede en Redis kommando og har modtaget et svar.
    -   Lade StackExchange.Redis automatisk at genoprette forbindelsen i stedet for at kontrollere forbindelsesstatus og genoprette forbindelse til dig selv. **Undgå brug af egenskaben ConnectionMultiplexer.IsConnected**.
    -   Snowballing – nogle gange kan du støde på et problem, hvor du prøver igen og dette snowballs og genopretter aldrig. I dette tilfælde skal du overveje, ved hjælp af en eksponentiel backoff forsøg algoritme, som beskrevet i [Prøv igen generelle retningslinjer](best-practices-retry-general.md) udgivet af gruppen Microsoft Patterns og fremgangsmåder.
-   **Timeoutværdier**
    -   Overvej at arbejdsbelastningen og angive værdierne i overensstemmelse hermed. Hvis du gemmer store værdier, kan du angive timeouten til en højere værdi.
    -   Angive `AbortOnConnectFail` til falsk, og lad StackExchange.Redis genoprette forbindelsen for dig.
    -   Bruge en enkelt forekomst af ConnectionMultiplexer for programmet. Du kan bruge en LazyConnection til at oprette en enkelt forekomst, der returneres af en forbindelse egenskab, som vist i [Opret forbindelse til den ved hjælp af klassen ConnectionMultiplexer cache](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
    -   Angiv den `ConnectionMultiplexer.ClientName` egenskab til en app forekomst entydigt navn til diagnosticering.
    -   Bruge flere `ConnectionMultiplexer` forekomster til brugerdefineret arbejdsmængder.
    -   Hvis du har forskellige Indlæs i programmet, kan du følge denne model. Eksempel:
    -   Du kan have en mulitplekser til håndtering af store taster.
    -   Du kan have en mulitplekser til håndtering af små taster.
    -   Du kan angive forskellige værdier for forbindelse timeout og retry-logik for hver ConnectionMultiplexer, du bruger.
    -   Angiv den `ClientName` egenskab på hver mulitplekser med hjælp til diagnosticering.
    -   Dette vil føre til mere strømlinet ventetid per `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>Hvilke Redis cache klienter kan jeg bruge?

En af de store fordele ved Redis er, at der er mange klienter, der understøtter mange forskellige udviklingssprog. Se [Redis klienter](http://redis.io/clients)for den aktuelle liste over klienter. Se, [hvordan du bruger Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) selvstudier, der dækker flere forskellige sprog og klienter, og klik på det ønskede sprog på sprog omskifter øverst i denne artikel.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>
### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Er der en lokal emulator til Azure Redis Cache?

Der er ingen lokale emulator for Azure Redis cachen, men du kan køre MSOpenTech versionen af redis-server.exe fra den [Redis kommandolinjen værktøjer](https://github.com/MSOpenTech/redis/releases/) på din lokale computer og oprette forbindelse til den for at få en lignende oplevelse til en lokal cache emulator, som vist i følgende eksempel.


    private static Lazy<ConnectionMultiplexer>
        lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Du kan vælge at konfigurere en [redis.conf](http://redis.io/topics/config) fil for at nærmere svarer til [standard cacheindstillinger](cache-configure.md#default-redis-server-configuration) for din online Azure Redis Cache, hvis du ønsker.

<a name="cache-commands"></a>
### <a name="how-can-i-run-redis-commands"></a>Hvordan kan jeg køre Redis kommandoer?

Du kan bruge en af de kommandoer, der er angivet på [Redis kommandoer](http://redis.io/commands#) med undtagelse af kommandoerne på listen på [Redis kommandoer, der ikke understøttes i Azure Redis Cache](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Hvis du vil køre Redis kommandoer har du forskellige valgmuligheder.

-   Hvis du har en Standard- eller Premium cache, kan du køre Redis kommandoer ved hjælp af [Redis Console](cache-configure.md#redis-console). Dette er en sikker måde til at køre Redis kommandoer i portalen Azure.
-   Du kan også bruge værktøjerne Redis kommandolinjen. Hvis du vil bruge dem, skal du udføre følgende trin.
-   Hent den [Redis kommandolinjen værktøjer](https://github.com/MSOpenTech/redis/releases/).
-   Oprette forbindelse til den cache ved hjælp af `redis-cli.exe`. Sende i den cache slutpunkt ved hjælp af den -h skifte og tasten ved hjælp af - en som vist i følgende eksempel.
-   `redis-cli -h <your cache="" name="">
.redis.cache.windows.net -a <key>
  `
  - Bemærk, at Redis kommandolinjen funktioner ikke fungerer sammen med SSL-porten, men du kan bruge et værktøj som `stunnel` til at oprette sikker forbindelse mellem værktøjerne til SSL-porten ved at følge vejledningen i blogindlægget [Præsentation af ASP.NET Session tilstand Provider til Redis Preview-versionen](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

<a name="cache-reference"></a>
### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Hvorfor Azure Redis Cache har ikke en MSDN klasse bibliotek reference som nogle af de andre Azure tjenester?

Microsoft Azure Redis Cache er baseret på populære Åbn kilden Redis Cache og kan åbnes af en lang række [Redis klienter](http://redis.io/clients) der er tilgængelige for mange programmeringssprog. Hver klient har sin egen API, der gør opkald på forekomsten af Redis cache ved hjælp af [Redis kommandoer](http://redis.io/commands).

Da hver klient er forskellige, er der ikke et centralt klassereference på MSDN; i stedet for opdaterer hver klient sin egen referencedokumentation. Ud over referencedokumentationen er der flere selvstudier, der viser, hvordan du kommer i gang med Azure Redis Cache ved hjælp af forskellige sprog og cache-klienter. Se, [hvordan du bruger Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) for at få adgang til disse selvstudier, og klik på det ønskede sprog på sprog omskifter øverst i denne artikel.


### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Kan jeg bruge Azure Redis Cache som en PHP session cache?

Ja, du kan bruge Azure Redis Cache som en PHP session cache, angive forbindelsesstrengen til din Azure Redis Cache forekomst i `session.save_path`.

>[AZURE.IMPORTANT] Når du bruger Azure Redis Cache som en PHP session cache, skal du URL-adressen kode nøglen bruges til at oprette forbindelse til cachen, som vist i følgende eksempel.
>
>`session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
>Hvis nøglen ikke er URL-kodes, modtager du muligvis en stil med følgende undtagelser:`Failed to parse session.save_path`

Du kan finde flere oplysninger om brug af Redis Cache som en PHP session cache med PhpRedis-klienten, [PHP Session handler](https://github.com/phpredis/phpredis#php-session-handler).



<a name="cache-ssl"></a>
### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Hvornår skal jeg aktivere ikke SSL-porten til at oprette forbindelse til Redis?

Redis serveren understøtter ikke SSL af feltet, men Azure Redis Cache gør. Hvis du opretter forbindelse til Azure Redis Cache og kunden understøtter SSL, som StackExchange.Redis, skal du bruge SSL.

Bemærk, at den ikke-SSL-port er deaktiveret som standard for nye Azure Redis Cache forekomster. Hvis din kunde ikke understøtter SSL, skal du aktivere ikke SSL-porten ved at følge vejledningen i afsnittet [Access porte](cache-configure.md#access-ports) i artiklen [konfigurere et cachen i Azure Redis Cache](cache-configure.md) .

Redis funktioner såsom `redis-cli` fungerer ikke sammen med SSL-porten, men du kan bruge et værktøj som `stunnel` til at oprette sikker forbindelse mellem værktøjerne til SSL-porten ved at følge vejledningen i blogindlægget [Præsentation af ASP.NET Session tilstand Provider til Redis Preview-versionen](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

Finde oplysninger om at hente værktøjerne Redis den [hvordan kan jeg køre Redis kommandoer?](#cache-commands) sektion.



### <a name="what-are-some-production-best-practices"></a>Hvad er nogle gode ideer, fremstilling?

-   [StackExchange.Redis bedste fremgangsmåder](#stackexchangeredis-best-practices)
-   [Konfiguration og begreber](#configuration-and-concepts)
-   [Test af ydeevne](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis bedste fremgangsmåder

-   Angive `AbortConnect` til falsk, og lad derefter ConnectionMultiplexer forbinde automatisk. [Se her for at få oplysninger](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
-   Genbruge ConnectionMultiplexer – ikke oprette et nyt navn for hver enkelt anmodning. Den `Lazy<ConnectionMultiplexer>` mønster [vist her](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) anbefales.
-   Redis fungerer bedst med mindre værdier, så skal du overveje fisken større data i flere taster op. I [dette Redis diskussion](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)betragtes 100 kb "store". Læs [denne artikel](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) for at få en eksempel-problem, der kan skyldes store værdier.
-   Konfigurere [Indstillinger for trådgruppe](#important-details-about-threadpool-growth) for at undgå timeout.
-   Brug mindst standard connectTimeout på 5 sekunder. Dette vil give StackExchange.Redis tilstrækkelig tid til at genoprette forbindelsen, i tilfælde af et netværk blip.
-   Vær opmærksom på de ydeevne omkostninger, der er knyttet til forskellige handlinger, du kører. For eksempel på `KEYS` kommandoen er handlingen O(n) og undgås. [Redis.io websted](http://redis.io/commands/) indeholder oplysninger om tid kompleksitet for hver handling, der understøtter. Klik på hver kommando for at se kompleksitet for hver handling.

#### <a name="configuration-and-concepts"></a>Konfiguration og begreber

-   Brug Standard eller Premium niveau til fremstilling systemer. De grundlæggende trin er et enkelt node system med ingen replikering af data og ingen SERVICENIVEAUAFTALE. Også bruge mindst en C1 cache. C0 cache er virkelig beregnet til enkel Udviklingscenter/afprøve scenarier.
-   Husk, at Redis er et **I hukommelsen** datalager. Læs [denne artikel](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) , så du kender scenarier, hvor data kan gå tabt.
-   Udvikle dit system, så den kan håndtere forbindelse blips [på grund af rettelse og belastningsjustering](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Test af ydeevne

-   Start ved hjælp af `redis-benchmark.exe` til at få føling mulige overførselshastighed før skrive dine egne performance tester. Bemærk Denne redis-benchmark ikke understøtter SSL, så du har til at [aktivere ikke-SSL-porten via Azure-portalen](cache-configure.md#access-ports) , før du kører en test. Du kan se eksempler [hvordan kan jeg benchmark- og teste ydeevnen for min cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   Klienten VM, der bruges til at teste skal være i samme område som din Redis cache forekomst.
-   Det anbefales at bruge Dv2 VM serie for din klient, som de har bedre hardware og giver de bedste resultater.
-   Sørg for din klient VM, du vælger har mindst meget computing og båndbredde mulighed som cachen du tester. 
-   Aktivere VRSS på klientcomputeren, hvis du er i Windows. [Se her for at få oplysninger](https://technet.microsoft.com/library/dn383582.aspx).
-   Premium niveau Redis forekomster vil har bedre netværk ventetid og overførselshastighed fordi kører på bedre hardware til både CPU og netværk.

<a name="cache-redis-commands"></a>
### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Hvad er nogle af overvejelser, når du bruger almindelige Redis kommandoer?

-   Du bør kunne ikke køre bestemte Redis kommandoer, tage lang tid at fuldføre uden at forstå påvirkningen af disse kommandoer.
    -   For eksempel ikke kan køre kommandoen [TASTER](http://redis.io/commands/keys) i fremstilling som det kan tage lang tid til at returnere afhængigt af antallet taster. Redis er en enkelt-threaded server, og det behandler kommandoer én ad gangen. Hvis du har andre kommandoer, der er udstedt efter TASTER, er de ikke behandlet, indtil Redis behandler kommandoen TASTER. [Redis.io websted](http://redis.io/commands/) indeholder oplysninger om tid kompleksitet for hver handling, der understøtter. Klik på hver kommando for at se kompleksitet for hver handling.
-   Nøglestørrelser - skal jeg bruge, lille nøgleværdier eller stor nøgleværdier? Generelt afhænger det af scenariet. Hvis scenariet kræver større taster kan du justere ConnectionTimeout og prøv igen værdier og justere din forsøg. Fra et Redis server perspektiv mindre værdier skal være opfyldt have bedre ydeevne.
-   Det betyder ikke, at du ikke kan gemme større værdier i Redis; Du skal være opmærksom på følgende overvejelser. Latenstider vil være højere. Hvis du har ét sæt data, der er større og en, der er mindre, kan du bruge flere forekomster af ConnectionMultiplexer, hver konfigureret med en anden række timeout, og prøv igen værdier, som det er beskrevet i forrige afsnit for [Hvad konfigurationsindstillinger StackExchange.Redis gør](#cache-configuration) .



<a name="cache-benchmarking"></a>
### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hvordan kan jeg benchmark- og teste ydeevnen for min cache?

-   [Aktivere cachen diagnostics](cache-how-to-monitor.md#enable-cache-diagnostics) , så du kan [overvåge](cache-how-to-monitor.md) tilstanden for din cache. Du kan få vist målene på portalen Azure, og du kan også [hente og gennemse](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) dem ved hjælp af værktøjerne efter eget valg.
-   Du kan bruge redis benchmark.exe for at indlæse test din Redis server.
-   Sørg for, at afkrydsningsfeltet Indlæs test af klienten og Redis cachen er i samme område.
-   Brug redis cli.exe og overvåge cachen ved hjælp af kommandoen oplysninger.
-   Hvis din Indlæs forårsager høj hukommelsesfragmentering bør derefter du skalere op til en større cache.
-   Finde oplysninger om at hente værktøjerne Redis den [hvordan kan jeg køre Redis kommandoer?](#cache-commands) sektion.

Følgende er et eksempel på brug af redis benchmark.exe. For præcise resultater, du Kør denne kommando fra en VM i det samme område som din cache.

-   Test Pipelined angive anmodninger ved hjælp af en 1 k-data

    redis benchmark.exe - h **yourcache**. redis.cache.windows.net - en **yourAccesskey** -t angive 1024 - P 50 - n 1000000 -d
    
-   Test Pipelined få anmodninger, der anvender en 1 k-data. 
    Bemærk: Køre SÆTTET teste vist over først at udfylde cache
    
    redis benchmark.exe - h **yourcache**. redis.cache.windows.net - en **yourAccesskey** -t få -n 1000000 - d 1024 - P 50

<a name="threadpool"></a>
### <a name="important-details-about-threadpool-growth"></a>Vigtige oplysninger om trådgruppe forøgelse

CLR-trådgruppe har to typer threads - "Arbejder" og "I/o fuldførelsesport" (også kendt IOCP) tråde. 

-   Der bruges som tråde når for ting som behandling `Task.Run(…)` eller `ThreadPool.QueueUserWorkItem(…)` metoder. Disse tråde bruges også af forskellige komponenter i CLR, når arbejdet skal udføres på en baggrundstråd.
-   IOCP tråde anvendes, når asynkron EY sker (fx læse fra netværket). 

Tråd puljen indeholder nye arbejdstråde eller i/o-fuldførelse tråde efter behov (uden en hvilken som helst throttling) indtil den når indstillingen "Minimum" for hver type af tråd. Det mindste antal tråde er som standard angivet til antallet processorer på et system. 

Når antallet af eksisterende (optaget) tråde besøg "mindste" antallet af tråde, trådgruppe vil fra begrænsning hastighed, som er serveren nye tråde til én tråd per 500 millisekunder. Det betyder, at hvis systemet får en burst af arbejde, der er brug for en IOCP tråd, behandler den, der fungerer meget hurtigt. Men hvis burst af arbejdet, er større end indstillingen konfigureret "Minimum", der bliver nogle forsinkelse i processing del af arbejdet, som trådgruppe venter på en af to ting til at opstå.

1. En eksisterende tråd bliver gratis at behandle arbejdet.
1. Ingen eksisterende tråd bliver gratis 500ms, så der oprettes en ny tråd.

Grundlæggende, betyder det, når antallet optaget tråde er større end Min tråde, skal du sandsynligvis betaler en 500ms forsinkelse før netværkstrafik er behandlet af programmet. Det er også vigtigt at huske, når en eksisterende tråd forbliver inaktiv i mere end 15 sekunder (baseret på hvad jeg huske), der ryddes op og kan gentage denne cyklus af vækst og krympning.

Hvis vi se på et eksempel på fejlmeddelelse fra StackExchange.Redis (opbygge 1.0.450 eller nyere), vises, udskrives den nu trådgruppe statistik (se IOCP og arbejder nedenfor).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
    IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Du kan se, der er 6 optaget tråde for IOCP tråd og systemet er konfigureret til at tillade 4 minimum tråde i eksemplet ovenfor. I dette tilfælde klienten ville har sandsynligvis set to 500 ms forsinkelser fordi 6 > 4.

Bemærk, at StackExchange.Redis kan ramme timeout, hvis vækst af IOCP eller arbejder tråde bliver begrænset.

### <a name="recommendation"></a>Anbefaling

Ud fra disse oplysninger, anbefaler vi, at kunder værdien mindste konfiguration for IOCP og arbejder tråde til noget, der er større end standardværdien. Vi kan ikke give generel vejledning på hvad denne værdi skal være, fordi den korrekte værdi for ét program bliver for høj/lav til et andet program. Denne indstilling kan også påvirke ydeevnen for andre dele af komplicerede programmer, så hver kunden har brug at finjustere denne indstilling til deres særlige behov. Et godt udgangspunkt er 200 eller 300, og derefter teste og tilpasse efter behov.

Sådan konfigureres denne indstilling:

-   I ASP.NET, bruge ["minIoThreads" denne indstilling][] under den `<processModel>` konfigurationselementet i web.config. Hvis du kører inde i Azure websteder, vises denne indstilling ikke gennem konfigurationsindstillingerne. Du skal dog stadig ikke kunne angive ud fra et program (se nedenfor) fra din Application_Start metode i global.asax.cs.

> **Vigtig bemærkning:** den værdi, der er angivet i dette konfiguration element er en *per core -* indstilling. Eksempelvis hvis du har en 4 core maskine og vil indstillingen minIOThreads skal være 200 på kørselstidspunktet, du vil bruge `<processModel minIoThreads="50"/>`.

-   Uden for ASP.NET, bruge [ThreadPool.SetMinThreads(...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API.

<a name="server-gc"></a>
### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Aktivere server globalt katalog at få flere overførselshastighed på klienten, når du bruger StackExchange.Redis

Aktivere server globalt katalog kan optimere klienten og give bedre ydeevne og overførselshastighed, når du bruger StackExchange.Redis. Du kan finde flere oplysninger om server globalt katalog, og hvordan du aktivere den, i følgende artikler.

-   [Aktivere server globalt katalog](https://msdn.microsoft.com/library/ms229357.aspx)
-   [Grundlæggende regler for Spildopsamling](https://msdn.microsoft.com/library/ee787088.aspx)
-   [Spildopsamling og ydeevne](https://msdn.microsoft.com/library/ee851764.aspx)







<a name="cache-monitor"></a>
### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hvordan overvåge systemtilstand og ydeevnen af min cache?

Microsoft Azure Redis Cache forekomster kan overvåges i [Azure-portalen](https://portal.azure.com). Du kan se målepunkter, fastgøre målepunkter diagrammer til Startboard, tilpasse dato- og klokkeslætsintervallet for overvågning diagrammer, tilføje og fjerne målepunkter fra diagrammerne og angive vigtige beskeder, når bestemte betingelser er opfyldt. Du kan finde yderligere oplysninger finder [Skærm Azure Redis Cache](cache-how-to-monitor.md).

Afsnittet **Support + fejlfinding** af bladet Redis Cache **Indstillinger** indeholder også flere værktøjer til overvågning og fejlfinding i forbindelse med din cache. 

-   **Fejlfinding i forbindelse med** indeholder oplysninger om almindelige problemer og strategier for at løse dem.
-   **Overvågningslogge** indeholder oplysninger om handlinger, der udføres på din cache. Du kan også bruge filtrering til at udvide denne visning for at medtage andre ressourcer.
-   **Ressource tilstand** ser din ressource og fortæller dig, hvis den kører som forventet. Du kan finde flere oplysninger om tjenesten Azure ressource tilstand, [sundhed Azure Ressourceoversigt](../resource-health/resource-health-overview.md).
-   **Ny understøtter anmodning** indeholder indstillinger for at åbne en supportanmodning for din cache.

Disse funktioner gør det muligt at overvåge tilstanden for din Azure Redis Cache forekomster og hjælpe dig med at administrere dine cachelagring programmer. Få mere at vide under [Support & fejlfinding i forbindelse med indstillinger](cache-configure.md#support-amp-troubleshooting-settings).

### <a name="my-cache-diagnostics-storage-account-settings-changed-what-happened"></a>Min cache diagnosticering lagerplads kontoindstillinger ændres, Hvad skete der?

Cachelagre i samme region og abonnement dele de samme diagnosticering lagerplads indstillinger, og hvis konfigurationen er ændret (Diagnosticering aktiveret/deaktiveret eller ændre kontoen lagerplads) gælder for alle cache i abonnementet, der er i den pågældende region. Hvis diagnosticeringsindstillingerne for din cache har ændret, Kontrollér, om diagnosticering indstillingerne for en anden cachen i den samme abonnement og område er ændret. En metode til at kontrollere er at få vist overvågningsloggene for din cache for en `Write DiagnosticSettings` begivenhed. Du kan finde flere oplysninger om at arbejde med overvågningslogge se [se begivenheder og overvågningsloggen](../monitoring-and-diagnostics/insights-debugging-with-events.md) og [overvåge forskellige handlinger med ressourcestyring](../resource-group-audit.md). Du kan finde flere oplysninger om overvågning Azure Redis Cache begivenheder, se [Handlinger og beskeder](cache-how-to-monitor.md#operations-and-alerts).

### <a name="why-is-diagnostics-enabled-for-some-new-caches-but-not-others"></a>Hvorfor er diagnosticering aktiveret for nogle ny cache, men ikke andre?

Cachelagre i samme område og abonnement dele de samme indstillinger for diagnosticering lagerplads. Hvis du opretter en ny cache i samme område og abonnement som en anden cache, der indeholder diagnosticering aktiveret, aktiveres diagnosticering på ny cache ved hjælp af de samme indstillinger.


<a name="cache-timeouts"></a>
### <a name="why-am-i-seeing-timeouts"></a>Hvorfor får jeg vist timeout?

Der sker timeout i den klient, som du kan bruge til at tale med Redis. For det meste understøtter Redis server ikke timeout. Når en kommando, der sendes til Redis-server, kommandoen er i kø, Redis server til sidst henter kommandoen og udfører den. Men klienten kan timeout under denne proces, og hvis det er tilfældet en undtagelse er opløftet i opkald side. Se [fejlfinding af klienten side](cache-how-to-troubleshoot.md#client-side-troubleshooting) og [StackExchange.Redis timeout undtagelser](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions)kan finde flere oplysninger om fejlfinding af problemer med timeout.


<a name="cache-disconnect"></a>
### <a name="why-was-my-client-disconnected-from-the-cache"></a>Hvorfor blev mine klienten blev afbrudt fra cachen?

Følgende er nogle almindelige årsag til en cache Afbryd forbindelse.

-   Klientsiden årsager
    -   Klientprogrammet blev genaktiveres.
    -   Klientprogrammet udført en handling af skalering.
        -   Cloud Services eller Web Apps, kan det skyldes automatisk skalering.
    -   Netværk laget på klientsiden ændres.
    -   Der opstod midlertidige fejl i klienten eller i noderne netværk mellem klienten og serveren.
    -   Båndbredde grænseværdi begrænsningerne er nået.
    -   CPU bundet handlinger tog lang tid at fuldføre.
-   Serversiden årsager
    -   På den standard cache med tilbud, startes cachetjeneste Azure Redis en fail-over fra den primære node til den sekundære node.
    -   Azure retter den forekomst, hvor cachen blev installeret
        -   Det kan skyldes Redis serveropdateringer eller generelle VM vedligeholdelse.







### <a name="which-azure-cache-offering-is-right-for-me"></a>Hvilke Azure Cache tilbyder er bedst for mig?

>[AZURE.IMPORTANT]I henhold til sidste år [meddelelse](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), skal Azure administrerede Cachetjeneste og Azure i rolle Cache service udgå på 30 November 2016. Vores anbefaling er at bruge [Azure Redis Cache](https://azure.microsoft.com/services/cache/). Du kan finde oplysninger om overflytning [overførsel fra administrerede Cachetjeneste til Azure Redis Cache](cache-migrate-to-redis.md).

### <a name="azure-redis-cache"></a>Azure Redis Cache
Azure Redis Cache er alment tilgængelig i størrelse op til 53 GB og har en tilgængelighed SLA 99,9%. Det nye [premium niveau](cache-premium-tier-intro.md) indeholder størrelser op til 530 GB og understøttelse af klynge, VNET og brugerdata, med en 99,9% SLA.

Azure Redis Cache giver kunder muligheden for at bruge en sikker, dedikeret Redis cache, administreres af Microsoft. Med dette tilbud får du udnytte de mange funktioner og økosystem, der leveres af Redis, og pålidelig vært og overvåge fra Microsoft.

I modsætning til traditionelle cachelagre som håndtere kun nøgle-værdi-par, er Redis populære til dens meget performant datatyper. Redis også understøtter kørsel af atomisk handlinger på disse filtyper, som føjes til en streng stigende værdien i en hash trykke på en liste Computing sæt skæringspunkt, union og forskellen; eller få medlemmet med højeste placering i et sorteret sæt. Andre funktioner omfatter understøttelse af transaktioner, pub/sub, Lua scripting, taster med et begrænset time-to-live, og indstillinger for søgekonfiguration til at foretage Redis opfører sig mere som en traditionel cache.

En anden vigtige højde for Redis succes er sund, levende Åbn kilde økosystemet indbygget rundt om den. Dette afspejles i det forskelligartede sæt Redis-klienter, der er tilgængelige på tværs af flere sprog. Så det kan anvendes af næsten alle arbejdsbelastningen, du vil oprette inde i Azure. 

Se [Sådan Brug Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) og [Azure Redis Cache dokumentation](https://azure.microsoft.com/documentation/services/redis-cache/)kan finde flere oplysninger om Introduktion til Azure Redis Cache.

### <a name="managed-cache-service"></a>Administrerede cachetjeneste
[Administreret tjeneste er indstillet til udgå 30 November 2016 Cache.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

### <a name="in-role-cache"></a>I rolle Cache
[I rolle Cache er angivet til udgå 30 November 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)





["minIoThreads" denne indstilling]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
