<properties 
    pageTitle="Azure Redis Cache eksempler | Microsoft Azure" 
    description="Lær at bruge Azure Redis Cache" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-samples"></a>Azure Redis Cache-eksempler 

Dette emne indeholder en liste over Azure Redis Cache eksempler, som dækker scenarier som forbindelse til en cache læse og skrive data til og fra en cache og bruger ASP.NET Redis Cache-udbydere. Nogle af eksemplerne er kan downloades projekter, og nogle indeholder en trinvis vejledning og medtage kodestykker, men ikke opretter en kæde til et projekt, der kan downloades.

## <a name="hello-world-samples"></a>Hej verden eksempler

Eksemplerne i dette afsnit viser grundlæggende i at oprette forbindelse til en Azure Redis Cache forekomst og læse og skrive data til cachen med forskellige sprog og Redis klienter.

[Hej verden](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) eksempel viser, hvordan du udfører forskellige cache-handlinger ved hjælp af [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET-klienten.

Dette eksempel vises, hvordan du:

-   Bruge forskellige indstillinger til forbindelse
-   Læse og skrive objekter til og fra cachen bruger synkron og asynkrone handlinger
-   Bruge Redis Control/MSET kommandoer til at returnere værdier for angivne nøgler
-   Udføre Redis transaktions handlinger
-   Arbejde med Redis lister og sorteret sæt
-   Store .NET objekter ved hjælp af JsonConvert serializers
-   Bruge Redis sæt til at implementere mærkning
-   Arbejde med Redis klynge

Se i dokumentationen til [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) på github kan finde flere oplysninger, og se [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) enhedstest for flere brugsscenarier.

[Sådan bruger du Azure Redis Cache med Python](cache-python-get-started.md) viser, hvordan du kommer i gang med Azure Redis Cache ved hjælp af Python og [redis Reg.](https://github.com/andymccurdy/redis-py) klienten.

[Arbejde med .NET objekter i cachen](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) viser én måde at sekventielt .NET objekter, så du kan skrive dem til og læse dem fra en Azure Redis Cache forekomst. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Bruge Redis Cache som en skala ud Backplane for ASP.NET SignalR

[Brug Redis Cache som en skala ud Backplane til ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) eksempel viser, hvordan du kan bruge Azure Redis Cache som en SignalR backplane. Du kan finde flere oplysninger om backplane, [SignalR Scaleout med Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Redis Cache kunde forespørgsel eksempel

Dette eksempel demonstrerer sammenligner ydeevne mellem at få adgang til data fra en cache og få adgang til data fra brugerdata lagerplads. Dette eksempel har to projekter.

-   [Demo hvordan Redis Cache kan forbedre ydeevnen ved at cachelagre data](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-   [Frø Database og Cache for videoen](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET Session stat og outputcachelagring

[Bruge Azure Redis Cache til at gemme ASP.NET SessionState og OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) eksempel demonstrerer, hvordan du kan bruge Azure Redis Cache til at gemme ASP.NET Session og Output Cache ved hjælp af udbyderne af SessionState og OutputCache til Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Administrere Azure Redis cachen med MAML

[Administrere Azure Redis Cache ved hjælp af Azure Management biblioteker](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) eksemplet viser, hvordan kan du bruge Azure Management biblioteker til at administrere - (oprette / opdatere / slette) din Cache. 

## <a name="custom-monitoring-sample"></a>Brugerdefineret overvågning eksempel

[Access Redis Cache overvågning data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) -eksempel demonstrerer, hvordan du kan få adgang til overvågningsdata til din Azure Redis Cache uden for portalen Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>En Twitter-typografi Klon skrevet ved hjælp af PHP og Redis

[Retwis](https://github.com/SyntaxC4-MSFT/retwis) prøven er Redis Hej verden. Det er en minimale Twitter-typografi sociale netværk Klon, der er skrevet ved hjælp af Redis og PHP ved hjælp af [Predis](https://github.com/nrk/predis) -klienten. Koden er udviklet til at være meget simple og på samme tid til at vise forskellige Redis datastrukturer.

## <a name="bandwidth-monitor"></a>Båndbredde skærm

Eksemplet [båndbredde skærm](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) gør det muligt at overvåge den båndbredde, der bruges på klienten. For at måle båndbredden, køre eksemplet på klientmaskinen for cachen, foretage opkald til cachen og ser den båndbredde, der rapporteres af eksemplet båndbredde skærm.
