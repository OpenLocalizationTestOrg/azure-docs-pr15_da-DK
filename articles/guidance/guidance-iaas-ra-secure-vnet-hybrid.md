<properties
   pageTitle="Implementere en sikker hybrid netværksarkitektur i Azure | Microsoft Azure"
   description="Sådan implementere en sikker hybrid netværksarkitektur i Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-your-on-premises-datacenter"></a>Implementere en DMZ mellem Azure og dit lokale datacenter

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

I denne artikel beskriver bedste fremgangsmåder til at implementere et sikkert hybrid-netværk, der udvides et lokalt netværk til Azure. Denne referencearkitektur implementerer et DMZ mellem et lokalt netværk og en Azure virtuelt netværk ved hjælp af brugerdefinerede omdirigerer (UDRs). DMZ indeholder meget tilgængeligt netværk virtuelle anvendelser (NVAs), der implementerer sikkerhed funktionalitet som firewalls og pakke inspektion. Alle udgående trafik fra VNet er gældende tunnelført til internettet via lokale netværk, så den kan overvåges. 

Denne arkitektur kræver en forbindelse til din lokale datacenter, der er implementeret ved hjælp af enten en [VPN-gateway][ra-vpn], eller en [ExpressRoute] [ ra-expressroute] forbindelse.

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Ressourcestyring] [ resource-manager-overview] og klassisk. Denne referencearkitektur bruger Ressourcestyring, som Microsoft anbefaler til nye installationer.

Typisk anvendelse sager for denne arkitektur omfatter:

- Hybrid programmer hvor arbejdsbelastninger Kør delvist i det lokale miljø og delvist i Azure.

- Azure infrastruktur, som omdirigerer indgående trafik fra lokale og internettet.

- Programmer, der kræves for at overvåge udgående trafik. Dette er ofte et lovmæssige krav i mange kommercielle systemer og kan hjælpe med at forhindre offentliggørelse af personlige oplysninger.

## <a name="architecture-diagram"></a>Arkitektur diagram

I det følgende diagram fremhæver de vigtige komponenter i denne arkitektur:

> Et Visio-dokument, der indeholder dette arkitektur diagram kan hentes på [Microsoft download center][visio-download]. I dette diagram er på siden "DMZ – privat".

[! [0]][0]

- **Lokale netværk.** Dette er et netværk af computere og enheder, der er forbundet via en privat LAN-netværk, der er implementeret i en organisation.

- **Azure virtuelt netværk (VNet).** VNet vært programmet og andre ressourcer, der kører i skyen.

- **Gatewayen.** Gatewayen gør forbindelse mellem router i det lokale netværk og VNet.

- **Netværk virtuelle maskinen (for).** FOR er et generisk udtryk, der beskriver en VM udføre opgaver som at tillade eller nægte adgang som en firewall, optimere WAN handlinger (herunder Netværkskomprimering), brugerdefinerede distribuere eller andre netværksfunktioner. 

- **Web niveau, business niveau og data niveau undernet.** Dette er undernet, der er vært for de FOS og -tjenester, der implementerer et eksempel 3-niveau-program, der kører i skyen. Se [Kører Windows FOS for en N-delt arkitektur på Azure] [ ra-n-tier] kan finde flere oplysninger.

- **Brugerdefinerede omdirigerer (UDR).** [Brugerdefinerede omdirigerer] [ udr-overview] definerer strømmen af IP-trafik i Azure VNets. 

> [AZURE.NOTE] Afhængigt af din VPN-forbindelse krav, kan du konfigurere kant Gateway Protocol (BGP) omdirigerer som et alternativ til at bruge UDRs til at implementere på regler for videresendelse, dirigerer trafik tilbage via lokale netværk.

- **Administration af undernet.** Dette undernet indeholder VM'er, der implementerer administration og overvågning egenskaber for de komponenter, der kører i VNet. 

## <a name="recommendations"></a>Anbefalinger

Azure byder på mange forskellige ressourcer og ressourcetyper, så denne referencearkitektur kan være klargjort mange forskellige måder. Vi har angivet en Azure ressourcestyring skabelon for at installere den referencearkitektur, der følger disse anbefalinger. Hvis du vælger at oprette din egen referencearkitektur skal du følge disse anbefalinger, medmindre du har særlige krav, en anbefaling der ikke passer.

### <a name="rbac-recommendations"></a>RBAC anbefalinger

