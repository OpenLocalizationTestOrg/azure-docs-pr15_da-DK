<properties
   pageTitle="Oprette forbindelse dit lokale netværk til Azure | Microsoft Azure"
   description="Forklarer, og sammenligner de forskellige metoder til at oprette forbindelse til Microsofts skytjenester som Azure, Office 365 og Dynamics CRM Online."
   services=""
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="jdial"/>
   
# <a name="connecting-your-on-premises-network-to-azure"></a>Oprette forbindelse dit lokale netværk til Azure

Microsoft yder flere typer skytjenester. Mens du kan oprette forbindelse til alle tjenester via offentlige internettet, kan du også opretter forbindelse til nogle af de tjenester, der bruger et virtuelt privat netværk (VPN) tunnel via internettet eller via en direkte, privat forbindelse til Microsoft. I denne artikel hjælper dig med at finde ud af, hvilken indstilling du connectivity der bedst opfylder dine behov, der er baseret på typerne af Microsofts skytjenester, som du forbruger. De fleste organisationer anvender flere forskellige typer beskrevet nedenfor.

## <a name="connecting-over-the-public-internet"></a>Oprette forbindelse via offentlige internettet

Denne forbindelsestype giver adgang til Microsofts skytjenester direkte via internettet, som vist nedenfor.

