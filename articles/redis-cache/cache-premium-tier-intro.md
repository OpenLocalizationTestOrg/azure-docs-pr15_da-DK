<properties 
    pageTitle="Introduktion til Azure Redis Cache Premium niveau | Microsoft Azure" 
    description="Lær, hvordan du kan oprette og administrere Redis brugerdata Redis klynge, og VNET understøttelse af din Premium niveau Azure Redis Cache forekomster" 
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
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Introduktion til Azure Redis Cache Premium niveau
Azure Redis Cache er en fordelt, administrerede cache, der hjælper dig med at opbygge meget SVG og svarede programmer ved at indsende meget hurtig adgang til dine data. 

Det nye Premium-niveau er en klar Enterprise-niveau, som indeholder alle funktionerne Standard lag og meget mere, som bedre ydeevne, større arbejdsbelastninger, nedbrud, Importér/Eksportér og øget sikkerhed. Fortsætte med at læse for at få flere oplysninger om de ekstra funktioner af Premium cache-niveauet.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Bedre ydeevne sammenlignet med Standard eller Basic niveau
**Bedre ydeevne via Standard eller grundlæggende niveau.** Cachelagre i Premium lag er installeret på hardware, som har hurtigere processorer og giver bedre ydeevne sammenlignet til Basic eller Standard niveau. Premium niveau cache har højere overførselshastighed og nederste latenstider. 

**Overførsel for den samme størrelse Cache er højere i Premium sammenlignet med Standard niveau.** For eksempel gennemløb af en 53 GB P4 (Premium) cache er 250K anmodninger sekundet sammenlignet med 150 K for C6 (Standard).