Oprette flere RBAC roller for at administrere ressourcer i dit program. Overveje at oprette en DevOps [brugerdefineret rolle] [ rbac-custom-roles] med tilladelser til at administrere infrastruktur for programmet. Overveje at oprette et centralt IT-administrator, [brugerdefineret rolle] [ rbac-custom-roles] til at administrere netværksressourcer og en separat sikkerhed IT-administrator [brugerdefineret rolle] [ rbac-custom-roles] til at administrere sikkert netværksressourcer som NVAs. 

DevOps rolle skal indeholde tilladelser til at implementere programkomponenter samt skærm og genstart FOS. Centralt IT-administratorrolle skal indeholde tilladelser til at overvåge netværksressourcer. Ingen af disse roller bør have adgang til for ressourcerne, som det skal være begrænset til sikkerhed IT administratorrolle.

### <a name="resource-group-recommendations"></a>Ressource gruppe anbefalinger

Azure ressourcer som FOS, VNets og Indlæs balancere kan administreres nemt ved at gruppere dem sammen i grupper. Derefter kan du tildele roller over til hver ressourcegruppe til begrænsning af adgang.

Vi anbefaler, at oprettelsen af følgende:

- En ressourcegruppe, der indeholder de undernet (undtagen FOS), NSGs og gateway-ressourcer til at oprette forbindelse til den lokale netværk. Tildele den centrale IT-administratorrolle til denne ressourcegruppe.

- En ressourcegruppe, der indeholder FOS til NVAs (herunder belastning), feltet hyperlink og andre management FOS og UDR for det gateway-undernet, der Fremtvinger al trafik via NVAs. Tildele sikkerheden IT administratorrolle til denne ressourcegruppe.

- Separat ressourcegrupper for hvert niveau i programmet, der indeholder den justering af belastning og FOS. Bemærk, at denne ressourcegruppe ikke bør omfatter undernet for hvert niveau. Tildele rollen DevOps til denne ressourcegruppe.

### <a name="virtual-network-gateway-recommendations"></a>Virtuelt netværk gateway anbefalinger

Lokalt trafik passerer gennem et virtuelt netværk gatewayen til VNet. Vi anbefaler, at en [VPN-Azure gateway] [ guidance-vpn-gateway] eller en [Azure ExpressRoute gateway][guidance-expressroute]. 

### <a name="nva-recommendations"></a>FOR anbefalinger

NVAs giver forskellige tjenester til at administrere og overvåge netværkstrafik. Azure Marketplace indeholder flere tredjepartsforhandler NVAs, herunder:

- [Barracuda Web Application Firewall] [ barracuda-waf] og [Barracuda NextGen Firewall][barracuda-nf]

- [Sammenhængende netværk VNS3 Firewall/Router/VPN][vns3]

- [Fortinet FortiGate-VM][fortinet]

- [Firewall til SecureSphere webprogrammer][securesphere]

- [Firewall til DenyAll webprogrammer][denyall]

- [Se punkt vSEC][checkpoint]

Hvis ingen af disse tredjeparters NVAs opfylder dine behov, kan du oprette en brugerdefineret for ved hjælp af FOS. Du kan finde et eksempel på oprettelse af brugerdefinerede NVAs DMZ i denne referencearkitektur, der implementerer følgende funktioner:

- Trafik er distribueret ved hjælp af [IP-adresse for viderestilling af] [ ip-forwarding] på de for netværkskort.

- Trafik er tilladt at passerer gennem for kun, hvis det er relevant at gøre dette. Hver NVA VM i arkitekturen reference er en enkel Linux router med indgående trafik, der modtages på netværk interface *eth0*og udgående trafik tilsvarende regler, der er defineret af brugerdefinerede scripts, der sendes via netværket interface *eth1*. 

- Trafik distribueres til administration af undernet går ikke igennem NVAs, og NVAs kan kun konfigureres fra management undernet. Hvis der kræves trafik til administration af undernet skal distribueres via NVAs, er der ingen rute til administration af undernet at løse NVAs, hvis de går ned.  

- FOS til for medtages i en angivet bag en belastningsjustering tilgængelighed. UDR i gateway-undernet omdirigerer for anmodninger om til justering af belastning.

