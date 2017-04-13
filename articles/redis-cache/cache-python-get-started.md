<properties
    pageTitle="Sådan bruges Azure Redis Cache med Python | Microsoft Azure"
    description="Introduktion til Azure Redis Cache ved hjælp af Python"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/16/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-python"></a>Sådan bruges Azure Redis Cache med Python

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Dette emne beskrives, hvordan du kommer i gang med Azure Redis Cache ved hjælp af Python.


## <a name="prerequisites"></a>Forudsætninger

Installere [redis Reg.](https://github.com/andymccurdy/redis-py).


## <a name="create-a-redis-cache-on-azure"></a>Oprette en Redis cache på Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Hente tasterne host navn og access

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>Aktivere slutpunktet ikke SSL

Nogle Redis klienter ikke understøtter SSL, og som standard [ikke SSL-port er deaktiveret for nye Azure Redis Cache forekomster](cache-configure.md#access-ports). Samtidig med denne skriver understøtter [redis Reg.](https://github.com/andymccurdy/redis-py) klienten ikke SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Føje noget til cachen og hente det


    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Erstatte `<name>` med navnet på din cache og `key` med din access-nøgle.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
