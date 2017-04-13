<properties 
    pageTitle="Sådan konfigureres data brugerdata i en Premium Azure Redis Cache" 
    description="Lær at konfigurere og administrere data brugerdata din Premium niveau Azure Redis Cache forekomster" 
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
    ms.date="09/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Sådan konfigureres data brugerdata i en Premium Azure Redis Cache

Azure Redis Cache har forskellige cache tilbud som understøtter fleksibilitet valg af cachestørrelse og funktioner, herunder den nye Premium niveau.

Azure Redis Cache premium niveau omfatter funktioner som klynge, brugerdata og understøttelse af virtuelle netværk. I denne artikel beskrives, hvordan du kan konfigurere brugerdata i en forekomst af premium Azure Redis Cache.

Finde oplysninger om andre premium-funktioner i cachen, kan du se [Introduktion til Azure Redis Cache Premium niveau](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Hvad er bevarelse af data?
Redis brugerdata gør muligt at fastholdes data gemt i Redis. Du kan også tage snapshots og sikkerhedskopiere dataene, som du kan indlæse i tilfælde af en hardwarefejl. Dette er en stor fordel over Basic eller Standard niveau, hvor alle dataene er gemt i hukommelsen, og der kan være muligt tab af data i tilfælde af en mislykket, hvor cachen noder er ned. 

Azure Redis Cache indeholder Redis brugerdata ved hjælp af [RDB model](http://redis.io/topics/persistence), hvor data er gemt på en Azure-lager-konto. Når brugerdata er konfigureret, fortsætter Azure Redis Cache et øjebliksbillede af Redis cachen i Redis binært format på disken ud fra en konfigurerbar sikkerhedskopiering frekvens. Hvis der opstår en katastrofal hændelse, der deaktiverer både primær og replika cachen, genopbygges cachen med den seneste snapshot.

Brugerdata kan konfigureres fra bladet **Ny Redis Cache** under oprettelse af cachen og på bladet **Indstillinger** for eksisterende premium cache.

## <a name="create-a-premium-cache"></a>Oprette en premium cache

Hvis du vil oprette en cache og konfigurere brugerdata, logon til [Azure-portalen](https://portal.azure.com) og klikke på **Ny**->**Data + lagerplads**>**Redis Cache**.

![Oprette en Redis Cache][redis-cache-new-cache-menu]

For at konfigurere brugerdata skal du først vælge en af **Premium** cache i bladet **vælge din priser niveau** .

![Vælg din priser niveau][redis-cache-premium-pricing-tier]

Når en premium priser niveau er markeret, kan du klikke på **Redis brugerdata**.

![Redis brugerdata][redis-cache-persistence]

Trinnene i følgende afsnit beskriver, hvordan du konfigurerer Redis brugerdata på din nye premium cache. Når Redis brugerdata er konfigureret, kan du klikke på **Opret** for at oprette din nye premium cache med Redis brugerdata.

## <a name="configure-redis-persistence"></a>Konfigurere Redis brugerdata

Redis brugerdata er konfigureret på bladet **Redis data brugerdata** . For nye cache findes denne blade under oprettelsen af cachen, som beskrevet i forrige afsnit. For eksisterende cachelagre åbnes bladet **Redis bevarelse af data** fra bladet **Indstillinger** for din cache.

![Redis indstillinger][redis-cache-settings]

For at aktivere Redis brugerdata skal du klikke på **aktiveret** for at aktivere RDB (Redis database) sikkerhedskopi. Klik på **deaktiveret**for at deaktivere Redis brugerdata i en tidligere aktiveret premium cache.

For at konfigurere det ekstra interval skal du vælge en **Hyppighed af sikkerhedskopiering** fra på rullelisten. Valgmuligheder omfatter **15 minutter**, **30 minutter**, **60 minutter**, **6 timer**, **12 timer**og **24 timer**. Dette interval starter optælling, når forrige sikkerhedskopieringen er fuldført, og når det går startes en ny sikkerhedskopi.

Klik på **Lagerplads konto** for at markere lagerplads-konto, der skal bruges, og vælg enten **primær nøgle** eller **sekundær nøgle** bruge rullelisten **Lagerplads nøgle** . Skal du vælge en lagerplads konto i det samme område som cachen, og en **Premium lagerplads** konto anbefales, fordi premium lagerplads har højere overførselshastighed. 

>[AZURE.IMPORTANT] Hvis nøglen lagerplads for kontoen brugerdata gendannes, skal du rechoose tasten ønskede fra rullelisten **Lagerplads nøgle** .

![Redis brugerdata][redis-cache-persistence-selected]

Klik på **OK** for at gemme brugerdata konfigurationen.

Næste sikkerhedskopiering (eller første sikkerhedskopiering for ny cache) startes, når sikkerhedskopiering frekvens intervallet går.



## <a name="persistence-faq"></a>Brugerdata ofte stillede spørgsmål

Følgende liste indeholder svar på ofte stillede spørgsmål om Azure Redis Cache brugerdata.

-   [Kan jeg aktivere brugerdata i en tidligere oprettet cache?](#can-i-enable-persistence-on-a-previously-created-cache)
-   [Kan jeg ændre den hyppighed af sikkerhedskopiering, når jeg har oprettet cachen?](#can-i-change-the-backup-frequency-after-i-create-the-cache)
-   [Hvorfor Hvis jeg har en sikkerhedskopi hyppigheden for 60 minutter der er mere end 60 minutter mellem sikkerhedskopier?](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
-   [Hvad sker der med de gamle sikkerhedskopier, når en ny sikkerhedskopiering foretages?](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
-   [Hvad sker der, hvis jeg har skaleret til en anden størrelse og en sikkerhedskopi er gendannet, der blev foretaget før handlingen skalering?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Kan jeg aktivere brugerdata i en tidligere oprettet cache?

Ja, Redis brugerdata konfigureres både på oprettelse af cachen og på eksisterende premium cachelagre.

### <a name="can-i-change-the-backup-frequency-after-i-create-the-cache"></a>Kan jeg ændre den hyppighed af sikkerhedskopiering, når jeg har oprettet cachen?

Ja, kan du ændre den hyppighed af sikkerhedskopiering på bladet **Redis data brugerdata** . Flere oplysninger under [konfigurere Redis brugerdata](#configure-redis-persistence).

### <a name="why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Hvorfor Hvis jeg har en sikkerhedskopi hyppigheden for 60 minutter der er mere end 60 minutter mellem sikkerhedskopier?

Hyppighed af sikkerhedskopiering intervallet starter ikke, indtil den forrige sikkerhedskopieringen er fuldført. Hvis sikkerhedskopiering hyppigheden er 60 minutter, og det tager en sikkerhedskopi proces 15 minutter for at fuldføre, starter den næste sikkerhedskopiering ikke indtil 75 minutter efter starttidspunktet for den tidligere sikkerhedskopi.

### <a name="what-happens-to-the-old-backups-when-a-new-backup-is-made"></a>Hvad sker der med de gamle sikkerhedskopier, når en ny sikkerhedskopiering foretages?

Alle sikkerhedskopier med undtagelse af det seneste punkt slettes automatisk. Denne sletning muligvis ikke sker med det samme, men ældre sikkerhedskopier bevares ikke på ubestemt tid.

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Hvad sker der, hvis jeg har skaleret til en anden størrelse og en sikkerhedskopi er gendannet, der blev foretaget før handlingen skalering?

-   Hvis du har skaleres til en større størrelse, er der ingen virkning.
-   Hvis du har skaleret til en mindre størrelse, og du har en brugerdefineret [databaser](cache-configure.md#databases) , der er større end den [databaser begrænse](cache-configure.md#databases) til din nye størrelse, data i disse databaser ikke gendannes. Du kan finde flere oplysninger, se [er mine brugerdefinerede databaser påvirket indstilling under skalering?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
-   Hvis du har skaleret til en mindre størrelse, og der ikke er nok ledig plads i mindre størrelse til at indeholde data fra den seneste sikkerhedskopi, udskilles taster under gendannelsen, normalt ved hjælp af politikken [allkeys lru](http://redis.io/topics/lru-cache) eviction.

## <a name="next-steps"></a>Næste trin
Lær at bruge flere premium-funktioner i cachen.

-   [Introduktion til Azure Redis Cache Premium niveau](cache-premium-tier-intro.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