Du kan finde flere oplysninger om størrelse, overførselshastighed og båndbredde med premium cache se [Azure Redis Cache ofte stillede spørgsmål](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis bevarelse af data
Premium niveau giver dig mulighed at bevare cache dataene i en Azure-lager-konto. Alle dataene gemmes i en Basic/Standard cache kun i hukommelsen. I tilfælde af underliggende infrastruktur kan problemer der være muligt tab af data. Det anbefales at bruge funktionen Redis data brugerdata i Premium lag til at øge fleksibilitet mod datatab. Azure Redis Cache tilbyder RDB og AOF (kommer snart) indstillinger i [Redis brugerdata](http://redis.io/topics/persistence). 

Finde instruktioner til konfiguration af brugerdata, se, [hvordan du konfigurerer brugerdata i en Premium Azure Redis Cache](cache-how-to-premium-persistence.md).

##<a name="redis-cluster"></a>Redis klynge
Hvis du vil oprette cache større end 53 GB eller vil shard data på tværs af flere Redis noder, kan du bruge Redis klynge, som er tilgængelig i Premium lag. Hver node består af et primær/replika cache par, der administreres af Azure for høj tilgængelighed. 

**Redis klynge giver dig maksimale skala og overførselshastighed.** Overførselshastighed øges lineært, når du øger antallet af shards (noder) i klyngen. F.eks. Hvis du opretter en P4 klynge af 10 shards og derefter tilgængelige overførselshastigheden er 250K * 10 = 2,5 millioner anmodninger sekundet. Se [Azure Redis Cache ofte stillede spørgsmål](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) få mere at vide om størrelse, overførselshastighed og båndbredde med premium cache.

For at komme i gang med klynge skal se, [hvordan du konfigurerer databaseserver for en Premium Azure Redis Cache](cache-how-to-premium-clustering.md).

##<a name="enhanced-security-and-isolation"></a>Øget sikkerhed og isolationsniveauet

Cachelagre, der er oprettet i det grundlæggende eller Standard niveau er tilgængelige på det offentlige internet. Adgang til cachen er begrænset baseret på hurtigtast. Med Premium trin kan du yderligere sikre, at kun klienter inden for et bestemt netværk har adgang til cachen. Du kan installere Redis cachen i et [Virtuelt Azure-netværk (VNet)](https://azure.microsoft.com/services/virtual-network/). Du kan bruge alle funktionerne i VNet som undernet, politik for adgangskontrol, og andre funktioner til yderligere begrænse adgangen til Redis.

Du kan finde flere oplysninger, se, [hvordan du konfigurere understøttelse af virtuelt netværk for en Premium Azure Redis Cache](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importér/Eksportér

Importér/Eksportér er en Azure Redis Cache data management-handling, så du kan importere data til Azure Redis Cache eller eksportere data fra Azure Redis Cache ved at importere og eksportere et Redis Cache Database (RDB) snapshot fra en premium cache til en side blob på en Azure-lager-konto. Dette gør det muligt at overføre mellem forskellige Azure Redis Cache forekomster eller udfylde cachen med data, før brug.

Importér kan bruges til at hente Redis kompatible RDB filer fra en hvilken som helst Redis server, der kører i skyen eller miljøet, herunder Redis kører på Linux, Windows eller enhver skyen udbyder som Amazon webtjenester og andre. Import af data er en nem måde at oprette en cache med udfyldt på forhånd data. Azure Redis Cache indlæser RDB filer fra Azure-lager i hukommelsen under importen og derefter indsætter tasterne i cachen.

Eksportér giver dig mulighed at eksportere de data, der er gemt i Azure Redis Cache for Redis kompatible RDB fil(er). Du kan bruge denne funktion til at flytte data fra én Azure Redis Cache-forekomst, til en anden eller til en anden Redis server. Under eksporten, der oprettes en midlertidig fil på den VM, der er vært Azure Redis Cache server-forekomst, og filen er overført til kontoen udpegede lagerplads. Når eksporten er fuldført med enten en status for et gunstigt udfald eller fejl, slettes den midlertidige fil.

Se, [hvordan du importerer data til og eksportere data fra Azure Redis Cache](cache-how-to-import-export-data.md)kan finde flere oplysninger.

## <a name="reboot"></a>Genstart

Premium niveau gør det muligt for dig at genstarte en eller flere noder af din cache efter behov. Dette giver dig mulighed at teste din ansøgning om fleksibilitet i tilfælde af en fejl. Du kan genstarte følgende noder.

-   Master node i din cache
-   Slave node i din cache
-   Både master og slave noder af din cache
-   Når du bruger en premium cache med klynge, kan du genstarte masteren, slave eller begge noder til individuelle shards i cachen

Få mere at vide under [Genstart](cache-administration.md#reboot) og [Genstart ofte stillede spørgsmål](cache-administration.md#reboot-faq).

## <a name="schedule-updates"></a>Planlæg opdateringer

Funktionen planlagte opdateringer gør det muligt at angive et vedligeholdelsesvindue til din cache. Når vinduet reparation er angivet, foretages Redis serveropdateringer under dette vindue. Hvis du vil angive et vedligeholdelsesvindue, Vælg de ønskede dage og angive vedligeholdelse start vinduet time for hver dag. Bemærk, at det vedligeholdelse vindue tidspunkt er i UTC. 

Få mere at vide under [tidsplansopdateringer](cache-administration.md#schedule-updates) og [Planlæg opdateringer ofte stillede spørgsmål](cache-administration.md#schedule-updates-faq).

>[AZURE.NOTE] Kun Redis server, der er foretaget opdateringer under vinduet planlagt vedligeholdelse. Vinduet reparation gælder ikke til Azure opdateringer eller opdateringer til VM-operativsystemet.

## <a name="to-scale-to-the-premium-tier"></a>Skalere til premium-niveau

For at tilpasse til premium-niveau, skal du blot vælge en af premium niveauer i bladet **Skift priser niveau** . Du kan også tilpasse din cache til det premium niveau ved hjælp af PowerShell og CLI. Finde trinvise instruktioner, i [Sådan skala Azure Redis Cache](cache-how-to-scale.md) og [til at automatisere en handling af skalering](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Næste trin

Oprette en cache og udforske nye premium niveau funktioner.

-   [Sådan konfigureres brugerdata i en Premium Azure Redis Cache](cache-how-to-premium-persistence.md)
-   [Sådan konfigureres virtuelt netværk understøttelse af en Premium Azure Redis Cache](cache-how-to-premium-vnet.md)
-   [Sådan konfigureres databaseserver for en Premium Azure Redis Cache](cache-how-to-premium-clustering.md)
-   [Hvordan du kan importere data til og Eksporter data fra Azure Redis Cache](cache-how-to-import-export-data.md)
-   [Sådan administrerer du Azure Redis Cache](cache-administration.md)
  