En anden anbefaling skal du tænke på at oprette forbindelse flere NVAs i serie med hver udføre en særlig sikkerhed opgave for. Dette giver mulighed for hver sikkerhed funktionen administreres på grundlag af per for. En for implementere en firewall kan for eksempel placeres i serie med en kører identitet services for. Fordeling til forbedring af administrationen er tilføjelsen af ekstra netværk hop, der kan øge ventetid, så sørg for, ikke påvirker det programmets ydeevne.

### <a name="nsg-recommendations"></a>NSG anbefalinger

Gatewayen VPN-Fremviser en offentlig IP-adresse for forbindelsen til den lokale netværk. Vi anbefaler oprettelse af et netværk sikkerhedsgruppe (NSG) til indgående for undernet gennemførelse reglerne for at blokere al trafik ikke stammer fra det lokale netværk.

Vi anbefaler også, at du implementerer NSGs for hvert undernet for at give et andet niveau af beskyttelse mod indgående trafik springe en forkert konfigureret eller deaktiveret for. For eksempel implementerer web niveau undernet i reference arkitekturen en NSG med en regel for at ignorere alle anmodninger end dem, der er modtaget fra det lokale netværk (192.168.0.0/16) eller VNet og en anden regel, som ignorerer alle ikke anmodninger på port 80. 

### <a name="internet-access-recommendations"></a>Internet access anbefalinger

[Tving tunnel] [ azure-forced-tunneling] alle udgående Internettrafik til dit lokale netværk, ved hjælp af websted til websted VPN-tunnel og ruten til internettet via netværket adresse tranlation (NAT). Derved både forhindre utilsigtede uautoriseret videregivelse af enhver fortrolige oplysninger, der er gemt i dine data lag, og også tillade inspektion og overvågning af alle udgående trafik.

> [AZURE.NOTE] Ikke helt blokere internettrafik fra internettet, business og programmet lag. Hvis disse lag bruger Azure PaaS tjenester, de skal have offentlige IP-adresser i VM diagnosticering logføring for, at hente VM extensions og andre funktioner. Azure diagnosticering kræver også, at komponenter kan læse og skrive en internet-afhængige Azure lagerplads konto.

