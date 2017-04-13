<properties 
    pageTitle="Hvordan du kan overvåge Azure Redis Cache | Microsoft Azure" 
    description="Lær, hvordan du overvåge systemtilstand og ydeevne din Azure Redis Cache forekomster" 
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
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-monitor-azure-redis-cache"></a>Hvordan du kan overvåge Azure Redis Cache

Azure Redis Cache er der flere muligheder for overvågning din cache forekomster. Du kan se målepunkter, fastgøre målepunkter diagrammer til Startboard, tilpasse dato- og klokkeslætsintervallet for overvågning diagrammer, tilføje og fjerne målepunkter fra diagrammerne og angive vigtige beskeder, når bestemte betingelser er opfyldt. Disse funktioner gør det muligt at overvåge tilstanden for din Azure Redis Cache forekomster og hjælpe dig med at administrere dine cachelagring programmer.

Når cache diagnosticering er aktiveret, er måleredskaber for Azure Redis Cache forekomster indsamles cirka hvert 30 og gemt, så de kan vises i målepunkter diagrammerne og vurderet af regler for påmindelser.

Cache målepunkter er indsamlet ved hjælp af Redis [oplysninger](http://redis.io/commands/info) kommandoen. Du kan finde flere oplysninger om de forskellige oplysninger værdier, der bruges for hver cache metrisk, se [tilgængelige målepunkter og -rapportering intervaller](#available-metrics-and-reporting-intervals).

Sådan får du vist cache for statistik, [Gå](cache-configure.md#configure-redis-cache-settings) til din cache forekomst i [Azure-portalen](https://portal.azure.com). Måleredskaber for Azure Redis Cache forekomster kan findes på bladet **Redis målepunkter** .

![Redis målepunkter][redis-cache-redis-metrics-blade]

>[AZURE.IMPORTANT] Hvis følgende meddelelse vises i bladet **Redis målepunkter** , skal du følge trinnene i afsnittet [aktivere cachen diagnosticering](#enable-cache-diagnostics) til at aktivere cachen diagnosticering.
>
>`Monitoring may not be enabled. Click here to turn on Diagnostics.`

Bladet **Redis målepunkter** har **overvågnings** diagrammer, der viser cache målepunkter. De enkelte diagrammer kan tilpasses ved at tilføje eller fjerne målepunkter og ændre det interval, der rapportering. Til visning og konfiguration af handlinger og beskeder, har bladet **Redis Cache** afsnittet **Handlinger** , der viser cache **begivenheder** og **regler for påmindelser**.

## <a name="enable-cache-diagnostics"></a>Aktivere cachen diagnosticering

Azure Redis Cache giver dig mulighed for at få diagnosticering dataene, der er gemt med en konto med lagerplads, så du kan bruge alle funktioner, du vil få adgang til og behandle data direkte. Hvis cachen diagnosticering skal indsamles, skal skal gemmes, og vises i portalen Azure, en lagerplads konto være konfigureret. Cachelagre i samme område og abonnement dele den samme diagnosticering lagerplads konto, og når konfigurationen ændres gælder for alle cachelagre i abonnementet, der er i det pågældende område.

For at aktivere og konfigurere cache diagnosticering skal du gå til bladet **Redis Cache** for din cache forekomst. Hvis diagnosticering ikke er endnu aktiveret, vises en meddelelse i stedet for et diagram til diagnosticering.

![Aktivere cachen diagnosticering][redis-cache-enable-diagnostics]

Klik på meddelelsen for at få vist bladet **metrisk** og klik på **diagnosticering indstillinger** for at aktivere og konfigurere diagnosticering indstillingerne for forekomsten af cache.

![Indstillinger for diagnosticering][redis-cache-diagnostic-settings]

![Konfigurere diagnosticering][redis-cache-configure-diagnostics]

Klik på knappen **på** for at aktivere cachen diagnostics og vise diagnostics konfigurationen.

Klik på pilen til højre for **Lagerplads konto** for at vælge en lagerplads konto til at indeholde diagnosticering data. Bedste ydeevne ved at vælge en lagerplads konto i det samme område som din cache.

Når indstillingerne for diagnosticering er konfigureret, kan du klikke på **Gem** for at gemme konfigurationen. Bemærk, at det kan tage et øjeblik, før ændringerne kan træde i kraft.

>[AZURE.IMPORTANT] Cachelagre i samme område og abonnement dele de samme diagnosticering lagerplads indstillinger, og når konfigurationen er ændret (Diagnosticering aktiveret/deaktiveret eller ændre kontoen lagerplads) gælder for alle cache i abonnementet, der er i det pågældende område.

For at få vist den lagrede målepunkter, undersøge tabeller i din lagerplads konto med navne, der starter med `WADMetrics`. Du kan finde flere oplysninger om at få adgang til den lagrede målepunkter uden for portalen Azure, eksemplet [Access Redis Cache overvågning data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) .

>[AZURE.NOTE] Kun målepunkter, der er gemt i den valgte lagerplads konto vises i portalen Azure. Hvis du ændrer lagerplads konti, dataene i kontoen, der er konfigureret tidligere lagerplads bliver tilgængelig til hentning, men den vises ikke i portalen Azure.  

## <a name="available-metrics-and-reporting-intervals"></a>Tilgængelige målepunkter og -rapportering intervaller

Cache målepunkter rapporteres ved hjælp af flere reporting intervaller, herunder **tidligere time**, **i dag**, **sidste uge**og **brugerdefineret**. Bladet **metrisk** for hvert målepunkter diagram viser de middel, mindste og største værdier for hver metrisk i diagrammet, og nogle målepunkter viser et total for rapportering intervallet. 

Hver metrisk indeholder to versioner. En metrikværdi måler ydeevne for hele cachen og for cache, der bruger [klynge](cache-how-to-premium-clustering.md), en anden version af den metrikværdi, der indeholder `(Shard 0-9)` i navnet målinger ydeevne for en enkelt shard i en cache. Eksempelvis hvis en cache har 4 shards `Cache Hits` er den samlede størrelse af søgeresultaterne for hele cachen og `Cache Hits (Shard 3)` er lige søgeresultater for pågældende shard af cachen.

>[AZURE.NOTE] Selvom cachen er inaktiv med ingen tilsluttede aktive klientprogrammer, kan du se nogle cache aktivitet, som indbyrdes forbundne kunder, hukommelsesforbrug og handlinger, der udføres. Denne aktivitet er normal under handlingen af en forekomst, Azure Redis Cache.

| Metrisk            | Beskrivelse                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| I cachen        | Antallet af vellykket vigtige opslag under det angivne reporting interval. Dette er tilknyttet `keyspace_hits` fra Redis [oplysninger](http://redis.io/commands/info) kommando.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Udgivelsescachen      | Antallet af mislykkede vigtige opslag under det angivne reporting interval. Dette er tilknyttet `keyspace_misses` fra kommandoen Redis oplysninger. Udgivelsescachen, ikke nødvendigvis der er et problem med cachen. For eksempel, når du bruger cache-side programming mønstret, ser et program første i cachen for et element. Hvis elementet ikke er der (fundne forekomster), er varen hentet fra databasen og føjet til cachen til næste gang. Udgivelsescachen er normal funktionsmåde for cache-side programming mønster. Hvis tallet i cachen mislykkedes er højere end forventet, undersøge, om programmet logik, der udfylder og læser fra cachen. Hvis elementer er der udskilt fra cachen på grund af hukommelsesforbrug og derefter der kan være nogle udgivelsescachen, men der ville være en bedre metrikværdi til at overvåge for hukommelsesforbrug `Used Memory` eller `Evicted Keys`. |
| Forbundne klienter | Antallet af klientforbindelser til cachen under det angivne reporting interval. Dette er tilknyttet `connected_clients` fra kommandoen Redis oplysninger. Når [forbindelsen grænse](cache-configure.md#default-redis-server-configuration) er nået mislykkes efterfølgende forbindelse forsøg på at cachen. Bemærk, selvom der ikke er nogen aktive klientprogrammet, der kan stadig være et par forekomster af indbyrdes forbundne kunder på grund af interne processer og forbindelser.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Udskilt taster      | Antallet af elementer udskilt fra cachen under det angivne reporting interval skyldes det `maxmemory` grænse. Dette er tilknyttet `evicted_keys` fra kommandoen Redis oplysninger.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Udløbet taster      | Antallet af elementer udløbet fra cachen under det angivne reporting interval. Denne værdi, der er tilknyttet `expired_keys` fra kommandoen Redis oplysninger.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Får              | Antallet af få handlinger fra cachen under det angivne reporting interval. Denne værdi er summen af følgende værdier fra de Redis oplysninger kommandoen alle: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, og `cmdstat_getrange`, og er svarer til summen af i cachen og kiksere under det reporting interval.                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Redis serverens belastning | Procentdelen af skifter, hvori Redis serveren er optaget, behandler og ikke afventer inaktiv for meddelelser. Hvis tælleren når 100, betyder det Redis serveren har ramme et ydeevne loft og CPU'EN kan ikke behandle en arbejde hurtigere. Hvis du får vist høj belastning af Redis Server får du vist timeout undtagelser i klienten. I dette tilfælde skal du overveje skalering eller partitionering dine data i flere cache.                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Sæt              | Antallet af sæt handlinger til cachen under det angivne reporting interval. Denne værdi er summen af følgende værdier fra de Redis oplysninger kommandoen alle: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange`, og `cmdstat_setnx`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Samlet antal handlinger  | Det samlede antal kommandoer behandles af cache server under det angivne reporting interval. Denne værdi, der er tilknyttet `total_commands_processed` fra kommandoen Redis oplysninger. Bemærk, at når Azure Redis Cache bruges kun til pub/sub bliver der ingen måleredskaber for `Cache Hits`, `Cache Misses`, `Gets`, eller `Sets`, men der vil være `Total Operations` målepunkter, der afspejler cache forbruget for pub/sub handlinger.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Anvendte hukommelse       | Mængden af cache-hukommelse, der bruges til nøgle/værdi-par i cachen i MB under det angivne reporting interval. Denne værdi, der er tilknyttet `used_memory` fra kommandoen Redis oplysninger. Dette omfatter ikke metadata eller findelt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Anvendte hukommelse RSS   | Mængden af cache-hukommelse brugt i MB under det angivne reporting interval, herunder fragmentering og metadata. Denne værdi, der er tilknyttet `used_memory_rss` fra kommandoen Redis oplysninger.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| CPU               | CPU anvendelsen af Azure Redis Cache-server som en procentdel under det angivne reporting interval. Denne værdi, der er tilknyttet operativsystemet `\Processor(_Total)\% Processor Time` ydeevne tæller.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Cache læst        | Mængden data, der læses fra cachen i megabyte sekundet (MB/s) under det angivne reporting interval. Denne værdi er udledt fra netværkskort, der understøtter den virtuelle maskine, der hoster cachen og er ikke Redis bestemt. **Denne værdi svarer til den netværksbåndbredde, der bruges af denne cache. Hvis du vil konfigurere beskeder for server side netværksbåndbredde begrænsninger, derefter oprette den ved hjælp af dette `Cache Read` tæller. Se [denne tabel](cache-faq.md#cache-performance) for observerede båndbredde begrænsninger for forskellige cache priser niveauer og størrelser.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Skriv cache       | Mængden data, der er skrevet i cachen i megabyte sekundet (MB/s) under den angivne rapportering interval. Denne værdi er udledt fra netværkskort, der understøtter den virtuelle maskine, der hoster cachen og er ikke Redis bestemt. Denne værdi svarer til netværksbåndbredde af data, der sendes til cachen fra klienten på computeren.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |


## <a name="how-to-view-metrics-and-customize-charts"></a>Sådan vises målepunkter og tilpasse diagrammer

Du kan se en oversigt over målene for din cache på bladet **Redis målepunkter** . Adgang til den **Redis målepunkter** blade Vælg **alle indstillinger** > **Redis målepunkter**.

![Redis målepunkter][redis-cache-redis-metrics]


Bladet **Redis målepunkter** indeholder følgende diagrammer.

| Redis målepunkter diagram | Viste målepunkter     |
|------------------|-------------------|
| Cache læse- og cachen skrive | Cache læst |
|                            | Skriv cache |
| Forbundne klienter      | Forbundne klienter |
| Besøg og kiksere  | I cachen        |
|                  | Udgivelsescachen      |
| Samlet kommandoer   | Samlet antal handlinger  |
| Henter og sæt    | Får              |
|                  | Sæt              |
| CPU-brug         | CPU           |
| Hukommelsesforbrug      | Anvendte hukommelse   |
|                   | Anvendte hukommelse RSS |
| Redis serverens belastning | Serverens belastning   |
| Tasten Tæl | ALT-taster |
|           | Udskilt taster |
|           | Udløbet taster |


For en mere detaljeret visning af målene på et bestemt diagram og til at tilpasse diagrammet, skal du klikke på det ønskede diagram fra bladet **Redis målepunkter** til at vise bladet **metrisk** til diagrammet.

![Metriske blade][redis-cache-metric-blade]

Alle beskeder, der er angivet på målene vises som et diagram er angivet i bunden af bladet **metrisk** til diagrammet.

Tilføje eller fjerne målepunkter eller ændre reporting intervallet, skal du vælge **Rediger diagram**.

Klik på afkrydsningsfeltet ud for navnet på metrikværdien for at tilføje eller fjerne målepunkter fra diagrammet. Hvis du vil ændre det reporting interval, skal du klikke på det ønskede interval. Hvis du vil ændre den **diagramtype**, skal du klikke på den ønskede typografi. Når du har foretaget de ønskede ændringer, skal du klikke på **Gem**. 

![Rediger diagram][redis-cache-edit-chart]

Når du klikker på **Gem** bevares dine ændringer, indtil du forlader bladet **metrisk** . Når du kommer tilbage senere, får du vist det oprindelige metriske og tidsinterval igen. Du kan finde flere oplysninger om tilpasning af diagrammer, [overvåge tjenesten målepunkter](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Hvis du vil se målene for en bestemt tidsperiode på et diagram, skal du holder musen over en af de bestemte søjler eller punkter på det diagram, der svarer til det ønskede klokkeslæt, og omfanget af intervallet, der vises.

![Se detaljer om diagram][redis-cache-view-chart-details]

## <a name="how-to-monitor-a-premium-cache-with-clustering"></a>Hvordan du kan overvåge en premium cache med klynge

Premium cache, der har [klynge](cache-how-to-premium-clustering.md) aktiveret kan have op til 10 shards. Hver shard har sin egen målepunkter, og disse målepunkter samles for at give målepunkter for cachen som helhed. Hver metrisk indeholder to versioner. En metrikværdi måler ydeevne for hele cachen og en anden version af den metrikværdi, der indeholder `(Shard 0-9)` i navnet målinger ydeevne for en enkelt shard i en cache. Eksempelvis hvis en cache har 3 shards `Cache Hits` er den samlede størrelse af søgeresultaterne for hele cachen og `Cache Hits (Shard 2)` er lige søgeresultater for pågældende shard af cachen.

Hvert overvågning diagram viser omfanget af cachen sammen med målene for hver cache shard øverste niveau.

![Skærm][redis-cache-premium-monitor]

Musemarkøren hvile datapunkterne på viser detaljer for det pågældende punkt i gang. 

![Skærm][redis-cache-premium-point-summary]

Større værdier er typisk aggregatværdierne for cachen, mens de mindste værdier er individuelle omfanget af shard. Bemærk, at i dette eksempel er der tre shards og i cachen fordeles jævnt over shards.

![Skærm][redis-cache-premium-point-shard]

For at se flere detaljer, skal du klikke på diagrammet for at få vist en udvidet visning på bladet **metrisk** .

![Skærm][redis-cache-premium-chart-detail]

Hvert diagram indeholder som standard tælleren på øverste niveau cache ydeevne samt tællerne i ydeevne for de enkelte shards. Du kan tilpasse disse på bladet **Redigere diagrammet** .

![Skærm][redis-cache-premium-edit]

Se [tilgængelige målepunkter og -rapportering intervaller](#available-metrics-and-reporting-intervals)kan finde flere oplysninger om de tilgængelige ydeevne tællere.


## <a name="operations-and-alerts"></a>Handlinger og beskeder

Sektionen **Handlinger** på bladet **Redis Cache** har **begivenheder** og **regler for påmindelser** sektioner.

![Oeprations][redis-cache-operations-events]

Hvis du vil se en liste over seneste cache handlinger, skal du klikke på **begivenheder** diagrammet for at få vist bladet **begivenheder** . Hente og genoprette adgangstaster, og aktivering og løsning af regler for påmindelser er eksempler på Handlinger. Klik på hændelsen i bladet **begivenheder** kan finde flere oplysninger om hver enkelt hændelse.

Se [Vis hændelser og overvågningslogge](../monitoring-and-diagnostics/insights-debugging-with-events.md)kan finde flere oplysninger om begivenheder.

Afsnittet **regler for påmindelser** viser antallet af beskeder for forekomsten cache. En besked om gør det muligt at overvåge din cache forekomst og modtager en mail, når en bestemt metrikværdi når i grænseværdi, der er defineret i reglen. 

Regler for påmindelser evalueres omkring hver fem minutter, og når en besked om er aktiveret, der sendes en hvilken som helst konfigurerede beskeder. Reglen aktiveringer og meddelelser om behandles ikke øjeblikkeligt; der kan være en forsinkelse på flere minutter, før en besked om er aktiveret og sendes beskeder.

Regler for påmindelser kan vises og angive fra bladet **metrisk** for et bestemt overvågning diagram eller fra bladet **regler for påmindelser** .

Regler for påmindelser har følgende egenskaber.

| Reglen egenskab                 | Beskrivelse                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ressource                            | Den ressource, der er evalueret af reglen. Når du opretter en regel for beskeder fra en Redis cache, er cachen ressourcen.                                                                                                                                                                                                                                                                                                                                                  |
| Navn                                | Navn, der entydigt identificerer reglen inden for den aktuelle forekomst af cache.                                                                                                                                                                                                                                                                                                                                                                                       |
| Beskrivelse                         | Valgfri beskrivelse af reglen.                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Metrisk                              | Metrisk skal overvåges af reglen. Se en liste over cache målepunkter tilgængelige målepunkter og -rapportering intervaller.                                                                                                                                                                                                                                                                                                                                             |
| Betingelse                           | Operatoren betingelse for reglen. Mulige valg er: større end, større end eller lig med, mindre end, mindre end eller lig med                                                                                                                                                                                                                                                                                                                             |
| Grænseværdi                           | Den værdi, der bruges til at sammenligne med en metrikværdi ved hjælp af den operator, der er angivet af egenskaben betingelse. Afhængigt af metrikværdien denne værdi kan være i byte/sekund, byte, % eller antal.                                                                                                                                                                                                                                                                                     |
| Periode                              | Angiver den periode, hvor gennemsnitsværdien af metrikværdien bruges til reglen sammenligningen. Eksempelvis hvis perioden Over den seneste time, er gennemsnitsværdien af metrisk over det forrige time tidsinterval bruges til sammenligning. Hvis du vil have besked, når grænseværdi er opfyldt på grund af en samling i aktivitet, derefter er en kortere periode relevant. Hvis du vil have besked, når der er vedvarende aktivitet over grænsen, skal du bruge en længere periode. |
| Mailtjeneste og medadministratorer | Hvis sand, sendt tjenesteadministratoren og samtidig administrator, når påmindelsen er aktiveret.                                                                                                                                                                                                                                                                                                                                                                    |
| Ekstra administrator mail      | Valgfri mailadresse for en ekstra administrator til at få besked, når påmindelsen er aktiveret.                                                                                                                                                                                                                                                                                                                                                                    |

Der sendes kun én meddelelse per reglen aktivering. Når der er overskredet grænseværdi for en regel, og der sendes en meddelelse, evalueres reglen ikke igen, før metrikværdien ligger under grænsen. Hvis metrikværdien efterfølgende overskrider grænsen, påmindelsen er aktiveret igen, og der sendes en ny meddelelse.

For at få vist alle beskeder om reglerne for din cache-forekomst, skal du klikke på delen **regler for påmindelser** i bladet **Redis Cache** . For at få vist kun de beskeder om regler, der bruger en bestemt metrikværdi, gå til bladet **metrisk** til diagrammet indeholder, der metrisk.

![Regler for påmindelser][redis-cache-alert-rules]

Hvis du vil tilføje en besked om, skal du klikke på **Tilføj påmindelse** fra bladet **metrisk** eller bladet **regler for påmindelser** . 

Angiv de ønskede kriterier i bladet **Tilføj en påmindelse** regel, og klik på **OK**. 

![Tilføje reglen][redis-cache-add-alert]

>[AZURE.NOTE] Når der oprettes en påmindelse ved at klikke på **Tilføj påmindelse** fra bladet **metrisk** , vises kun målene vises i diagrammet i pågældende blade i rullemenuen **metrisk** . Når der oprettes en påmindelse ved at klikke på **Tilføj påmindelse** fra bladet **regler for påmindelser** , findes alle cache målepunkter i rullemenuen **metrisk** .

Når en besked om gemmes det vises på bladet **regler for påmindelser** og på bladet **metrisk** for alle diagrammer, der viser den metrikværdi, der bruges i reglen. Hvis du vil redigere en besked om, skal du klikke på navnet på reglen til at vise bladet **Rediger regel** . Fra bladet **Rediger regel** kan du redigere egenskaberne for reglen, slette eller deaktivere reglen eller genaktivere reglen, hvis den tidligere blev deaktiveret.

>[AZURE.NOTE] De ændringer, du foretager i egenskaberne for reglen kan tage et øjeblik, før de afspejles på bladet **regler for påmindelser** eller bladet **metrisk** .

Når en besked om er aktiveret, sendes en mail afhængigt af konfigurationen af reglen, og et advarselsikon vises i webdelen **regler for påmindelser** på bladet **Redis Cache** .

En besked om betragtes som løses, når betingelsen beskeder om længere evalueres til sand. Når betingelsen reglen er blevet løst, erstattes den advarselsikon med en afkrydsning. Få at vide om beskeder om aktiveringer og løsninger, skal du klikke på delen **begivenheder** på bladet **Redis Cache** til at få vist hændelserne bladet **begivenheder** .

Du kan finde flere oplysninger om beskeder i Azure, [Modtag påmindelser](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

## <a name="metrics-on-the-redis-cache-blade"></a>Målepunkter på bladet Redis Cache

Bladet **Redis Cache** viser følgende kategorier af målepunkter.

-   [Overvågning af diagrammer](#monitoring-charts)
-   [Brug af diagrammer](#usage-charts)

### <a name="monitoring-charts"></a>Overvågning af diagrammer

Sektionen **overvågning** har **rammer og er fraværende**, **henter og angiver**, **forbindelser**og **Samlede kommandoer** diagrammer.

![Overvågning af diagrammer][redis-cache-monitoring-part]

**Overvågnings** diagrammer vises følgende målepunkter.

| Overvågning diagram | Cache målepunkter     |
|------------------|-------------------|
| Besøg og kiksere  | I cachen        |
|                  | Udgivelsescachen      |
| Henter og sæt    | Får              |
|                  | Sæt              |
| Forbindelser      | Forbundne klienter |
| Samlet kommandoer   | Samlet antal handlinger  |

Finde oplysninger om visning af målene og tilpasning af de enkelte diagrammer i dette afsnit, i afsnittet følgende [hvordan du kan få vist målepunkter og tilpasse målepunkter diagrammer](#how-to-view-metrics-and-customize-charts) .

### <a name="usage-charts"></a>Brug af diagrammer

Sektionen **brugen** har **Redis Server indlæsning**, **Hukommelsesforbrug**, **Netværk båndbredde**og **CPU-brug** diagrammer og viser også **priser niveau** til forekomsten cache.

![Brug af diagrammer][redis-cache-usage-part]

**Priser niveau** vises cache priser klasse og kan bruges til at [skala](cache-how-to-scale.md) cachen til en anden priser niveau.

**Brugen** diagrammer vises følgende målepunkter.

| Diagram over forbrug       | Cache målepunkter |
|-------------------|---------------|
| Redis serverens belastning | Serverens belastning   |
| Hukommelsesforbrug      | Anvendte hukommelse   |
| Netværksbåndbredde | Skriv cache   |
| CPU-brug         | CPU           |

Finde oplysninger om visning af målene og tilpasning af de enkelte diagrammer i dette afsnit, i afsnittet følgende [hvordan du kan få vist målepunkter og tilpasse målepunkter diagrammer](#how-to-view-metrics-and-customize-charts) .
  
<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png



