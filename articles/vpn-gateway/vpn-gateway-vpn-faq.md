<properties 
   pageTitle="Virtuelt netværk VPN Gateway ofte stillede spørgsmål om | Microsoft Azure"
   description="Gatewayen VPN ofte stillede spørgsmål. Ofte stillede spørgsmål om Microsoft Azure virtuelle tværs lokale netværksforbindelser, hybride konfiguration forbindelser og VPN-Gateways"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="yushwang" />

# <a name="vpn-gateway-faq"></a>VPN-Gateway ofte stillede spørgsmål

## <a name="connecting-to-virtual-networks"></a>Oprette forbindelse til virtuelle netværk

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Kan jeg oprette forbindelse virtuelle netværk i forskellige Azure områder?
Ja. Faktisk, er der ingen begrænsninger, der område. Et virtuelt netværk kan oprette forbindelse til en anden virtuelt netværk i det samme område eller i et andet Azure område.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Kan jeg oprette forbindelse virtuelle netværk i forskellige abonnementer?
Ja.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Kan jeg oprette forbindelse til flere websteder fra et enkelt virtuelt netværk?

Du kan oprette forbindelse til flere websteder ved hjælp af Windows PowerShell og Azure REST API'er. I afsnittet [med flere websted og VNet-VNet Connectivity](#multi-site-and-vnet-to-vnet-connectivity) ofte stillede spørgsmål.
## <a name="what-are-my-cross-premises-connection-options"></a>Hvad er mit indstillinger til forbindelse i tværs det lokale miljø?

Følgende forbindelser i tværs lokale understøttes:

- [Websted til websted](vpn-gateway-site-to-site-create.md) – VPN-forbindelse over IPsec (IKE v1 og IKE v2). Denne type forbindelse kræver en VPN-enhed eller RRAS.

- [Punkt-til-Site](vpn-gateway-point-to-site-create.md) – VPN-forbindelse over SSTP (Secure Socket Tunneling Protocol). Denne forbindelse kræver ikke en VPN-enhed.

- [VNet-VNet](virtual-networks-configure-vnet-to-vnet-connection.md) – denne type forbindelse er den samme som en-til-websted konfiguration. VNet til VNet er en VPN-forbindelse over IPsec (IKE v1 og IKE v2). Det kræver ikke en VPN-enhed.

- [Flere websted](vpn-gateway-multi-site.md) – dette er en variation af en-til-websted konfiguration, som gør det muligt at oprette forbindelse flere lokale websteder til et virtuelt netværk.

- [ExpressRoute](../expressroute/expressroute-introduction.md) – ExpressRoute er en direkte forbindelse til Azure fra din WAN, ikke via offentlige internettet. Se [ExpressRoute teknisk oversigt](../expressroute/expressroute-introduction.md) og [ExpressRoute ofte stillede spørgsmål om](../expressroute/expressroute-faqs.md) kan finde flere oplysninger.

