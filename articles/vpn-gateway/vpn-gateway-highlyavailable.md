<properties
   pageTitle="Oversigt over meget konfigurationer med Azure VPN Gateways | Microsoft Azure"
   description="I denne artikel indeholder en oversigt over meget tilgængelige konfigurationsindstillinger ved hjælp af Azure VPN Gateways."
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
   ms.date="09/24/2016"
   ms.author="yushwang"/>

# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Meget tilgængelige tværs lokale og VNet-VNet forbindelse

I denne artikel indeholder en oversigt over meget tilgængelige konfigurationsindstillinger for din tværs det lokale miljø og VNet-VNet forbindelse ved hjælp af Azure VPN gateways.

## <a name = "activestandby"></a>Om Azure VPN gateway redundans

Hver Azure VPN-gateway, består af to forekomster i en aktiv standby konfiguration. For en hvilken som helst planlagt vedligeholdelse eller ikke-planlagt afbrydelser, sker der med den aktive forekomst forekomsten standby overtage (failover) automatisk, og genoptage S2S VPN eller VNet-VNet forbindelser. Skift over medfører en kort afbrydelse. Forbindelsen skal gendannes i 10 til 15 sekunder til planlagt vedligeholdelse. Ikke-planlagt problemer bliver forbindelse gendannelse længere, omkring et minut til 1 og halvdelen minutter i værste fald. P2S forbindelser afbrydes P2S VPN klientforbindelser til gatewayen, og brugerne skal oprette forbindelse igen fra klientcomputere.

