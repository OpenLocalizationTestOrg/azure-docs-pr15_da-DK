<properties
   pageTitle="Offentlige og private IP-adresser (klassisk) i Azure | Microsoft Azure"
   description="Få mere at vide om offentlige og private IP-adresser i Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="jdial" />

# <a name="ip-addresses-classic-in-azure"></a>IP-adresser (klassisk) i Azure
Du kan tildele IP-adresser til Azure ressourcer til at kommunikere med andre Azure ressourcer, dit lokale netværk og internettet. Der findes to typer IP-adresser, du kan bruge i Azure: offentlige og private.

Offentlige IP-adresser, der bruges til kommunikation med internettet, herunder Azure offentligt tjenester.

Privat IP-adresser, der bruges til kommunikation inden for en Azure virtuelt netværk (VNet), en skybaseret tjeneste og dit lokale netværk, når du bruger en VPN-gateway eller ExpressRoute kredsløb for at udvide dit netværk til Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring implementeringsmodel](virtual-network-ip-addresses-overview-arm.md).

## <a name="public-ip-addresses"></a>Offentlige IP-adresser
Offentlige IP-adresser Tillad Azure ressourcer til at kommunikere med internettet og Azure offentligt tjenester som [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure begivenhed Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL-databaser](../sql-database/sql-database-technical-overview.md)og [Azure-lager](../storage/storage-introduction.md).

En offentlig IP-adresse er knyttet til følgende ressourcetyper:

