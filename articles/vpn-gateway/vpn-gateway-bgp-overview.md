<properties
   pageTitle="Oversigt over BGP med Azure VPN Gateways | Microsoft Azure"
   description="I denne artikel indeholder en oversigt over BGP med Azure VPN Gateways."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/16/2016"
   ms.author="yushwang"/>

# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Oversigt over BGP med Azure VPN Gateways

I denne artikel indeholder en oversigt over BGP (kant Gateway Protocol) support i Azure VPN Gateways.

## <a name="about-bgp"></a>Om BGP

BGP er den standard routing protokol, der bruges i internettet til at udveksle routing- og reachability oplysninger mellem to eller flere netværk. Når den bruges i forbindelse med Azure virtuelle netværk, BGP aktiverer Azure VPN Gateways og dine lokale VPN-enheder, der kaldes BGP peers eller tilstødende netværk, til at udveksle "dirigerer", der oplyser begge gateways på tilgængelighed og adgangen til disse præfikser at gå gennem gateways eller routere involveret. BGP kan også aktivere overførsel routing mellem flere netværk på overførsel af omdirigerer en BGP gateway lærer fra én BGP peer til alle andre BGP peers.
 
### <a name="why-use-bgp"></a>Hvorfor bruge BGP?

BGP er en valgfri funktion, du kan bruge med Azure rute-baserede VPN-gateways. Du skal også kontrollere dine lokale VPN enheder understøtter BGP, før du aktiverer funktionen. Du kan fortsætte med at bruge Azure VPN gateways og dine lokale VPN enheder uden BGP. Det er tilsvarende ved at bruge statisk omdirigerer (uden BGP) *kontra* ved hjælp af dynamiske routing med BGP mellem dit netværk og Azure.

Der er flere fordele og nye muligheder med BGP:

#### <a name="support-automatic-and-flexible-prefix-updates"></a>Understøtte automatisk og fleksibel præfiks opdateringer

Med BGP skal du definere et minimum præfiks til en bestemt BGP peer over IPsec S2S VPN-tunnel. Det kan være så lille som et præfiks, du host (/ 32) af BGP peer-IP-adressen på din lokale VPN-enhed. Du kan styre, hvor lokale netværk præfikser, du vil give besked Azure tillade Azure virtuelle netværket adgang til.
    
Du kan også annoncere en større præfikser på enheder, der kan indeholde nogle af dine VNet adressepræfikser, som et stort privat IP-adresseområde (f.eks., 10.0.0.0/8). Bemærk, selvom præfikserne ikke må være identisk med en af dine VNet præfikser. Disse identisk med din VNet præfikser omdirigerer afvises.

>[AZURE.IMPORTANT] I øjeblikket reklame standardruten (0.0.0.0/0) til Azure VPN gateways, blokeres. Yderligere opdatering vil blive leveret, når denne egenskab er aktiveret.

#### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a>Understøtte flere tunneler mellem en VNet og et lokalt websted med automatisk failover baseret på BGP

Du kan oprette flere forbindelser mellem din Azure VNet og dine lokale VPN enheder på samme placering. Denne funktion giver flere tunneler (stier) mellem de to netværk i en aktiv-aktiv konfiguration. Hvis en af tunneler afbrydes, de tilsvarende omdirigerer vil tilbagekaldes via BGP og trafikken flyttes automatisk til de resterende tunneler.
    
I det følgende diagram viser et eksempel på denne meget tilgængelige konfiguration:
    
![Flere aktive stier](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a>Understøtte overførsel routing mellem dit lokale netværk og flere Azure VNets

BGP gør det muligt for flere gateways til at få mere at vide og overføre præfikser fra forskellige netværk, uanset om de er direkte eller indirekte forbundet. Dette kan aktivere overførsel rute med Azure VPN gateways mellem dine lokale websteder eller på tværs af flere Azure virtuelle netværk.
    
I det følgende diagram viser et eksempel på en med flere hop topologi med forskellige stier, der kan forsendelse trafik mellem de to lokale netværk via Azure VPN gateways i Microsoft-Networks:

![Med flere hop overførsel](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faqs"></a>BGP ofte stillede spørgsmål


[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 




## <a name="next-steps"></a>Næste trin

Se [Introduktion til BGP på Azure VPN gateways](./vpn-gateway-bgp-resource-manager-ps.md) til trin til at konfigurere BGP til tværs det lokale miljø og VNet-VNet forbindelser.

