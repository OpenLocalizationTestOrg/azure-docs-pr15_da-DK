<properties
   pageTitle="Oversigt over Azure virtuelt netværk (VNet)"
   description="Få mere at vide om virtuelle netværk (VNets) i Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="virtual-network-overview"></a>Oversigt over virtuelt netværk

En Azure virtuelt netværk (VNet) er en repræsentation af dit eget netværk i skyen.  Det er en logisk isolationsniveauet Azure cloud dedikeret til dit abonnement. Du kan fuldt styre IP-adresseblokke, DNS-indstillinger, sikkerhedspolitikker og distribuere tabeller inden for dette netværk. Du kan også yderligere segment skal tildeles din VNet til undernet og start Azure IaaS virtuelle maskiner (VM'er) og/eller [Cloud services (PaaS rolle forekomster)](../cloud-services/cloud-services-choose-me.md). Desuden kan du oprette forbindelse det virtuelle netværk til dit lokale netværk ved hjælp af en af de [Indstillinger for forbindelse](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) i Azure. Egentlig, kan du udvide dit netværk til Azure, med fuld kontrol på IP-adresseblokke med fordelen enterprise skala Azure leverer.

For bedre at forstå VNets, se nærmere på figuren nedenfor, som viser en forenklet lokale netværk.

![Lokale netværk](./media/virtual-networks-overview/figure01.png)

Skærmbilledet ovenfor viser et lokalt netværk med forbindelse til offentlige internettet via en router. Du kan også få vist en firewall mellem router og en DMZ, der er vært for en DNS-server og en web serverfarm. Web-serverfarm er Indlæs afstemmes ved hjælp af belastningsjustering hardware, der vises på internettet, og forbruger ressourcer fra det interne undernet. Det interne undernet er adskilt fra DMZ med en anden firewall og hosts Active Directory-domænecontrolleren servere, databaseservere og application servere.

Det samme netværk kan bruges i Azure, som vist i nedenstående illustration.

![Azure virtuelt netværk](./media/virtual-networks-overview/figure02.png)

Bemærk, hvordan Azure infrastrukturen overtager rolle router, tillade adgang fra din VNet til offentlige internettet uden brug af en hvilken som helst konfiguration. Firewalls kan erstattes af netværk sikkerhedsgrupper (NSGs) anvendt på hvert enkelt undernet. Og fysisk Indlæs balancere erstattes af balancere af internet modstående og interne Indlæs i Azure.

>[AZURE.NOTE] Der er to installationsmåder at i Azure: Klassisk (også kaldet Service Management) og Azure ressource Manager (ARM). Klassisk VNets blev føjet til en forbindelse gruppe eller oprettet som et internationale VNet. Hvis du har en VNet i en gruppe af forbindelsen mellem processorer, anbefales det at [overføre den til en internationale VNet](virtual-networks-migrate-to-regional-vnet.md).

## <a name="virtual-network-benefits"></a>Virtuelt netværk fordele

- **Isolationsniveauet**. VNets er helt adskilt fra hinanden. Som gør det muligt at oprette adskilte netværk til udvikling, test og fremstilling, der bruger de samme CIDR adresseblokke.

- **Adgang til det offentlige Internet**. Forekomster af alle IaaS FOS og PaaS rolle i en VNet kan få adgang til offentlige internettet som standard. Du kan kontrollere adgangen ved hjælp af netværk sikkerhedsgrupper (NSGs).

- **Adgang til FOS inden for VNet**. PaaS rolle forekomster og IaaS FOS kan startes i det samme virtuelle netværk, og de kan oprette forbindelse til hinanden ved hjælp af private IP-adresser, selvom de er i forskellige undernet uden brug af til at konfigurere en gateway eller bruge offentlige IP-adresser.

- **Navneoversættelse**. Azure leverer interne navneoversættelse for IaaS FOS og PaaS rolle forekomster implementeret i din VNet. Du kan også installere dine egne DNS-servere og konfigurere VNet for at bruge dem.

- **Sikkerhed**. Trafik og ind virtuelle maskiner og PaaS rolle forekomster i en VNet kan styres ved hjælp af netværk sikkerhedsgrupper.

- **Forbindelse**. VNets kan være forbundet med hinanden ved hjælp af netværk gateways eller VNet peering. VNets kan være forbundet til lokale datacentre via til websted VPN-netværk eller Azure ExpressRoute. Hvis du vil vide mere om forbindelse for VPN-til-websted skal du besøge [om VPN gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site). Hvis du vil vide mere om ExpressRoute skal du besøge [ExpressRoute teknisk oversigt](../expressroute/expressroute-introduction.md). Hvis du vil have mere for at vide om VNet peering skal du besøge [VNet peering](virtual-network-peering-overview.md).

    >[AZURE.NOTE] Kontrollér, at du opretter en VNet før du anvender en hvilken som helst IaaS FOS eller PaaS rolle forekomster til Azure-miljøet. ARM-baseret FOS kræver en VNet, og hvis du ikke angiver en eksisterende VNet, Azure opretter en standard VNet, der kan have en CIDR adresse Bloker falde sammen med dit lokale netværk. Gør det muligt for dig at oprette forbindelse din VNet til dit lokale netværk.

## <a name="subnets"></a>Undernet

Undernet er et område af IP-adresser i VNet, du kan opdele en VNet i flere undernet for organisation og sikkerhed. FOS og PaaS rolle forekomster installeret på undernet (samme eller forskellige) i en VNet kan kommunikere med hinanden uden ekstra konfiguration. Du kan også konfigurere distribuere tabeller og NSGs til et undernet.

## <a name="ip-addresses"></a>IP-adresser


Der findes to typer IP-adresser, der er tildelt til ressourcer i Azure: *offentlige* og *private*. Offentlige IP-adresser Tillad Azure ressourcer til at kommunikere med internettet og andre Azure offentligt tjenester som [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure begivenhed Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Privat IP-adresser tillader kommunikation mellem ressourcer i et virtuelt netværk og sammen med dem, der er forbundet via en VPN, uden at bruge en Internet-distribueret IP-adresser.

Hvis du vil vide mere om IP-adresser i Azure, skal du besøge [IP-adresser i virtuelt netværk](virtual-network-ip-addresses-overview-arm.md)

## <a name="azure-load-balancers"></a>Azure Indlæs balancere

Virtuelle maskiner og skytjenester i en virtuel netværk kan vises på internettet ved hjælp af Azure Indlæs balancere. Line of Business-applikationer, der er interne kan kun være Indlæs afstemmes ved hjælp af interne justering af belastning.

- **Eksterne justering af belastning**. Du kan bruge en ekstern belastningsjustering til at levere høj tilgængelighed til IaaS FOS PaaS rolle forekomster af og adgang til fra det offentlige Internet.

- **Interne justering af belastning**. Du kan bruge en intern belastningsjustering til at levere høj tilgængelighed til IaaS FOS og PaaS rolle forekomster adgang til fra andre tjenester i dine VNet.

Hvis du vil vide mere om justering af belastning i Azure, ved at besøge [indlæse belastningsjusteringstjenesten oversigt](../load-balancer/load-balancer-overview.md).

## <a name="network-security-group-nsg"></a>Sikkerhedsgruppe netværk (NSG)

Du kan oprette NSGs for at styre indgående og udgående adgang til netværksgrænseflader (NIC) FOS og undernet. Hver NSG indeholder en eller flere regler, der angiver eller ej trafik er blevet godkendt eller afvist baseret på kildens IP-adresse, kildeport, destinationens IP-adresse og destinationsport. Hvis du vil vide mere om NSGs, gå til [Hvad er en sikkerhedsgruppe netværk](virtual-networks-nsg.md).

## <a name="virtual-appliances"></a>Virtuel udstyr

Et virtuelt maskinen er bare en anden VM i din VNet, der kører en software baseret maskinen funktion, som firewall, WAN optimering eller uautoriseret adgang registrering. Du kan oprette en rute i Azure til at distribuere din VNet trafik via et virtuelt maskinen at bruge dens egenskaber.

For eksempel kan NSGs bruges til at give sikkerhed på din VNet. Omvendt NSGs indeholder lag 4 listen ACL (Access Control) til indgående og udgående pakker. Hvis du vil bruge en 7 sikkerhedsmodel lag, skal du bruge en firewall maskinen.

Virtuel anvendelser afhænger af [brugerdefinerede omdirigerer og IP-videresendelse](virtual-networks-udr-overview.md).

## <a name="limits"></a>Begrænsninger
Der er begrænsninger på antallet af virtuelle netværk, der er tilladt i et abonnement, skal du henvise til [Azure netværk begrænsninger](../azure-subscription-service-limits.md#networking-limits) kan finde flere oplysninger.

## <a name="pricing"></a>Priser
Der er uden ekstra omkostninger til brug af virtuelle netværk i Azure. Beregn forekomster startet inden for Vnet, debiteres standard satser, som beskrevet i [Azure VM priser](https://azure.microsoft.com/pricing/details/virtual-machines/). [VPN Gateways](https://azure.microsoft.com/pricing/details/vpn-gateway/) og [offentlige IP-adresser] (https://azure.microsoft.com/pricing/details/ip-addresses/) bruges i VNet vil også være opkrævet standard satser.

## <a name="next-steps"></a>Næste trin

- [Opret en VNet](virtual-networks-create-vnet-arm-pportal.md) og undernet.
- [Oprette en VM i en VNet](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Få mere at vide om [NSGs](virtual-networks-nsg.md).
- Få mere at vide om [brugerdefinerede omdirigerer og IP-videresendelse](virtual-networks-udr-overview.md).
