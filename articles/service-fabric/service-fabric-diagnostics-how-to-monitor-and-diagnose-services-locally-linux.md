<properties
   pageTitle="Lokalt overvåge og diagnosticere tjenester, der er skrevet med Azure Service strukturen | Microsoft Azure"
   description="Lær at overvåge og diagnosticere dine tjenester, der er skrevet ved hjælp af Microsoft Azure Service strukturen på en lokal udvikling maskine."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Overvåge og diagnosticere tjenester i en lokal computer udvikling opsætning


> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

Overvågning, opdage, diagnosticering og fejlfinding i forbindelse med Tillad services til at fortsætte med mindst forstyrrelse for brugeroplevelsen. Overvågnings- og diagnosticering er kritiske i en faktisk udløst produktionsmiljø. Indfører en lignende model under udviklingen af tjenester sikrer, at den diagnosticering rørledning fungerer, når du flytter til et produktionsmiljø. Tjenesten strukturen gør det nemt for tjenesten udviklere at implementere diagnostics, der fungerer problemfrit sammen på tværs af både enkelt-maskine lokale udvikling konfigurationer og reale fremstilling klynge konfigurationer.


## <a name="debugging-service-fabric-java-applications"></a>Fejlfinding Service-strukturen Java-programmer

[Flere logføring strukturer](http://en.wikipedia.org/wiki/Java_logging_framework) er tilgængelige for Java-programmer. Da `java.util.logging` er standardindstillingen med JRE, bruges den også [kodeeksempler i github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  Den følgende diskussion forklarer, hvordan du konfigurerer den `java.util.logging` framework. 
 
Brug af java.util.logging omdirigerer du programmet logfilerne til hukommelse, outputstreams, console filer eller sockets. For hver af disse indstillinger, der er standard-programmer, der er angivet i en ramme. Du kan oprette en `app.properties` fil for at konfigurere fil handler for dit program til at omdirigere alle logfiler til en lokal fil. 

Følgende kodestykke indeholder et eksempel på konfiguration af: 

```java 
handlers = java.util.logging.FileHandler
 
java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

Mappen, som peger på den `app.properties` filen skal findes. Efter den `app.properties` filen er oprettet, skal du også ændre posten punkt scriptet `entrypoint.sh` i den `<applicationfolder>/<servicePkg>/Code/` mappe for at angive egenskaben `java.util.logging.config.file` til `app.propertes` fil. Posten skal se ud som følgende kodestykke:

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```
 
 
Denne konfiguration resulterer i logfiler, der indsamles i en roterende måde på `/tmp/servicefabric/logs/`. **%U** og **maksimumtid** Tillad oprettelse af flere filer med filnavne mysfapp0.log, mysfapp1.log osv. Hvis nogen handler eksplicit er konfigureret, som standard registreret console handler. Loggene kan få i syslog under /var/log/syslog.
 
Se [kodeeksempler i github](http://github.com/Azure-Samples/service-fabric-java-getting-started)kan finde flere oplysninger.  



## <a name="next-steps"></a>Næste trin
Den samme sporing kode, der er føjet til dit program fungerer også med diagnosticering af dit program på en Azure-klynge. Se disse artikler, der beskriver de forskellige indstillinger for værktøjer og beskriver, hvordan du kan angive dem.
* [Sådan indsamler logfiler med Azure diagnosticering](service-fabric-diagnostics-how-to-setup-lad.md)
