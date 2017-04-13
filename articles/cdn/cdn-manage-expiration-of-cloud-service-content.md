<properties
 pageTitle="Hvordan du administrerer udløb af Azure Web Apps/Cloud Services, ASP.NET og IIS indhold i Azure CDN | Microsoft Azure"
 description="Beskriver, hvordan du administrerer udløb af skyen service indhold i Azure CDN"
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="09/19/2016"
 ms.author="casoper"/>

# <a name="how-to-manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Hvordan du administrerer udløb af Azure Web Apps/Cloud Services, ASP.NET eller IIS indhold i Azure CDN

> [AZURE.SELECTOR]
- [Azure Web Apps/Cloud Services, ASP.NET eller IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Azure blob-lagringstjeneste](cdn-manage-expiration-of-blob-content.md)

Filer fra en hvilken som helst offentligt tilgængelige origin webserver kan cachelagres i Azure CDN, indtil dens time to live (TTL) går.  TTL bestemmes af [ *Cache-Control* sidehoved](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) i HTTP-svar fra den oprindelige server.  I denne artikel beskrives, hvordan du kan angive `Cache-Control` sidehoveder til Azure Web Apps, Azure Cloud Services, ASP.NET-programmer og Internet Information Services-websteder, som alle er konfigureret på samme måde.

>[AZURE.TIP] Du kan vælge at angive ingen TTL på en fil.  I dette tilfælde anvendes Azure CDN automatisk standard TTL af syv dage.
>
>Du kan finde flere oplysninger om, hvordan Azure CDN fungerer til hastighed for adgang til filer og andre ressourcer, [Azure CDN oversigt](./cdn-overview.md).

## <a name="setting-cache-control-headers-in-configuration"></a>Angive Cache-Control sidehoveder i konfiguration

Du kan styre opdateringsfrekvens til statisk indhold, som billeder og typografiark, ved at ændre **applicationHost.config** eller **web.config** filerne til dit webprogram.  Elementet **system.webServer\staticContent\clientCache** i konfigurationsfil indstiller den `Cache-Control` sidehoved til indholdet. For **web.config**påvirker indstillinger for søgekonfiguration alt i mappen og alle undermapper, medmindre de tilsidesættes på niveauet for undermappe.  Du kan for eksempel angive en standard time to live i roden have alle statisk indhold, der er cachelagret for 3 dage, men har en undermappe, der er mere variable indhold med en cacheindstilling af 6 timer.  For **applicationHost.config**, alle programmer på webstedet, påvirkes, men kan tilsidesættes i **web.config** -filer i programmerne.

Følgende XML-viser og eksempel på indstillingen **clientCache** til at angive den maksimale alder for 3 dage:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Angive **UseMaxAge** tilføjer en `Cache-Control: max-age=<nnn>` sidehoved til svaret baseret på den værdi, der er angivet i attributten **CacheControlMaxAge** . Formatet af timespan er for attributten **cacheControlMaxAge** er <days>. <hours>:<min>:<sec>. Finde flere oplysninger om noden **clientCache** , [Client Cache <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Angive Cache-Control sidehoveder i kode

Du kan angive CDN cachelagring funktionsmåde fra et program ved at angive egenskaben **HttpResponse.Cache** til ASP.NET-programmer. Se [HttpResponse.Cache egenskab](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) og [HttpCachePolicy klasse](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)kan finde flere oplysninger om egenskaben **HttpResponse.Cache** .  

Hvis du vil cache programmet indhold i ASP.NET fra et program skal du sørge for, at indholdet er markeret som kan cachelagres ved at angive HttpCacheability til *offentlige*. Du skal også kontrollere, at der er angivet en cache kontrollere. Cache kontrollere kan være en senest ændret tidsstempel angive ved at ringe til SetLastModified eller en etag værdi angive ved at ringe til SetETag. Du kan også du kan også angive en cache udløbsdatoen ved at ringe til SetExpires, eller du kan stole på den cache heuristik, der er beskrevet tidligere i dette dokument.  

For eksempel cache indhold til en time, tilføje følgende:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Næste trin

- [Læs mere om elementet **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
- [Læs dokumentationen til egenskaben **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
- [Læs i dokumentationen til den **HttpCachePolicy klasse**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  
