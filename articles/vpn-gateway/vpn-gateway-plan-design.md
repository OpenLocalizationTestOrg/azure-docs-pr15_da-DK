<properties 
   pageTitle="VPN-Gateway, planlægning og design | Microsoft Azure"
   description="Få mere at vide om VPN-Gateway, planlægning og design til tværs det lokale miljø, hybride og VNet-VNet forbindelser"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc"/>

# <a name="planning-and-design-for-vpn-gateway"></a>Planlægning og design til VPN-Gateway

Planlægge og designe dit tværs det lokale miljø og VNet-VNet konfigurationer kan være enten enkle eller komplicerede, afhængigt af dine netværk behov. I denne artikel vejledes du gennem grundlæggende overvejelser om planlægning og design.

## <a name="planning"></a>Planlægning


### <a name="compare"></a>Tværs lokale connectivity indstillinger

Hvis du vil forbinde dine lokale websteder sikkert til et virtuelt netværk, du har tre forskellige måder at gøre det: websted til websted, punkt-til-websted, og ExpressRoute. Sammenligne de forskellige tværs lokale forbindelser, der er tilgængelige. Den indstilling, du vælger kan stole på forskellige overvejelser, f.eks.:


- Hvilken slags overførselshastighed kræver din løsning?
- Vil du kommunikerer via det offentlige Internet via sikker VPN eller via en privat forbindelse?
- Har du en offentlig IP-adresse bruges?
- Planlægger du at bruge en VPN-enhed? Hvis det er tilfældet, er det kompatible?
- Har du forbindelse blot nogle få computere, eller vil du have en fast forbindelse til dit websted?
- Hvilken type VPN-gateway, som kræves til den løsning, du vil oprette?
- Hvilke gateway SKU skal du bruge?


Den følgende tabel kan hjælpe dig med at bestemme den bedste løsning forbindelse til din løsning.


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>Gatewayen krav efter VPN type og SKU

[AZURE.INCLUDE [vpn-gateway-gwsku](../../includes/vpn-gateway-gwsku-include.md)]

Se [Indstillinger for VPN-Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku)kan finde flere oplysninger om gateway lagerenheder.

#### <a name="aggregate-throughput-by-sku-and-vpn-type"></a>Sammenlæg overførselshastighed efter SKU og VPN-type

Den følgende tabel viser typerne gateway og anslåede Sammenlæg overførselshastigheden. Anslået Sammenlæg dataoverførsel kan være en afgørende faktor for dit design.
Priser er forskellen mellem gateway lagerenheder. Finde oplysninger om priser, [VPN-Gateway priser](https://azure.microsoft.com/pricing/details/vpn-gateway/). I denne tabel gælder for både ressourcestyring og klassisk installation modeller.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

#### <a name="supported-configurations-by-sku-and-vpn-type"></a>Understøttede konfigurationer efter SKU og VPN-type

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 

### <a name="wf"></a>Arbejdsproces

Følgende liste viser almindelige arbejdsprocessen til skyen connectivity:

1.  Designe og planlægge topologien connectivity og få vist adresseområder for alle netværk, du vil oprette forbindelse.
2.  Oprette en Azure virtuelt netværk. 
3.  Oprette en VPN-gateway for det virtuelle netværk.
4.  Oprette og konfigurere forbindelser til lokale netværk eller andre virtuelle netværk (som det er nødvendigt).
5.  Oprette og konfigurere en punkt-til-Site forbindelse til din Azure VPN-gateway (som det er nødvendigt).
 

## <a name="design"></a>Design

### <a name="topologies"></a>Forbindelse topologier

Start ved at kigge på diagrammer i artiklen [Om VPN-Gateway](vpn-gateway-about-vpngateways.md) . I artiklen indeholder grundlæggende diagrammer, installation modeller for hver topologi (ressourcestyring eller klassisk), og hvilke installation værktøjer, du kan bruge til at installere din konfiguration.   

### <a name="designbasics"></a>Grundlæggende om design

I følgende afsnit beskrives de grundlæggende funktioner VPN-gateway. Overvej også at [netværk services begrænsninger](../articles/azure-subscription-service-limits.md#networking-limits).


#### <a name="subnets"></a>Om undernet

Når du opretter forbindelser, skal du overveje dit undernet områder. Du kan ikke have overlappende undernet-adresseområder. En overlappende undernet er, når et virtuelt netværk eller en lokal placering indeholder det samme adresseområde, der indeholder anden placeringen. Det betyder, at du har brug for dit netværk teknikere til dine lokale lokalt netværk til skærer ud af et område, så du kan bruge til din IP-adresse til Azure håndtere mellemrum/undernet. Du skal adresseområde, der ikke bruges på den lokale lokale netværk. 

Undgå overlappende undernet er også vigtigt, når du arbejder med VNet-VNet forbindelser. Hvis dit undernet overlapper og en IP-adresse, der findes i både afsendelse og VNets, mislykkedes VNet-VNet forbindelser. Azure kan distribuere dataene til de andre VNet, fordi destinationsadressen er en del af den afsendende VNet. 

VPN Gateways kræver et bestemt undernet, kaldet en gateway-undernet. Alle gateway-undernet skal navngives GatewaySubnet kan fungere korrekt. Sørg for ikke at navngive din gateway-undernet et andet navn, og ikke kan installere FOS eller andet til gateway-undernet. Se [Gateway undernet](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Om lokale netværk gateways

Gatewayen lokale netværk typisk refererer til din lokale placering. I klassisk installation modellen kaldes gatewayen lokale netværk for en lokal Network-webstedet. Når du konfigurerer en gateway lokale netværk, du give den et navn, angive den offentlige IP-adressen på den lokale VPN-enhed og angive de adressepræfikser, der er i den lokale placering. Azure kigger på destination adressepræfikser for netværkstrafik, hører den konfiguration, som du har angivet for gatewayen lokale netværk og sender pakker i overensstemmelse hermed. Du kan ændre disse adressepræfikser efter behov. Du kan finde yderligere oplysninger finder [lokale netværk gateways](vpn-gateway-about-vpn-gateway-settings.md#lng).


#### <a name="gwtype"></a>Om forskellige typer gateway

Vælge den korrekte gateway type for topologien er meget vigtigt. Hvis du vælger en forkert type, fungerer din gateway ikke korrekt. Typen gateway angiver, hvordan gatewayen selve opretter forbindelse og er en påkrævet indstilling for implementeringsmodel ressourcestyring.

Typerne gateway er:

- VPN
- ExpressRoute

#### <a name="connectiontype"></a>Om forskellige forbindelsestyper

Hver konfiguration kræver et bestemt forbindelsestype. Forbindelsestyperne er:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient


#### <a name="vpntype"></a>Om VPN-datatyper

Hver konfiguration kræver en bestemt VPN-type. Hvis du kombinerer to konfigurationer, som oprettelse af forbindelse til websted og en punkt-til-Site forbindelse til den samme VNet, skal du bruge en VPN-type, der opfylder kravene begge forbindelse.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)] 

De følgende tabeller viser typen VPN, som den er tilknyttet til hver forbindelseskonfiguration. Kontrollér, at typen VPN-forbindelse til din gateway stemmer overens med den konfiguration, som du vil oprette. 


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)] 

