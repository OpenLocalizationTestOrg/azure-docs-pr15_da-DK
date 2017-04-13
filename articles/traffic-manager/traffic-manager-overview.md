<properties
    pageTitle="Hvad er trafik Manager | Microsoft Azure"
    description="Denne artikel kan hjælpe dig med at forstå, hvad trafik Manager er, og om det er det rigtige trafik routing valg for dit program"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-traffic-manager"></a>Oversigt over trafik Manager

Microsoft Azure trafik Manager kan du kontrollere fordelingen af brugeren bliver trafik til tjenesten slutpunkter i forskellige datacentre. Tjenesten slutpunkter understøttes af trafik Manager omfatter FOS Azure, Web Apps og tjenester i skyen. Du kan også bruge trafik Manager med eksterne, ikke Azure slutpunkter.

Trafik Manager bruger Domain Name System (DNS) til at dirigere anmodninger om klienten til det mest relevante slutpunkt, der er baseret på en [trafik-routing metode](traffic-manager-routing-methods.md) og tilstand for slutpunkterne. Trafik Manager indeholder en række trafik-routing metoder dem efter behov for andet program, slutpunkt sundhed [overvågning](traffic-manager-monitoring.md)og automatisk failover. Trafik Manager er tolerant for fejl, herunder fejl på en hel Azure område.

## <a name="traffic-manager-benefits"></a>Fordele ved trafik Manager

Trafik Manager kan hjælpe dig:

- **Forbedre tilgængeligheden af kritiske programmer**

    Trafik Manager leverer høj tilgængelighed til dine programmer ved at overvåge slutpunkterne og give automatisk failover, når et slutpunkt går ned.

- **Forbedre svartid for høj ydeevne-programmer**

    Azure giver dig mulighed at køre skytjenester eller websteder i datacentre placeret over hele verden. Trafik Manager forbedrer programmet reaktionstid ved at dirigere trafik til slutpunktet med den laveste netværksventetid for klienten.

- **Udføre service vedligeholdelse uden nedetid**

    Du kan udføre planlagt vedligeholdelse handlinger på dine programmer uden nedetid. Trafik Manager dirigerer trafik til den alternative slutpunkter, mens vedligeholdelsen er i gang.

- **Kombinere i det lokale miljø og skyen-baserede programmer**

    Trafik Manager understøtter ekstern, ikke Azure slutpunkter for at aktivere den skal bruges sammen med hybride i skyen og lokale installationer, herunder "burst-til-skyen," "overføre-til-skyen," og "failover i skyen" scenarier.

- **Distribuere trafik til store, komplekse installationer**

    Bruger [indlejrede trafik Manager profiler](traffic-manager-nested-profiles.md), kombineres trafik-routing metoder for at oprette avancerede og fleksible regler for support af større og mere komplekse installationer.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>Næste trin

- Lær mere om, [hvordan trafik Manager fungerer](traffic-manager-how-traffic-manager-works.md).

- Lær, hvordan du udvikler programmer med høj tilgængelighed ved hjælp af [trafik Manager slutpunkt overvågning](traffic-manager-monitoring.md).

- Lær mere om [trafik-routing metoder](traffic-manager-routing-methods.md) , der understøttes af trafik Manager.

- [Opret en trafik Manager profil](traffic-manager-manage-profiles.md).

