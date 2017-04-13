<properties
   pageTitle="Azure virtuelt netværk (VNet) Plan og vejledning til Design af | Microsoft Azure"
   description="Lær at planlægge og designe virtuelle netværk i Azure baseret på dine krav til isolationsniveauet, connectivity og placering."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/08/2016"
   ms.author="jdial" />

# <a name="plan-and-design-azure-virtual-networks"></a>Planlægge og designe Azure virtuelle netværk

Er ikke nok nemt at oprette en VNet for at eksperimentere med, men sandsynligvis, du vil installere flere VNets over tid til at understøtte fremstilling behovene i organisationen. Med nogle planlægning og design, vil du kunne installere VNets og forbinde de ressourcer, du skal bruge mere effektivt. Hvis du ikke kender med VNets, det anbefales, som du [Få mere at vide om VNets](virtual-networks-overview.md) , og [hvordan du kan installere](virtual-networks-create-vnet-arm-pportal.md) en før du fortsætter. 

## <a name="plan"></a>Planlægge

Et indgående kendskab til Azure abonnementer, områder og netværksressourcer er afgørende for succes. Du kan bruge på listen over overvejelser i forbindelse med under som udgangspunkt. Når du forstår disse overvejelser i forbindelse med, kan du definerer kravene til dit netværksdesign.

### <a name="considerations"></a>Overvejelser i forbindelse med

Før besvare planlægningen spørgsmål nedenfor, skal du overveje følgende:

