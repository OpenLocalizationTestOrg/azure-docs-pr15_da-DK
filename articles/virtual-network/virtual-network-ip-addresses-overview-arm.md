<properties
   pageTitle="Offentlige og private IP-adresser i Azure ressourcestyring | Microsoft Azure"
   description="Få mere at vide om offentlige og private IP-adresser i Azure ressourcestyring."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="ip-addresses-in-azure"></a>IP-adresser i Azure
Du kan tildele IP-adresser til Azure ressourcer til at kommunikere med andre Azure ressourcer, dit lokale netværk og internettet. Der findes to typer IP-adresser, du kan bruge i Azure:

- **Offentlige IP-adresser**: bruges til kommunikation med internettet, herunder Azure offentligt tjenester
- **Privat IP-adresser**: bruges til kommunikation inden for en Azure virtuelt netværk (VNet) og dit lokale netværk, når du bruger en VPN-gateway eller ExpressRoute kredsløb for at udvide dit netværk til Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][Klassisk implementeringsmodel](virtual-network-ip-addresses-overview-classic.md).

Hvis du er bekendt med den klassiske implementeringsmodel, markere den [forskelle i IP håndtere mellem klassisk og ressourcestyring](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Offentlige IP-adresser
Offentlige IP-adresser Tillad Azure ressourcer til at kommunikere med internettet og Azure offentligt tjenester som [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure begivenhed Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL-databaser](../sql-database/sql-database-technical-overview.md)og [Azure-lager](../storage/storage-introduction.md).

I Azure Resource Manager en [offentlige IP-](resource-groups-networking.md#public-ip-address) adresse er en ressource, der har sit eget egenskaber. Du kan knytte en offentlige IP-adresseressource med en af de følgende ressourcer:

- Virtuelle maskiner (VM)
- Internettet Indlæs balancere
- VPN gateways
- Program-gateways

### <a name="allocation-method"></a>Fordelingsmetoden
Der findes to metoder, en IP-adresse er allokeret til en *offentlig* IP-ressource - *dynamiske* eller *statiske*. Metoden til standard fordeling er *dynamiske*, hvor en IP-adresse er **ikke** tildelt på tidspunktet for oprettelsen. I stedet, tildeles den offentlige IP-adresse, når du starter (eller oprette) tilknyttet ressourcen (som en VM eller indlæse belastningsjusteringstjenesten). IP-adressen er udgivet, når du stopper (eller slette) ressourcen. Derved IP-adressen, der skal ændres, når du stopper og starter en ressource.

For at sikre IP-adressen for den tilknyttede ressource forbliver uændret, kan du angive metoden til fordeling eksplicit til *statisk*. I dette tilfælde tildeles en IP-adresse med det samme. Det er udgivet kun, når du sletter ressourcen eller ændre dens allokeringsmetode til *dynamisk*.

>[AZURE.NOTE] Selvom du angiver metoden til fordeling til *statisk*, kan du angive den faktiske IP-adresse, der er tildelt til den *offentlige IP-ressource*. I stedet får det tildelt fra en gruppe af tilgængelige IP-adresser på ressourcen, der er oprettet i Azure placering.

Statisk offentlige IP-adresser er ofte anvendte i følgende situationer:

- Slutbrugere skal opdatere firewallregler til at kommunikere med dine Azure ressourcer.
- DNS-navneoversættelse, hvor kræver en ændring i IP-adresse ved at opdatere en poster.
- Ressourcerne Azure kommunikere med andre apps eller tjenester, der bruger en IP-adresse-baserede sikkerhedsmodel.
- Du bruger SSL-certifikater, der er knyttet til en IP-adresse.

>[AZURE.NOTE] På listen over IP-intervaller, offentlige IP-adresser (dynamic/statisk) er allokeret til Azure ressourcer er blevet publiceret på [Azure Datacenter IP-intervaller](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>DNS-hostname opløsning
Du kan angive en DNS-domæne navn etiket til en offentlig IP-ressource, der opretter en tilknytning til *domainnamelabel*. *placering*. cloudapp.azure.com til den offentlige IP-adresse i Azure-administreret DNS-serverne. Eksempelvis hvis du opretter en offentlige IP-ressource med **contoso** som en *domainnamelabel* i **Vest USA** Azure *placering*, fuldt kvalificeret domæne navn (fulde Domænenavn) **contoso.westus.cloudapp.azure.com** oversætter til den offentlige IP-adresse ved en ressource. Du kan bruge denne fulde Domænenavn til at oprette et brugerdefineret domæne CNAME-post, der peger på den offentlige IP-adresse i Azure.

>[AZURE.IMPORTANT] Hver domæne navn etiket, der er oprettet skal være entydig i dens Azure placering.  

### <a name="virtual-machines"></a>Virtuelle maskiner
Du kan knytte en offentlig IP-adresse til en [Windows](../virtual-machines/virtual-machines-windows-about.md) eller [Linux](../virtual-machines/virtual-machines-linux-about.md) VM ved at tildele den til dens **netværksgrænsefladen**. I forbindelse med flere netværk-grænsefladen VM, kan du tildele det til den *primære* netværksgrænseflade kun. Du kan tildele et dynamisk eller en statisk offentlige IP-adresse til en VM.

### <a name="internet-facing-load-balancers"></a>Internettet Indlæs balancere
Du kan knytte en offentlig IP-adresse til en [Azure belastning](../load-balancer/load-balancer-overview.md), ved at tildele den til Indlæs belastningsjusteringstjenesten **front end** -konfiguration. Denne offentlige IP-adresse, der fungerer som en netværksbelastningen virtuelle IP-adresse (VIP). Du kan tildele et dynamisk eller en statisk offentlige IP-adresse til en front end belastningsjustering. Du kan også tildele flere offentlige IP-adresser til belastningsjustering front end, som gør det muligt for [Flere VIP](../load-balancer/load-balancer-multivip.md) scenarier som et med flere lejer miljø med SSL-baserede websteder.

### <a name="vpn-gateways"></a>VPN gateways
[Azure VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) bruges til at oprette forbindelse til en Azure virtuelle netværk (VNet) til andre Azure VNets eller til et lokalt netværk. Du vil tildele en offentlig IP-adresse til den **IP-konfiguration** til at aktivere det til at kommunikere med det eksterne netværk. I øjeblikket, kan du kun tildele en *dynamisk* offentlige IP-adresse til en VPN-gateway.

### <a name="application-gateways"></a>Program-gateways
Du kan knytte en offentlig IP-adresse til en Azure [Application Gateway](../application-gateway/application-gateway-introduction.md), ved at tildele den til den gateway **frontend** konfiguration. Denne offentlige IP-adresse, der fungerer som en netværksbelastningen VIP. I øjeblikket, kan du kun tildele en *dynamisk* offentlige IP-adresse til en programmet gateway front end-konfiguration.

### <a name="at-a-glance"></a>På et hurtigt
Tabellen nedenfor viser egenskaben bestemte gennem som en offentlig IP-adresse kan være knyttet til en ressource på øverste niveau, og de mulige allokering metoder (dynamisk eller statisk), der kan bruges.

|På øverste niveau ressource|IP-adresse tilknytning|Dynamisk|Statisk|
|---|---|---|---|
|Virtuelt|Netværksgrænsefladen|Ja|Ja|
|Justering af belastning|Front-end-konfiguration|Ja|Ja|
|VPN-gateway|Gatewayen IP-konfiguration|Ja|Nej|
|Programmet gateway|Front-end-konfiguration|Ja|Nej|

## <a name="private-ip-addresses"></a>Privat IP-adresser
Privat IP-adresser Tillad Azure ressourcer til at kommunikere med andre ressourcer i et [virtuelt netværk](virtual-networks-overview.md) eller et lokalt netværk via en VPN-gateway eller ExpressRoute kredsløb, uden at bruge en Internet-nås IP-adresse.

En privat IP-adresse er knyttet til følgende typer Azure ressourcer i implementeringsmodel Azure ressourcestyring:

- FOS
- Interne Indlæs balancere (ILBs)
- Program-gateways

### <a name="allocation-method"></a>Fordelingsmetoden
En privat IP-adresse er allokeret fra adresseområde på det undernet, som er tilknyttet ressourcen. Adresseområde på undernettet selve er en del af den VNet adresseområde.

Hvor der er allokeret en privat IP-adresse på to måder: *dynamiske* eller *statiske*. Metoden til standard fordeling er *dynamiske*, hvor IP-adressen tildeles automatisk fra ressourcens undernet (ved hjælp af DHCP). Kan ændre denne IP-adresse, når du stopper og starter ressourcen.

Du kan angive metoden til fordeling til *statisk* at sikre, at IP-adressen forbliver uændret. I dette tilfælde skal du også angive en gyldig IP-adresse, der er en del af ressourcens undernet.

Statisk private IP-adresser, der ofte bruges til:

- VM'er, der fungerer som domænecontrollere eller DNS-servere.
- Ressourcer, der kræver firewallregler ved hjælp af IP-adresser.
- Ressourcer, der åbnes af andre apps ressourcer via en IP-adresse.

### <a name="virtual-machines"></a>Virtuelle maskiner
En privat IP-adresse er tildelt til **netværksgrænsefladen** på en [Windows](../virtual-machines/virtual-machines-windows-about.md) eller [Linux](../virtual-machines/virtual-machines-linux-about.md) VM. I tilfælde af flere netværk-grænsefladen VM får hver grænseflade privat er tildelt en IP-adresse. Du kan angive metoden til fordeling som dynamisk eller statisk for en netværksgrænseflade.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>Interne DNS-hostname opløsning (for FOS)
Alle Azure FOS er konfigureret med [Azure-administreret DNS-servere](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) som standard, medmindre du eksplicit konfigurere brugerdefinerede DNS-servere. Disse DNS-servere giver interne navneoversættelse for VM'er, der er placeret i den samme VNet.

Når du opretter en VM, føjes en tilknytning til hostname til dens privat IP-adresse til Azure-administreret DNS-serverne. I tilfælde af flere netværk-grænseflade VM er værtsnavnet knyttet til private IP-adressen på den primære netværk-grænseflade.

VM'er, der er konfigureret med Azure-administreret DNS-servere vil kunne løse værtsnavne af alle FOS i deres VNet til deres private IP-adresser.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interne Indlæs balancere (ILB) og program-gateways
Du kan tildele en privat IP-adresse til **front-end** konfigurationen af en [Azure interne justering af belastning](../load-balancer/load-balancer-internal-overview.md) (ILB) eller en [Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Denne privat IP-adresse, der fungerer som et internt slutpunkt, der er kun tilgængelig for ressourcerne i dets virtuelt netværk (VNet) og eksterne netværk, der er knyttet til VNet. Du kan tildele enten en dynamiske eller statiske privat IP-adresse til front-end konfigurationen.

### <a name="at-a-glance"></a>På et hurtigt
Tabellen nedenfor viser egenskaben bestemte gennem som en privat IP-adresse kan være knyttet til en ressource på øverste niveau, og de mulige allokering metoder (dynamisk eller statisk), der kan bruges.

|På øverste niveau ressource|IP-adresse tilknytning|Dynamisk|Statisk|
|---|---|---|---|
|Virtuelt|Netværksgrænsefladen|Ja|Ja|
|Justering af belastning|Front-end-konfiguration|Ja|Ja|
|Programmet gateway|Front-end-konfiguration|Ja|Ja|

## <a name="limits"></a>Begrænsninger

Begrænsningerne pålagt IP-adresser er angivet i det samlede sæt af [filstørrelsesgrænser for netværk](azure-subscription-service-limits.md#networking-limits) i Azure. Disse begrænsninger er efter område og efter abonnement. Du kan [kontakte support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) for at øge standardgrænser op til maksimalt begrænsningerne baseret på virksomhedens behov.

## <a name="pricing"></a>Priser

Offentlige IP-adresser skal muligvis en nominel opladning. Hvis du vil vide mere om IP-adresse priser i Azure, gennemgå siden [IP-adresse priser](https://azure.microsoft.com/pricing/details/ip-addresses) .

## <a name="next-steps"></a>Næste trin
- [Installere en VM med en statiske offentlige IP-Adresser ved hjælp af portalen Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [Installere en VM med en statiske offentlige IP-Adresser ved hjælp af en skabelon](virtual-network-deploy-static-pip-arm-template.md)
- [Installere en VM med en statisk privat IP-adresse ved hjælp af portalen Azure](virtual-networks-static-private-ip-arm-pportal.md)
