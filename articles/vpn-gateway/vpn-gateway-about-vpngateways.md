<properties 
   pageTitle="Om VPN-Gateway | Microsoft Azure"
   description="Få mere at vide om VPN-Gateway-forbindelser til Azure virtuelle netværk."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway"></a>Om VPN-Gateway


Et virtuelt netværksgateway bruges til at sende netværkstrafik mellem Azure virtuelle netværk og lokale placeringer og også mellem virtuelle netværk i Azure (VNet til VNet). Når du konfigurerer en VPN-gateway, skal du oprette og konfigurere et virtuelt netværksgateway og en virtuel gateway netværksforbindelse.

Når du opretter et virtuelt netværk gateway ressource i implementeringsmodel ressourcestyring angive flere indstillinger. En af de nødvendige indstillinger er '-GatewayType'. Der er to typer af virtuelt netværk gateway: Vpn og ExpressRoute. 

Når netværkstrafik sendes på en dedikeret privat forbindelse, kan du bruge typen gateway 'ExpressRoute'. Dette kaldes også som en ExpressRoute gateway. Når netværkstrafik sendes krypterede på tværs af en offentligt tilgængelig forbindelse, kan du bruge typen gateway 'Vpn'. Dette kaldes en VPN-gateway. Websted til websted, punkt-til-websted og VNet-VNet forbindelser alle bruger en VPN-gateway.

Hver virtuelt netværk kan have kun én virtuelt netværksgateway per gateway type. Du kan for eksempel har et virtuelt netværksgateway, der anvender - GatewayType ExpressRoute og en, der bruger - GatewayType Vpn. I denne artikel fokuserer primært på VPN-Gateway. Du kan finde flere oplysninger om ExpressRoute, [ExpressRoute teknisk oversigt](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Priser

[AZURE.INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)] 


## <a name="gateway-skus"></a>Gatewayen lagerenheder