- Skytjenester
- IaaS virtuelle maskiner (VM'er)
- PaaS rolle forekomster
- VPN gateways
- Program-gateways

### <a name="allocation-method"></a>Fordelingsmetoden
Når en offentlig IP-adresse skal tildeles til en ressource, Azure, er det *dynamisk* allokeres fra en gruppe af tilgængelige offentlige IP-adressen på den placering, ressourcen, der er oprettet. Denne IP-adresse er udgivet, når ressourcen, der er stoppet. I tilfælde af en skybaseret tjeneste, dette sker, når alle forekomster af rolle er stoppet, som kan undgås ved hjælp af en *statisk* (reserveret) IP-adresse (se [Cloud Services](#Cloud-services)).

>[AZURE.NOTE] På listen over IP-intervaller, offentlige IP-adresser er allokeret til Azure ressourcer er blevet publiceret på [Azure Datacenter IP-intervaller](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>DNS-hostname opløsning
Når du opretter en skybaseret tjeneste eller en IaaS VM, skal du angive et navn i skyen tjenesten DNS-der er entydige på tværs af alle de ressourcer i Azure. Dette opretter en tilknytning i Azure-administreret DNS-serverne for *dnsname*. cloudapp.net til den offentlige IP-adresse ved en ressource. Når du opretter en skybaseret tjeneste med en skybaseret tjeneste DNS-navnet på **contoso**, oversætter eksempelvis fuldt kvalificeret domæne navn (fulde Domænenavn) **contoso.cloudapp.net** til den offentlige IP-adresse (VIP) skytjenesten. Du kan bruge denne fulde Domænenavn til at oprette et brugerdefineret domæne CNAME-post, der peger på den offentlige IP-adresse i Azure.

### <a name="cloud-services"></a>Skytjenester
En skybaseret tjeneste har altid en offentlig IP-adresse, der kaldes en virtuel IP-adresse (VIP). Du kan oprette slutpunkter i en skybaseret tjeneste knytte forskellige porte i VIP til interne porte på FOS og rolle forekomster i skytjenesten. 

En skybaseret tjeneste kan indeholde flere IaaS FOS eller PaaS rolle forekomster, alle udsatte gennem samme skyen service VIP. Du kan også tildele [flere VIPs til en skybaseret tjeneste](../load-balancer/load-balancer-multivip.md), som gør det muligt for flere VIP scenarier som flere lejer miljø med SSL-baserede websteder.

Du kan sikre, at den offentlige IP-adresse til en skybaseret tjeneste forbliver uændret, selvom alle rolle forekomster er stoppet, ved hjælp af en *statisk* offentlige IP-adresse, kaldes [Reserveret IP-adresse](virtual-networks-reserved-public-ip.md). Du kan oprette en statisk (reserveret) IP-ressource i en bestemt placering og tildele den til en skybaseret tjeneste i den nye placering. Du kan ikke angive faktiske IP-adressen for den reserverede IP, det er allokeret fra puljen af tilgængelige IP-adresser på den placering, den er oprettet. Denne IP-adresse er ikke frigivet, indtil du sletter den eksplicit.

Statisk (reserveret) offentlige IP-adresser er ofte anvendte i scenarier, hvor det er en skybaseret tjeneste:

- kræver firewallregler skal være konfiguration af slutbrugere.
- afhænger af eksterne DNS-navneoversættelse, og vil kræve en dynamisk IP-adresse, opdatere en poster.
- forbruger eksterne webtjenester som bruge IP-baserede sikkerhedsmodel.
- bruger SSL-certifikater, der er knyttet til en IP-adresse.

>[AZURE.NOTE] Når du opretter en klassisk VM, oprettes en objektbeholder *skybaseret tjeneste* af Azure, som har en virtuel IP-adresse (VIP). Når oprettelse sker via portalen, er en standard RDP- eller SSH *slutpunkt* konfigureret af portal, så du kan oprette forbindelse til VM via skyen service VIP. Denne skyen service VIP kan være reserveret, som giver en reserverede IP-adresse til at oprette forbindelse til VM effektivt. Du kan åbne ekstra porte ved at konfigurere flere slutpunkter.

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS FOS og PaaS rolle forekomster
Du kan tildele en offentlige IP-adressen direkte til en IaaS [VM](../virtual-machines/virtual-machines-linux-about.md) eller PaaS rolle forekomst i en skybaseret tjeneste. Dette kaldes en forekomst niveau offentlige IP-adresse ([ILPIP](virtual-networks-instance-level-public-ip.md)). Denne offentlige IP-adresse kan kun være dynamisk.

>[AZURE.NOTE] Dette er anderledes end VIP af skybaseret tjeneste, som er en objektbeholder til IaaS FOS eller PaaS rolle forekomster, da en skybaseret tjeneste kan indeholde flere IaaS FOS eller PaaS rolle forekomster, alle fremvises samme skyen service VIP.

### <a name="vpn-gateways"></a>VPN gateways
En [VPN-gateway,](../vpn-gateway/vpn-gateway-about-vpngateways.md) kan bruges til at forbinde en Azure VNet til andre Azure VNets eller lokale netværk. En VPN-gateway er tildelt en offentlige IP-adresse *dynamisk*, som gør det muligt kommunikation med det eksterne netværk.

### <a name="application-gateways"></a>Program-gateways
En Azure [Application gateway](../application-gateway/application-gateway-introduction.md) kan bruges til Layer7-belastning til distribution netværkstrafik baseret på HTTP. Programmet gateway er tildelt en offentlige IP-adresse *dynamisk*, der fungerer som den netværksbelastningen VIP.

### <a name="at-a-glance"></a>Et hurtigt overblik
Tabellen nedenfor viser hver ressourcetype med mulige allokering metoder (dynamic/statisk) og muligheden for at tildele flere offentlige IP-adresser.

|Ressource|Dynamisk|Statisk|Flere IP-adresser|
|---|---|---|---|
|Skybaseret tjeneste|Ja|Ja|Ja|
|IaaS VM eller PaaS rolle forekomst|Ja|Nej|Nej|
|VPN-gateway|Ja|Nej|Nej|
|Programmet gateway|Ja|Nej|Nej|

## <a name="private-ip-addresses"></a>Privat IP-adresser
Privat IP-adresser Tillad Azure ressourcer til at kommunikere med andre ressourcer i en skybaseret tjeneste eller et [virtuelt netværk](virtual-networks-overview.md)(VNet) eller til lokale netværk (via VPN-gateway eller ExpressRoute kredsløb), uden at bruge en Internet-nås IP-adresse.

I Azure klassisk implementeringsmodel, kan en privat IP-adresse tildeles til Azure følgende ressourcer:

- IaaS FOS og PaaS rolle forekomster
- Interne justering af belastning
- Programmet gateway

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS FOS og PaaS rolle forekomster
Virtuelle maskiner (VM'er), der er oprettet med den klassiske implementeringsmodel placeres altid i en skybaseret tjeneste, der ligner PaaS rolle forekomster. Funktionsmåden for private IP-adresser og som derfor er de samme for disse ressourcer.

Det er vigtigt at være opmærksom på, at en skybaseret tjeneste kan være installeret på to måder:

- Som en *enkeltstående* skybaseret tjeneste, hvor det ikke er i et virtuelt netværk.
- Som en del af et virtuelt netværk.

#### <a name="allocation-method"></a>Fordelingsmetoden
I tilfælde af en *enkeltstående* skybaseret tjeneste få ressourcer en privat IP-adresse, der er allokeret *dynamisk* fra Azure datacenter privat IP-adresseområde. Det kan bruges kun til kommunikation med andre FOS inden for samme skytjenesten. Denne IP-adresse kan ændre Når ressourcen afbrydes og startes.

I tilfælde af en skybaseret tjeneste, der er implementeret i et virtuelt netværk, få ressourcer privat IP-adresser, der allokeres fra adresseområde på den tilknyttede subnet(s) (som angivet i dens netværkskonfiguration). Denne privat IP-adresser, kan bruges til kommunikation mellem alle FOS inden for VNet.

Desuden i tilfælde af skytjenester inden for en VNet, er et privat IP-adresse allokeret *dynamisk* (ved hjælp af DHCP) som standard. Det kan ændre Når ressourcen afbrydes og startes. For at sikre IP-adressen forbliver uændret, skal du angive metoden til fordeling til *statisk*, og Angiv en gyldig IP-adresse i det tilsvarende adresseområde.

Statisk private IP-adresser, der ofte bruges til:

 - VM'er, der fungerer som domænecontrollere eller DNS-servere.
 - VM'er, der kræver firewallregler ved hjælp af IP-adresser.
 - VM'er, der kører tjenester, der åbnes af andre apps via en IP-adresse.

#### <a name="internal-dns-hostname-resolution"></a>Interne DNS-hostname opløsning
Alle Azure FOS og PaaS rolle forekomster er konfigureret med [Azure-administreret DNS-servere](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) som standard, medmindre du eksplicit konfigurere brugerdefinerede DNS-servere. Disse DNS-servere få interne navneoversættelse for FOS og rolle forekomster, der er placeret i den samme VNet eller skybaseret tjeneste.

Når du opretter en VM, føjes en tilknytning til hostname til dens privat IP-adresse til Azure-administreret DNS-serverne. I tilfælde af en multi-NIC VM er værtsnavnet knyttet til den private IP-adressen på den primære NIC. Oplysningerne tilknytning er dog begrænset til ressourcer i den samme skytjeneste eller VNet.

I tilfælde af en *enkeltstående* skybaseret tjeneste, vil du kunne løse værtsnavne af alle forekomster af FOS/rolle i den samme skytjeneste kun. I tilfælde af en skybaseret tjeneste i en VNet, vil du kunne løse værtsnavne af alle forekomster FOS/rolle i VNet.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interne Indlæs balancere (ILB) og program-gateways
Du kan tildele en privat IP-adresse til **front-end** konfigurationen af en [Azure interne justering af belastning](../load-balancer/load-balancer-internal-overview.md) (ILB) eller en [Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Denne privat IP-adresse, der fungerer som et internt slutpunkt, der er kun tilgængelig for ressourcerne i dets virtuelt netværk (VNet) og eksterne netværk, der er knyttet til VNet. Du kan tildele enten en dynamiske eller statiske privat IP-adresse til front-end konfigurationen. Du kan også tildele flere private IP-adresser til at aktivere flere vip scenarier.

### <a name="at-a-glance"></a>Et hurtigt overblik
Tabellen nedenfor viser hver ressourcetype med mulige allokering metoder (dynamic/statisk) og muligheden for at tildele flere private IP-adresser.

|Ressource|Dynamisk|Statisk|Flere IP-adresser|
|---|---|---|---|
|VM (i en *enkeltstående* skybaseret tjeneste)|Ja|Ja|Ja|
|PaaS rolle forekomst (i en *enkeltstående* skybaseret tjeneste)|Ja|Nej|Ja|
|VM eller PaaS rolle forekomst (i en VNet)|Ja|Ja|Ja|
|Interne Indlæs belastningsjusteringstjenesten front-end|Ja|Ja|Ja|
|Programmet gateway front-end|Ja|Ja|Ja|

## <a name="limits"></a>Begrænsninger

Tabellen nedenfor viser begrænsningerne pålagt IP håndtere i Azure per abonnement. Du kan [kontakte support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) for at øge standardgrænser op til maksimalt begrænsningerne baseret på virksomhedens behov.

||Standardgrænse|Højeste antal|
|---|---|---|
|Offentlige IP-adresser (dynamisk)|5|Kontakt support|
|Reserverede offentlige IP-adresser|20|Kontakt support|
|Offentlige VIP hver installation (skytjeneste)|5|Kontakt support|
|Privat VIP (ILB) hver installation (skytjeneste)|1|1|

Kontrollér, at du læser det samlede sæt af [filstørrelsesgrænser for netværk](azure-subscription-service-limits.md#networking-limits) i Azure.

## <a name="pricing"></a>Priser

I de fleste tilfælde er offentlige IP-adresser gratis. Der er et nominel gebyr bruge yderligere og/eller statisk offentlige IP-adresser. Kontrollér, at du forstår [priser struktur for offentlige IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Forskelle mellem ressourcestyring og klassisk installationer
Nedenfor finder du en sammenligning af IP-adresse funktioner i ressourcestyring og klassisk implementeringsmodel.

||Ressource|Klassisk|Ressourcestyring|
|---|---|---|---|
|**Offentlige IP-adresse**|VM|Kaldes en ILPIP (kun dynamisk)|Kaldes en offentlige IP (dynamiske eller statiske)|
|||Tildelt til en IaaS VM eller en forekomst af PaaS rolle|Der er knyttet til den VM NIC|
||Internet modstående justering af belastning|Kaldes VIP (dynamisk) eller reserveret IP-adresse (statisk)|Kaldes en offentlige IP (dynamiske eller statiske)|
|||Tildelt til en skybaseret tjeneste|Der er knyttet til den justering af belastning front-end config|
||||
|**Privat IP-adresse**|VM|Kaldes en DIP|Kaldes en privat IP-adresse|
|||Tildelt til en IaaS VM eller en forekomst af PaaS rolle|Tildelt til den VM NIC|
||Interne justering af belastning (ILB)|Tildelt til ILB (dynamiske eller statiske)|Tildelt til den ILB front-end konfiguration (dynamisk eller statisk)|

## <a name="next-steps"></a>Næste trin
- [Implementer en VM med en statisk privat IP-adresse](virtual-networks-static-private-ip-classic-pportal.md) ved hjælp af portalen klassisk.
