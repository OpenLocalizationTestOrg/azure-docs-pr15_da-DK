<properties 
    pageTitle="Sådan administrerer du Azure Redis Cache | Microsoft Azure"
    description="Lær at udføre administrationsopgaver såsom genstarte og Planlæg opdateringer for Azure Redis Cache"
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
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="how-to-administer-azure-redis-cache"></a>Sådan administrerer du Azure Redis Cache

Dette emne beskrives, hvordan du kan udføre administrationsopgaver såsom at du har genstartet og planlægning af opdateringer til din Azure Redis Cache forekomster.

>[AZURE.IMPORTANT] De indstillinger og funktioner, der er beskrevet i denne artikel er kun tilgængelige for Premium niveau cache.


## <a name="administration-settings"></a>Indstillinger for administration

Indstillingerne for Azure Redis Cache **Administration** kan du udføre følgende administrative opgaver til premium-cache. For at få adgang til af administrationsindstillinger, klik på **Listeindstillinger** eller **Indstillinger for alle** fra Redis Cache blade og Rul til sektionen **Administration** i bladet **Indstillinger** .

![Administration](./media/cache-administration/redis-cache-administration.png)

-   [Genstart](#reboot)
-   [Planlæg opdateringer](#schedule-updates)

## <a name="reboot"></a>Genstart

Bladet **Genstart** gør det muligt for dig at genstarte en eller flere noder af din cache. Dette gør det muligt at teste din ansøgning om fleksibilitet i tilfælde af en fejl.

![Genstart](./media/cache-administration/redis-cache-reboot.png)

Hvis du har en premium cache med klynge aktiveret, kan du vælge hvilke shards cachen at genstarte computeren.

![Genstart](./media/cache-administration/redis-cache-reboot-cluster.png)

Vælg de ønskede noder for at genstarte en eller flere noder af din cache, og klik på **Genstart**. Hvis du har en premium cache med klynge aktiveret, kan du vælge shard(s) genstart, og klik derefter på **Genstart**. Efter et par minutter igen det valgte node(r) genstart, og du er online senere et par minutter.

Klientprogrammer påvirkningen varierer afhængigt af den node(r), at du genstarter.

-   **Master** - når noden master genstartes, Azure Redis Cache skifter til noden replika og øger det master. Under denne failover, kan der være et kort interval, hvor forbindelser, kan muligvis ikke cachen.
-   **Slave** - når noden slave genstartes, der typisk ikke nogen betydning for cache klienter.
-   **Begge mastersider og slave** - når begge cache noder er genstartet, går tabt alle data i cachen og forbindelser til cache-fejl, indtil den primære node træder igen er online. Hvis du har konfigureret [data brugerdata](cache-how-to-premium-persistence.md), genoprettes den seneste sikkerhedskopiering, når cachen dukker igen er online. Bemærk, at en hvilken som helst cache skriver, der er opstået efter den seneste sikkerhedskopiering går tabt.
-   **Node(r) af en premium cache med klynge aktiveret** – når du genstarter node(r) af en premium cache med klynge aktiveret, funktionsmåden er den samme som når du genstarter node(r) af en ikke-grupperede cache.


>[AZURE.IMPORTANT] Genstart er kun tilgængelig for Premium niveau cache.

## <a name="reboot-faq"></a>Genstart ofte stillede spørgsmål

-   [Hvilke node skal jeg genstart for at teste mit program?](#which-node-should-i-reboot-to-test-my-application)
-   [Kan jeg genstarte cachen for at rydde klientforbindelser?](#can-i-reboot-the-cache-to-clear-client-connections)
-   [Mister jeg data fra min cache Hvis en genstart jeg?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
-   [Kan jeg genstarte min cachen, ved hjælp af PowerShell, CLI eller andre administrationsværktøjer?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
-   [Hvilke priser niveauer kan bruge funktionen genstart?](#what-pricing-tiers-can-use-the-reboot-functionality)


### <a name="which-node-should-i-reboot-to-test-my-application"></a>Hvilke node skal jeg genstart for at teste mit program?

Genstart noden **Master** for at teste fleksibilitet i dit program mod af den primære node af din cache. Genstart noden **Slave** for at teste fleksibilitet i dit program mod af den sekundære node. For at teste fleksibilitet i dit program mod tænkelige af cachen, skal du genstarte **begge** noder.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Kan jeg genstarte cachen for at rydde klientforbindelser?

Ja, hvis du genstarter cachen alle klientforbindelser ikke er markeret. Dette kan være nyttigt i tilfælde, hvor alle klientforbindelser bruges op, for eksempel på grund af en logisk fejl eller en fejl i klientprogrammet. Hvert priser niveau har forskellige [klienten forbindelse begrænsninger](cache-configure.md#default-redis-server-configuration) for de forskellige størrelser, og når disse grænser er nået, ingen flere klientforbindelser accepteres. At du har genstartet cachen indeholder en måde at fjerne alle klientforbindelser.

>[AZURE.IMPORTANT] Hvis din klientforbindelser bruges på grund af en logisk fejl eller fejl i Klientkoden, være opmærksom på, at forbinde StackExchange.Redis automatisk, når noden Redis er igen er online. Hvis det underliggende ikke løser problemet, vil klientforbindelser fortsat bruges.

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Mister jeg data fra min cache Hvis en genstart jeg?

Hvis du genstarter **Master** - og **Slave** noder alle data i cachen (eller i shard, hvis du bruger en premium cache med klynge aktiveret) går tabt. Hvis du har konfigureret [data brugerdata](cache-how-to-premium-persistence.md), bliver den seneste sikkerhedskopiering gendannet, når cachen dukker igen er online. Bemærk, at en hvilken som helst cache skriver, der er opstået efter at sikkerhedskopien blev oprettet går tabt.

Hvis du genstarter kun én af noderne, data er ikke typisk tabt, men det kan stadig være. For eksempel hvis noden master genstartes og en cache skrivning er i gang, data fra cachen Skriv går tabt. Ville være et andet scenarie for tab af data, hvis du genstarte en node og anden noden sker der med gå på grund af en mislykket på samme tid. Finde flere oplysninger om mulige årsager til tab af data, [Hvad skete der med mine data i Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md).

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Kan jeg genstarte min cachen, ved hjælp af PowerShell, CLI eller andre administrationsværktøjer?

Ja, til PowerShell finder instruktioner [til at genstarte en Redis cache](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Hvilke priser niveauer kan bruge funktionen genstart?

Genstart er kun tilgængelig i premium priser niveau.

## <a name="schedule-updates"></a>Planlæg opdateringer

**Planlæg opdateringer** blade kan du angive et vedligeholdelsesvindue til din cache. Når vinduet reparation er angivet, foretages Redis serveropdateringer under dette vindue. Bemærk, at vinduet reparation gælder kun for Redis serveropdateringer, og ikke til en hvilken som helst Azure opdateres, eller indeholder sikkerhedsopdateringer til operativsystemet af FOS, der hoster cachen.

![Planlæg opdateringer](./media/cache-administration/redis-schedule-updates.png)

Markér de ønskede dage for at angive et vedligeholdelsesvindue, og Angiv vedligeholdelse vinduet starttidspunktet for hver dag, og klik på **OK**. Bemærk, at det vedligeholdelse vindue tidspunkt er i UTC. 

>[AZURE.NOTE] Vinduet standard vedligeholdelse efter opdateringer er 5 timer. Denne værdi kan ikke konfigureres fra Azure-portalen, men du kan konfigurere på PowerShell ved hjælp af den `MaintenanceWindow` -parameter for [Ny AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx) cmdlet. Du kan finde flere oplysninger, se [kan jeg administrerede planlagte opdateringer ved hjælp af PowerShell, CLI eller andre administrationsværktøjer?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

## <a name="schedule-updates-faq"></a>Planlægge opdateringer ofte stillede spørgsmål

-   [Når forekommer opdateringer, hvis jeg ikke bruger funktionen tidsplan opdateringer?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
-   [Hvilken type opdateringer er foretaget i vinduet planlagt vedligeholdelse?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
-   [Kan jeg administrerede planlagte opdateringer ved hjælp af PowerShell, CLI eller andre administrationsværktøjer?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
-   [Hvilke priser niveauer kan bruge funktionen tidsplan opdateringer?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Når forekommer opdateringer, hvis jeg ikke bruger funktionen tidsplan opdateringer?

Hvis du ikke angiver et vedligeholdelsesvindue, der er foretaget opdateringer til enhver tid.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Hvilken type opdateringer er foretaget i vinduet planlagt vedligeholdelse?

Kun Redis server, der er foretaget opdateringer under vinduet planlagt vedligeholdelse. Vinduet reparation gælder ikke til Azure opdateringer eller opdateringer til VM-operativsystemet.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Kan jeg administrerede planlagte opdateringer ved hjælp af PowerShell, CLI eller andre administrationsværktøjer?

Ja, kan du administrere dine planlagte opdateringer ved hjælp af de følgende PowerShell-cmdletter.

-   [Get-AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763835.aspx)
-   [Ny AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763834.aspx)
-   [Ny AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx)
-   [Fjern AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763837.aspx)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Hvilke priser niveauer kan bruge funktionen tidsplan opdateringer?

Planlæg opdateringer er kun tilgængelig i premium priser niveau.

## <a name="next-steps"></a>Næste trin

-   Oplev flere funktioner i [Azure Redis Cache premium niveau](cache-premium-tier-intro.md) .





