<properties
   pageTitle="Oversigt over Azure netværk sikkerhed | Microsoft Azure"
   description=" I denne artikel gør det nemt for dig at forstå, hvad Microsoft Azure har til at tilbyde i området af netværkssikkerhed. Vi giver dig grundlæggende forklaringer core netværk sikkerhedsbegreber og krav og oplysninger på hvad Azure har til at tilbyde i hver af disse områder. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-network-security-overview"></a>Oversigt over Azure netværk sikkerhed

Microsoft Azure omfatter en robust netværksinfrastruktur at understøtte dit program og service connectivity krav. Netværksforbindelsen er muligt mellem ressourcer i Azure, der er placeret mellem lokale og Azure hostet ressourcer, og til og fra internettet og Azure.

Formålet med denne artikel er at gøre det nemmere for dig at forstå, hvad Microsoft Azure har til at tilbyde i området af netværkssikkerhed. Her er grundlæggende forklaringer til grundlæggende netværk sikkerhedsbegreber og krav. Vi også give du oplysninger om hvad Azure har til at tilbyde i hver af disse områder. Der findes flere links til andre indhold, der gør det muligt at få en bedre forståelse for de områder, hvor du er interesseret.

Artiklen oversigt over Azure netværk sikkerhed fokuserer på følgende:

- Azure netværk
- Adgangskontrol til netværket
- Sikre remote access og tværs lokale forbindelsen
- Tilgængelighed
- Logføring
- Navneoversættelse
- DMZ arkitektur
- Azure Security Center

## <a name="azure-networking"></a>Azure netværk

Virtuelle maskiner skal netværksforbindelse. For at understøtte dette krav, kræver Azure virtuelle maskiner have forbindelse til et virtuelt Azure-netværk. Et virtuelt Azure-netværket ikke er en logisk konstruktion indbygget oven på fysiske Azure netværk-strukturen. Hver logiske Azure virtuelt netværk er adskilt fra alle andre Azure virtuelle netværk. Dette hjælper med at sikre, at netværkstrafik i dine installationer ikke er tilgængeligt for andre Microsoft Azure-kunder.

Lær mere:

- [Oversigt over virtuelt netværk](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Adgangskontrol til netværket
Adgangskontrol til netværket er kunsten at begrænse tilslutning til og fra bestemte enheder eller undernet i et virtuelt Azure-netværk. Formålet med adgangskontrol til netværket er at sørge for, at dine virtuelle computere og tjenester er tilgængelige for brugere og enheder, du vil have dem tilgængelige. Adgang til kontrolelementer for er baseret på tillade eller nægte beslutninger om forbindelser til og fra et virtuelt eller en tjeneste.

Azure understøtter flere typer adgangskontrol til netværket. Dette omfatter:

- Netværk lag kontrolelement
- Distribuere kontrolelement og tvunget tunnelføring
- Virtuelt netværk sikkerhedsfunktioner

### <a name="network-layer-control"></a>Netværk lag kontrolelement
En hvilken som helst sikker installation kræver nogle mål for adgangskontrol til netværket. Formålet med adgangskontrol til netværket er at sørge for, virtuelle computere og netværk services, der kører på disse virtuelle maskiner kan kun kommunikere med andre netværksenheder, at de skal bruge til at kommunikere med, og alle andre forbindelse forsøg er blokeret.

Hvis du har brug for standardnetværk niveau adgangskontrol (baseret på IP-adresse og TCP eller UDP-protokoller), kan du bruge netværk sikkerhedsgrupper. Et netværk sikkerhed gruppe (NSG) er en grundlæggende med høj sikkerhed pakke filtrering firewall, og det gør det muligt at kontrollere adgangen, der er baseret på en [5-tupel](https://www.techopedia.com/definition/28190/5-tuple). NSGs leverer ikke programmet lag inspektion eller godkendt adgang til kontrolelementer.

Lær mere:

- [Netværk sikkerhedsgrupper](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Distribuere kontrolelement og tvunget tunnelføring
Muligheden for at styre routing funktionsmåde på dine Azure virtuelle netværk er en kritiske netværk sikkerheds- og kontrolelementet funktion. Hvis routing er konfigureret forkert, programmer og tjenester, der findes på din virtuelle maskine kan oprette forbindelse til enheder, du ikke vil have dem til at oprette forbindelse til, herunder enheder ejes og administreres af potentielle hackere.

Azure netværk understøtter muligheden for at tilpasse routing funktionsmåden for netværkstrafik på dine Azure virtuelle netværk. Dette gør det muligt at ændre standard routing posterne i tabellen i Azure virtuelle netværket. Kontrol af routing funktionsmåde sikrer du, at al trafik fra en bestemt enhed eller gruppe af enheder placeres i eller forlader netværket Azure virtuelle via en bestemt placering.

For eksempel kan du har et virtuelt netværk sikkerhed maskinen på netværket Azure virtuelle. Du vil sikre dig, at al trafik til og fra netværket Azure virtuelle går igennem den virtuelle sikkerhed maskinen. Du kan gøre dette ved at konfigurere [Brugerdefineret omdirigerer](../virtual-network/virtual-networks-udr-overview.md) i Azure.

[Tvungen tunnelføring](https://www.petri.com/azure-forced-tunneling) er en funktion, du kan bruge til at sikre, at dine tjenester ikke kan starte en forbindelse til enheder på internettet. Bemærk, at dette er anderledes end acceptere indgående forbindelser og derefter svarer på dem. Front end-servere skal reagere på anmodning fra internettet hosts, og så Internet-leveres trafik er tilladt indgående disse-servere og webserverne har tilladelse til at svare.

Hvad du ikke vil tillade er en front end-server til at starte en anmodning om udgående. Sådanne anmodninger kan repræsentere en sikkerhedsrisiko, fordi disse forbindelser kan bruges til at hente malware. Selvom du vil disse front end-servere at starte udgående anmodninger til internettet, kan du få Tving dem til at gå gennem din lokale web stedfortrædere, så kan du drage fordel af URL-adressen, filtrering og logføring.

Du skulle i stedet bruge tvunget tunnelføring for at undgå dette. Når du aktiverer tvunget tunnelføring, tvunget alle forbindelser til internettet via din lokale gateway. Du kan konfigurere tvunget tunnelføring ved at drage fordel af brugerdefineret omdirigerer.

Lær mere:

- [Hvad er brugerdefineret omdirigerer og IP-videresendelse](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Virtuelt netværk sikkerhedsfunktioner
Mens netværk sikkerhedsgrupper, brugerdefineret omdirigerer og tvunget tunnelføring giver dig et niveau af sikkerhed på netværk og transport lag [OSI-modellen](https://en.wikipedia.org/wiki/OSI_model), kan der være tidspunkter, hvor du vil aktivere sikkerhed på niveauer, der er højere end netværket.

For eksempel, kan indeholde dine krav til sikkerhed:

- Godkendelse og autorisation før du tillade adgang til dit program
- Uautoriseret adgang registrering og uautoriseret adgang svar
- Programmet lag kontrol for overordnet protokoller
- URL-filtrering
- Netværk niveau antivirus- og Antimalwareprogram
- Beskyttelse mod bot
- Programmet adgangskontrol
- Yderligere DDoS-beskyttelse (over den DDoS beskyttelse leveres Azure-strukturen selve)

Du kan få adgang til disse udvidet netværk sikkerhedsfunktioner ved hjælp af en Azure partnerløsning. Du kan finde de mest aktuelle Azure partner network sikkerhedsløsninger ved at besøge [Azure Marketplace](https://azure.microsoft.com/marketplace/) og søge efter "sikkerhed" og "netværkssikkerhed".

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Sikker fjernadgang og Cross lokale forbindelse
Installation, konfiguration og administration af dine Azure ressourcer behov udføres fra en fjernplacering. Desuden kan du overveje at implementere [hybrid IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) -løsninger, der har komponenter lokalt og i Azure offentlige skyen. Disse situationer kræver sikker fjernadgang.

Azure netværk understøtter sikker fjernadgang følgende scenarier:

- Oprette forbindelse individuelle arbejdsstationer til et virtuelt Azure-netværk
- Oprette forbindelse dit lokale netværk til en Azure virtuelt netværk med en VPN-forbindelse
- Oprette forbindelse dit lokale netværk til en Azure virtuelt netværk med en dedikeret WAN-forbindelse
- Oprette forbindelse Azure virtuelle netværk til hinanden

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Oprette forbindelse individuelle arbejdsstationer til en Azure virtuelt netværk
Der kan være tidspunkter, hvor du vil aktivere individuelle udviklere eller handlinger personale til at administrere virtuelle computere og tjenester i Azure. For eksempel du skal have adgang til en virtuel maskine i et virtuelt Azure-netværk, og din sikkerhedspolitik tillader ikke RDP eller SSH fjernadgang til individuelle virtuelle computere. I dette tilfælde kan du bruge en VPN-forbindelse til punkt-til-websted.

Punkt-til-site VPN-forbindelse bruger [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) -protokollen til gør det muligt at konfigurere en privat og sikker forbindelse mellem brugeren og det virtuelle Azure-netværk. Når VPN-forbindelsen er oprettet, vil brugeren kunne RDP eller SSH via VPN-forbindelse til en hvilken som helst virtuel maskine på det virtuelle Azure-netværk (Hvis resultatet af, at brugeren kan godkende og er godkendt).

Lær mere:

- [Konfigurere en punkt-til-Site forbindelse til et virtuelt netværk ved hjælp af PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Oprette forbindelse dit lokale netværk til en Azure virtuelt netværk med en VPN-forbindelse
Du vil oprette forbindelse til et virtuelt netværk med Azure hele virksomhedens netværk, eller dele af den. Dette er fælles for hybride IT scenarier hvor virksomheder [udvide deres lokale datacenter til Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). I mange tilfælde skal firmaer være vært for dele af en tjeneste i Azure og dele lokale miljø, som når en løsning indeholder front end-servere i Azure og lokale back end-databaser. Disse slags "tværs lokale" forbindelser også gøre styringen af Azure findes ressourcer mere sikre og aktivere scenarier som udvide Active Directory-domænecontrollere på Azure.

En metode til at udføre dette er at bruge en [VPN til-websted](https://www.techopedia.com/definition/30747/site-to-site-vpn). Forskellen mellem en VPN til-websted og en VPN-punkt-til-websted er, at en punkt-til-site VPN-forbindelse opretter forbindelse en enkelt enhed til et virtuelt Azure-netværk, mens en VPN til-websted i et helt netværk (såsom dit lokale netværk) opretter forbindelse til et virtuelt Azure-netværk. Websted til websted VPN til et virtuelt netværk Azure bruger meget sikre IPSec-tunneltilstand VPN-protokollen.

Lær mere:

- [Oprette en ressourcestyring VNet med en-til-websted VPN-forbindelse ved hjælp af portalen Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Planlægning og design til VPN-gateway](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Oprette forbindelse dit lokale netværk til en Azure virtuelt netværk med en dedikeret WAN-forbindelse
Punkt-til-websted og websted til websted VPN-forbindelser er effektive for aktivering af forbindelse i tværs det lokale miljø. Imidlertid overveje nogle organisationer de skal have følgende ulemper:

- VPN-forbindelser flytte data via internettet – dette viser disse forbindelser til potentielle sikkerhedsproblemer, der er tilknyttet flytning af data via et offentligt netværk. Pålidelighed og tilgængelighed til internetforbindelser kan desuden sikres.
- VPN-forbindelser til Azure virtuelle netværk kan betragtes som båndbredde begrænset til nogle programmer og formål, som de maksimale ud på omkring 200Mbps.

Organisationer, hvor du skal bruge det højeste niveau af sikkerhed og tilgængelighed til deres forbindelser i tværs lokale typisk brug dedikeret WAN links til at oprette forbindelse til eksterne websteder. Azure giver dig mulighed for at bruge en dedikeret WAN-forbindelse, du kan bruge til at oprette forbindelse til dit lokale netværk til et virtuelt Azure-netværk. Dette er aktiveret via Azure ExpressRoute.

Lær mere:

- [ExpressRoute teknisk oversigt](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Oprette forbindelse Azure virtuelle netværk til hinanden
Det er muligt for dig at bruge mange Azure virtuelle netværk til dine installationer. Der er mange grunde, hvorfor du kan gøre dette. En af årsager kan være til at forenkle administration; en anden være af sikkerhedsmæssige årsager. Uanset motivation eller begrundelse for at placere ressourcer på forskellige Azure virtuelle netværk, kan der være tidspunkter, hvor du vil ressourcer på hver af netværkene til at oprette forbindelse til hinanden.

Der ville være en af mulighederne for tjenester på én Azure virtuelt netværk til at oprette forbindelse til tjenester i en anden Azure virtuelt netværk ved "løkker tilbage" via internettet. Forbindelsen skal starte i et virtuelt Azure-netværk, gå via internettet, og derefter vende tilbage til destinationen Azure virtuelt netværk. Denne indstilling Fremviser forbindelsen til de sikkerhedsproblemer, der er forbundet til en hvilken som helst internetbaserede kommunikation.

En bedre mulighed kunne være at oprette en Azure virtuelle VPN netværk til Azure virtuelle netværk til websted. Denne Azure virtuelle netværk til Azure virtuelle netværk til websted VPN bruger den samme [IPSec-tunneltilstand](https://technet.microsoft.com/library/cc786385.aspx) protokol som i tværs lokale-til-websted VPN-forbindelse, der er nævnt ovenfor.

Fordelen ved at bruge en Azure virtuelle netværk til Azure virtuelle netværk til websted VPN er der er oprettet VPN-forbindelse via Azure netværk strukturen; Det kan ikke oprette forbindelse via internettet. Dette giver dig et ekstra sikkerhedslag sammenlignet med websted til websted VPN, der har forbindelse via internettet.

Lær mere:

- [Konfigurere en VNet-VNet forbindelse ved hjælp af Azure ressourcestyring og PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Tilgængelighed
Tilgængelighed er en vigtig komponent i en hvilken som helst sikkerhedsprogram. Hvis dine brugere og systemer ikke kan få adgang til hvad de har brug at få adgang til via netværket, der kan betragtes tjenesten er blevet kompromitteret. Azure har netværk teknologier, der understøtter følgende høj tilgængelighed netværksadgang:

- HTTP-baseret justering af belastning
- Netværk niveau justering af belastning
- Global justering af belastning

Justering af belastning er en funktion, der er udviklet til at distribuere lige forbindelser mellem flere enheder. Mål belastning er:

- Større tilgængelighed –, når du indlæser balance forbindelser på tværs af flere enheder, en eller flere af enhederne, der kan ikke være tilgængelige, og de tjenester, der kører på de resterende online enheder kan fortsætte med at levere indhold fra tjenesten
- Øge ydeevnen – når du indlæser balance forbindelser på tværs af flere enheder, en enkelt enhed nødt ikke til at udføre processor forekomst. I stedet spredes behandling og hukommelse behov for fungerer indholdet på tværs af flere enheder.

### <a name="http-based-load-balancing"></a>HTTP-baseret justering af belastning
Organisationer, der kører webbaserede tjenester ofte ønsker at have en HTTP-baseret justering af belastning foran disse webtjenester for at sikre tilstrækkelig niveauer af ydeevne og høj tilgængelighed. I modsætning til traditionelle netværk-baserede Indlæs balancere er belastningsjustering beslutninger, som HTTP-baseret Indlæs balancere baseret på egenskaber på HTTP-protokollen, ikke på netværk og transport lagprotokoller.

For at give dig HTTP-baseret for dine webbaseret tjenester til justering af belastning, giver dig med Azure gatewayen Azure-program. Gatewayen Azure-program understøtter:

- HTTP-baseret justering af belastning – Indlæs justering af belastning beslutninger oprettes på basis egenskab speciel til HTTP-protokollen
- Cookie-baserede session forbindelse – denne egenskab sørger for, at forbindelser til en af serverne bag, belastningsjustering forbliver intakte mellem klienten og serveren. Denne måde sikres stabilitet transaktioner.
- SSL aflastning – når en klientforbindelse er oprettet med justering af belastning, session mellem klienten og justering af belastning er krypteret med HTTPS (SSL /) protocol. For at øge ydeevnen, har dog mulighed for at have forbindelse mellem justering af belastning og webserver bag Indlæs belastningsjusteringstjenesten Brug HTTP (ikke-krypteret)-protokollen. Dette kaldes som "SSL offload" fordi webserverne bag justering af belastning ikke oplevelse for processor meget tid med kryptering og derfor skal kunne serviceanmodninger hurtigere.
- URL-baserede distribution af indhold – denne funktion gør det muligt for justering af belastning tage beslutninger om på hvor du vil videresende forbindelser, der er baseret på URL-adressen. Dette giver meget mere fleksibel end løsninger, der gør indlæse justering af belastning beslutninger baseret på IP-adresser.

Lær mere:

- [Programmet Gateway oversigt](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Netværk niveau justering af belastning
I modsætning til HTTP-baseret belastning gør netværk niveau belastning Indlæs justering af belastning beslutninger baseret på IP-adresse og port (TCP eller UDP) tal.
Du kan få fordel af netværk niveau justering af belastning i Azure ved hjælp af Azure belastning. Nogle vigtigste karakteristika for den Azure justering af belastning omfatter:

- Netværk niveau belastning baseret på IP-adresse og port tal
- Understøttelse af enhver programmet lag protokol
- Indlæse saldi Azure virtuelle maskiner og skyen tjenester rolle forekomster
- Kan bruges til både internettet (eksterne belastning) og ikke-Internet modstående (interne belastning) programmer og virtuelle maskiner
- Slutpunkt overvågning, som bruges til at finde ud af, hvis nogen af tjenesterne bag justering af belastning er utilgængelige

Lær mere:

- [Internet modstående justering af belastning mellem flere forskellige virtuelle computere eller tjenester](../load-balancer/load-balancer-internet-overview.md)
- [Interne Indlæs belastningsjusteringstjenesten oversigt](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Global justering af belastning
Nogle organisationer vil det højeste niveau af tilgængelighed, der er muligt. En metode til at nå dette mål er at host programmer i globalt fordelt datacentre. Når et program er hostet i datacentre, der er placeret i hele verden, er det muligt for en hele geopolitiske område, der skal være tilgængelige og stadig er programmet at køre.

Ud over de tilgængelighed fordele, du får ved at placere programmer i globalt fordelt datacentre, kan også du få forbedret ydeevne. Disse ydeevnefordele kan hentes ved hjælp af en funktion, der omdirigerer anmodninger om tjenesten til det datacenter, der er tættest den enhed, der opretter anmodningen.

Global belastning kan give dig begge af disse fordele. I Azure, kan du få fordel af globale belastning ved hjælp af Azure trafik Manager.

Lær mere:

- [Hvad er trafik Manager?](../traffic-manager/traffic-manager-overview.md)

## <a name="logging"></a>Logføring
Logføring på et netværk niveau er en funktion, der er vigtige for en hvilken som helst netværk sikkerhed scenarie. I Azure, kan du logge oplysninger, der fås til netværk sikkerhedsgrupper til at få netværk niveau logføring oplysninger. Med NSG logføring får du oplysninger fra:

- Overvågningslogge – disse logfiler bruges til at få vist alle handlinger, der er sendt til dine Azure abonnementer. Disse logfiler er aktiveret som standard og kan bruges i portalen Azure.
- Hændelseslogfiler – disse logfiler indeholder oplysninger om hvilke NSG regler blev anvendt.
- Tæller logfiler – disse logfiler fortæller dig, hvor mange gange hver NSG regel blev anvendt for at nægte eller tillade trafik.

Du kan også bruge [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), et effektiv datavisualisering værktøj, til at få vist og analysere disse logfiler.

Lær mere:

- [Log analyser af netværk sikkerhedsgrupper (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)

## <a name="name-resolution"></a>Navneoversættelse
Navneoversættelse er en vigtig funktion for alle tjenester, du er vært for i Azure. Sammensat af funktionen navn opløsning kan føre til en hacker omdirigering af anmodninger fra websteder, til en hacker websted fra et sikkerhed perspektiv. Sikker navneoversættelse er et krav for alle dine tjenester uden skyen hostet.

Der findes to typer navneoversættelse, der skal håndteres:

- Interne navneoversættelse – interne navneoversættelse bruges af tjenester på din virtuelle Azure-netværk, dit lokale netværk eller begge dele. Navne, som bruges til interne navneoversættelse er ikke tilgængelige via internettet. Til optimal sikkerhed er det vigtigt, at din interne navn løsning farveskema ikke er tilgængelige for eksterne brugere.
- Eksterne navneoversættelse – eksterne navneoversættelse bruges af personer og enheder uden for din lokale og Azure virtuelle netværk. Dette er de navne, der er synlig på internettet og bruges til at dirigere forbindelse til din skybaserede tjenester.

Til interne navneoversættelse har du to muligheder:

- En Azure virtuelle netværk DNS-server – Når du opretter et nyt Azure virtuelt netværk, oprettes en DNS-server for dig. Denne DNS-server, kan du løse navnene på de maskiner, der er placeret på det pågældende Azure virtuelle netværk. Denne DNS-server kan ikke konfigureres og administreres af Azure-strukturen manager, og som derfor gør det en sikker navn opløsning løsning.
- Få dine egne DNS-server – du har mulighed for at placere en DNS-server efter eget valg på netværket Azure virtuelle. Denne DNS-server kan være en Active Directory-integrerede DNS-server eller et dedikeret DNS serverløsning, der leveres af en Azure-partner, som du kan få fra Azure Marketplace.

Lær mere:

- [Oversigt over virtuelt netværk](../virtual-network/virtual-networks-overview.md)
- [Administrere DNS-servere, der bruges af et virtuelt netværk (VNet)](../virtual-network/virtual-networks-manage-dns-in-vnet.md)

For eksterne DNS-opløsning har du to muligheder:

- Være vært for dine egne eksterne DNS-server i det lokale miljø
- Være vært for dine egne eksterne DNS-server med en tjenesteudbyder

Mange store virksomheder skal være vært for deres egne DNS-servere lokalt. De kan gøre dette, fordi de har netværk ekspertise og global tilstedeværelse til at gøre dette.

I de fleste tilfælde er det bedre at hoste din DNS-navneoversættelse til en udbyder. Disse tjenesteudbydere har netværk ekspertise og global tilstedeværelse til at sikre, at meget høj tilgængelige for din navneoversættelse. Tilgængelighed er vigtigt for DNS-tjenester, fordi Hvis din navneoversættelse mislykkes, ikke er nogen, vil kunne oprette forbindelse modstående services på internettet.

Azure giver dig en høj tilgængelighed og performant eksterne DNS-løsning i form af Azure DNS. Denne eksterne navn opløsning løsning udnytter i hele verden Azure DNS-infrastrukturen. Det gør det muligt at være vært for dit domæne i Azure ved hjælp af de samme legitimationsoplysninger, API'er, værktøjer og fakturering som dine andre Azure tjenester. Som en del af Azure arver det også den stærke sikkerhedskontrol, der er indbygget i platformen.

Lær mere:

- [Azure DNS-oversigt](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>DMZ arkitektur
Mange enterprise organisationer bruger netværksarkitekturen til at inddele deres netværk for at oprette en bufferen zone mellem internettet og deres tjenester. DMZ del af netværket betragtes som en lav sikkerhed zone og ingen værdiskabende Aktiver er placeret i dette netværkssegment. Typisk får du vist sikkerhed netværksenheder, som har en netværkskort på segmentet DMZ og en anden netværksgrænseflade, der er tilsluttet et netværk, der indeholder virtuelle computere og tjenester, der accepterer indgående forbindelser fra internettet.

Der findes en lang række variationer af DMZ design og beslutningen om at installere en DMZ, og klik derefter hvilken type DMZ skal bruges, hvis du beslutter dig for at bruge en, er baseret på dit netværk sikkerhedskravene.

Lær mere:

- [Microsoft Cloud Services og netværkssikkerhed](../best-practices-network-security.md)

## <a name="azure-security-center"></a>Azure Security Center
Sikkerhedscenter kan du forhindre, at registrere og besvare trusler og giver du større indsigt i, og kontrol over, sikkerheden for dine Azure ressourcer. Det giver integreret sikkerhed overvågning og politik for administration på tværs af dine abonnementer på Azure, hjælper med at registrere trusler, der ellers går sig og fungerer med et omfattende netværk af sikkerhedsløsninger.

Azure Sikkerhedscenter hjælper dig med at optimere og overvåge netværkssikkerhed ved at:

- Give netværk sikkerhedsanbefalinger
- Overvågning tilstanden for din sikkerhed netværkskonfiguration
- Advarer dig mod netværket baseret trusler, både på slutpunkt og netværk

Lær mere:

- [Introduktion til Azure Security Center](../security-center/security-center-intro.md)
