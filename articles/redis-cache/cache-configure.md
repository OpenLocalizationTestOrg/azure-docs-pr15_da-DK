<properties 
    pageTitle="Sådan konfigureres Azure Redis Cache | Microsoft Azure"
    description="Forstå konfiguration Redis standard til Azure Redis Cache og Lær, hvordan du konfigurerer din Azure Redis Cache forekomster"
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
    ms.date="08/25/2016"
    ms.author="sdanie" />

# <a name="how-to-configure-azure-redis-cache"></a>Sådan konfigureres Azure Redis Cache

Dette emne beskrives, hvordan du gennemse og opdatere konfigurationen af din Azure Redis Cache forekomster og dækker standard Redis serverkonfiguration til Azure Redis Cache forekomster.

>[AZURE.NOTE] Du kan finde flere oplysninger om konfiguration og bruge premium-funktioner i cachen, se [Sådan konfigureres brugerdata i en Premium Azure Redis Cache](cache-how-to-premium-persistence.md), [hvordan du konfigurerer databaseserver for en Premium Azure Redis Cache](cache-how-to-premium-clustering.md)og [hvordan du kan konfigurere understøttelse af virtuelt netværk for en Premium Azure Redis Cache](cache-how-to-premium-vnet.md).

## <a name="configure-redis-cache-settings"></a>Konfigurere Redis cacheindstillinger

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Redis Cache indeholder følgende indstillinger på bladet **Indstillinger** .

![Redis cacheindstillinger](./media/cache-configure/redis-cache-settings.png)



