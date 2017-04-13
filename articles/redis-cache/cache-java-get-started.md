<properties
   pageTitle="Sådan bruges Azure Redis Cache med Java | Microsoft Azure"
    description="Introduktion til Azure Redis Cache ved hjælp af Java"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor=""/>

<tags
    ms.service="cache"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/24/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-java"></a>Sådan bruges Azure Redis Cache med Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis Cache giver dig adgang til en dedikeret Redis cache, administreres af Microsoft. Din cache er tilgængelig fra et program i Microsoft Azure.

Dette emne beskrives, hvordan du kommer i gang med Azure Redis Cache ved hjælp af Java.

## <a name="prerequisites"></a>Forudsætninger

[Jedis](https://github.com/xetorthio/jedis) - Java-klienten til Redis

Dette selvstudium bruger Jedis, men du kan bruge enhver Java-klient, der er angivet på [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Oprette en Redis cache på Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Hente tasterne host navn og access

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>Aktivere slutpunktet ikke SSL

Nogle Redis klienter ikke understøtter SSL, og som standard [ikke SSL-port er deaktiveret for nye Azure Redis Cache forekomster](cache-configure.md#access-ports). Samtidig med denne skriver understøtter [Jedis](https://github.com/xetorthio/jedis) klienten ikke SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## <a name="add-something-to-the-cache-and-retrieve-it"></a>Føje noget til cachen og hente det

    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
    public class App
    {
      public static void main( String[] args )
      {
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Næste trin

- [Aktivere cachen diagnosticering](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) , så du kan [overvåge](https://msdn.microsoft.com/library/azure/dn763945.aspx) tilstanden for din cache.
- Læs den officielle [Redis dokumentation](http://redis.io/documentation).

