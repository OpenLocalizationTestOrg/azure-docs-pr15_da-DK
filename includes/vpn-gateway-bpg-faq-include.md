### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Understøttes BGP på alle Azure VPN Gateway SKU'er?

Nej, understøttes BGP på Azure **Standard** og **ved anvendelse** VPN gateways. **Grundlæggende** SKU understøttes ikke.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Kan jeg bruge BGP med Azure Policy-Based VPN gateways?

Nej, understøttes BGP på rute-baserede VPN-gateways kun.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Kan jeg bruge private ASN'er (selvstændige System tal)?

Ja, du kan bruge din egen offentlige ASN'er eller privat ASN'er til både dit lokale netværk og Azure virtuelle netværk.

#### <a name="are-there-asns-reserved-by-azure"></a>Er der ASN'er reserveret Azure?

Ja, er følgende ASN'er reserveret ved Azure til både intern og ekstern peerings:

- Offentlige ASN'er: 8075 8076, 12076
- Privat ASN'er: 65515 65517 65518, 65519, og 65520

Du kan ikke angive disse ASN'er for dine til lokale VPN-enheder, når du opretter forbindelse til Azure VPN gateways.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Kan jeg bruge den samme ASN til både lokalt VPN-netværk og Azure VNets?

Nej, skal du tildele forskellige ASN'er mellem dit lokale netværk og dit Azure VNets, hvis du opretter dem sammen med BGP. Azure VPN Gateways har en standard ASN af 65515, der er tildelt, om BGP er aktiveret for ikke til tværs lokale forbindelsen. Du kan tilsidesætte denne standard ved at tildele en anden ASN, når du opretter gatewayen VPN, eller du kan ændre ASN, når gatewayen er blevet oprettet. Du skal tildele din lokale ASN'er til den tilsvarende Azure Gateways lokale netværk.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Adressepræfikser hvilken der Azure VPN gateways annoncere for mig?

Azure VPN-gateway vil annoncere følgende omdirigerer til dine lokale BGP enheder:

- Din VNet adressepræfikser
- Adressepræfikser for hver lokale netværk Gateways tilsluttet gatewayen Azure VPN
- Omdirigerer lært fra andre BGP peering sessioner med forbindelse til Azure VPN gatewayen, **undtagen standard route eller omdirigerer overlapper med en hvilken som helst VNet præfiks**.

#### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Kan jeg annoncere standardruten (0.0.0.0/0) til Azure VPN gateways?

Ikke på nuværende tidspunkt.

#### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Kan jeg annoncere de nøjagtige præfikser som præfikser min virtuelt netværk?

Nej, reklame de samme præfikser som et af netværket virtuel adressepræfikser vil blive blokeret eller filtreret efter den Azure platform. Du kan dog annoncere et præfiks, der er et udsnit af hvad du har i netværket virtuel. For eksempel netværket virtuel kan bruge adresse mellemrum 10.10.0.0/16, og du kan annoncere 10.0.0.0/8.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Kan jeg bruge BGP med mine VNet-VNet forbindelser?

Ja, kan du bruge BGP til både tværs lokale forbindelser og VNet-VNet forbindelser.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Kan jeg blande BGP med ikke-BGP forbindelser til min Azure VPN gateways?

Ja, kan du blande både BGP og ikke BGP forbindelser til den samme Azure VPN-gateway.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Betyder Azure VPN gateway support BGP overførsel routing?

Ja, BGP overførsel routing understøttes, med den undtagelse, at Azure VPN gateways er **ikke** annoncere standard omdirigerer til andre BGP peers. Hvis du vil aktivere overførsel distributionen på tværs af flere Azure VPN gateways, skal du aktivere BGP på alle mellemliggende VNet-VNet forbindelser.

### <a name="can-i-have-more-than-one-tunnels-between-azure-vpn-gateway-and-my-on-premises-network"></a>Kan jeg have mere end én tunneler mellem Azure VPN-gateway og min lokale netværk?

Ja, du kan oprette mere end én S2S VPN-tunneler mellem en VPN-Azure gateway og dit lokale netværk. Vær opmærksom på, at der skal tælles alle disse tunneler mod det samlede antal tunneler for din Azure VPN gateways. Hvis du har to overflødige tunneler mellem din Azure VPN-gateway og én af dit lokale netværk, vil de forbruge 2 tunneler af den samlede kvoten for din Azure VPN-gateway (10 til Standard) og 30 for ved anvendelse.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Kan jeg have flere tunneler mellem to Azure VNets med BGP?

Nej, overflødige tunneler mellem et par af virtuelle netværk understøttes ikke.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Kan jeg bruge BGP til S2S VPN i en VPN-ExpressRoute/S2S CO-existence konfiguration?

Ikke på nuværende tidspunkt.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Hvilken adresse bruger Azure VPN-gateway for BGP peer-IP-adresse?

Gatewayen Azure VPN tildeler en enkelt IP-adresse fra det GatewaySubnet område, der er defineret for det virtuelle netværk. Som standard er den anden sidste adressen på området. Eksempelvis hvis din GatewaySubnet er 10.12.255.0/27, bliver lige fra 10.12.255.0 til 10.12.255.31, BGP peer-IP-adressen på gatewayen Azure VPN 10.12.255.30. Du kan finde disse oplysninger, når du få vist Azure VPN gateway oplysninger.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Hvad er kravene til BGP peer-IP-adresserne på min VPN-enhed?

Din lokale BGP peer-adresse **Må ikke** være den samme som den offentlige IP-adresse på enheden VPN. Bruge en anden IP-adresse på VPN-enhed til din BGP peer-IP-adresse. Det kan være en adresse, der er tildelt til tilbagekoblingsgrænseflade på enheden. Angiv denne adresse i den tilsvarende lokale netværksgateway der repræsenterer placeringen.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Hvad skal jeg angive som adressepræfikser min for gatewayen lokale netværk når jeg bruger BGP?

Azure lokale netværksgateway angiver de indledende mailadresse præfikser for det lokale netværk. Med BGP, skal du allokerer præfikset host (/ 32 præfiks) på din BGP peer-IP-adresse som adresseområde for den lokale netværk. Hvis din BGP peer-IP-adresse er 10.52.255.254, skal du angive "10.52.255.254/32" som localNetworkAddressSpace på den lokale netværksgateway, der repræsenterer denne lokale netværk. Dette er at sikre, at gatewayen Azure VPN fastlægger BGP sessionen gennem S2S VPN-tunnel.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Hvad skal jeg føje til min lokale VPN-enhed til BGP peering sessionen?

Du bør tilføje en host rute af Azure BGP peer-IP-adressen på din VPN-enhed, der peger mod IPsec S2S VPN-tunnel. Azure VPN Peer IP er "10.12.255.30", skal du tilføje en host rute for "10.12.255.30" med en nexthop grænseflade på den tilsvarende IPsec tunnelgrænseflade på enheden VPN.
