<properties
    pageTitle="Microsoft Azure-abonnement og begrænsninger for tjenesten, kvoter og begrænsninger"
    description="Indeholder en liste over almindelige Azure abonnement og begrænsninger for tjenesten, kvoter og begrænsninger. Dette omfatter oplysninger om, hvordan du øge begrænsninger sammen med maksimumværdier."
    services=""
    documentationCenter=""
    authors="rothja"
    manager="jeffreyg"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="btardif"/>

# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure-abonnement og begrænsninger for tjenesten, kvoter og begrænsninger

## <a name="overview"></a>Oversigt

Dette dokument angiver nogle af de mest almindelige Microsoft Azure-grænser. Dette omfatter ikke alle Azure tjenester i øjeblikket. Over tid, vil disse begrænsninger udvidet og opdateret dækker flere af platformen.

Besøg [Azure priser oversigt](https://azure.microsoft.com/pricing/) for at få flere oplysninger om Azure priser. Det, du kan beregne omkostningerne ved hjælp af [Priser Lommeregner](https://azure.microsoft.com/pricing/calculator/) eller ved at besøge siden priser for en tjeneste (for eksempel [Windows VM'er](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)).

> [AZURE.NOTE] Hvis du vil hæve grænsen over **Standard grænse**, kan du [åbne en online anmodning om kundesupport gratis](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Begrænsningerne kan ikke hæves over **Maksimumgrænsen** værdi i tabellerne nedenfor. Hvis der er ingen **Maksimumgrænsen** kolonne, har den angivne ressource ikke justerbare begrænsninger.

## <a name="limits-and-the-azure-resource-manager"></a>Begrænsninger og Azure ressourcestyring

Nu er det muligt at kombinere flere Azure ressourcer i til en enkelt Azure ressourcegruppe. Når du bruger ressourcegrupper, bliver begrænsninger, der var én gang globale administreres på en regionalt plan med Azure ressourcestyring. Se [Oversigt over Azure ressourcestyring](azure-resource-manager/resource-group-overview.md)kan finde flere oplysninger om Azure ressourcegrupper.

En ny tabel er blevet føjet til afspejler eventuelle forskelle i begrænsninger, når du bruger ressourcestyring Azure i begrænsningerne nedenfor. Der er for eksempel en **Abonnement begrænsninger** tabel og en **Abonnement begrænsninger - Azure ressourcestyring** tabel. Når en begrænsning gælder for begge scenarier, er det kun vises i den første tabel. Medmindre andet er angivet, er begrænsninger globale på tværs af alle områder.

> [AZURE.NOTE] Det er vigtigt at understrege, kvoter for ressourcer i Azure ressourcegrupper er per-område tilgængelig for dit abonnement, og der ikke er per-abonnement, som er service management kvoter. Lad os bruge core kvotaer som et eksempel. Hvis du har brug at anmode om en kvote stigning med understøttelse af kerner, skal du beslutte, hvor mange kerner, du vil bruge i hvilke områder, og foretag derefter en bestemt anmodning om Azure ressourcegruppe core kvoter for beløb og områder, du vil. Derfor, hvis du vil bruge 30 kerner i Vest Europa til at køre programmet dér specifikt skal du anmode om 30 kerner i Vest Europa. Men du vil ikke have en central kvote øge i en hvilken som helst andet område – kun Vest Europe har kvoten 30-core.
<!-- -->
Du kan derfor kan være praktisk at overveje at beslutte, hvad din kvoter for Azure ressourcegruppe skal være for din arbejdsbyrde i en hvilken som helst ét område, og anmode om dette beløb i hvert område, hvor du overvejer installation. Se [fejlfinding af problemer med installation](./resource-manager-common-deployment-errors.md) for mere hjælp opdager din aktuelle kvoter for bestemte områder.

## <a name="service-specific-limits"></a>Tjenestens begrænsninger

- [Active Directory](#active-directory-limits)
- [Administration af API](#api-management-limits)
- [App Service](#app-service-limits)
- [Application Gateway](#application-gateway-limits)
- [Programmet indsigt](#application-insights-limits)
- [Automatisering](#automation-limits)
- [Azure Redis Cache](#azure-redis-cache-limits)
- [Azure RemoteApp](#azure-remoteapp-limits)
- [Sikkerhedskopi](#backup-limits)
- [Batchen](#batch-limits)
- [BizTalk-tjenester](#biztalk-services-limits)
- [LEVERANDØR(ER)](#cdn-limits)
- [Cloud Services](#cloud-services-limits)
- [Data Factory](#data-factory-limits)
- [Data sø Analytics](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [Begivenhed Hubs](#event-hubs-limits)
- [IoT-Hub](#iot-hub-limits)
- [Vigtige samling](#key-vault-limits)
- [Media Services](#media-services-limits)
- [Mobile aftale](#mobile-engagement-limits)
- [Mobile-tjenester](#mobile-services-limits)
- [Overvågning](#monitoring-limits)
- [Multi-Factor Authentication](#multi-factor-authentication)
- [Netværk](#networking-limits)
- [Meddelelse om Hub Service](#notification-hub-service-limits)
- [Funktionsdygtige indsigt](#operational-insights-limits)
- [Ressourcegruppe](#resource-group-limits)
- [Opgavestyring](#scheduler-limits)
- [Søgning](#search-limits)
- [Service Bus](#service-bus-limits)
- [Gendannelse af websteder](#site-recovery-limits)
- [SQL-Database](#sql-database-limits)
- [Lagerplads](#storage-limits)
- [StorSimple System](#storsimple-system-limits)
- [Stream Analytics](#stream-analytics-limits)
- [Abonnement](#subscription-limits)
- [Trafik Manager](#traffic-manager-limits)
- [Virtuelle maskiner](#virtual-machines-limits)
- [Virtuelt skala sæt](#virtual-machine-scale-sets-limits)


### <a name="subscription-limits"></a>Begrænsninger for abonnement
#### <a name="subscription-limits"></a>Begrænsninger for abonnement
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Abonnement begrænsninger - Azure ressourcestyring

Følgende begrænsninger for anvendelse, når du bruger de Azure ressourcestyring og Azure ressourcegrupper. Begrænsninger, der ikke er blevet ændret med Azure ressourcestyring vises ikke nedenfor. Se tabellen ovenfor for disse begrænsninger.

Du kan finde oplysninger om håndtering af begrænsninger på ressourcestyring anmodninger, [Throttling ressourcestyring anmodninger](resource-manager-request-limits.md).

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### <a name="resource-group-limits"></a>Ressourcegruppe begrænsninger

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Virtuelle maskiner begrænsninger
#### <a name="virtual-machine-limits"></a>Virtuelt begrænsninger
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtuelle maskiner begrænsninger - Azure ressourcestyring

Følgende begrænsninger for anvendelse, når du bruger de Azure ressourcestyring og Azure ressourcegrupper. Begrænsninger, der ikke er blevet ændret med Azure ressourcestyring vises ikke nedenfor. Se tabellen ovenfor for disse begrænsninger.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Virtuelt skala sæt begrænsninger

[AZURE.INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>Begrænsninger for netværk

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Begrænsninger for netværk
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway begrænser

[AZURE.INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="traffic-manager-limits"></a>Begrænsninger for trafik Manager

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>DNS-begrænsninger

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Lagergrænser

Se yderligere oplysninger om konto lagergrænser [Azure lagerplads skalerbarhed og ydeevne destinationer](../articles/storage/storage-scalability-targets.md).

#### <a name="storage-service-limits"></a>Tjenesten lagergrænser

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### <a name="virtual-machine-disk-limits"></a>Virtuelt disk begrænsninger

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Du kan finde yderligere oplysninger i [virtuelt størrelser](../articles/virtual-machines/virtual-machines-linux-sizes.md) .

**Standard lagerplads konti**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Premium lagerplads konti**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>Ressource udbyder lagergrænser

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### <a name="cloud-services-limits"></a>Cloud Services begrænsninger

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### <a name="app-service-limits"></a>App Service begrænsninger
Følgende begrænsninger for App Service medtages begrænsninger for Web Apps, Mobile-Apps, API Apps og logik Apps.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Scheduler begrænsninger

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batchen begrænsninger

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###<a name="biztalk-services-limits"></a>Begrænsninger for BizTalk-tjenester
I følgende tabel vises begrænsningerne for Azure BizTalk-tjenester.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### <a name="documentdb-limits"></a>DocumentDB begrænsninger

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

Kvoter vises med en stjerne (*), som [kan tilpasses ved at kontakte Azure support](./documentdb/documentdb-increase-limits.md).

### <a name="mobile-engagement-limits"></a>Begrænsninger for mobile aftale

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### <a name="search-limits"></a>Begrænsninger for søgning

Priser niveauer bestemmer kapacitet og begrænsninger for din søgetjeneste. Niveauer, omfatter:

- *Gratis* med flere lejer tjeneste, der deles med andre Azure abonnenter, som er beregnet til evaluering og small udviklingsprojekter.
- *Grundlæggende* indeholder dedikeret dataressourcer til fremstilling arbejdsbelastninger mindre skaleres med op til tre replikaer for meget tilgængelige forespørgsel arbejdsmængder.
- *Standard (S1, S2, S3, S3 høj tæthed)* er større fremstilling arbejdsmængder. Der findes flere niveauer i det standard niveau, så du kan vælge en ressource konfiguration for bestemte scenarier.

**Begrænsninger for hver abonnement**

[AZURE.INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Begrænsninger for hver search-tjenesten**

[AZURE.INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Du kan finde mere detaljerede oplysninger om andre begrænsninger for, herunder dokumentstørrelse, forespørgsler i andet, taster, mødeindkaldelser og svar, [Service begrænsninger i Azure Søg](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Begrænsninger for Media Services

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>LEVERANDØR(er) begrænsninger

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Begrænsninger for mobile-tjenester

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitoring-limits"></a>Overvågning begrænsninger

[AZURE.INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Meddelelse om Hub Service begrænsninger

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Begivenhed Hubs begrænsninger

[AZURE.INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus begrænsninger

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Begrænsninger for IoT-Hub

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>Data Factory begrænsninger

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Begrænsninger for sø analyse af data
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="stream-analytics-limits"></a>Streame Analytics begrænsninger
[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory-begrænsninger

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### <a name="azure-remoteapp-limits"></a>Azure RemoteApp begrænsninger

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>Begrænsninger for StorSimple System

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### <a name="operational-insights-limits"></a>Begrænsninger for funktionsdygtige indsigt

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Begrænsninger for sikkerhedskopiering

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Begrænsninger for gendannelse af websted

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Programmet indsigt begrænsninger

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Begrænsninger for administration af API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Azure Redis Cache begrænsninger

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Begrænsninger for nøgle samling

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automatisering begrænsninger
[AZURE.INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Begrænsninger for SQL-Database

I [SQL Database ressource begrænsninger](sql-database/sql-database-resource-limits.md)for SQL-Database begrænsninger.

## <a name="see-also"></a>Se også

[Forstå Azure begrænsninger og øger](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Virtuelt og skyen Service størrelser til Azure](virtual-machines/virtual-machines-linux-sizes.md)

[Størrelser for Cloud Services](cloud-services/cloud-services-sizes-specs.md)