[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Du kan finde flere oplysninger om gateway lagerenheder, [Gateway lagerenheder](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Anslået Sammenlæg overførselshastighed ved SKU

Den følgende tabel viser typerne gateway og anslåede Sammenlæg overførselshastigheden. I denne tabel gælder for både ressourcestyring og klassisk installation modeller.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

## <a name="configuring-a-vpn-gateway"></a>Konfigurere en VPN-Gateway

Når du konfigurerer en VPN-gateway, afhænger af de instruktioner, du bruger den implementeringsmodel, som du brugte til at oprette netværket virtuel. Eksempelvis hvis du har oprettet din VNet ved hjælp af den klassiske implementeringsmodel, bruge du retningslinjer og instruktioner til den klassiske implementeringsmodel til at oprette og konfigurere indstillingerne for VPN-gateway. Se [forstå ressourcestyring og klassisk installation modeller](../resource-manager-deployment-model.md)kan finde flere oplysninger om installation modeller.

En VPN-gateway forbindelse er baseret på flere ressourcer, der er konfigureret med bestemte indstillinger. De fleste af ressourcerne, der kan konfigureres separat, selvom de skal konfigureres i en bestemt rækkefølge i nogle tilfælde. Du kan starte ud af, oprette og konfigurere ressourcer ved hjælp af en konfigurationsværktøjet, som portalen Azure. Du kan derefter senere beslutter at skifte til et andet værktøj, som PowerShell, for at konfigurere yderligere ressourcer eller ændre eksisterende ressourcer, når det er relevant. Du kan ikke i øjeblikket, konfigurere hver ressource og ressourceindstillinger i portalen Azure. Vejledningen i artiklerne for hver forbindelse topologi angive, hvornår der er brug for en bestemt konfigurationsværktøjet. Du kan finde oplysninger om individuelle ressourcer og indstillinger for VPN-Gateway, [om VPN-Gateway-indstillinger](vpn-gateway-about-vpn-gateway-settings.md).

De følgende afsnit indeholder tabeller, hvor der står:

- tilgængelige implementeringsmodel
- tilgængelige konfigurationsværktøjer
- links, der fører dig direkte til en artikel, hvis det er muligt

Bruge diagrammer og beskrivelser til hjælp til at vælge den forbindelse topologi så det svarer til dine behov. De viser de vigtigste oprindelige topologier, men det er muligt at opbygge mere komplekse konfigurationer ved hjælp af diagrammerne som en vejledning.

## <a name="site-to-site-and-multi-site"></a>Websted til og med flere-websted

### <a name="site-to-site"></a>Websted til websted

En-til-websted (S2S) VPN-gateway forbindelse er en forbindelse via VPN-IPsec/IKE (IKEv1 eller IKEv2) tunnel. Denne type forbindelse kræver en VPN-enhed placeret i det lokale miljø, der har en offentlig IP-adresse, der er tildelt og ikke er placeret bag en NAT. S2S forbindelser kan bruges til tværs lokal og hybrid konfigurationer.   

![S2S forbindelse] (./media/vpn-gateway-about-vpngateways/demos2s.png "websted til websted")


### <a name="multi-site"></a>Flere websted

Du kan oprette og konfigurere en VPN-gateway forbindelse mellem din VNet og flere lokale netværk. Når du arbejder med flere forbindelser, skal du bruge en RouteBased VPN type (dynamisk gateway for klassisk VNets). Dele den tilgængelige båndbredde, fordi en VNet kan kun have én VPN-gateway, alle forbindelser til gatewayen. Dette kaldes ofte "med flere websted" forbindelse.
 

![Flere Site-forbindelse] (./media/vpn-gateway-about-vpngateways/demomulti.png "flere websted")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Installation-modeller og metoder til at websted og med flere websted

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## <a name="vnet-to-vnet"></a>VNet-VNet

Oprette forbindelse et virtuelt netværk til en anden virtuelt netværk minder (VNet til VNet) om forbindelse en VNet til en lokal placering. Begge typer connectivity bruges en VPN-gateway for at give en sikker tunnel ved hjælp af IPsec/IKE. Du kan også kombinere VNet-VNet kommunikation med flere websted forbindelse konfigurationer. Her kan du oprette netværkstopologier, der kombinerer tværs lokale forbindelse med mellem virtuelle netværksforbindelse.

Den VNets, du opretter forbindelse kan være:

- i de samme eller forskellige områder
- i de samme eller forskellige abonnementer 
- i de samme forskellige installation modeller


![VNet til VNet forbindelse] (./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-vnet")

#### <a name="connections-between-deployment-models"></a>Forbindelser mellem installation modeller

Azure aktuelt har to installation modeller: Klassisk og ressourcestyring. Hvis du har brugt Azure i et stykke tid, har du sandsynligvis Azure FOS og forekomst roller, der kører i et klassisk VNet. Din nyere FOS og rolle forekomster kører muligvis i en VNet, der er oprettet i ressourcestyring. Du kan oprette en forbindelse mellem VNets tillade ressourcerne, der i én VNet til at kommunikere direkte med ressourcer i en anden.

#### <a name="vnet-peering"></a>VNet peering

Du kan muligvis bruge VNet peering for at oprette forbindelsen, så længe netværket virtuel opfylder visse krav. VNet peering bruger ikke en virtuelt netværksgateway. Se [VNet peering](../virtual-network/virtual-network-peering-overview.md)kan finde flere oplysninger.


### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Installation-modeller og metoder til VNet-VNet

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## <a name="point-to-site"></a>Punkt-til-websted

En VPN-punkt-til-websted (P2S) gateway-forbindelse kan du oprette en sikker forbindelse til netværket virtuel fra den enkelte klientcomputer. P2S er en VPN-forbindelse over SSTP (Secure Socket Tunneling Protocol). P2S forbindelser kræver ikke en VPN-enhed eller en offentligt IP-adresse til at arbejde. Du har oprettet VPN-forbindelse ved at starte fra klientcomputeren. Denne løsning er nyttig, når du vil oprette forbindelse til din VNet fra en fjernplacering, f.eks, fra home eller en konference, eller hvis du kun har nogle klienter, der har brug for at oprette forbindelse til en VNet. P2S forbindelser kan bruges sammen med S2S forbindelser gennem den samme VPN-gateway, forudsat at alle konfigurationskravene til begge forbindelser er kompatibel.


![Punkt-til-site forbindelse] (./media/vpn-gateway-about-vpngateways/demop2s.png "punkt-til-websted")

### <a name="deployment-models-and-methods-for-point-to-site"></a>Installation-modeller og metoder til punkt-til-websted

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


## <a name="expressroute"></a>ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

I en ExpressRoute forbindelse, er et virtuelt netværksgateway konfigureret med typen gateway 'ExpressRoute' i stedet for 'Vpn'. Du kan finde flere oplysninger om ExpressRoute, [ExpressRoute teknisk oversigt](../expressroute/expressroute-introduction.md).


## <a name="site-to-site-and-expressroute-coexisting-connections"></a>Websted til websted og ExpressRoute fungerer sammen forbindelser

ExpressRoute er en direkte, dedikeret forbindelse fra din WAN (ikke via offentlige internettet) til Microsoft Services, herunder Azure. Rejser af websted til websted VPN trafik krypteret via offentlige internettet. Flere fordele ved har at kunne konfigurere VPN til-websted og ExpressRoute forbindelser til det samme virtuelle netværk.

Du kan konfigurere en VPN til-websted som en sikker failover sti til ExpressRoute, eller du kan bruge VPN til-websted til at oprette forbindelse til websteder, der ikke er en del af dit netværk, men, der er forbundet via ExpressRoute. Meddelelse om, at dette kræver to virtuelt netværk gateways til det samme virtuelle netværk og et ved at - GatewayType Vpn, og den anden ved hjælp af - GatewayType ExpressRoute.


![Coexist forbindelse] (./media/vpn-gateway-about-vpngateways/demoer.png "expressroute site2site")


### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Installation-modeller og metoder til S2S og ExpressRoute

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 


## <a name="next-steps"></a>Næste trin

Planlægge din VPN-gateway konfiguration. Se [VPN Gateway planlægning og Design](vpn-gateway-plan-design.md) og [oprette forbindelse til dit lokale netværk til Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md).








 