-   [Support og fejlfinding i forbindelse med indstillinger](#support-amp-troubleshooting-settings)
-   [Generelle indstillinger](#general-settings)
    -   [Egenskaber](#properties)
    -   [Access-taster](#access-keys)
    -   [Avancerede indstillinger](#advanced-settings)
    -   [Redis Cache Advisor](#redis-cache-advisor)
-   [Indstillinger for skalering](#scale-settings)
    -   [Priser niveau](#pricing-tier)
    -   [Redis klyngestørrelse](#cluster-size)
-   [Indstillinger for administration af data](#data-management-settings)
    -   [Redis bevarelse af data](#redis-data-persistence)
    -   [Importér/Eksportér](#importexport)
-   [Indstillinger for administration](#administration-settings)
    -   [Genstart](#reboot)
    -   [Planlæg opdateringer](#schedule-updates)
-   [Indstillinger for diagnosticering](#diagnostics-settings)
-   [Netværksindstillinger](#network-settings)
-   [Indstillinger for administration af ressource](#resource-management-settings)

## <a name="support--troubleshooting-settings"></a>Support og fejlfinding i forbindelse med indstillinger

Indstillingerne i sektionen **Support + fejlfinding** giver dig indstillinger til at løse problemer med din cache.

![Understøttelse af + fejlfinding](./media/cache-configure/redis-cache-support-troubleshooting.png)

Klik på **Diagnosticer og løse problemer med** skal være forsynet med almindelige problemer og strategier til at løse dem.

Klik på **aktivitetslog** for at få vist handlinger, der udføres på din cache. Du kan også bruge filtrering til at udvide denne visning for at medtage andre ressourcer. Du kan finde flere oplysninger om at arbejde med overvågningslogge se [se begivenheder og overvågningsloggen](../monitoring-and-diagnostics/insights-debugging-with-events.md) og [overvåge forskellige handlinger med ressourcestyring](../resource-group-audit.md). Du kan finde flere oplysninger om overvågning Azure Redis Cache begivenheder, se [Handlinger og beskeder](cache-how-to-monitor.md#operations-and-alerts).

**Ressource tilstand** ser din ressource og fortæller dig, hvis den kører som forventet. Du kan finde flere oplysninger om tjenesten Azure ressource tilstand, [sundhed Azure Ressourceoversigt](../resource-health/resource-health-overview.md).

>[AZURE.NOTE] Ressource-tilstand er i øjeblikket ikke rapportere om tilstanden for Azure Redis Cache forekomster hostet i et virtuelt netværk. Du kan finde flere oplysninger, se [alle cache-funktioner fungerer, når vært for en cache i en VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

Klik på **Ny understøtter anmodning** for at åbne en supportanmodning for din cache.

## <a name="general-settings"></a>Generelle indstillinger

Indstillingerne i sektionen **Generelt** gør det muligt at få adgang til og konfigurere følgende indstillinger for din cache.

![Generelle indstillinger](./media/cache-configure/redis-cache-general-settings.png)

-   [Egenskaber](#properties)
-   [Access-taster](#access-keys)
-   [Avancerede indstillinger](#advanced-settings)
-   [Redis Cache Advisor](#redis-cache-advisor)

### <a name="properties"></a>Egenskaber

Klik på **Egenskaber** for at få vist oplysninger om din cache, herunder cache slutpunkt og porte.

![Redis Cache egenskaber](./media/cache-configure/redis-cache-properties.png)

### <a name="access-keys"></a>Access-taster

Klik på **Access-taster** til at få vist eller genoprette hurtigtasterne til din cache. Disse taster bruges sammen med værtsnavn og porte fra bladet **Egenskaber** ved de klienter, oprette forbindelse til din cache.

![Redis Cache adgangstaster](./media/cache-configure/redis-cache-manage-keys.png)






### <a name="advanced-settings"></a>Avancerede indstillinger

Følgende indstillinger er konfigurere på bladet **Avancerede indstillinger** .

-   [Access-porte](#access-ports)
-   [Maxmemory-politik og maxmemory reserveret](#maxmemory-policy-and-maxmemory-reserved)
-   [Keyspace meddelelser (avancerede indstillinger)](#keyspace-notifications-advanced-settings)


### <a name="access-ports"></a>Access-porte

Ikke-SSL-adgang er som standard deaktiveret for ny cache. Klik på **Nej** for **Tillad adgang kun via SSL** på **Avancerede indstillinger blade** for at aktivere ikke SSL-porten, og klik derefter på **Gem**.

![Redis Cache Access porte](./media/cache-configure/redis-cache-access-ports.png)

### <a name="maxmemory-policy-and-maxmemory-reserved"></a>Maxmemory-politik og maxmemory reserveret

Indstillingerne **Maxmemory politik** og **maxmemory reserveret** på bladet **Avancerede indstillinger** konfigurere hukommelse politikker for cachen. **Maxmemory -** politikindstillingen konfigurerer politikken eviction for cachen og **maxmemory reserveret** konfigurerer hukommelse reserveret til ikke-cache processer.

![Redis Cache Maxmemory politik](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Maxmemory politik** giver mulighed at vælge mellem følgende eviction politikker.

-   permanente-lru - dette er standardværdien.
-   allkeys lru
-   permanente tilfældigt
-   allkeys-tilfældigt
-   permanente ttl
-   noeviction

Du kan finde flere oplysninger om maxmemory politikker [Eviction politikker](http://redis.io/topics/lru-cache#eviction-policies).

Indstillingen **maxmemory reserveret** konfigurerer mængden hukommelse i MB, der er reserveret til ikke-cache handlinger som gentagelse under failover. Det kan også bruges, når du har en høj fragmentering-bredde-forhold. Angive denne værdi kan du skal have en mere ensartet Redis server oplevelse, når din Indlæs varierer. Denne værdi skal du vælge højere for arbejdsbelastninger, som er skrive aktiveret. Det er ikke tilgængelig til lagring af cachelagrede data, når hukommelse er reserveret til sådanne handlinger.

>[AZURE.IMPORTANT] Indstillingen **maxmemory reserveret** er kun tilgængelig for Standard og Premium gemmer.

### <a name="keyspace-notifications-advanced-settings"></a>Keyspace meddelelser (avancerede indstillinger)

Redis keyspace meddelelser er konfigureret på bladet **Avancerede indstillinger** . Keyspace beskeder tillader klienter at modtage meddelelser, når bestemte hændelser indtræffer.

![Redis Cache avancerede indstillinger](./media/cache-configure/redis-cache-advanced-settings.png)

>[AZURE.IMPORTANT] Keyspace beskeder og indstillingen **besked-keyspace-hændelser** er kun tilgængelige for Standard og Premium gemmer.

Du kan finde yderligere oplysninger finder [Redis Keyspace meddelelser](http://redis.io/topics/notifications). Eksempel på kode, finder du i [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) filen i stikprøven [Hej verden](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis Cache Advisor

Bladet **anbefalinger** viser anbefalinger til din cache. Under normale handlinger vises nogen anbefalinger. 

![Anbefalinger](./media/cache-configure/redis-cache-no-recommendations.png)

Hvis der opstår en hvilken som helst betingelser under datahandlinger af din cache som høj hukommelsesforbrug, netværksbåndbredde eller serverens belastning, vises en besked på bladet **Redis Cache** .

![Anbefalinger](./media/cache-configure/redis-cache-recommendations-alert.png)

Yderligere oplysninger kan findes på bladet **anbefalinger** .

![Anbefalinger](./media/cache-configure/redis-cache-recommendations.png)

Du kan overvåge disse målepunkter på afsnittene, [overvågnings diagrammer](cache-how-to-monitor.md#monitoring-charts) og [diagrammer brugen](cache-how-to-monitor.md#usage-charts) af bladet **Redis Cache** .

Hvert priser niveau har forskellige begrænsninger for klientforbindelser, hukommelse og båndbredde. Hvis din cache nærmer sig maksimalt kapacitet til denne statistik over en vedvarende tidsperiode, oprettes en anbefaling. Du kan finde flere oplysninger om målepunkter og begrænsninger for korrekturlæst af værktøjet **anbefalinger** , i følgende tabel.

| Redis Cache metrisk      | Flere oplysninger under                                                  |
|-------------------------|---------------------------------------------------------------------------|
| Brug af netværksbåndbredde | [Cache ydeevne - tilgængelige båndbredde](cache-faq.md#cache-performance) |
| Forbundne klienter       | [Standard Redis serverkonfiguration - maxclients](#maxclients)            |
| Serverens belastning             | [Brug af diagrammer - Redis serverens belastning](cache-how-to-monitor.md#usage-charts)  |
| Hukommelsesforbrug            | [Cache ydeevne - størrelse](cache-faq.md#cache-performance)                |

Hvis du vil opgradere din cache, skal du klikke på **Opgrader nu** for at ændre [priser niveau](#pricing-tier) og tilpasse din cache. Finde flere oplysninger om valg af et priser trin [Hvad Redis Cache tilbyder og størrelse skal jeg bruge?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="scale-settings"></a>Indstillinger for skalering

Indstillingerne i sektionen **skala** gør det muligt at få adgang til og konfigurere følgende indstillinger for din cache.

![Netværk](./media/cache-configure/redis-cache-scale.png)

-   [Priser niveau](#pricing-tier)
-   [Redis klyngestørrelse](#cluster-size)

### <a name="pricing-tier"></a>Priser niveau

Klik på **priser niveau** for at få vist eller ændre det priser niveau for din cache. Se, [hvordan du skala Azure Redis Cache](cache-how-to-scale.md)kan finde flere oplysninger om skalering.

![Redis Cache priser niveau](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>
### <a name="redis-cluster-size"></a>Redis klyngestørrelse

Klik på **(PREVIEW) Redis klyngestørrelse** for at ændre klyngestørrelsen på til en løbende premium cache med klynge aktiveret.

>[AZURE.NOTE] Bemærk, mens Azure Redis Cache Premium niveauet er blevet frigivet til at være generelt tilgængelig, funktionen Redis klyngestørrelse er i øjeblikket i Vis udskrift.

![Redis klyngestørrelse](./media/cache-configure/redis-cache-redis-cluster-size.png)

Brug skyderen for at ændre klyngestørrelse, skrive et tal mellem 1 og 10 i tekstfeltet **Shard Tæl** og klik på **OK** for at gemme.

>[AZURE.IMPORTANT] Redis klynge er kun tilgængelig for Premium cache. Se, [hvordan du konfigurerer databaseserver for en Premium Azure Redis Cache](cache-how-to-premium-clustering.md)kan finde flere oplysninger.


## <a name="data-management-settings"></a>Indstillinger for administration af data

Indstillingerne i sektionen **Datastyring** giver dig mulighed at få adgang til og konfigurere følgende indstillinger for din cache.

![Datastyring](./media/cache-configure/redis-cache-data-management.png)

-   [Redis bevarelse af data](#redis-data-persistence)
-   [Importér/Eksportér](#importexport)

### <a name="redis-data-persistence"></a>Redis bevarelse af data

Klik på **Redis bevarelse af data** for at aktivere, deaktivere eller konfigurere data brugerdata i din premium cache.

![Redis bevarelse af data](./media/cache-configure/redis-cache-persistence-settings.png)

For at aktivere Redis brugerdata skal du klikke på **aktiveret** for at aktivere RDB (Redis database) sikkerhedskopi. Klik på **deaktiveret**for at deaktivere Redis brugerdata.

For at konfigurere det ekstra interval skal du vælge en **Hyppighed af sikkerhedskopiering** fra på rullelisten. Valgmuligheder omfatter **15 minutter**, **30 minutter**, **60 minutter**, **6 timer**, **12 timer**og **24 timer**. Dette interval starter optælling, når forrige sikkerhedskopieringen er fuldført, og når det går startes en ny sikkerhedskopi.

Klik på **Lagerplads konto** for at markere lagerplads-konto, der skal bruges, og vælg enten **primær nøgle** eller **sekundær nøgle** bruge rullelisten **Lagerplads nøgle** . Skal du vælge en lagerplads konto i det samme område som cachen, og en **Premium lagerplads** konto anbefales, fordi premium lagerplads har højere overførselshastighed. Når lagerplads nøglen til kontoen brugerdata gendannes, skal du vælge den ønskede nøgle igen fra rullelisten **Lagerplads nøgle** .

Klik på **OK** for at gemme brugerdata konfigurationen.

>[AZURE.IMPORTANT] Redis data brugerdata er kun tilgængelig for Premium cache. Se, [hvordan du konfigurerer brugerdata i en Premium Azure Redis Cache](cache-how-to-premium-persistence.md)kan finde flere oplysninger.

### <a name="importexport"></a>Importér/Eksportér

Importér/Eksportér er en Azure Redis Cache data management-handling, så du kan importere data til Azure Redis Cache eller eksportere data fra Azure Redis Cache ved at importere og eksportere et snapshot af en Redis Cache Database (RDB) fra en premium cache til en side blob på en Azure-lager-konto. Dette gør det muligt at overføre mellem forskellige Azure Redis Cache forekomster eller udfylde cachen med data, før brug.

Importér kan bruges til at hente Redis kompatible RDB filer fra en hvilken som helst Redis server, der kører i skyen eller miljøet, herunder Redis kører på Linux, Windows eller enhver skyen udbyder som Amazon webtjenester og andre. Import af data er en nem måde at oprette en cache med udfyldt på forhånd data. Under importen Azure Redis Cache indlæser RDB filer fra Azure-lager i hukommelsen og derefter indsætter tasterne i cachen.

Eksportér giver dig mulighed at eksportere de data, der er gemt i Azure Redis Cache for Redis kompatible RDB fil(er). Du kan bruge denne funktion til at flytte data fra én Azure Redis Cache-forekomst, til en anden eller til en anden Redis server. Under eksporten en midlertidig fil oprettes på VM, der er vært Azure Redis Cache server-forekomst, og filen er overført til kontoen udpegede lagerplads. Når eksporten er fuldført med enten en status for et gunstigt udfald eller fejl, slettes den midlertidige fil.

>[AZURE.IMPORTANT] Importér/Eksportér er kun tilgængelig for Premium niveau cache. Se flere oplysninger og instruktioner, [importere og eksportere data i Azure Redis Cache](cache-how-to-import-export-data.md).


## <a name="administration-settings"></a>Indstillinger for administration

Indstillinger i sektionen **Administration** kan du udføre følgende administrative opgaver til premium-cache. 

![Administration](./media/cache-configure/redis-cache-administration.png)

-   [Genstart](#reboot)
-   [Planlæg opdateringer](#schedule-updates)

>[AZURE.IMPORTANT] Indstillingerne i dette afsnit er kun tilgængelige for Premium niveau cache.

### <a name="reboot"></a>Genstart

Bladet **Genstart** gør det muligt for dig at genstarte en eller flere noder af din cache. Dette gør det muligt at teste din ansøgning om fleksibilitet i tilfælde af en fejl.

![Genstart](./media/cache-configure/redis-cache-reboot.png)

Hvis du har en premium cache med klynge aktiveret, kan du vælge hvilke shards cachen at genstarte computeren.

![Genstart](./media/cache-configure/redis-cache-reboot-cluster.png)

Genstarte en eller flere noder af din cache, Vælg de ønskede noder, og klik på **Genstart**. Hvis du har en premium cache med klynge aktiveret, kan du vælge shard(s) genstart, og klik derefter på **Genstart**. Efter et par minutter igen det valgte node(r) genstart, og du er online senere et par minutter.

>[AZURE.IMPORTANT] Genstart er kun tilgængelig for Premium niveau cache. Flere oplysninger og vejledninger, finder du under [administration af Azure Redis Cache - genstart](cache-administration.md#reboot).

### <a name="schedule-updates"></a>Planlæg opdateringer

**Planlæg opdateringer** blade kan du angive et vedligeholdelsesvindue til Redis server-opdateringer i din cache. 

>[AZURE.IMPORTANT] Bemærk, at vinduet reparation gælder kun for Redis serveropdateringer, og ikke til en hvilken som helst Azure opdateres, eller indeholder sikkerhedsopdateringer til operativsystemet af FOS, der hoster cachen.

![Planlæg opdateringer](./media/cache-configure/redis-schedule-updates.png)

Markér de ønskede dage for at angive et vedligeholdelsesvindue, og Angiv vedligeholdelse vinduet starttidspunktet for hver dag, og klik på **OK**. Bemærk, at det vedligeholdelse vindue tidspunkt er i UTC. 

>[AZURE.IMPORTANT] Planlæg opdateringer er kun tilgængelig for Premium niveau cachelagre. Se [Azure Redis Cache administration - Planlæg opdateringer](cache-administration.md#schedule-updates)for flere oplysninger og vejledninger.

## <a name="diagnostics-settings"></a>Indstillinger for diagnosticering

Afsnittet **diagnosticering** kan du konfigurere diagnosticering til din Redis Cache.

![Diagnosticering](./media/cache-configure/redis-cache-diagnostics.png)

Klik på **diagnosticering** til at [konfigurere kontoen lagerplads](cache-how-to-monitor.md#enable-cache-diagnostics) bruges til at gemme cache diagnosticering.

![Redis Cache diagnosticering](./media/cache-configure/redis-cache-diagnostics-settings.png)

Klik på **Redis målepunkter** til at [få vist målepunkter](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts) til cache og **regler for påmindelser** til at [konfigurere regler for påmindelser](cache-how-to-monitor.md#operations-and-alerts).

Du kan finde flere oplysninger om Azure Redis Cache diagnosticering, se, [hvordan du overvåge Azure Redis Cache](cache-how-to-monitor.md).


## <a name="network-settings"></a>Netværksindstillinger

Indstillinger i afsnittet **netværk** gør det muligt at få adgang til og konfigurere følgende indstillinger for din cache.

![Netværk](./media/cache-configure/redis-cache-network.png)

>[AZURE.IMPORTANT] Virtuel netværksindstillinger er kun tilgængelige for premium cachelagre, der er konfigureret med VNET support under oprettelse af cache. Understøtter oplysninger om oprettelse af en premium cache med VNET og opdaterer indstillingerne for den, se, [hvordan du konfigurerer virtuelle netværk Support til en Premium Azure Redis Cache](cache-how-to-premium-vnet.md).

## <a name="resource-management-settings"></a>Indstillinger for administration af ressource

![Ressourcestyring](./media/cache-configure/redis-cache-resource-management.png)

Sektionen **mærker** hjælper dig med at organisere dine ressourcer. Du kan finde yderligere oplysninger [ved hjælp af mærker til at organisere dine Azure ressourcer](../resource-group-using-tags.md).

Sektionen **låser** kan du låse et abonnement, ressourcegruppe eller ressource til at forhindre andre brugere i organisationen fra kommer til at slette eller ændre kritiske ressourcer. Du kan finde yderligere oplysninger finder [Lås ressourcer med Azure ressourcestyring](../resource-group-lock-resources.md).

Afsnittet **brugere** understøtter rollebaseret adgangskontrol (RBAC) i Azure portalen for at hjælpe organisationer med at overholde krav til deres adgang, skal du blot og præcist. Du kan finde yderligere oplysninger finder [Rollebaseret adgangskontrol i portalen Azure](../active-directory/role-based-access-control-configure.md).

Klik på **Eksporter skabelon** for at opbygge og eksportere en skabelon for ressourcerne udløst til fremtidige installationer. Du kan finde flere oplysninger om at arbejde med skabeloner, [Implementer ressourcer med Azure ressourcestyring skabeloner](../resource-group-template-deploy.md).

## <a name="default-redis-server-configuration"></a>Standard Redis serverkonfiguration

Nye Azure Redis Cache forekomster er konfigureret med følgende Redis konfiguration standardværdier.

>[AZURE.NOTE] Indstillingerne i dette afsnit kan ikke ændres ved hjælp af den `StackExchange.Redis.IServer.ConfigSet` metode. Hvis denne metode kaldes med en af kommandoer i dette afsnit, der genereres en undtagelse, der er som følger:  
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>De værdier, der kan konfigureres, som **max-hukommelse-politik**, kan konfigureres gennem de Azure portal eller kommandolinjen administrationsværktøjer som Azure CLI eller PowerShell.

|Indstilling|Standardværdi|Beskrivelse|
|---|---|---|
|databaser|16|Standardantallet af databaser er 16, men du kan konfigurere et andet nummer, der er baseret på priser niveau. <sup>1</sup> standarddatabasen er DB 0, kan du vælge et andet navn på en hver forbindelse datotype ved hjælp af `connection.GetDatabase(dbid)` hvor database-id er et tal mellem `0` og `databases - 1`.|
|maxclients|Afhænger af den priser trin<sup>2</sup>|Dette er det maksimale antal indbyrdes forbundne kunder, der er tilladt på samme tid. Når grænsen er nået Luk Redis alle nye forbindelserne at sende en fejl 'maksimale antal klienter, der er nået'.|
|maxmemory-politik|permanente lru|Maxmemory politik er indstillingen til hvordan Redis vælger, hvad der skal fjernes, når maxmemory (størrelsen af cachen tilbyder, du valgte, da du oprettede cachen) er nået. Standardindstillingen er med Azure Redis Cache permanente-lru, som fjerner de pågældende taster med et sæt med en LRU algoritme udløbe. Denne indstilling kan konfigureres i portalen Azure. Kan finde flere oplysninger under [Maxmemory-politik og maxmemory reserveret](#maxmemory-policy-and-maxmemory-reserved).|
|maxmemory-eksempler|3|LRU og minimale TTL algoritmer ikke er præcis algoritmer, men tilnærmet algoritmer (for at gemme hukommelse), så du kan vælge samt stikprøvestørrelsen skal kontrolleres. For eksempel til standard Redis kontrolleres tre taster, og vælg det, der blev brugt mindre for nylig.|
|LUA tidsgrænse|5.000|Maks kørselstid af et Lua script i millisekunder. Hvis den maksimale kørselstid er nået logge Redis, der et script er stadig i udførelse af efter maksimalt tilladte tid og begynder at besvare forespørgsler med en fejl.|
|LUA-begivenhed-grænse|500|Dette er den maksimale størrelse for script begivenhed kø.|
|klient-output-bufferen-grænse normalclient-output-bufferen-grænse pubsub|0 0 032mb 8mb 60|Klient output bufferen begrænsninger kan bruges til at gennemtvinge afbrydelse af klienter, der ikke læse data fra serveren hurtigt nok til en eller anden grund (en almindelig årsag er, at en Pub/Sub-klient ikke kan bruge meddelelser, der er lige så hurtigt som udgiveren kan producerer dem). Du kan finde flere oplysninger [http://redis.io/topics/clients](http://redis.io/topics/clients).|

<a name="databases"></a>
<sup>1</sup> Grænsen for `databases` er forskellige for hver Azure Redis-cachen priser niveau og kan angives til oprettelse af cache. Hvis ingen `databases` indstilling er angivet under oprettelse af cachen, standardværdien er 16.

-   Grundlæggende og Standard cache
    -   C0 (250 MB) cache - op til 16 databaser
    -   C1 (1 GB) cache - op til 16 databaser
    -   C2 (2,5 GB) cache - op til 16 databaser
    -   C3 (6 GB) cache - op til 16 databaser
    -   C4 (13 GB) cache - op til 32 databaser
    -   C5 (26 GB) cache - op til 48 databaser
    -   C6 (53 GB) cache - op til 64 databaser
-   Premium cachelagre
    -   P1 (6 GB - 60 GB) - op til 16 databaser
    -   P2 (13 GB - 130 GB) - op til 32 databaser
    -   P3 (26 GB - 260 GB) - op til 48 databaser
    -   P4 (53 GB - 530 GB) - op til 64 databaser
    -   Alle premium cache med Redis klynge aktiveret - Redis klynge understøtter kun brug af database 0 så `databases` begrænse for en hvilken som helst premium cache med Redis klynge aktiveret er værdien 1 og kommandoen [Select](http://redis.io/commands/select) er ikke tilladt. Du kan finde flere oplysninger, se [har jeg brug at foretage ændringer til min klientprogrammet bruge klynge?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)


>[AZURE.NOTE] Den `databases` indstilling kan være konfigureret kun under oprettelse af cache og kun ved hjælp af PowerShell, CLI eller andre management-klienter. Et eksempel på konfiguration af `databases` under oprettelse af cache ved hjælp af PowerShell, se [Ny AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).


<a name="maxclients"></a>
<sup>2</sup> `maxclients` adskiller sig til hver Azure Redis-Cache priser niveau.

-   Grundlæggende og Standard cache
    -   C0 (250 MB) cache - op til 256 forbindelser
    -   C1 (1 GB) cache - op til 1.000 forbindelser
    -   C2 (2,5 GB) cache - op til 2.000 forbindelser
    -   C3 (6 GB) cache - op til 5.000 forbindelser
    -   C4 (13 GB) cache - op til 10.000 forbindelser
    -   C5 (26 GB) cache - op til 15.000 forbindelser
    -   C6 (53 GB) cache - op til 20.000 forbindelser
-   Premium cache
    -   P1 (6 GB - 60 GB) - op til 7.500 forbindelser
    -   P2 (13 GB - 130 GB) - op til 15.000 forbindelser
    -   P3 (26 GB - 260 GB) - op til 30.000 forbindelser
    -   P4 (53 GB - 530 GB) - op til 40.000 forbindelser

## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Redis kommandoer, der ikke understøttes i Azure Redis Cache

>[AZURE.IMPORTANT] Følgende kommandoer er deaktiveret, fordi konfiguration og administration af Azure Redis Cache forekomster administreres af Microsoft. Hvis du forsøger at aktivere dem, du modtager en fejlmeddelelse i stil til `"(error) ERR unknown command"`.
>
>-  BGREWRITEAOF
>-  BGSAVE
>-  CONFIG
>-  FEJLFINDING AF
>-  OVERFØRE
>-  GEM
>-  LUKNING
>-  SLAVEOF
>-  KLYNGE - klynge skrive kommandoer er deaktiveret, men skrivebeskyttet klynge kommandoer er tilladt.

Du kan finde flere oplysninger om Redis kommandoer, [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Redis console

Du kan udføre kommandoer sikkert til din Azure Redis Cache forekomster ved hjælp af **Redis Console**, som er tilgængelige for Standard og Premium gemmer.

>[AZURE.IMPORTANT] Konsollen Redis fungerer ikke sammen med VNET, klynge, og andre end 0-databaser. 
>
>-  [VNET](cache-how-to-premium-vnet.md) - når din cache er en del af en VNET, kun klienter i VNET kan få adgang til cachen. Da konsollen Redis bruger redis cli.exe klienten hostes på VM'er, der ikke er en del af din VNET, kan den ikke oprette forbindelse til din cache.
>-  [Clustering](cache-how-to-premium-clustering.md) - feltet Redis Console bruger redis cli.exe klienten som ikke understøtter klynge på nuværende tidspunkt. Værktøjet redis cli i [ustabil](http://redis.io/download) grenen af Redis lager på GitHub implementerer grundlæggende support, når det i gang med det `-c` skifte. Finde flere oplysninger under [afspilning med klyngen](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) på [http://redis.io](http://redis.io) i [Redis klynge selvstudium](http://redis.io/topics/cluster-tutorial).
>-  Konsollen Redis opretter en ny forbindelse til databasen 0, hver gang, du sender en kommando. Du kan ikke bruge den `SELECT` kommando for at vælge en anden database, fordi databasen nulstilles til 0 med hver kommando. Finde oplysninger om at køre Redis kommandoer, herunder ændre til en anden database [hvordan kan jeg køre Redis kommandoer?](cache-faq.md#how-can-i-run-redis-commands)

Klik på **konsol** fra bladet **Redis Cache** for at få adgang til konsollen Redis.

![Redis console](./media/cache-configure/redis-console-menu.png)

For at udstede kommandoer din cache-forekomst, du blot skrive den ønskede kommando i konsollen.

![Redis console](./media/cache-configure/redis-console.png)

Liste over Redis kommandoer, som er deaktiveret for Azure Redis cachen, i afsnittet tidligere [Redis kommandoer, der ikke understøttes i Azure Redis Cache](#redis-commands-not-supported-in-azure-redis-cache) . Du kan finde flere oplysninger om Redis kommandoer, [http://redis.io/commands](http://redis.io/commands). 

## <a name="move-your-cache-to-a-new-subscription"></a>Flytte din cache til et nyt abonnement

Du kan flytte din cache til et nyt abonnement ved at klikke på **Flyt**.

![Flytte Redis Cache](./media/cache-configure/redis-cache-move.png)

Se [flytte ressourcer til ny ressourcegruppe eller et andet abonnement](../resource-group-move-resources.md)for at få oplysninger om flytning af ressourcer fra én ressourcegruppe til en anden, og fra et abonnement til en anden.

## <a name="next-steps"></a>Næste trin
-   Finde flere oplysninger om at arbejde med Redis kommandoer [hvordan kan jeg køre Redis kommandoer?](cache-faq.md#how-can-i-run-redis-commands).
