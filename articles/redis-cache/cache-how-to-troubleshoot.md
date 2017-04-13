<properties 
    pageTitle="Fejlfinding i forbindelse med Azure Redis Cache | Microsoft Azure" 
    description="Lær, hvordan du kan løse almindelige problemer med Azure Redis Cache." 
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
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-troubleshoot-azure-redis-cache"></a>Fejlfinding i forbindelse med Azure Redis Cache

I denne artikel indeholder en vejledning til fejlfinding i forbindelse med følgende kategorier af Azure Redis Cache problemer.

-   [Klient side fejlfinding](#client-side-troubleshooting) – i dette afsnit får du retningslinjer på identificere og løse problemer, der er forårsaget af programmet til at oprette forbindelse til Azure Redis Cache.
-   [Server side fejlfinding](#server-side-troubleshooting) – i dette afsnit får du retningslinjer på identificere og løse problemer, der er forårsaget på serversiden Azure Redis Cache.
-   [StackExchange.Redis timeout undtagelser](#stackexchangeredis-timeout-exceptions) – i dette afsnit indeholder oplysninger om fejlfinding af problemer ved hjælp af StackExchange.Redis-klienten.


>[AZURE.NOTE] Flere fejlfindingstrin i denne vejledning indeholder instruktioner til at køre Redis kommandoer og overvåge forskellige ydeevne målepunkter. Se artiklerne i afsnittet [Yderligere oplysninger](#additional-information) for flere oplysninger og vejledninger.

## <a name="client-side-troubleshooting"></a>Fejlfinding af klienten side


Dette afsnit beskrives fejlfinding i forbindelse med problemer, der kan opstår på grund af en betingelse på klientprogrammet.

-   [Hukommelsestrykket på klienten](#memory-pressure-on-the-client)
-   [Burst af trafik](#burst-of-traffic)
-   [Høj klient CPU-brug](#high-client-cpu-usage)
-   [Klient Side båndbredde er overskredet](#client-side-bandwidth-exceeded)
-   [Store anmodning/svar størrelse](#large-requestresponse-size)
-   [Hvad skete der med mine data i Redis?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Hukommelsestrykket på klienten

#### <a name="problem"></a>Problem

Hukommelsestrykket på klientmaskinen fører til alle typer problemer med ydeevnen, der kan forsinke behandling af data, der blev sendt af forekomsten Redis uden forsinkelser. Når hukommelsesforbrug rammer, har systemet typisk til siden data fra den fysiske hukommelse til virtuel hukommelse, som er på disken. Denne *side fejlagtigt* får systemet til meget langsom.

#### <a name="measurement"></a>Mål 

1.  Overvåge hukommelsesforbrug på maskine at sikre, at det ikke overstiger tilgængelig hukommelse. 
2.  Overvåge den `Page Faults/Sec` ydeevne tæller. De fleste systemer vil lade siden fejl selv under normal drift, så pas på spidser i tælleren siden fejl ydeevne, som svarer til timeout.

#### <a name="resolution"></a>Opløsning

Opgradere din klient til en større klient VM størrelse med mere hukommelse, eller gå til din hukommelse brugsmønstre for at reducere hukommelse consuption.


### <a name="burst-of-traffic"></a>Burst af trafik

#### <a name="problem"></a>Problem

Indstilling trafik kombineres med dårlig `ThreadPool` indstillinger kan medføre forsinkelser i databehandlingen allerede sendt af Redis-serveren, men endnu ikke er consumed på klientsiden.

#### <a name="measurement"></a>Mål 

Overvåge hvordan din `ThreadPool` statistik ændrer sig med tiden ved hjælp af kode [således](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Du kan også se på de `TimeoutException` meddelelse fra StackExchange.Redis. Her er et eksempel:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

I den ovenstående meddelelse er der flere problemer, der er interessante:

 1. Bemærk, at i den `IOCP` sektion og `WORKER` sektion, du har en `Busy` værdi, der er større end den `Min` værdi. Det betyder, at din `ThreadPool` indstillinger være nødvendigt at justere.
 2. Du kan også se `in: 64221`. Dette angiver, at 64211 byte har modtaget på kernen socket layer, men endnu ikke har læst af programmet (fx StackExchange.Redis). Det betyder normalt, at dit program ikke du læser data fra netværket så hurtigt som serveren er sende det til dig.

#### <a name="resolution"></a>Opløsning

Konfigurere [Indstillinger for trådgruppe](https://gist.github.com/JonCole/e65411214030f0d823cb) for Sørg for, at din tråd puljen skaleres hurtigt under burst scenarier.


### <a name="high-client-cpu-usage"></a>Høj klient CPU-brug

#### <a name="problem"></a>Problem

Høj CPU-brug på klienten er en angivelse, der systemet kan holde med det arbejde, der er det blevet bedt om at udføre. Det betyder, at klienten kan muligvis ikke behandle et svar fra Redis i tide, selvom Redis sendt svaret meget hurtigt.

#### <a name="measurement"></a>Mål

Overvåge System bred CPU-brug via portalen Azure eller via tælleren tilknyttede ydeevne. Pas på ikke til at overvåge *proces* CPU, fordi en enkelt proces kan have lav CPU-brug på samme tid, som overordnede system-CPU'EN kan være høj. Holde øje med spidser i CPU-brug, der svarer til timeout. Som et resultat af høj CPU, kan du også se høj `in: XXX` værdier i `TimeoutException` fejlmeddelelser, som beskrevet i afsnittet [Burst af trafik](#burst-of-traffic) .

>[AZURE.NOTE] StackExchange.Redis 1.1.603 og nyere indeholder den `local-cpu` metriske i `TimeoutException` fejlmeddelelser. Sikre, at du bruger den nyeste version af [StackExchange.Redis NuGet pakke](https://www.nuget.org/packages/StackExchange.Redis/). Der er fejl, der hele tiden der løses i koden for at gøre det mere robust for timeout så har du den nyeste version er vigtigt.

#### <a name="resolution"></a>Opløsning

Opgradere til en større VM med flere CPU-kapaciteten eller undersøge, hvad der er årsag CPU spidser. 



### <a name="client-side-bandwidth-exceeded"></a>Klient side båndbredde er overskredet

#### <a name="problem"></a>Problem

Forskellige store klientcomputere har begrænsninger på hvor meget netværksbåndbredde de er tilgængelige. Hvis klienten overstiger den tilgængelige båndbredde, derefter behandles data ikke på klientsiden så hurtigt som serveren er at sende den. Det kan medføre timeout.

#### <a name="measurement"></a>Mål

Overvåge, hvordan din båndbredden ændrer sig med tiden ved hjælp af kode [således](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Bemærk, at denne kode ikke kører muligvis korrekt i nogle miljøer med begrænsede tilladelser (som Azure websteder).

#### <a name="resolution"></a>Opløsning 

Øge klient VM størrelse eller reducere netværksbåndbredde forbrug.


### <a name="large-requestresponse-size"></a>Store anmodning/svar størrelse

#### <a name="problem"></a>Problem

Et stort anmodning/svar kan medføre timeout. Som et eksempel, forudsættes din timeoutværdien, der er konfigureret på din klient er 1 sekund. Dit program anmoder om to nøgler (fx 'A' og "B") på samme tid (ved hjælp af den samme fysisk netværksforbindelse). De fleste klienter understøtter "Pipelining" af anmodninger, så begge 'A' og 'B' der sendes en anmodning på kablet til serveren én ad gangen uden at vente på svar. Serveren, der sender svar tilbage i samme rækkefølge. Hvis svaret 'A' er stort kan nok den optage de fleste af timeout for efterfølgende anmodninger om. 

I følgende eksempel demonstrerer dette scenario. I dette scenarie skal anmodning 'A' og 'B' sendes hurtigt kan serveren, der starter med at sende svar 'A' og 'B' hurtigt, men på grund af data Overførselstider "B" sidder fast bag andre anmodningen og får timeout, selvom serveren svarede hurtigt.

  	|-------- 1 Second Timeout (A)----------|
  	|-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Mål

Dette er en svært at måle. Du har grundlæggende instrumentere din klientkode for at spore store mødeindkaldelser og svar. 

#### <a name="resolution"></a>Opløsning

1.  Redis er udviklet til et stort antal små værdier i stedet for et par store værdier. Den foretrukne løsning er at opdele dataene til relaterede mindre værdier. Se [Hvad er området ideel værdi størrelse for redis? Er 100KB for stor?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) sende få mere at vide om hvorfor mindre værdier er anbefalet.
2.  Øge størrelsen af din VM (for klient- og Redis Cache), hvis du vil have højere båndbredde-funktioner, reducere Overførselstider data for større svar. Bemærk, at få mere båndbredde på lige serveren eller blot på klienten muligvis ikke nok. Måle dit båndbredden og Sammenlign det med funktionerne i størrelsen på VM, du har i øjeblikket.
3.  Øge antallet af `ConnectionMultiplexer` objekter brug og round robin-anmodninger over forskellige forbindelser.


### <a name="what-happened-to-my-data-in-redis"></a>Hvad skete der med mine data i Redis?

#### <a name="problem"></a>Problem

Jeg forventet for visse data skal være i min Azure Redis Cache-forekomst, men det synes ikke at være der.

##### <a name="resolution"></a>Opløsning

Se [Hvad skete der med mine data i Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) for mulige årsager og løsninger.


## <a name="server-side-troubleshooting"></a>Fejlfinding af Server side

Dette afsnit beskrives fejlfinding i forbindelse med problemer, der kan opstår på grund af en betingelse på cache-serveren.

-   [Hukommelsestrykket på serveren](#memory-pressure-on-the-server)
-   [Højt CPU-brug / Server indlæse](#high-cpu-usage-server-load)
-   [Server Side båndbredden er overskredet](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Hukommelsestrykket på serveren

#### <a name="problem"></a>Problem

Hukommelsestrykket på serversiden fører til alle typer problemer med ydeevnen, der kan forsinke behandling af anmodninger. Når hukommelsesforbrug rammer, har systemet typisk til siden data fra den fysiske hukommelse til virtuel hukommelse, som er på disken. Denne *side fejlagtigt* får systemet til meget langsom. Der er flere mulige årsager til denne hukommelse tryk: 

1.  Du har udfyldt cachen til fuld kapacitet med data. 
2.  Redis ser høj hukommelsesfragmentering - skyldes oftest lagring af store objekter (Redis er optimeret til en lille objekter - se [Hvad er området ideel værdi størrelse for redis? Er 100KB for stor?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) sende få mere at vide). 

#### <a name="measurement"></a>Mål

Redis Fremviser to målepunkter, der kan hjælpe dig med at identificere problemet. Først er `used_memory` og den anden er `used_memory_rss`. [Denne statistik](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) er tilgængelige i portalen Azure eller via kommandoen [Redis oplysninger](http://redis.io/commands/info) .

#### <a name="resolution"></a>Opløsning

Der er flere mulige ændringer, kan du holde hukommelsesforbrug sund:

1. [Konfigurer en politik for hukommelse](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) og angive udløb gange på tasterne på. Bemærk, at dette ikke muligvis tilstrækkelig, hvis du har fragmentering.
2. [Konfigurer en maxmemory reserveret værdi](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , der er stort nok til kompensation for hukommelsesfragmentering.
3. Opdele store cachelagrede objekterne i mindre relaterede objekter.
4. [Skala](cache-how-to-scale.md) til en større cache.
5. Hvis du bruger en [premium cache med Redis klynge aktiveret](cache-how-to-premium-clustering.md) kan du [øge antallet af shards](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Højt CPU-brug / Server indlæse

#### <a name="problem"></a>Problem

Højt CPU-brug kan betyde, at klientsiden kan ikke behandle et svar fra Redis i tide, selvom Redis sendt svaret meget hurtigt.

#### <a name="measurement"></a>Mål

Overvåge System bred CPU-brug via portalen Azure eller via tælleren tilknyttede ydeevne. Pas på ikke til at overvåge *proces* CPU, fordi en enkelt proces kan have lav CPU-brug på samme tid, som overordnede system-CPU'EN kan være høj. Holde øje med spidser i CPU-brug, der svarer til timeout.

#### <a name="resolution"></a>Opløsning

[Skala](cache-how-to-scale.md) til en større cache klasse med flere CPU-kapaciteten eller undersøge, hvad der er årsag CPU spidser. 

### <a name="server-side-bandwidth-exceeded"></a>Server Side båndbredden er overskredet

#### <a name="problem"></a>Problem

Forskellige store cache forekomster har begrænsninger på hvor meget netværksbåndbredde de er tilgængelige. Hvis serveren overstiger den tilgængelige båndbredde, sendes data ikke klienten som hurtigt. Det kan medføre timeout.

#### <a name="measurement"></a>Mål

Du kan overvåge den `Cache Read` metrikværdi, som er mængden data, der læses fra cachen i megabyte sekundet (MB/s) under det angivne reporting interval. Denne værdi svarer til den netværksbåndbredde, der bruges af denne cache. Hvis du vil konfigurere beskeder for server side netværksbåndbredde begrænsninger, kan du oprette dem ved hjælp af dette `Cache Read` tæller. Sammenlign din målinger med værdierne i [denne tabel](cache-faq.md#cache-performance) for observerede båndbredde begrænsninger for forskellige cache priser niveauer og størrelser.

#### <a name="resolution"></a>Opløsning

Hvis du er ensartet nær den observerede maksimale båndbredde til størrelsen på dine priser niveau og cache, kan du overveje at [Skalering](cache-how-to-scale.md) til en priser niveau eller størrelse, der har større netværksbåndbredde, ved hjælp af værdierne i [denne tabel](cache-faq.md#cache-performance) som vejledning.


## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis timeout undtagelser

StackExchange.Redis bruger en konfiguration at navngivne `synctimeout` for synkron handlinger, som har en standardværdi på 1000 ms. Hvis et synkron opkald ikke fuldføres inden for den fastsatte tid, viser StackExchange.Redis klienten en timeoutfejl, der ligner følgende eksempel.

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Denne fejlmeddelelse indeholder målepunkter, der kan hjælpe Peg årsag og mulige løsning på problemet. Den følgende tabel indeholder oplysninger om fejl meddelelse statistik.

| Fejlmetrik-meddelelse | Detaljer                                                                                                                                                                                                                                          |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| inst       | I det sidste gang udsnit: 0-kommandoer er udstedt                                                                                                                                                                                              |
| MGR        | Socket manager udfører `socket.select` hvilket betyder, at den beder OS til at angive en socket, der har noget at gøre; grundlæggende: læseren omfatter ikke aktivt læsning fra netværket fordi det ikke tror, at der er noget at gøre |
| kø      | Der er 73 Samlet antal igangværende handlinger                                                                                                                                                                                                        |
| qu         | 6 igangværende handlinger er i ikke-sendte køen og endnu ikke har skrevet til udgående netværket                                                                                                                                                           |
| Qs         | 67 he igangværende handlinger, der er sendt til serveren, men et svar er endnu ikke tilgængelig. Svaret kan være `Not yet sent by the server` eller`sent by the server but not yet processed by the client.`                                                   |
| QC         | 0 igangværende handlinger har set svar, men endnu ikke er blevet markeret som fuldført på grund af venter på fuldførelse løkke                                                                                                                                      |
| wR         | Der er en aktiv writer (dvs. ikke, blive ignoreres 6 ikke-sendte anmodninger) byte/activewriters                                                                                                                                                   |
| i         | Der er ingen aktive læsere og nul byte er tilgængelige til at blive læst på NIC byte/activereaders                                                                                                                                               |


### <a name="steps-to-investigate"></a>Trin til at undersøge

1. Som en bedste fremgangsmåde Sørg for, at bruger du følgende mønster til at oprette forbindelse ved brug af StackExchange.Redis klienten.


        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


    Få mere at vide under [oprette forbindelse til den ved hjælp af StackExchange.Redis cache](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

2. Sørg for, at din Azure Redis Cache og klientprogrammet er i samme område i Azure. For eksempel du kan få en timeout når din cache er i af USA, men klienten er placeret i Vest USA og anmodningen ikke fuldføres inden for den `synctimeout` interval, eller du kan får timeout, når du foretager fejlfinding af fra din lokale computer. 

    Det anbefales det for at har cachen og i klienten i det samme Azure område. Hvis du har et scenarie, der indeholder cross internationale opkald, skal du indstille den `synctimeout` interval til en værdi, der er højere end 1000 ms Standardinterval ved at medtage en `synctimeout` egenskab i forbindelsesstrengen. I følgende eksempel viser en StackExchange.Redis cache forbindelse streng kodestykke med en `synctimeout` af 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...

3. Sikre, at du bruger den nyeste version af [StackExchange.Redis NuGet pakke](https://www.nuget.org/packages/StackExchange.Redis/). Der er fejl, der hele tiden der løses i koden for at gøre det mere robust for timeout så har du den nyeste version er vigtigt.

4. Hvis der er forespørgsler, der er få bundet båndbredde begrænsninger på serveren eller klienten, kan det tage længere tid at afslutte og dermed medføre timeout. Hvis din timeout er på grund af netværksbåndbredde på serveren, finder du [Server side båndbredde overskredet](#server-side-bandwidth-exceeded). Hvis din timeout er på grund af klienten netværksbåndbredde, finder du [klient side båndbredde overskredet](#client-side-bandwidth-exceeded).

6. Er du få CPU bundet på serveren eller på klienten?
    -   Kontrollere, hvis du er få bundet ved CPU på din klient, hvilket kan medføre anmodningen, så den ikke behandles inden for den `synctimeout` interval, og som derfor forårsager et timeout. Flytte til en større klient eller distribuere afkrydsningsfeltet Indlæs kan hjælpe med at styre dette. 
    -   Kontrollér, om du er ved at CPU bundet på serveren ved at overvåge den `CPU` [cache ydeevne metrisk](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Anmodninger om kommer, mens Redis er CPU bundet kan medføre disse anmodninger om at timeout. Du kan distribuere belastning på tværs af flere shards i en premium cache eller opgradere til en større størrelse eller priser niveau for at løse dette. Du kan finde flere oplysninger, se [Server Side båndbredden er overskredet](#server-side-bandwidth-exceeded).

7. Er der kommandoer tager lang tid at behandle på serveren? Længerevarende kommandoer, der tager lang tid at behandle på redis-serveren kan medføre timeout. Nogle eksempler på længerevarende kommandoer er `mget` med stort antal taster `keys *` eller dårligt skrevne lua scripts. Du kan oprette forbindelse til din Azure Redis Cache-forekomst, ved hjælp af redis cli klienten eller bruge [Redis Console](cache-configure.md#redis-console) og køre [SlowLog](http://redis.io/commands/slowlog) kommando for at se, om der er anmodninger om tager længere tid end forventet. Redis Server og StackExchange.Redis er optimeret til mange små anmodninger i stedet for færre store anmodninger. Opdele dataene i mindre dele, kan det forbedre ting her. 

    Finde oplysninger om at oprette forbindelse til Azure Redis Cache SSL slutpunktet ved hjælp af redis cli og stunnel, kan du se blogindlægget [Præsentation af ASP.NET Session tilstand Provider til Redis Preview-versionen](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) . Du kan finde flere oplysninger [SlowLog](http://redis.io/commands/slowlog).

8. Høj Redis serverbelastning kan medføre timeout. Du kan overvåge serverens belastning ved at overvåge den `Redis Server Load` [cache ydeevne metrisk](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). En serverbelastning på 100 (maksimumværdien) betyder, at redis serveren er optaget, med ingen Inaktivitetstid, behandling af anmodninger. For at se, hvis visse anmodninger at optage alle muligheden for server skal du køre kommandoen SlowLog, som beskrevet i forrige afsnit. Du kan finde flere oplysninger, se [høj CPU-brug / Server indlæse](#high-cpu-usage-server-load).

9. Er der andre begivenhed på klientsiden, som kan have beskadiget blip et netværk? Kontrollere på klienten (web, arbejder rolle eller en Iaas VM), hvis der var en begivenhed som skalering antallet af forekomster af klienten, op eller ned eller anvender en ny version af klienten eller automatisk skalering er aktiveret? I vores test vi har fundet, autoskalering eller skalering op/ned kan medføre kan udgående netværksforbindelsen gå tabt i flere sekunder. StackExchange.Redis kode er tolerant for sådanne begivenheder og vil oprette forbindelse igen. I denne periode fornyet forbindelse kan anmodninger i køen timeout.

10. Er der en stor anmodning går forud for flere små anmodninger til Redis cachen, der fik timeout? Parameteren `qs` i fejlen meddelelse fortæller dig, hvor mange anmodninger blev sendt fra klienten til serveren, men endnu ikke har behandlet et svar. Denne værdi kan holde voksende, fordi StackExchange.Redis anvender en enkelt TCP-forbindelse og kan kun læse ét svar ad gangen. Selvom den første operation fik timeout, den forhindrer ikke de data, der sendes til/fra serveren, og andre anmodninger blokeres, indtil dette er gjort, forårsager Sådan fungerer tid. En løsning er at minimere risikoen for timeout ved at sikre, at din cache er stort nok til arbejdsbelastningen og opdele store værdier i mindre dele. En anden mulig løsning er at bruge en samling af `ConnectionMultiplexer` objekter i din kunde, og vælg den mindst indlæst `ConnectionMultiplexer` når du sender en ny anmodning. Dette bør forhindre timeout for en enkelt fra medfører, at andre anmodninger om at også timeout.

11. Hvis du bruger `RedisSessionStateprovider`, Kontrollér, at du har sat forsøg timeout korrekt. `retrytimeoutInMilliseconds`skal være højere end `operationTimeoutinMilliseonds`, ellers opstår der ingen yderligere forsøg. I følgende eksempel `retrytimeoutInMilliseconds` er indstillet til 3000. Se [ASP.NET Session tilstand Provider til Azure Redis Cache](cache-aspnet-session-state-provider.md) og [hvordan du bruger konfigurationsparametrene af Session tilstand udbyder og Output Cache udbyder](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)kan finde flere oplysninger.


    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


12. Kontrollere hukommelsesforbrug på Azure Redis Cache-serveren ved at [overvåge](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` og `Used Memory`. Hvis en politik for eviction er på plads, Redis starter evicting tasterne hvornår `Used_Memory` når cachestørrelse. Ideelt set `Used Memory RSS` kun skal være en smule højere end `Used memory`. En stor forskel betyder, at der er hukommelsesfragmentering (intern eller ekstern. Når `Used Memory RSS` er mindre end `Used Memory`, betyder det en del af cache-hukommelse er blevet skiftet af operativsystemet. Hvis dette sker, kan du forvente nogle betydeligt latenstider. Fordi Redis ikke har kontrol over hvordan dens tildelinger er tilknyttet hukommelsessider, høj `Used Memory RSS` er ofte et resultat af en samling i hukommelsesforbrug. Når Redis Frigør hukommelse, hukommelsen gives tilbage til allokatoren, og allokatoren muligvis eller må ikke give hukommelsen tilbage til systemet. Der kan være en uoverensstemmelse mellem den `Used Memory` værdi og hukommelse forbrug som operativsystemet har rapporteret. Det kan være på grund af fakultet hukommelse er blevet brugt og udgivet som Redis, men ikke givet tilbage til systemet. Du kan udføre følgende trin for at reducere problemer med manglende hukommelse.
    -   Opgradere cachen til en større, så du ikke kører sig i forhold til hukommelse begrænsninger på systemet.
    -   Angive udløb gange på tasterne, så det ældre værdier er udskilt proaktiv.
    -   Overvåge på den `used_memory_rss` cache metrisk. Når denne værdi nærmer sig størrelsen på deres cache, er du sandsynligvis at begynde at se problemer med ydeevnen. Distribuere data på tværs af flere shards, hvis du er ved hjælp af en premium cache, eller du kan opgradere til en større cache.

    Du kan finde yderligere oplysninger finder [Hukommelsestrykket på serveren](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Yderligere oplysninger

-   [Hvilke Redis Cache tilbyder og størrelse skal jeg bruge?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
-   [Hvordan kan jeg benchmark- og teste ydeevnen for min cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Hvordan kan jeg køre Redis kommandoer?](cache-faq.md#how-can-i-run-redis-commands)
-   [Hvordan du kan overvåge Azure Redis Cache](cache-how-to-monitor.md)