Vi anbefaler yderligere, at du kontrollerer udgående Internettrafik er gældende tunnelført korrekt. Hvis du bruger en VPN-forbindelse med [routing og remote access service] [ routing-and-remote-access-service] på en lokal server, du bruger et værktøj som [WireShark] [ wireshark] eller [Microsoft meddelelse Analyzer](https://www.microsoft.com/en-us/download/details.aspx?id=44226).

### <a name="management-subnet-recommendations"></a>Administration af undernet anbefalinger

Administration af undernet indeholder et link, der er felt, som udfører administration og overvågning funktionalitet. Implementere nedenstående anbefalinger til boksen gå:
- Kan ikke oprette en offentlig IP-adresse i boksen gå. 
- Oprette en rute for at få adgang til feltet gå gennem den indgående gateway og implementere en NSG i management undernet til kun at besvare anmodninger fra tilladte ruten.
- Begrænse udførelse af alle sikker administrationsopgaver i feltet gå.

### <a name="nva-recommendations"></a>FOR anbefalinger

Medtage et lag 7 for at afslutte programmet forbindelser på niveauet for og vedligeholde lighed med back end-niveauer. Dette garanterer symmetriske connectivity som svar trafik fra back end-lag returnerer gennem for.

## <a name="scalability-considerations"></a>Overvejelser i forbindelse med skalerbarhed

Reference arkitekturen implementerer belastningsjustering, der fører lokale netværkstrafik til en samling af for enheder. Som beskrevet tidligere, for enheder er FOS udfører netværk trafik routing regler og er installeret i en [tilgængelighed angive][availability-set]. Dette design kan du overvåge gennemløb af NVAs over tid og tilføje for enheder som svar på øges i belastning.

Gatewayen standard SKU VPN understøtter vedvarende overførselshastighed på op til 100 Mbps. Den høj ydeevne SKU indeholder op til 200 Mbps. Overvej at opgradere til en gateway ExpressRoute til højere båndbredder. ExpressRoute giver op til 10 Gbps båndbredde kortere ventetid end en VPN-forbindelse.

> [AZURE.NOTE] [Implementere en Hybrid netværksarkitektur med Azure og lokale VPN] artiklerne[ guidance-vpn-gateway] og [implementere en hybrid netværksarkitektur med Azure ExpressRoute] [ guidance-expressroute] beskrives problemer i den omgivende skalerbarhed over Azure gateways.

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Reference arkitekturen implementerer belastningsjustering distribuere anmodninger fra det lokale til en samling af for enheder i Azure. FOR enheder er FOS udfører netværk trafik routing regler og er installeret i en [tilgængelighed angive][availability-set]. Justering af belastning forespørgsler en tilstand efterprøvning af af, implementeret på hver for regelmæssigt og fjerner alle ikke svarer NVAs fra puljen. 

Hvis du bruger Azure ExpressRoute til at levere forbindelsen mellem VNet og on-premises netværket, [konfigurere en VPN-gateway for at give failover] [ guidance-vpn-failover] Hvis ExpressRoute forbindelsen bliver utilgængeligt.

Se artiklerne [implementere en Hybrid netværksarkitektur med Azure og lokale VPN] til specifikke oplysninger om vedligeholde tilgængelighed for VPN- og ExpressRoute forbindelser,[ guidance-vpn-gateway] og [implementere en hybrid netværksarkitektur med Azure ExpressRoute][guidance-expressroute].

## <a name="manageability-considerations"></a>Overvejelser i forbindelse med administration

Alle programmer og ressource overvågning skal udføres af feltet hyperlink i management-undernet. Afhængigt af dine krav til programmet, skal du muligvis tilføje yderligere overvågning ressourcer i management-undernet, men igen nogen af disse yderligere ressourcer skal være adgang via feltet gå.

Hvis gateway forbindelse fra dit lokale netværk til Azure ned, kan du stadig når boksen gå ved at installere en PIP, føje det til feltet hyperlink og remoting i fra internettet.

Hvert niveau undernet i reference arkitekturen er beskyttet af NSG regler, og det kan være nødvendigt at oprette en regel for at åbne port 3389 for RDP adgang på Windows FOS eller port 22 for SSH adgang på Linux FOS. Andre administration og værktøjer til overvågning af kan kræve regler til at åbne flere porte.

Hvis du bruger ExpressRoute til at levere forbindelsen mellem dit lokale datacenter og Azure, skal du bruge [Azure Connectivity værktøjskassen (AzureCT)] [ azurect] til at overvåge og foretage fejlfinding af forbindelsesproblemer.

> [AZURE.NOTE] Du kan finde flere oplysninger, der er specielt med henblik på overvågning og administration af VPN- og ExpressRoute forbindelser i artiklerne [implementere en Hybrid netværksarkitektur med Azure og lokale VPN] [ guidance-vpn-gateway] og [implementere en hybrid netværksarkitektur med Azure ExpressRoute][guidance-expressroute].

## <a name="security-considerations"></a>Overvejelser om sikkerheden

Denne referencearkitektur implementerer flere sikkerhedsniveauer: 

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a>Routing alle lokale anmodninger gennem for

UDR i gateway-undernet blokerer alle bruger anmodninger om end dem, der er modtaget fra det lokale. UDR gennemløb tilladt anmodninger om til NVAs i den private DMZ-undernet, og disse anmodninger sendes til programmet, hvis de er tilladt i for reglerne. Andre omdirigerer kan føjes til UDR, men sikre, at de ikke ved et uheld igennem NVAs eller blokere administrative trafik beregnet til administration af undernet.

Justering af belastning foran NVAs fungerer også som en sikkerhedsenhed med ved at ignorere trafik på porte, der ikke er åben i belastningsjustering regler. Indlæse balancere i reference arkitekturen lyt kun efter HTTP-anmodninger på port 80 og HTTPS-anmodninger på port 443. Alle yderligere regler, der er føjet til Indlæs balancere skal være dokumenteret og trafikken skal overvåges for at sikre, at der er ingen sikkerhedsproblemer.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>Brug af NSGs til at blokere/gang trafik mellem program

Hver af websteder, business og data lag begrænser trafikken mellem dem ved hjælp af NSGs. Det vil sige, business niveau bruger en NSG for at blokere al trafik, der ikke stammer fra web niveau, og data niveauet bruger en NSG for at blokere al trafik, der ikke stammer fra business niveauet. Hvis du har et krav til at udvide NSG reglerne for at tillade bredere adgang til disse lag, du afveje disse krav ulemper sikkerhed. Hver ny indgående gangsti repræsenterer en salgsmulighed for utilsigtet eller purposeful data uautoriseret videregivelse eller et andet program skader.

### <a name="devops-access"></a>DevOps access

Begrænse de handlinger, der kan udføre DevOps for hvert niveau, ved hjælp af [RBAC] [ rbac] til at administrere tilladelser. Når gives tilladelser, kan du bruge [princippet bag mindste tilladelse][security-principle-of-least-privilege]. Log alle administrative handlinger og udføre almindelige revisioner for at sikre, at alle ændringer i konfigurationen blev planlagt.

> [AZURE.NOTE] Mere omfattende oplysninger, eksempler og scenarier om administration af netværkssikkerhed med Azure, se [Microsofts skytjenester og netværkssikkerhed][cloud-services-network-security]. Du kan finde detaljerede oplysninger om beskyttelse af ressourcer i skyen, i [Introduktion til Microsoft Azure sikkerhed][getting-started-with-azure-security]. Finde flere oplysninger på at løse sikkerhedsproblemer på tværs af en Azure gateway-forbindelse i [implementere en Hybrid netværksarkitektur med Azure og lokale VPN] [ guidance-vpn-gateway] og [implementere en hybrid netværksarkitektur med Azure ExpressRoute][guidance-expressroute].

## <a name="solution-deployment"></a>Løsningsinstallation

En installation for en referencearkitektur, der implementerer disse anbefalinger er tilgængelig på Github. Denne referencearkitektur omfatter et virtuelt netværk (VNet), netværk sikkerhedsgruppe (NSG), justering af belastning og to virtuelle maskiner (VM'er).

Arkitekturen reference kan installeres enten med Windows eller Linux FOS ved at følge vejledningen herunder: 

1. Højreklik på knappen nedenfor, og vælg enten "Åbn link på en ny fane" eller "Åbn hyperlink i nyt vindue":  
[![Installere på Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet%2Fazuredeploy.json)

2. Når linket er åbnet i portalen Azure, skal du angive værdier for nogle af indstillingerne: 
    - **Ressourcegruppe** navnet allerede er defineret i parameterfilen, så vælge **Opret ny** og angive `ra-private-dmz-rg` i tekstfeltet.
    - Vælg området på rullelisten **placering** .
    - Ikke redigere **Skabelon rod Uri** eller tekstfelterne **Parameter rod Uri** .
    - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
    - Klik på knappen **Køb** .

3. Vente på at fuldføre installationen.

4. Parameter filerne indeholder faste administratorens brugernavn og adgangskode til alle FOS, og det anbefales, at du straks ændrer begge. Markere det i portalen Azure for hver VM i installationen, og klik derefter på på **Nulstil adgangskode** i bladet **Support + fejlfinding** . Vælg **Nulstil adgangskode** i feltet **tilstand** rullemenuen og derefter vælge et nyt **brugernavn** og en **adgangskode**. Klik på knappen **Opdater** for at bevare.

## <a name="next-steps"></a>Næste trin

- Lær at implementere en [DMZ mellem Azure og internettet](./guidance-iaas-ra-secure-vnet-dmz.md).
- Lær at implementere en [meget tilgængelige hybrid netværksarkitektur](./guidance-hybrid-network-expressroute-vpn-failover.md).

<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-create-availability-set.md
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[barracuda-nf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/barracuda-ng-firewall/
[barracuda-waf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/
[checkpoint]: https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/
[cloud-services-network-security]: https://azure.microsoft.com/documentation/articles/best-practices-network-security/
[denyall]: https://azure.microsoft.com/marketplace/partners/denyall/denyall-web-application-firewall/
[fortinet]: https://azure.microsoft.com/marketplace/partners/fortinet/fortinet-fortigate-singlevmfortigate-singlevm/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[ip-forwarding]: ../virtual-network/virtual-networks-udr-overview.md#IP-forwarding
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[ra-n-tier]: ./guidance-compute-n-tier-vm.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[rbac-custom-roles]: ../active-directory/role-based-access-control-custom-roles.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[routing-and-remote-access-service]: https://technet.microsoft.com/library/dd469790(v=ws.11).aspx
[securesphere]: https://azure.microsoft.com/marketplace/partners/imperva/securesphere-waf-for-azr/
[security-principle-of-least-privilege]: https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vns3]: https://azure.microsoft.com/marketplace/partners/cohesive/cohesiveft-vns3-for-azure/
[wireshark]: https://www.wireshark.org/
[0]: ./media/blueprints/hybrid-network-secure-vnet.png "Secure hybrid netværksarkitektur"