- Alt, hvad du har oprettet i Azure består af en eller flere ressourcer. En virtual machine (VM) er en ressource, netværk adapter interface (NIC) bruges af en VM er en ressource, den offentlige IP-adresse, der bruges af en NIC er en ressource, VNet NIC er forbundet med er en ressource.
- Du opretter ressourcer inden for en [Azure område](https://azure.microsoft.com/regions/#services) og abonnement. Og ressourcer kan kun være forbundet til en VNet, som findes i samme område og de er i-abonnement. 
- Du kan oprette forbindelse VNets til hinanden ved hjælp af en Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md). Du kan også forbinde VNets på tværs af områder og abonnementer på denne måde.
- Du kan oprette forbindelse VNets til dit lokale netværk ved hjælp af en af de [Indstillinger for forbindelse](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) i Azure. 
- Forskellige ressourcer, der kan grupperes i [grupper](../azure-resource-manager/resource-group-overview.md#resource-groups), gør det nemmere at håndtere ressourcen, som en enhed. En ressourcegruppe kan indeholde ressourcer fra flere områder, så længe ressourcerne, der hører til samme-abonnement.

### <a name="define-requirements"></a>Definere krav

Bruge spørgsmål nedenfor som udgangspunkt for din Azure netværksdesign.  

1. Hvilke Azure steder skal du bruge vært VNets?
2. Har du brug at levere kommunikation mellem disse Azure placeringer?
3. Har du brug at levere kommunikation mellem din Azure VNet(s) og din lokale datacenter(s)?
4. Hvor mange Infrastructure som en tjeneste (IaaS) FOS cloud services roller og web apps har du brug for din løsning?
5. Har du brug for til at isolere trafik baseret på grupper af FOS (det vil sige front end-Webserverne og back-end-databaseservere)?
6. Har du brug for til at styre trafikken ved hjælp af virtuelle anvendelser?
7. Skal brugerne forskellige sæt tilladelser til forskellige Azure ressourcer?

### <a name="understand-vnet-and-subnet-properties"></a>Forstå VNet og undernet egenskaber

VNet og undernet ressourcer hjælpe dig med at definere sikkerhed rammen for arbejdsbelastninger, som kører i Azure. En VNet er er betegnet ved en samling af adresse mellemrum, defineret som CIDR dokumentkomponenter. 

>[AZURE.NOTE] Administratorerne kender CIDR notation. Hvis du ikke kender med CIDR, [vide mere om den](http://whatismyipaddress.com/cidr).

VNets indeholder følgende egenskaber.

|Egenskaben|Beskrivelse|Begrænsninger|
|---|---|---|
|**Navn**|VNet navn|Strengen med op til 80 tegn. Kan indeholde bogstaver, tal, understregningstegn, perioder eller bindestreger. Skal starte med et bogstav eller et tal. Skal slutte med et bogstav, tal eller understregningstegn. Kan indeholder store eller små bogstaver.|  
|**placering**|Azure placering (også kaldet område).|Skal være en af de gyldige Azure placeringer.|
|**addressSpace**|Samling af adresse præfikser på enheder, der udgør VNet i CIDR notation.|En matrix med gyldige CIDR adresseblokke, herunder offentlige IP-adresseområder skal være.|
|**undernet**|Samling af undernet, der udgør VNet|Se nedenstående undernet egenskaber for tabel.||
|**dhcpOptions**|Objekt, der indeholder en enkelt påkrævet egenskab, der hedder **DNS-serverne**.||
|**DNS-serverne**|Matrix af DNS-servere, der anvendes af VNet. Hvis ingen server ikke er angivet, bruges Azure interne navneoversættelse.|Skal være en matrix med op til 10 DNS-servere efter IP-adresse.| 

Et undernet er en underordnet ressource af en VNet, og gør det muligt at definere segmenter af adresse mellemrum i en CIDR blok, ved hjælp af IP-adressepræfikser. Netværkskort kan føjes til undernet og tilsluttet FOS, give forbindelse til forskellige arbejdsbelastninger.

Undernet indeholder følgende egenskaber. 

|Egenskaben|Beskrivelse|Begrænsninger|
|---|---|---|
|**Navn**|Undernet navn|Strengen med op til 80 tegn. Kan indeholde bogstaver, tal, understregningstegn, perioder eller bindestreger. Skal starte med et bogstav eller et tal. Skal slutte med et bogstav, tal eller understregningstegn. Kan indeholder store eller små bogstaver.|
|**placering**|Azure placering (også kaldet område).|Skal være en af de gyldige Azure placeringer.|
|**addressPrefix**|Enkelt adressepræfiks, der udgør undernet i CIDR notation|Skal være en enkelt CIDR blok, der er en del af en af de VNet adresse mellemrum.|
|**networkSecurityGroup**|NSG anvendt til undernettet|Se [NSGs](resource-groups-networking.md#Network-Security-Group)|
|**routeTable**|Distribuere tabel, der er anvendt til undernettet|Se [UDR](resource-groups-networking.md#Route-table)|
|**ipConfigurations**|Samling af IP-konfiguration-objekter, der bruges af netværkskort tilsluttet til undernettet|Se [IP-konfiguration](../resource-groups-networking.md#IP-configurations)|

### <a name="name-resolution"></a>Navneoversættelse

Som standard din VNet bruger [Azure-producenter navneoversættelse.](virtual-networks-name-resolution-for-vms-and-role-instances.md#Azure-provided-name-resolution) til at fortolke navne i VNet og på offentlige internettet. Men hvis du knytter din VNets til din lokale datacentre, skal du angive [dine egne DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#Name-resolution-using-your-own-DNS-server) , for at finde navne mellem dine netværk.  

### <a name="limits"></a>Begrænsninger

Gennemse netværk begrænsningerne i artiklen [Azure for](../azure-subscription-service-limits.md#networking-limits) at sikre, at dit design ikke er i konflikt med en af grænserne. Nogle grænser forbedres ved at åbne en supportbilletter.

### <a name="role-based-access-control-rbac"></a>Rollebaseret adgangskontrol (RBAC)

Du kan bruge [Azure RBAC](../active-directory/role-based-access-built-in-roles.md) til at styre adgangsniveauet, forskellige brugere kan være nødvendigt at forskellige ressourcer i Azure. På denne måde kan du udskille det arbejde, der er udført af dit team, der er baseret på deres behov. 

Udstrækning det virtuelle netværk er bekymrede, har brugere i rollen **Bidragyder netværk** fuld kontrol over Azure ressourcestyring virtuelt netværksressourcer. På samme måde, brugere i rollen **Klassisk netværk bidragyder** har fuld kontrol over klassisk virtuelt netværksressourcer.

>[AZURE.NOTE] Du kan også [oprette dine egne roller](../active-directory/role-based-access-control-configure.md) for at adskille dine administrative behov.

## <a name="design"></a>Design

Når du mestrer svarene på spørgsmålene i afsnittet [planlægge](#Plan) , kan du gennemgå følgende, før du definerer dine VNets.

### <a name="number-of-subscriptions-and-vnets"></a>Antallet af abonnementer og VNets

Du bør overveje at oprette flere VNets i følgende situationer:

- **VM'er, der skal placeres på forskellige placeringer i Azure**. VNets i Azure er internationale. De kan ikke dække placeringer. Derfor skal du mindst én VNet for hver Azure placering, du vil host FOS i.
- **Arbejdsbelastninger, der skal være helt adskilt fra hinanden**. Du kan oprette separate VNets, der også bruger de samme IP-adresse mellemrum, til at isolere forskellige arbejdsbelastninger fra hinanden. 

Husk på, er begrænsningerne vises over hver region, per abonnement. Det betyder, at du kan bruge flere abonnementer for at øge grænsen for ressourcer, du kan bevare i Azure. Du kan bruge en VPN til-websted eller et ExpressRoute kredsløb skal oprettes forbindelse VNets i forskellige abonnementer.

### <a name="subscription-and-vnet-design-patterns"></a>Abonnement og VNet design mønstre

Tabellen nedenfor viser nogle almindelige designs til brug af abonnementer og VNets.

|Scenarie|Diagram|Medarbejdere|Ulemper|
|---|---|---|---|
|Enkelt to VNets per app-abonnement|![Enkelt abonnement](./media/virtual-network-vnet-plan-design-arm/figure1.png)|Kun én abonnement til at administrere.|Maksimale antal VNets per Azure område. Flere abonnementer skal du herefter. Gennemgå artiklen om [Azure begrænser](../azure-subscription-service-limits.md#networking-limits) få mere at vide.|
|Ét abonnement per app, to VNets per app|![Enkelt abonnement](./media/virtual-network-vnet-plan-design-arm/figure2.png)|Bruger kun to VNets per abonnement.|Sværere at administrere, når der er for mange apps.|
|Ét abonnement per afdeling, to VNets per app.|![Enkelt abonnement](./media/virtual-network-vnet-plan-design-arm/figure3.png)|Saldo mellem antallet af abonnementer og VNets.|Maksimale antal VNets per afdeling (abonnement). Gennemgå artiklen om [Azure begrænser](../azure-subscription-service-limits.md#networking-limits) få mere at vide.|
|Ét abonnement per afdeling, to VNets hver gruppe af apps.|![Enkelt abonnement](./media/virtual-network-vnet-plan-design-arm/figure4.png)|Saldo mellem antallet af abonnementer og VNets.|Apps skal isolere ved hjælp af undernet og NSGs.|


### <a name="number-of-subnets"></a>Antallet af undernet

Du bør overveje flere undernet i en VNet i følgende situationer:

- **Der er ikke nok private IP-adresser for alle netværkskort i et undernet**. Hvis dit undernet adresseområde ikke indeholder nok ledig IP-adresser for antallet af netværkskort i undernettet, skal du oprette flere undernet. Husk på, Azure forbeholder sig 5 private IP-adresser fra alle undernet, der ikke kan bruges: første og sidste adressen på adresseområde (for undernetadresse og multicast) og 3 adresser, der skal anvendes internt (til DHCP og DNS formål). 
- **Sikkerhed**. Du kan bruge undernet til at adskille grupper af FOS fra hinanden for arbejdsbelastninger, som har en med flere lag struktur og anvende forskellige [netværk sikkerhedsgrupper (NSGs)](virtual-networks-nsg.md#subnets) for disse undernet.
- **Hybrid connectivity**. Du kan bruge VPN gateways og ExpressRoute kredsløb til at [oprette forbindelse](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) din VNets til hinanden, og at center(s) dine lokale data. VPN gateways og ExpressRoute kredsløb kræver et undernet af deres egne skal oprettes.
- **Virtuel anvendelser**. Du kan bruge et virtuelt maskinen, som en firewall, WAN genvejstast eller VPN-gateway i en Azure VNet. Når du gør det, du har brug for at [dirigere trafik](virtual-networks-udr-overview.md) til disse anvendelser og isolere dem i deres egen undernet.

### <a name="subnet-and-nsg-design-patterns"></a>Undernet og NSG designs

Tabellen nedenfor viser nogle almindelige designs til brug af undernet.

|Scenarie|Diagram|Medarbejdere|Ulemper|
|---|---|---|---|
|Enkelt undernet, NSGs per programlag per app|![Enkelt undernet](./media/virtual-network-vnet-plan-design-arm/figure5.png)|Kun ét undernet til at administrere.|Flere NSGs, der er nødvendige for at isolere hvert program.|
|Ét undernet per app, NSGs per programlag|![Undernet per app](./media/virtual-network-vnet-plan-design-arm/figure6.png)|Færre NSGs til at administrere.|Flere undernet til at administrere.|
|Ét undernet per programlag, NSGs per app.|![Undernet per lag](./media/virtual-network-vnet-plan-design-arm/figure7.png)|Saldo mellem antallet af undernet og NSGs.|Maksimale antal NSGs per abonnement. Gennemgå artiklen om [Azure begrænser](../azure-subscription-service-limits.md#networking-limits) få mere at vide.|
|Ét undernet per programlag per app, NSGs per undernet|![Undernet per lag per app](./media/virtual-network-vnet-plan-design-arm/figure8.png)|Muligvis mindre antallet af NSGs.|Flere undernet til at administrere.|

## <a name="sample-design"></a>Eksempel design

For at illustrere anvendelse af oplysninger i denne artikel, bør du overveje følgende scenario.

Du arbejder i en virksomhed, der indeholder 2 datacentre i Nordamerika og to datacentre Europe. Du har identificeret 6 anden kunde modstående programmer vedligeholdes af 2 forskellige afdelinger, du vil overføre til Azure som et pilotprojekt. Den grundlæggende arkitektur i programmer er som følger:

- App1, App2, App3 og App4 er webprogrammer baseret på Linux-servere, der kører Ubuntu. Hvert program, der opretter forbindelse til en separat application server, der er vært RESTful tjenester på Linux-servere. RESTful tjenesterne oprette forbindelse til en back-end MySQL-database.
- App5 og App6 er webprogrammer baseret på Windows-servere, der kører Windows Server 2012 R2. Hvert program, der opretter forbindelse til en back-end SQL Server-database.
- Alle apps er i øjeblikket er hostet på en af firmaets datacentre i Nordamerika.
- Lokalt datacentre Brug 10.0.0.0/8-adresseområde.

Du har brug for til at designe en virtuel netværksløsning, der opfylder følgende krav:

- Hver afdeling skal berøres ikke af ressourceforbrug andre afdelinger.
- Du skal minimere mængden VNets og undernet for at gøre det nemmere at administrere.
- Hver afdeling skal have en enkelt test/udvikling VNet bruges til alle programmer.
- Hvert program, der er hostet i 2 forskellige, Azure datacentre per kontinent (Nordamerika og Europe).
- Hvert program er helt adskilt fra hinanden.
- Hvert program kan åbnes af kunder via internettet med HTTP.
- Hvert program kan åbnes af brugere, der er forbundet til de lokale datacentre ved hjælp af en krypteret tunnel.
- Forbindelse til lokale datacentre skal bruge eksisterende VPN-enheder.
- Firmaets netværk gruppe skal have fuld kontrol over VNet konfigurationen.
- Udviklere i hver afdeling skal kun kunne installeres FOS til eksisterende undernet.
- Der overføres alle programmer, som de er til Azure (elevator-og-Skift).
- Databaser i hver placering skal replikeres til andre Azure placeringer en gang om dagen.
- Hvert program, der skal bruge 5-front end-webserverne, 2 application servere (når det er nødvendigt) og 2 databaseservere.

### <a name="plan"></a>Planlægge

Du skal starte dit design, planlægning ved at besvare spørgsmål i afsnittet [Definer krav](#Define-requirements) , som vist nedenfor.

1. Hvilke Azure steder skal du bruge vært VNets?

    2 placeringer i Nordamerika og 2 placeringer i Europa. Du skal vælge dem, der er baseret på den fysiske placering af dine eksisterende lokalt datacentre. På denne måde forbindelsen fra din fysiske placeringer til Azure har en bedre ventetid.

2. Har du brug at levere kommunikation mellem disse Azure placeringer?

    Ja. Da databaserne, der skal replikeres til alle placeringer.

3. Har du brug at levere kommunikation mellem din Azure VNet(s) og dine lokale data center(s)?

    Ja. Siden brugere tilsluttet skal datacentre lokale kunne få adgang til programmerne via en krypteret tunnel.
 
4. Hvor mange IaaS FOS har du brug for til din løsning?

    200 IaaS FOS. App1, App2 og App3 kræver 5-webserverne hver, 2 programmer servere hver og 2 databaseservere hver. Det er en total af 9 IaaS FOS per programmet eller 36 IaaS FOS. App5 og App6 kræver 5-Webserverne og 2 databaseservere. Det er en total på 7 IaaS FOS per programmet eller 14 IaaS FOS. Du skal derfor 50 IaaS FOS for alle programmer i hvert område for Azure. Da vi skal bruge 4 områder, vil der være 200 IaaS FOS.

    Du skal også give DNS-servere i hver VNet eller i din lokale datacentre til at oversætte navn mellem din Azure IaaS FOS og dit lokale netværk. 

5. Har du brug for til at isolere trafik baseret på grupper af FOS (det vil sige front end-Webserverne og back-end-databaseservere)?

    Ja. Hvert program, der skal være helt adskilt fra hinanden, og hver programlag bør også være isolerede. 

6. Har du brug for til at styre trafikken ved hjælp af virtuelle anvendelser?

    Nej. Virtuel udstyr kan bruges til at give mere kontrol over trafikken, herunder mere detaljerede data plan logføring. 

7. Skal brugerne forskellige sæt tilladelser til forskellige Azure ressourcer?

    Ja. Netværk teamet skal have fuld kontrol på de virtuelle netværksindstillinger, mens udviklere kun skal kunne installere deres FOS til et eksisterende undernet. 

### <a name="design"></a>Design

Du skal følge det design, der angiver abonnementer, VNets, undernet og NSGs. Vi vil diskutere NSGs her, men du skal lære mere om [NSGs](virtual-networks-nsg.md) før du afslutter dit design.

**Antallet af abonnementer og VNets**

Følgende krav er relateret til abonnementer og VNets:

- Hver afdeling skal berøres ikke af ressourceforbrug andre afdelinger.
- Du skal minimere mængden VNets og undernet.
- Hver afdeling skal have en enkelt test/udvikling VNet bruges til alle programmer.
- Hvert program, der er hostet i 2 forskellige, Azure datacentre per kontinent (Nordamerika og Europe).

Baseret på disse krav, du skal bruge et abonnement til hver afdeling. På denne måde forbrug af ressourcer fra en afdeling kan ikke regnes med begrænsninger for andre afdelinger. Og da du vil minimere antallet af VNets, bør du overveje at bruge **et abonnement per afdeling, to VNets hver gruppe af apps** mønster, som set under.

![Enkelt abonnement](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Du skal også angive adresseområder for hver VNet. Da du har brug for forbindelsen mellem lokale data Centrerer og områderne Azure adresseområder bruges til Azure VNets kan ikke falde sammen med det lokale netværk, og det adresseområde, der bruges af hver VNet ikke skal falde sammen med andre eksisterende VNets. Du kan bruge adresse mellemrummene i tabellen nedenfor til at opfylder kravene.  

|**Abonnement**|**VNet**|**Azure område**|**Adresseområde**|
|---|---|---|---|
|BU1|ProdBU1US1|Vest USA|172.16.0.0/16|
|BU1|ProdBU1US2|Indtastning af østasiatiske USA|172.17.0.0/16|
|BU1|ProdBU1EU1|Nord Europe|172.18.0.0/16|
|BU1|ProdBU1EU2|Vest Europe|172.19.0.0/16|
|BU1|TestDevBU1|Vest USA|172.20.0.0/16|
|BU2|TestDevBU2|Vest USA|172.21.0.0/16|
|BU2|ProdBU2US1|Vest USA|172.22.0.0/16|
|BU2|ProdBU2US2|Indtastning af østasiatiske USA|172.23.0.0/16|
|BU2|ProdBU2EU1|Nord Europe|172.24.0.0/16|
|BU2|ProdBU2EU2|Vest Europe|172.25.0.0/16|

**Antallet af undernet og NSGs**

Følgende krav er relateret til undernet og NSGs:

- Du skal minimere mængden VNets og undernet.
- Hvert program er helt adskilt fra hinanden.
- Hvert program kan åbnes af kunder via internettet med HTTP.
- Hvert program kan åbnes af brugere, der er forbundet til de lokale datacentre ved hjælp af en krypteret tunnel.
- Forbindelse til lokale datacentre skal bruge eksisterende VPN-enheder.
- Databaser i hver placering skal replikeres til andre Azure placeringer en gang om dagen.

Baseret på disse krav, kan du bruge ét undernet per programlag og bruger NSGs til at filtrere trafik i programmet. På denne måde kan du kun har 3 undernet i hver VNet (front-end, programlag og data lag) og én NSG per programmet per undernet. I dette tilfælde skal du overveje at bruge **ét undernet per programlag, NSGs per app** design mønster. I nedenstående figur vises brugen af det design mønster, der repræsenterer **ProdBU1US1** VNet.

![Ét undernet per lag, én NSG per programmet i lag](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Skal du også oprette en ekstra undernet for VPN-forbindelsen mellem VNets, og din lokale datacentre. Og du skal angive adresseområde for hvert undernet. Nedenstående figur viser et eksempel løsning for **ProdBU1US1** VNet. Du vil gentage dette scenarie for hver VNet. Hver farve repræsenterer et andet program.

![Eksempel VNet](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Adgangskontrol**

Følgende krav er relateret til adgangskontrol:

- Firmaets netværk gruppe skal have fuld kontrol over VNet konfigurationen.
- Udviklere i hver afdeling skal kun kunne installeres FOS til eksisterende undernet.

Baseret på disse krav, kan du føje brugere fra netværk-teamet til den indbyggede **Netværk bidragyder** rolle i hvert abonnement og oprette en brugerdefineret rolle for programmet udviklere i hvert give dem tilladelse til at føje FOS til eksisterende undernet abonnement.

## <a name="next-steps"></a>Næste trin

- [Implementer et virtuelt netværk](virtual-networks-create-vnet-arm-template-click.md) baseret på et scenarie.
- Forstå, hvordan du [justering af belastning](../load-balancer/load-balancer-overview.md) IaaS FOS og [administrere routing over flere Azure områder](../traffic-manager/traffic-manager-overview.md).
- Få mere at vide om [NSGs, og hvordan du planlægger og designer](virtual-networks-nsg.md) en NSG løsning.
- Lær mere om dine [tværs det lokale miljø og VNet connectivity indstillinger](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site).
