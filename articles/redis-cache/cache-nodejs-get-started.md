<properties
    pageTitle="Sådan bruges Azure Redis Cache med Node.js | Microsoft Azure"
    description="Introduktion til Azure Redis Cache ved hjælp af Node.js og node_redis."
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Sådan bruges Azure Redis Cache med Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis Cache giver dig adgang til en sikker, dedikeret Redis cache, administreres af Microsoft. Din cache er tilgængelig fra et program i Microsoft Azure.

Dette emne beskrives, hvordan du kommer i gang med Azure Redis Cache ved hjælp af Node.js. Se [opbygge et Node.js Chat program med Socket.IO på en Azure-websted](../app-service-web/web-sites-nodejs-chat-app-socketio.md)til et andet eksempel på brug af Azure Redis Cache med Node.js.


## <a name="prerequisites"></a>Forudsætninger

Installere [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Dette selvstudium bruger [node_redis](https://github.com/mranney/node_redis). Eksempler på brug af andre Node.js klienter, individuelle dokumentationen til de Node.js klienter, der er angivet på [Node.js Redis klienter](http://redis.io/clients#nodejs).

## <a name="create-a-redis-cache-on-azure"></a>Oprette en Redis cache på Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Hente tasterne host navn og access

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Oprette forbindelse til sikker brug af SSL-cachen

De seneste builds af [node_redis](https://github.com/mranney/node_redis) yde support til at oprette forbindelse til Azure Redis Cache ved hjælp af SSL. I følgende eksempel vises, hvordan du opretter forbindelse til Azure Redis Cache ved hjælp af 6380 SSL slutpunkt. Erstatte `<name>` med navnet på din cache og `<key>` med enten produktnøglen primær eller sekundær som beskrevet i afsnittet forrige [hente tasterne host navn og adgang](#retrieve-the-host-name-and-access-keys) .

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Føje noget til cachen og hente det

I følgende eksempel viser, hvordan til at oprette forbindelse til en Azure Redis Cache-forekomst, og gemme og hente et element fra cachen. Du kan finde flere eksempler på brug af Redis med [node_redis](https://github.com/mranney/node_redis) -klienten, [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
    
    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });
    
    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Output:

    OK
    value


## <a name="next-steps"></a>Næste trin

- [Aktivere cachen diagnosticering](cache-how-to-monitor.md#enable-cache-diagnostics) , så du kan [overvåge](cache-how-to-monitor.md) tilstanden for din cache.
- Læs den officielle [Redis dokumentation](http://redis.io/documentation).