### <a name="devices"></a>VPN-enheder til forbindelser til websted

Hvis du vil konfigurere en forbindelse til websted, uanset implementeringsmodel, du skal bruge følgende elementer:

- En VPN-enhed, der er kompatibelt med Azure VPN gateways
- En offentligt IPv4 IP-adresse, der ikke er bag en NAT

Du skal har erfaring med at konfigurere din VPN-enhed, eller har en person, der kan konfigurere enheden for dig. Du kan finde flere oplysninger om VPN-enheder, [om VPN enheder](vpn-gateway-about-vpn-devices.md). VPN-enheder artikel indeholder oplysninger om valideret enheder, krav til enheder, der ikke er blevet godkendt og links til enhed konfigurationsdokumenter, hvis det er muligt.

### <a name="forcedtunnel"></a>Overvej at tvunget tunnel routing

For de fleste konfigurationer, kan du konfigurere tvunget tunnelføring. Tvunget tunnelføring kan du Omdiriger eller "kraft" al trafik til internetbaserede tilbage til din lokale placering via en VPN-websted til websted tunnel til inspektion og revision. Dette er et kritiske sikkerhedskrav til de fleste enterprise IT politikker. 

Uden tvunget tunnelføring vil internetbaserede trafik fra din FOS i Azure altid gennemgang fra Azure-netværksinfrastruktur direkte ud til internettet, uden mulighed for at gør det muligt at undersøge eller overvåge trafikken. Uautoriseret adgang til internettet kan potentielt føre til offentliggørelse af oplysninger eller andre typer brud på sikkerhed.

**Tvungen tunnelføring diagram**

![Tvungen tunnelføring forbindelse] (./media/vpn-gateway-plan-design/forced-tunnel.png "Tvungen tunnelføring")

En tvunget tunnelføring forbindelse kan konfigureres i begge installation-modeller og ved hjælp af forskellige værktøjer. Se tabellen nedenfor for at få flere oplysninger. Vi opdaterer denne tabel, som nye artikler, nye installation-modeller og yderligere værktøjer bliver tilgængelige for denne konfiguration. Når en artikel, der er tilgængelige, link vi direkte til den fra tabellen.

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 



## <a name="next-steps"></a>Næste trin

Se [Ofte stillede spørgsmål om VPN-Gateway](vpn-gateway-vpn-faq.md) og [Om VPN gatewayen](vpn-gateway-about-vpngateways.md) artikler kan finde flere oplysninger kan hjælpe dig med dit design.

Se [Om VPN-Gateway indstillinger](vpn-gateway-about-vpn-gateway-settings.md)kan finde flere oplysninger om indstillinger for specifik gateway.