![Aktiv Standby](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>Meget tilgængelige tværs lokale Connectivity

For at give bedre tilgængelighed til dine cross lokale forbindelser, er der et par indstillinger tilgængelige:

- Flere lokale VPN-enheder
- Aktiv-aktiv Azure VPN-gateway
- Kombination af begge

### <a name = "activeactiveonprem"></a>Flere lokale VPN-enheder

Du kan bruge flere VPN-enheder fra dit lokale netværk til at oprette forbindelse til din Azure VPN-gateway, som vist i følgende diagram:

![Flere On-Premises VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

Denne konfiguration indeholder flere aktive tunneler fra den samme Azure VPN-gateway til dine lokale enheder på samme placering. Der er nogle krav og begrænsninger:

1. Du skal oprette flere S2S VPN-forbindelser fra dine VPN-enheder til Azure. Når du knytter flere VPN-enheder fra det samme lokale netværk til Azure, skal du oprette én lokale netværksgateway for hver VPN-enhed, og én forbindelse fra din Azure VPN-gateway til gatewayen lokale netværk.

2. Lokale netværk gateways svarer til dine VPN-enheder skal have entydige offentlige IP-adresser i egenskaben "GatewayIpAddress".

3. BGP er påkrævet for denne konfiguration. Hver lokale netværksgateway, der repræsenterer en VPN-enhed skal have en entydig BGP peer-IP-adresse angivet i egenskaben "BgpPeerIpAddress".

4. Egenskabsfeltet AddressPrefix i hver lokale netværksgateway skal ikke overlapper. Du skal angive "BgpPeerIpAddress" i /32 CIDR format i feltet AddressPrefix, for eksempel 10.200.200.254/32.

5. Du skal bruge BGP at gøre opmærksom på de samme præfikser af det samme lokale netværk præfikser til din Azure VPN-gateway, og trafikken skal videresendes til disse tunneler samtidigt.

6. Hver forbindelse tælles mod det maksimale antal tunneler for din Azure VPN-gateway, 10 til Basic og Standard lagerenheder og 30 for HighPerformance SKU. 

I denne konfiguration gatewayen Azure VPN som stadig findes i aktiv standby-tilstand, så de samme failover funktionsmåde og kort afbrydelse stadig sker som beskrevet [ovenfor](#activestandby). Men denne konfiguration beskytter mod fejl eller afbrydelser på dit lokale netværk og VPN-enheder.
 
### <a name="active-active-azure-vpn-gateway"></a>Aktiv-aktiv Azure VPN-gateway

Nu kan du oprette en VPN-Azure gateway i en aktiv-aktiv konfiguration, hvor begge forekomster af gatewayen FOS fastlægger S2S VPN tunneler til din lokale VPN-enhed, som vist i det følgende diagram:

![Aktiv-aktiv](./media/vpn-gateway-highlyavailable/active-active.png)

I denne konfiguration hver Azure gateway-forekomst, har en entydig offentlige IP-adresse, og hver fastlægger en IPsec/IKE S2S VPN tunnel til din lokale VPN-enhed, der er angivet i dit lokale netværksgateway og forbindelse. Bemærk, at begge VPN-tunneler rent faktisk er en del af den samme forbindelse. Du kan stadig har brug at konfigurere din lokale VPN-enhed for at acceptere eller tunneller to S2S VPN til disse to Azure VPN gateway offentlige IP-adresser.

Da Azure gatewayforekomster er i aktiv aktiv konfiguration, sendes trafik fra din Azure virtuelt netværk til dit lokale netværk via begge tunneler samtidigt, selvom din lokale VPN-enhed kan prioriteret først én tunnel oven på hinanden. Bemærk selvom samme TCP eller UDP strømmen skal altid gennemgang af samme tunnel eller stien, medmindre en vedligeholdelse sker på en af forekomsterne.

Når et planlagt vedligeholdelse eller en ikke-planlagt begivenhed sker på én gateway forekomst, afbrydes IPSec-tunnel fra forekomsten til din lokale VPN-enhed. De tilsvarende omdirigerer på dine VPN-enheder skal fjernes eller trækkes automatisk, så trafikken kan skiftes til de andre aktive IPSec-tunnel. På siden Azure sker parameteren over automatisk fra den pågældende forekomst til den aktive forekomst.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>To-redundans: aktiv-aktiv VPN gateways til både Azure og lokale netværk

Den mest pålidelige mulighed er at kombinere aktiv-aktiv gateways på både netværket og Azure, som vist i diagrammet nedenfor.

![Skærmdump af dobbelt redundans](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

Her du oprette og konfigurere Azure VPN gateway i en aktiv-aktiv konfiguration og opretter to lokale netværk gateways og to forbindelser til din to on-premises VPN-enheder som beskrevet ovenfor. Resultatet er et fuldt connectivity af 4 IPSec-tunneler mellem din Azure virtuelt netværk og dit lokale netværk.

Alle gateways og tunneler er aktive fra Azure side, så trafikken fordeles mellem alle 4 tunneler samtidigt, selvom hver TCP eller UDP flow igen skal følge den samme tunnel eller en sti fra Azure side. Selvom ved at sprede trafikken, kan du se lidt bedre overførselshastighed over IPSec-tunneler, er det primære mål med denne konfiguration til høj tilgængelighed. Og på grund af statistiske art det strækker sig, det er svært at angive mål på hvordan forskellige programmet trafik betingelser påvirker Sammenlæg overførselshastigheden.

Denne topologi kræver to lokale netværk gateways og to forbindelser til at understøtte par af lokale VPN-enheder, og BGP er påkrævet for at tillade de to forbindelser til det samme lokale netværk. Disse krav er den samme som [ovenfor](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>Meget tilgængelige VNet-VNet forbindelse via VPN Azure-Gateways

Samme aktiv-aktiv konfiguration kan også anvende på Azure VNet til VNet forbindelser. Du kan oprette aktiv-aktiv VPN gateways for begge virtuelle netværk og forbinde dem sammen til formular samme fuldt forbindelsen af 4 tunneler mellem de to VNets, som vist i diagrammet nedenfor:

![VNet-VNet](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

Dette sikrer, at der er altid et par af tunneler mellem de to virtuelle netværk for en hvilken som helst planlagt vedligeholdelse begivenheder, give endnu bedre tilgængelighed. Selvom den samme topologi for tværs lokale connectivity kræver to forbindelser, skal VNet-VNet topologien ovenstående kun én forbindelse for hver gateway. Desuden er BGP valgfrit, medmindre overførsel routing via VNet-VNet forbindelsen er påkrævet.


## <a name="next-steps"></a>Næste trin

Se [Konfigurere aktiv-aktiv VPN Gateways til tværs det lokale miljø og VNet-VNet forbindelser](vpn-gateway-activeactive-rm-powershell.md) til trin til at konfigurere aktiv-aktiv tværs lokale og VNet-VNet forbindelser.
