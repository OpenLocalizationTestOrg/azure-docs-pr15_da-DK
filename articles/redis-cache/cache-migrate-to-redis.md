<properties 
    pageTitle="Cache overføre til Redis | Microsoft Azure"
    description="Lær at overføre administrerede Cachetjeneste programmer til Azure Redis Cache"
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
    ms.date="09/30/2016"
    ms.author="sdanie" />

# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Overføre fra administrerede Cachetjeneste til Azure Redis Cache

Overføre dine programmer, der bruger Azure administrerede Cachetjeneste til Azure Redis Cache kan gøres med minimale ændringer i dit program, afhængigt af de administrerede Cachetjeneste funktioner, der bruges af din cachelagring program. Mens API'erne ikke er ens de ligner, og meget af din eksisterende kode, der bruger administrerede Cachetjeneste til at få adgang til en cache kan genbruges med minimale ændringer. Dette emne viser, hvordan du gør det er nødvendigt konfiguration og programmet ændringer til at overføre din administrerede Cachetjeneste programmer til at bruge Azure Redis cachen, og viser, hvordan nogle af funktionerne i Azure Redis Cache kan bruges til at implementere funktionaliteten i en administreret Cachetjeneste cache.

## <a name="migration-steps"></a>Overførselstrin

Følgende trin er påkrævet til at overføre et administrerede Cache-tjenesteprogram bruge Azure Redis Cache.

-   Knytte administrerede Cachetjeneste funktioner til Azure Redis Cache
-   Vælg en Cache-tilbud
-   Oprette en Cache
-   Konfigurere Cache-klienter
    -   Fjerne tjenestekonfiguration administrerede Cache
    -   Konfigurere en cache-klient ved hjælp af StackExchange.Redis NuGet pakke
-   Overføre administrerede Cachetjeneste kode
    -   Oprette forbindelse til den ved hjælp af klassen ConnectionMultiplexer cache
    -   Access enkle datatyper i cachen
    -   Arbejde med .NET objekter i cachen
-   Overføre ASP.NET Session tilstand og outputcachelagring til Azure Redis Cache 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Knytte administrerede Cachetjeneste funktioner til Azure Redis Cache

Azure administrerede Cachetjeneste og Azure Redis Cache ligner, men implementere nogle af deres funktioner på forskellige måder. Dette afsnit beskrives nogle af forskellene og giver vejledning til implementering af funktioner i administrerede Cachetjeneste i Azure Redis Cache.