Du kan finde flere oplysninger om forbindelser, [Om VPN-Gateway](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Hvad er forskellen på et websted til websted forbindelse og punkt-til-websted?

**Websted til websted** forbindelser kan du oprette forbindelse mellem en af de computere, der er placeret i din virksomhed til en hvilken som helst virtuel computer eller rolle forekomst i netværket virtuel, afhængigt af hvordan du vælger at konfigurere routing. Det er en god indstilling for forbindelse altid tilgængelig tværs lokale og er velegnet til hybridkonfigurationer. Denne type forbindelse er baseret på en IPsec VPN anvendelses (hardware eller bløde maskinen), som skal være installeret på kanten af netværket. Hvis du vil oprette denne type forbindelse, skal du have den nødvendige VPN-hardware og en eksternt modstående IPv4-adresse.

**Punkt-til-Site** forbindelser gør det muligt at oprette forbindelse fra en enkelt computer fra et vilkårligt sted på noget placeret i netværket virtuel. Det bruger Windows i feltet VPN-klient. Du har installeret et certifikat og en VPN-klient konfigurationspakke, som indeholder de indstillinger, der tillader din computer for at oprette forbindelse til en hvilken som helst virtuel computer eller rolle forekomst i det virtuelle netværk som en del af konfigurationen af punkt-til-websted. Det er godt, når du vil oprette forbindelse til et virtuelt netværk, men ikke er placeret i det lokale miljø. Det er også en god indstilling, når du ikke har adgang til VPN hardware eller en eksternt modstående IPv4-adresse, hvor begge er nødvendige for en forbindelse til websted. 

Du kan konfigurere netværket virtuel bruge både-til-websted og punkt-til-Site samtidig, forudsat, at du oprette forbindelsen til websted ved hjælp af en distribution baseret VPN-type til din gateway. Distribuere-baserede VPN typer kaldes dynamiske gateways i modellen, klassisk installation.

### <a name="what-is-expressroute"></a>Hvad er ExpressRoute?

ExpressRoute kan du oprette private forbindelser mellem Microsoft datacentre og infrastruktur, der er i din virksomhed eller i et miljø med samtidig placering. Med ExpressRoute, kan du oprette forbindelse til Microsofts skytjenester som Microsoft Azure og Office 365 på en ExpressRoute partner samtidig placering facilitet eller forbinde direkte fra dit eksisterende WAN-netværk (som en MPLS VPN leveres af en udbyder af netværk).

ExpressRoute forbindelser tilbyder bedre sikkerhed, flere pålidelighed, større båndbredde og nederste latenstider end typisk forbindelser via internettet. I nogle tilfælde kan ved hjælp af ExpressRoute forbindelser til at overføre data mellem dit lokale netværk og Azure også afkast betydeligt omkostninger fordele. Hvis du har allerede oprettet en forbindelse i tværs lokale fra dit lokale netværk til Azure, kan du overføre til en ExpressRoute forbindelse og holde netværket virtuel intakt.

Se [ExpressRoute ofte stillede spørgsmål](../expressroute/expressroute-faqs.md) få mere at vide.

## <a name="site-to-site-connections-and-vpn-devices"></a>Websted til websted forbindelser og VPN-enheder

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Hvad skal jeg overveje, når du vælger en VPN-enhed?

Vi har valideret et sæt af standard-til-websted VPN-enheder i fællesskab med enhed leverandører. En liste over kendte kompatible VPN-enheder, deres tilsvarende konfigurationsvejledning eller eksempler og specifikationer for enhed kan findes [her](vpn-gateway-about-vpn-devices.md). Alle enheder i serien enhed er angivet som kendte kompatible skal arbejde med virtuelt netværk. For at konfigurere din VPN-enhed, du henvise til enhed konfiguration eksempel eller link, der svarer til relevante enhed familie.

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>Hvad gør jeg, hvis jeg har en VPN-enhed, der ikke findes på Enhedslisten kendte kompatible?

Hvis du ikke kan se din enhed på listen som en kendte kompatible VPN-enhed, og du vil bruge den til din VPN-forbindelse, skal du kontrollere, at den opfylder de understøttede IPsec/IKE konfiguration indstillinger og parametre vises [her](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Enheder, der opfylder minimumkravene skal fungerer fint med VPN gateways. Kontakt producenten af enheden for at få yderligere vejledning i support og konfiguration.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Hvorfor går der min baseret på politik VPN-tunnel, når trafik er inaktiv?

Dette er forventede funktionsmåde for baseret på politik (også kendt som statisk routing) VPN gateways. Når trafik over tunnelen er inaktiv i mere end fem minutter, vil tunnelen torn. Når trafik starter, der flyder i den ønskede retning, genoprettes tunnelen med det samme.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Kan jeg bruge softwaren VPN til at oprette forbindelse til Azure?

Vi understøtter Windows Server 2012 Routing og Remote Access (RRAS) servere for at websted tværs lokale konfiguration.

Andre software VPN-løsninger skal arbejde med vores gateway, som de er i overensstemmelse med branche standard IPSec-installationer. Kontakte forhandleren af softwaren til konfiguration og support instruktioner.

## <a name="point-to-site-connections"></a>Punkt-til-Site forbindelser

### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>Hvilke operativsystemer kan jeg bruge sammen med punkt-til-websted?

Følgende operativsystemer understøttes:

- Windows 7 (32-bit og 64-bit)

- Windows Server 2008 R2 (kun 64-bit)

- Windows 8 (32-bit og 64-bit)

- Windows 8.1 (32-bit og 64-bit)

- Windows Server 2012 (kun 64-bit)

- Windows Server 2012 R2 (kun 64-bit)

- Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>Kan jeg bruge en hvilken som helst software VPN-klient til punkt-til-websted, der understøtter SSTP?

Nej. Support er begrænset kun til de versioner af Windows-operativsystemet er anført ovenfor.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Hvor mange VPN-klient slutpunkter kan jeg have i min konfiguration af punkt-til-websted?

Vi understøtter op til 128 VPN-klienter for at kunne oprette forbindelse til et virtuelt netværk på samme tid.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Kan jeg bruge mit eget internt PKI rodnøglecenter til punkt-til-Site forbindelse?

Ja. Tidligere kunne kun selvsignerede rodcertifikater bruges. Du kan stadig overføre 20 rodcertifikater.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Kan jeg gennemgang af proxyer og firewalls ved hjælp af muligheden for punkt-til-websted?

Ja. Vi bruger SSTP (Secure Socket Tunneling Protocol) til tunnel gennem firewalls. Denne tunnel vises som en HTTPs-forbindelse.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Hvis jeg genstarter en klientcomputer, der er konfigureret til punkt-til-websted, bliver VPN automatisk at genoprette forbindelsen?

Som standard kan klientcomputeren ikke genoprette VPN-forbindelsen automatisk.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Indeholder understøttelse af punkt-til-websted automatisk-forbinde og DDNS i VPN-klienter?

Genoprette forbindelse automatisk og DDNS understøttes i øjeblikket ikke i punkt-til-Site VPN.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Kan jeg have til-websted og punkt-til-Site konfigurationer køre sammen i det samme virtuelle netværk?

Ja. Begge disse løsninger fungerer, hvis du har en RouteBased VPN-type til din gateway. I modellen Klassisk installation skal du en dynamisk gateway. Vi kan ikke punkt-til-supportwebsted for statisk routing VPN gateways eller gateways ved hjælp af - VpnType PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Kan jeg konfigurere et punkt-til-websted-klienten til at oprette forbindelse til flere virtuelle netværk på samme tid?

Ja, det er muligt. Men virtuelle netværk kan ikke have overlappende IP-præfikser og punkt-til-Site adresse mellemrum skal ikke overlapper mellem de virtuelle netværk.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Hvor meget overførselshastighed kan jeg forvente via forbindelser til websted eller punkt-til-websted?

Det er svært at bevare den nøjagtige overførsel af VPN-tunneler. IPsec og SSTP er kryptering aktiveret VPN-protokoller. Overførselshastighed er også begrænset af ventetid og båndbredde mellem dine lokale og internettet.

## <a name="gateways"></a>Gateways

### <a name="what-is-a-policy-based-static-routing-gateway"></a>Hvad er en gateway for baseret på politik (statisk-routing)?

Baseret på politik gateways implementere baseret på politik VPN. Baseret på politik VPN'er kryptere og direkte pakker gennem IPSec-tunneler baseret på kombinationerne af adressepræfikser mellem dit lokale netværk og Azure VNet. Politik (eller trafik Datavælger) er normalt defineret som en access-liste i VPN konfigurationen.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Hvad er en distribution baseret (dynamic-routing) gateway?

Distribuere-baserede gateways implementere de rute-baserede VPN. Distribuere-baserede VPN bruger "omdirigerer" i IP videresender eller routing tabel til direkte pakker til deres tilsvarende tunnel grænseflader. Tunnel grænseflader derefter kryptere eller dekryptere pakkerne og tunneler. Vælgeren politik eller trafik til distribution baseret VPN er konfigureret som en-til-alle (eller jokertegn).

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Kan jeg få min VPN gateway IP-adresse, før jeg opretter den?

Nej. Du skal oprette din gateway først for at få IP-adressen. IP-adressen ændres, hvis du sletter og genskabe din VPN-gateway.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Hvordan mit VPN-tunnel få godkendes?

Azure VPN bruger PSK (forhåndsdelte nøgle) godkendelse. Vi oprette en delt nøgle (PSK), når vi oprette en VPN-tunnel. Du kan ændre den automatisk oprettede PSK til din egen med angive forhåndsdelte nøgle PowerShell-cmdlet eller REST-API.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Kan jeg bruge angive forhåndsdelte nøgle API til at konfigurere min politik-baserede (statisk routing) gateway VPN?

Ja, angive forhåndsdelte nøgle API og PowerShell-cmdlet, der kan bruges til at konfigurere både Azure baseret på politik (statisk) VPN'er og distribuere-baserede (dynamisk) routing VPN.

### <a name="can-i-use-other-authentication-options"></a>Kan jeg bruge andre indstillinger for godkendelse?

Vi er begrænset til at bruge foruddefinerede delte nøgler (PSK) til godkendelse.

### <a name="what-is-the-gateway-subnet-and-why-is-it-needed"></a>Hvad er "gateway undernettet", og hvorfor er det er nødvendigt?

Vi har en gateway-tjeneste, som vi køre for at aktivere tværs lokale forbindelse. 

Du skal oprette en gateway undernet til din VNet til at konfigurere en VPN-gateway. Alle gateway-undernet skal navngives GatewaySubnet kan fungere korrekt. Ikke navngive din gateway-undernet noget andet. Og ikke kan installere FOS eller andet til gateway-undernet.

Gatewayen undernet minimumstørrelse afhænger helt af konfigurationen, du vil oprette. Selvom det er muligt at oprette en gateway-undernet så lille som /29 for nogle konfigurationer, anbefaler vi, at du opretter en gateway undernet af /28 eller større (/ 28 /27, /26 osv.). 

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Kan jeg installere virtuelle maskiner eller rolle forekomster til min gateway-undernet?

Nej.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Hvordan kan jeg angive hvor trafikken går igennem gatewayen VPN?

Hvis du ved hjælp af portalen Azure klassisk, kan du tilføje hvert område, du vil sendt gennem gatewayen til netværket virtuel på siden netværk under lokalnetværk.

### <a name="can-i-configure-forced-tunneling"></a>Kan jeg konfigurere tvunget tunnelføring?

Ja. Se [konfigurere tvunget tunnelføring](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Kan jeg konfigurere mit eget VPN-serveren i Azure og bruge det til at oprette forbindelse til min lokale netværk?

Ja, kan du installere din egen VPN gateways eller -servere i Azure, enten fra Azure Marketplace eller oprette dine egne VPN routere. Du skal konfigurere brugerdefinerede omdirigerer i netværket virtuel at sikre, at trafik dirigeres korrekt mellem dit lokale netværk og dit undernet virtuelt netværk.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Hvorfor er bestemte porte åbnet på min VPN-gateway?

De er påkrævede til Azure infrastruktur kommunikation. De er beskyttet (låst) ved at Azure certifikater. Uden korrekte certifikater, eksterne enheder, herunder kunder over disse gateways ikke medføre indflydelse på disse slutpunkter.

En VPN-gateway er grundlæggende en hører til flere enhed med én NIC at trykke på det kunde private netværk, og én NIC modstående det offentlige netværk. Azure infrastruktur objekter kan ikke udnytte kunde privat netværk overholdelse årsager, så de har brug at anvende offentlige slutpunkter til infrastruktur kommunikation. Offentlige slutpunkterne scannes med jævne mellemrum ved Azure sikkerhed overvågningslog.


### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Flere oplysninger om gateway typer, krav og overførselshastighed

Du kan finde kan finde flere oplysninger [Om VPN-Gateway indstillinger](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>Flere websted og VNet-VNet forbindelse

### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>Hvilken type gateways kan understøtte flere websted og VNet-VNet forbindelse?

Kun rute-baserede (dynamisk routing) VPN.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Kan jeg oprette forbindelse en VNet med typen RouteBased VPN-forbindelse til en anden VNet med en PolicyBased VPN type?

Ingen, begge virtuelle netværk skal bruge rute-baserede (dynamisk routing) VPN.

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>Er VNet-VNet trafikken sikker?

Ja, det er beskyttet af IPsec/IKE kryptering.

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>VNet-VNet trafik rejser over det grundlæggende element Azure?

Ja.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Hvor mange lokale websteder og virtuelle netværk kan et virtuelt netværk forbindelse til?

Maks. 10 kombineret for Basic og Standard dynamisk Routing gateways; 30 for høj ydeevne VPN gateways.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Kan jeg bruge punkt-til-Site VPN'er med min virtuelt netværk med flere VPN-tunneler?

Ja, punkt-til-websted (P2S) VPN'er kan bruges med gateways VPN-forbindelse til flere lokale websteder og andre virtuelle netværk.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Kan jeg konfigurere flere tunneler mellem min virtuelt netværk og min lokale websted ved hjælp af flere websted VPN?

Nej, overflødige tunneler mellem en Azure virtuelt netværk og et lokalt websted understøttes ikke.

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>Kan der være overlappende adresse mellemrum mellem forbundne virtuelle netværk og lokale lokale websteder?

Nej. Overlappende adresse mellemrum medfører netværk konfiguration filoverførsel eller "Oprette virtuelle netværk" for mislykkes.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Får jeg flere båndbredde med flere til websted VPN'er end til et enkelt virtuelt netværk?

Nej, alle VPN-tunneler, herunder punkt-til-Site VPN'er dele den samme Azure VPN-gateway og den tilgængelige båndbredde.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Kan jeg bruge Azure VPN-gateway til forsendelse trafik mellem mine lokale websteder eller et andet virtuelle netværk?

**Klassisk implementeringsmodel**<br>
Overførsel trafik via Azure VPN-gateway er muligt ved hjælp af klassisk installation modellen, men er afhængig af statisk definerede adresse mellemrum i konfigurationsfil netværk. BGP understøttes ikke endnu med Azure virtuelle netværk og VPN gateways ved hjælp af den klassiske implementeringsmodel. Uden BGP er manuelt at definere overførsel adresse mellemrum meget fejl giver risiko og anbefales ikke.<br>
**Ressourcestyring implementeringsmodel**<br>
Hvis du bruger implementeringsmodel Ressourcestyring, i afsnittet [BGP](#bgp) kan finde flere oplysninger.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Genererer Azure den samme IPsec/IKE allerede delte nøgle for alle mine VPN-forbindelser til det samme virtuelle netværk?

Nej, genererer Azure som standard forskellige foruddefinerede delte nøgler til forskellige VPN-forbindelser. Du kan dog bruge angive VPN-Gateway-tasten REST-API eller PowerShell-cmdlet til at angive den nøgleværdi, du foretrækker. Tasten skal være alfanumeriske streng af længden mellem 1 og 128 tegn.

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>Azure tage et gebyr for trafik mellem virtuelle netværk?

Azure gebyrer for trafik mellem forskellige Azure virtuelle netværk, kun for trafik gå på tværs fra én Azure område til en anden. Gebyr rente er angivet på siden Azure [VPN Gateway priser](https://azure.microsoft.com/pricing/details/vpn-gateway/) .


### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Kan jeg oprette forbindelse et virtuelt netværk med IPsec VPN til min ExpressRoute kredsløb?

Ja, dette understøttes. Få mere at vide under [konfigurere ExpressRoute og websted til websted VPN-forbindelser, der fungerer sammen](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="bgp"></a>BGP

[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 



## <a name="cross-premises-connectivity-and-vms"></a>Tværs lokale forbindelsen og FOS

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Hvis min virtual machine er i et virtuelt netværk, og jeg har en forbindelse i tværs det lokale miljø, hvor skal jeg oprette forbindelse til VM?

Har du et par muligheder. Hvis du har aktiveret RDP, og du har oprettet et slutpunkt, kan du oprette forbindelse til din virtuelle maskine ved hjælp af VIP. Det er tilfældet, skal du angive VIP og den port, som du vil oprette forbindelse til. Du skal konfigurere porten på din virtuelle maskine for trafikken. Typisk skal du gå til portalen Azure klassisk og gemme indstillingerne for RDP-forbindelse til din computer. Indstillingerne for indeholder den nødvendige forbindelsesoplysninger.

Hvis du har et virtuelt netværk med tværs lokale connectivity konfigureret, kan du oprette forbindelse til din virtuelle maskine ved hjælp af interne DIP eller privat IP-adresse. Du kan også oprette forbindelse til din virtuelle maskine ved interne DIP fra en anden virtuel maskine, der er placeret på det samme virtuelle netværk. Du kan ikke RDP til din computer og virtuelle ved hjælp af DIP, hvis du vil oprette forbindelse fra et sted uden for din virtuelt netværk. Hvis du har et punkt-til-Site virtuelt netværk konfigureret, og du ikke kan oprette en forbindelse fra din computer, kan du f.eks, oprette forbindelse til den virtuelle maskine ved DIP.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Hvis min virtuelt er i et virtuelt netværk med tværs lokale connectivity, al trafik fra min VM går gennem denne forbindelse?

Nej. Kun den trafik, der har en destination gennemgår IP-adresse, der er indeholdt i de virtuelle netværk lokale netværk IP-adresseområder, du har angivet gatewayen virtuelt netværk. Trafik har en IP-adresse, der er placeret i det virtuelle netværk forbliver i det virtuelle netværk destination. Anden trafik er sendt gennem justering af belastning til de offentlige netværk, eller hvis tvunget tunnelføring bruges, sendes via Azure VPN gatewayen. Hvis du foretager fejlfinding, er det vigtigt at sikre dig, at du har alle de områder, der vises i dit lokalnetværk, du vil sende via en gateway. Kontrollér, at de lokalnetværk adresseområder ikke overlapper med en af adresseområder i det virtuelle netværk. Desuden, du vil bekræfte, at den DNS-server, du bruger er løse navnet til den korrekte IP-adresse.


## <a name="virtual-network-faq"></a>Virtuelt netværk ofte stillede spørgsmål

Du får vist flere virtuelt netværksoplysninger i [Virtuelle netværk ofte stillede spørgsmål](../virtual-network/virtual-networks-faq.md).
 