![Internet] (./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Denne forbindelse er typisk den første type, der bruges til at oprette forbindelse til Microsofts skytjenester. Tabellen nedenfor viser fordele og ulemper ved denne forbindelsestype.



| **Fordele**| **Overvejelser i forbindelse med**|
|---------|---------|
|Kræver ingen ændring til dit lokale netværk, så længe alle klientenheder har ubegrænset adgang til alle IP-adresser og porte på internettet.|Selvom trafik er ofte krypteret ved hjælp af HTTPS, kan det hentes undervejs da den kører på tværs offentlige internettet.|
|Kan oprette forbindelse til alle Microsoft cloud-tjenester på offentlige internettet.|Uventede ventetid fordi forbindelsen kører på tværs af internettet.|
|Bruger eksisterende forbindelse til internettet.||
|Administration af enheder, connectivity kræver ikke.||

Denne forbindelse har ingen forbindelse eller båndbredde omkostninger, idet du bruge din eksisterende Internetforbindelse. 

## <a name="connecting-with-a-point-to-site-connection"></a>Oprette forbindelse med en forbindelse til punkt-til-websted

Denne forbindelsestype giver adgang til nogle Microsofts skytjenester gennem en Secure Socket Tunneling Protocol (SSTP) tunnel via internettet, som vist nedenfor.

![P2S] (./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "Punkt-til-site forbindelse")

Forbindelsen er foretaget over eksisterende forbindelse til internettet, men kræver brug af en VPN-Gateway, Azure. Tabellen nedenfor viser fordele og ulemper ved denne forbindelsestype.

| **Fordele**| **Overvejelser i forbindelse med**|
|---------|---------|
|Kræver ingen ændring til dit lokale netværk, så længe alle klientenheder har ubegrænset adgang til alle IP-adresser og porte på internettet.|Selvom trafik er krypteret ved hjælp af IPSec, kan det hentes undervejs da den kører på tværs offentlige internettet.|
|Bruger eksisterende forbindelse til internettet.|Uventede ventetid fordi forbindelsen kører på tværs af internettet.|
|Overførselshastighed op til 200 Mb/s per gateway.|Kræver oprettelse og administration af separat forbindelser mellem hver enhed på dit lokale netværk og hver gatewayen hver enhed skal bruge til at oprette forbindelse til.|
|Kan bruges til at oprette forbindelse til Azure tjenester, der kan være forbundet til en virtuelle Azure-netværk (VNet) som virtuelle Azure-computere og Azure Cloud Services.|Kræver minimale løbende administration af en VPN-Gateway, Azure.|
||Kan ikke bruges til at oprette forbindelse til Microsoft Office 365 eller Dynamics CRM Online.
||Kan ikke bruges til at oprette forbindelse til Azure tjenester, der kan være forbundet med en VNet.|

Lær mere om [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) -tjenesten, dens [priser](https://azure.microsoft.com/pricing/details/vpn-gateway)og udgående data filoverførsel [priser](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Oprette forbindelse med en forbindelse til websted

Denne forbindelsestype giver adgang til nogle Microsofts skytjenester gennem en IPSec-tunnel via internettet, som vist nedenfor.

![S2S] (./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "Websted til websted forbindelse")

Forbindelsen er foretaget over eksisterende forbindelse til internettet, men kræver brug af en Azure VPN Gateway med dens tilknyttede priser og udgående dataoverførsel priser. Tabellen nedenfor viser fordele og ulemper ved denne forbindelsestype.

| **Fordele**| **Overvejelser i forbindelse med**|
|---------|---------|
|Alle enheder på dit lokale netværk kan kommunikere med Azure tjenester, der er forbundet med en VNet, så det er ikke nødvendigt at konfigurere individuelle forbindelser for hver enhed.|Selvom trafik er krypteret ved hjælp af IPSec, kan det hentes undervejs da den kører på tværs offentlige internettet.|
|Bruger eksisterende forbindelse til internettet.|Uventede ventetid fordi forbindelsen kører på tværs af internettet.|
|Kan bruges til at oprette forbindelse til Azure tjenester, der kan være forbundet med en VNet som virtuelle maskiner og Skytjenester.|Skal konfigurere og administrere en valideret VPN-enhed * i det lokale miljø.|
|Overførselshastighed op til 200 Mb/s per gateway.|Kræver minimale løbende administration af en VPN-Gateway, Azure.|
|Kan gennemtvinge udgående trafik startet fra skyen virtuelle maskiner via lokale netværk for inspektion og logføring ved hjælp af brugerdefinerede omdirigerer eller kant Gateway-protokollen (BGP) **.|Kan ikke bruges til at oprette forbindelse til Microsoft Office 365 eller Dynamics CRM Online.|
||Kan ikke bruges til at oprette forbindelse til Azure tjenester, der kan være forbundet med en VNet.|
||Hvis du bruger tjenester, der starter forbindelser til lokale enheder igen, og din sikkerhedspolitikker kræver det, skal du muligvis en firewall mellem lokale netværk og Azure.|

- * Få vist en liste over [valideret VPN-enheder](../vpn-gateway/vpn-gateway-about-vpn-devices.md#validated-vpn-devices).
- ** Få mere at vide om brug af [brugerdefinerede omdirigerer](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) eller [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) tvinge routing fra Azure VNets til en lokal enhed.

## <a name="connecting-with-a-dedicated-private-connection"></a>Oprette forbindelse med en dedikeret privat forbindelse

Denne forbindelsestype giver adgang til alle Microsofts skytjenester over en dedikeret privat forbindelse til Microsoft, der ikke sker via internettet, som vist nedenfor.

![ER] (./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ExpressRoute forbindelse")

Forbindelsen kræver brug af tjenesten ExpressRoute og en forbindelse til en udbyder af connectivity. Tabellen nedenfor viser fordele og ulemper ved denne forbindelsestype.

| **Fordele**| **Overvejelser i forbindelse med**|
|---------|---------|
|Trafik kan ikke hentes undervejs via offentlige internettet da en dedikeret forbindelse via en tjenesteudbyder bruges.|Kræver lokalt router Administration.|
|Båndbredde op til 10 Gb/s per ExpressRoute kredsløb og overførselshastighed op til 2 Gb/s til hver gatewayen.|Kræver en dedikeret forbindelse til en udbyder af connectivity.|
|Mere forudsigelige ventetid fordi det er en dedikeret forbindelse til Microsoft, der ikke sker via internettet.|Kræve minimale løbende administration af en eller flere Azure VPN Gateways (hvis kredsløbet tilsluttet VNets).|
|Kræver ikke krypteret kommunikation, selvom du kan kryptere trafik, hvis du ønsker.| Hvis du bruger skytjenester, der starter forbindelser til lokale enheder igen, skal du muligvis en firewall mellem lokale netværk og Azure.|
|Kan direkte oprette forbindelse til alle Microsofts skytjenester, med nogle få undtagelser *.|Kræver NAT (NAT) af lokal IP-adresser angivelse af Microsoft datacentre for tjenester, der kan være forbundet med en VNet.* *|
|Udgående trafik startet fra skyen virtuelle maskiner via lokale netværk for inspektion og logføring ved hjælp af BGP kan gennemtvinge.|

- * Få vist en [liste over tjenester](../expressroute/expressroute-faqs.md#supported-services) , der ikke kan bruges med ExpressRoute. Abonnementet Azure skal godkendes for at oprette forbindelse til Office 365.  Se [Azure ExpressRoute til Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) -artiklen få mere at vide.
- ** Få mere at vide om ExpressRoute [NAT](../expressroute/expressroute-nat.md) krav.

Lær mere om [ExpressRoute](../expressroute/expressroute-introduction.md), dens tilknyttede [priser](https://azure.microsoft.com/pricing/details/expressroute)og [connectivity udbydere](../expressroute/expressroute-locations.md#connectivity-provider-locations).

## <a name="additional-considerations"></a>Yderligere overvejelser

- Flere af indstillingerne ovenfor har forskellige maksimumbegrænsninger, de kan understøtte for VNet forbindelser, gateway forbindelser og andre kriterier. Det anbefales, at du gennemgå de Azure [netværk begrænser](../azure-subscription-service-limits.md#networking-limits) for at forstå, hvis nogen af disse påvirke typerne forbindelse, du vælger at bruge. 
- Hvis du planlægger at oprette forbindelse til en gateway fra et websted til websted VPN-forbindelse til den samme VNet som en ExpressRoute gateway, skal du først orientere dig om vigtige begrænsninger. Artiklen [konfigurere ExpressRoute og websted til websted fungerer sammen forbindelser](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) for at få flere oplysninger.

## <a name="next-steps"></a>Næste trin

Nedenstående ressourcer beskriver, hvordan at implementere Forbindelsestyperne dækket i denne artikel.

-   [Implementere en forbindelse til punkt-til-websted](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
-   [Implementere en forbindelse til websted](guidance-hybrid-network-vpn.md)
-   [Implementere en dedikeret privat forbindelse](guidance-hybrid-network-expressroute.md)
-   [Implementere en dedikeret privat forbindelse med en-til-websted forbindelse til høj tilgængelighed](guidance-hybrid-network-expressroute-vpn-failover.md)