|Administrerede Cachetjeneste funktion|Administreret Cachetjeneste support|Azure Redis Cache-support|
|---|---|---|
|Navngivet cache|En standard-cache er konfigureret, og i Standard- og Premium cachen tilbud, op til ni yderligere med navnet cache kan konfigureres, hvis du ønsker.|Azure Redis cache har en konfigurerbar antal databaser (standard 16), der kan bruges til at implementere en tilsvarende funktionalitet til navngivne cache. Du kan finde flere oplysninger [standard Redis serverkonfiguration](cache-configure.md#default-redis-server-configuration).|
|Høj tilgængelighed|Giver høj tilgængelighed til elementer i cachen i Standard- og Premium cache tilbud. Hvis elementer går tabt på grund af en mislykket, er sikkerhedskopier af elementer i cachen stadig tilgængelige. Skriver til sekundær cachen foretages synkront.|Høj tilgængelighed er tilgængelig i de Standard og Premium cache tilbud, der har en konfiguration med to node primær/replika (hver shard i en Premium cache har et par primær/replika). Skriver til replikaen foretages asynkront. Se [Azure Redis Cache priser](https://azure.microsoft.com/pricing/details/cache/)kan finde flere oplysninger.|
|Beskeder|Tillader klienter at modtage asynkron beskeder, når der optræder en række forskellige cache handlinger på en navngivet cache.|Klientprogrammer kan bruge Redis pub/sub eller [Keyspace beskeder](cache-configure.md#keyspace-notifications-advanced-settings) til at opnå en tilsvarende funktionalitet til meddelelser.|
|Lokal cache|Gemmer en kopi af cachelagrede objekter lokalt på klienten til meget hurtig adgang.|Klientprogrammer har brug at implementere denne funktionalitet ved hjælp af en ordbog eller lignende datastruktur.|
|Eviction politik|Ingen eller LRU. Standardpolitikken er LRU.|Azure Redis Cache understøtter følgende eviction politikker: permanente lru allkeys lru permanente tilfældige, allkeys-tilfældige permanente-ttl, og noeviction. Standardpolitikken er permanente lru. Du kan finde flere oplysninger [standard Redis serverkonfiguration](cache-configure.md#default-redis-server-configuration).|
|Udløbspolitikken|Udløbspolitikken standard er absolutte og udløb Standardinterval er ti minutter. Hvis du flytter og aldrig politikker er også tilgængelige.|Som standard elementer i cachen udløber ikke, men en udløb kan konfigureres på grundlag per skrive ved hjælp af cache sæt overloads. Du kan finde yderligere oplysninger finder [Tilføj og Hent objekter fra cachen](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache).|
|Områder og mærkning|Områder er undergrupper for cachelagrede elementer. Områder understøtter også anmærkningen cachelagrede elementer med flere beskrivende strenge af titlen mærker. Områder understøtter muligheden for at udføre søgningen handlinger på en hvilken som helst mærkede elementer i det pågældende område. Alle elementer i et område er placeret i en enkelt node i cachen klynge.|En Redis cache består af en enkelt node, (medmindre Redis klynge er aktiveret) så begrebet administrerede Cachetjeneste områder ikke gælder. Redis understøtter søgning og jokertegn handlinger, når du henter taster, så beskrivende mærker kan integreret i vigtige navnene og bruges til at hente elementerne senere. Et eksempel på implementere en mærkning løsning ved hjælp af Redis, finde [implementere cache mærkning med Redis](http://stackify.com/implementing-cache-tagging-redis/).|
|Serialisering|Administrerede Cache understøtter NetDataContractSerializer, BinaryFormatter og brug af brugerdefinerede serializers. Standard er NetDataContractSerializer.|Det er ansvarlig for klientprogrammet og sekventielt .NET objekter før placere dem i cachen, med valg af serialiseringsfunktion op til klienten programmet udvikler. Flere oplysninger og eksempler på kode, du se [arbejde med .NET objekter i cachen](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).|
| Cache emulator | Administrerede Cache giver en lokal cache emulator. | Azure Redis Cache har ikke en emulator, men du kan [køre MSOpenTech Opret af redis-server.exe lokalt](cache-faq.md#cache-emulator) for at give en emulator oplevelse. |

## <a name="choose-a-cache-offering"></a>Vælg en Cache-tilbud

Microsoft Azure Redis Cache findes i de følgende lag:

-   **Grundlæggende** – enkelt node. Flere størrelse op til 53 GB.
-   **Standard** – to noder primær/replika. Flere størrelse op til 53 GB. 99,9% SERVICENIVEAUAFTALE.
-   **Premium** – to noder primær/replika med op til 10 shards. Flere størrelser fra 6 GB til 530 GB (Kontakt os med at få mere at vide). Alle Standard niveau funktioner og mere herunder understøttelse af [Redis klynge](cache-how-to-premium-clustering.md), [Redis brugerdata](cache-how-to-premium-persistence.md)og [Azure virtuelt netværk](cache-how-to-premium-vnet.md). 99,9% SERVICENIVEAUAFTALE.

Hvert niveau varierer i forhold til funktioner og priser. Funktionerne er dækket senere i denne vejledning og yderligere oplysninger om priser, skal du se [Cache priser detaljer](https://azure.microsoft.com/pricing/details/cache/).

Et udgangspunkt til overførsel er at vælge den størrelse, der svarer til størrelsen af din tidligere administrerede Cachetjeneste cache, og derefter skalere op eller ned afhængigt af dit program krav. Have mere at vide om valg af det rigtige Azure Redis Cache tilbud, se [Hvad Redis Cache tilbyder og størrelse skal jeg bruge?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Oprette en Cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurere Cache-klienter

Når cachen er oprettet og konfigureret, er næste trin er at fjerne den administrerede Cachetjeneste konfiguration, og Tilføj Tilføj Azure Redis Cache konfigurationen og refererer til så cache kunder kan få adgang til cachen.

-   Fjerne tjenestekonfiguration administrerede Cache
-   Konfigurere en cache-klient ved hjælp af StackExchange.Redis NuGet pakke

### <a name="remove-the-managed-cache-service-configuration"></a>Fjerne tjenestekonfiguration administrerede Cache

Før klientprogrammerne kan konfigureres til Azure Redis Cache, den eksisterende administrerede Cachetjeneste konfiguration og samling referencer skal fjernes ved at fjerne pakken administrerede Cache Service NuGet.

Fjerne pakken administrerede Cache Service NuGet, højreklik på klientprojektet i **Solution Explorer** , og vælg **Administrer NuGet pakker**. Vælg noden **installerede pakker** , og skriv W**indowsAzure.Caching** i feltet Søg efter installerede pakker. Vælg **Windows** **Azure Cache** (eller **Windows** **Azure cachelagring** afhængigt af versionen af pakken NuGet), skal du klikke på **Fjern**, og klik derefter på **Luk**.

![Fjerne Azure administrerede Cache NuGet servicepakke](./media/cache-migrate-to-redis/IC757666.jpg)

Fjernelse af administrerede Cache Service NuGet pakken fjerner administrerede Cachetjeneste samlinger og administrerede Cachetjeneste posterne i app.config eller web.config af klientprogrammet. Da nogle tilpassede indstillinger ikke muligvis kan fjernes, når du fjerner pakken NuGet, Åbn web.config eller app.config og sikre, at følgende elementer er helt fjernet.

Sørg for, at den `dataCacheClients` fjernes posten fra den `configSections` element. Fjerner ikke hele `configSections` element; kun fjerne den `dataCacheClients` posten, hvis det er til stede.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
    </configSections>

Sørg for, at den `dataCacheClients` sektion fjernes. Den `dataCacheClients` sektionen vil være ligner det følgende eksempel.

    <dataCacheClients>
      <dataCacheClientname="default">
        <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
        <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
        <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="true">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Når den administrerede Cachetjeneste konfiguration er fjernet, kan du konfigurere cache-klient, som beskrevet i følgende afsnit.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurere en cache-klient ved hjælp af pakken StackExchange.Redis NuGet

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Overføre administrerede Cachetjeneste kode

API til StackExchange.Redis cache klienten ligner Cachetjeneste administreres. Dette afsnit indeholder en oversigt over forskellene.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Oprette forbindelse til den ved hjælp af klassen ConnectionMultiplexer cache

I administrerede Cachetjeneste forbindelser til cachen blev håndteret af den `DataCacheFactory` og `DataCache` klasser. I Azure Redis cachen disse forbindelser, der administreres af den `ConnectionMultiplexer` klasse.

Tilføj følgende ved hjælp af sætning til toppen af en fil, du vil have adgang til cachen.

    using StackExchange.Redis
                                
Hvis dette navneområde ikke løser, skal du sørge for, at du har tilføjet pakken StackExchange.Redis NuGet, som beskrevet i [konfigurere cache-klienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

>[AZURE.NOTE] Bemærk, at StackExchange.Redis klienten kræver .NET Framework 4 eller nyere.

For at oprette forbindelse til en Azure Redis Cache-forekomst, ringe statiske `ConnectionMultiplexer.Connect` metode og gang i den første eller sidste ark og nøgle. En metode til deling af en `ConnectionMultiplexer` forekomst i dit program er at have en statisk egenskab, der returnerer en forbundne forekomst, svarende til følgende eksempel. Dette er en tråd sikker måde initialiseret kun en enkelt forbindelse `ConnectionMultiplexer` forekomst. I dette eksempel `abortConnect` er indstillet til falsk, hvilket betyder, at opkaldet lykkes, selvom der ikke oprettes forbindelse til cachen. En vigtige funktion af `ConnectionMultiplexer` er, at det vil automatisk genoprette forbindelsen til cachen én gang netværksproblem eller andre årsager er løst.

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

Cache slutpunkt, nøgler og porte kan fås fra bladet **Redis Cache** for din cache forekomst. Du kan finde yderligere oplysninger finder [Redis Cache egenskaber](cache-configure.md#properties).

Når forbindelsen er oprettet, returnerer en reference til Redis cache-databasen ved at ringe på `ConnectionMultiplexer.GetDatabase` metode. Det objekt, der returneres fra den `GetDatabase` metode er et lette pass-through-objekt og behøver ikke at blive gemt.

    IDatabase cache = Connection.GetDatabase();
    
    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);
    
    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

StackExchange.Redis klienten bruger den `RedisKey` og `RedisValue` typer for at få adgang til og gemme elementer i cachen. Disse filtyper Afbild til mest enkle sprogtyper, herunder streng, og ofte bruges ikke direkte. Redis strenge er den mest grundlæggende type Redis værdi og kan indeholde mange typer data, herunder fortløbende binære streams, og du kan ikke bruge typen direkte, du vil bruge metoder, der indeholder `String` i navnet. For mest enkle datatyperne gemme og hente elementer fra den cache ved hjælp af den `StringSet` og `StringGet` metoder, medmindre du gemmer af websteder eller andre Redis datatyper i cachen. 

`StringSet`og `StringGet` ligner meget de Cachetjeneste administrerede `Put` og `Get` metoder, med en større forskel er, at før du angive og få et .NET objekt i cachen skal du sekventielt det først. 

Når du ringer til `StringGet`, hvis objektet findes, returneres, og hvis det ikke er tilfældet, returneres null. I dette tilfælde kan du hente værdien fra den ønskede datakilde og gemme dem i cachen til senere brug. Dette kaldes mønstret cache-side.

Angive udløb af et element i cachen, skal du bruge den `TimeSpan` -parameter for `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

Azure Redis Cache kan arbejde med .NET objekter samt enkle datatyper, men før et .NET objekt kan cachelagres det skal serialiseres. Dette er ansvarlig for programmet udvikler. Det giver udvikler fleksibilitet valg af serialiseringsfunktionen. Flere oplysninger og eksempler på kode, du se [arbejde med .NET objekter i cachen](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Overføre ASP.NET Session tilstand og outputcachelagring til Azure Redis Cache

Azure Redis Cache har udbydere til både ASP.NET Session tilstand og siden Output cachelagring. Hvis du vil overføre dit program, der bruger administrerede Cachetjeneste versioner af disse udbydere, først fjerne de eksisterende sektioner fra din web.config og derefter konfigurere Azure Redis Cache versionerne af udbyderne af. Flere oplysninger om brug af udbyderne af Azure Redis Cache ASP.NET, under [ASP.NET Session tilstand Provider til Azure Redis Cache](cache-aspnet-session-state-provider.md) og [ASP.NET Output Cache-Provider til Azure Redis Cache](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Næste trin

Udforsk [Azure Redis Cache dokumentation](https://azure.microsoft.com/documentation/services/cache/) til selvstudier, eksempler, videoer og meget mere.

