<properties
   pageTitle="Bedste fremgangsmåder for Azure netværk sikkerhed | Microsoft Azure"
   description="I denne artikel indeholder et sæt af bedste fremgangsmåder til netværk sikkerhed, bruge indbygget i Azure-funktioner."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor="TomShinder"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/25/2016"
   ms.author="TomSh"/>

# <a name="azure-network-security-best-practices"></a>Bedste fremgangsmåder for Azure netværk sikkerhed

Microsoft Azure gør det muligt at oprette forbindelse virtuelle maskiner og udstyr til andre enheder på netværket ved at placere dem på Azure virtuelle netværk. Et virtuelt Azure-netværket ikke er et virtuelt netværk konstruktion, der gør det muligt at oprette forbindelse virtuelle netværkskort til et virtuelt netværk til at tillade TCP/IP-baserede kommunikation mellem aktiveret netværksenheder. Azure virtuelle maskiner, der er tilsluttet et netværk med virtuelle Azure er i stand til at oprette forbindelse til enheder på det samme virtuelle netværk til Azure, forskellige Azure virtuelle netværk, på internettet eller endda på din egen lokale netværk.

I denne artikel beskriver vi en samling af bedste fremgangsmåder for Azure netværk sikkerhed. Disse bedste fremgangsmåder er afledt af vores oplevelse med Azure netværk og erfaringer fra kunder som dig selv.

For hver bedste fremgangsmåde gennemgår vi:

- Hvad er den bedste fremgangsmåde
- Hvorfor du vil aktivere den bedste fremgangsmåde
- Hvad kan ske, hvis du ikke aktiverer den bedste fremgangsmåde
- Mulige alternativer til den bedste fremgangsmåde
- Hvordan du kan lære at aktivere den bedste fremgangsmåde

I denne artikel Azure netværk Sikkerhed bedste fremgangsmåder er baseret på en enighed udtalelse og Azure-platformen funktioner og funktionssæt, som de findes på det tidspunkt, der er skrevet i denne artikel. Meninger og -teknologier ændrer sig med tiden og i denne artikel vil blive opdateret med jævne mellemrum at afspejle disse ændringer.

Azure netværk Sikkerhed bedste fremgangsmåder beskrives i denne artikel, omfatter:

- Logisk segmentet undernet
- Styre routing funktionsmåde
- Aktivere tvunget tunnelføring
- Bruge virtuelle netværk udstyr
- Installer netværksarkitekturen for sikkerhed inddeling
- Undgå visning på internettet med dedikeret WAN links
- Optimere oppetid og ydeevne
- Brug af globale belastning
- Deaktivere RDP adgang til Azure virtuelle maskiner
- Aktivér Azure Security Center
- Udvide dit datacenter til Azure


## <a name="logically-segment-subnets"></a>Logisk segmentet undernet

[Azure virtuelle netværk](https://azure.microsoft.com/documentation/services/virtual-network/) ligner en LAN på dit lokale netværk. Et virtuelt netværk med Azure formålet er, at du opretter et enkelt privat IP-adresse plads-baserede netværk, hvor du kan placere alle dine [virtuelle Azure-computere](https://azure.microsoft.com/services/virtual-machines/). Privat IP-adresse mellemrum tilgængelige er i klasse A (10.0.0.0/8), klasse B (172.16.0.0/12) og klasse C (192.168.0.0/16) områder.

Ligner hvad du har lokalt, skal du segment skal tildeles større adresseområde i undernet. Du kan bruge [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) baseret adressetildelinger principper til at oprette dit undernet.

Routing mellem undernet sker automatisk, og du behøver ikke at konfigurere manuelt routing-tabeller. Standardindstillingen er dog, der ikke er nogen adgang til kontrolelementer for netværk mellem de undernet, du opretter på Azure virtuelle netværket. For at oprette netværk adgang til kontrolelementer for mellem undernet, skal du placere noget mellem undernet.

En af de ting, du kan bruge til at udføre denne opgave er en [Sikkerhedsgruppe netværk](../virtual-network/virtual-networks-nsg.md) (NSG). NSGs er enkel med høj sikkerhed pakkeinspektionsenheder, der bruger 5-tuplen (den kilde-IP, kildeport, IP-destination, Destinationsport og lag 4 protocol) metode til at oprette Tillad/Afvis regler for netværkstrafik. Du kan tillade eller nægte trafik til og fra enkelt IP-adresse, til og fra flere IP-adresser eller endda til og fra hele undernet.

Ved hjælp af NSGs til netværksadgangskontrol mellem undernet gør det muligt at sætte ressourcer, der hører til den samme sikkerhedszone eller rolle i deres egen undernet. For eksempel Tænk på et enkelt lag 3-program, der indeholder et web trin, et program logik niveau og en databaseniveau. Du placerer virtuelle maskiner, der hører til hver af disse lag i deres egen undernet. Derefter skal du bruge NSGs til at styre trafik mellem undernet:

- Web niveau virtuelle computere kan kun igangsætte forbindelser til programmet logik-computere og kan kun acceptere forbindelser fra internettet
- Programmet logik virtuelle computere kan kun igangsætte forbindelser med databaseniveau og kan kun acceptere forbindelser fra web niveau
- Database niveau virtuelle computere kan ikke oprette forbindelse med noget uden for deres egne undernet og kan kun acceptere forbindelser fra programmet logik niveau

Hvis du vil vide mere om netværk sikkerhedsgrupper, og hvordan du kan bruge dem til at inddele logisk dine virtuelle Azure-netværk, skal du læse artiklen [Hvad er en sikkerhedsgruppe netværk](../virtual-network/virtual-networks-nsg.md) (NSG).

## <a name="control-routing-behavior"></a>Styre routing funktionsmåde

Når du placerer en virtuel maskine i et virtuelt Azure-netværk, vil du bemærke, at den virtuelle maskine kan oprette forbindelse til andre virtuel maskine på det samme Azure virtuelle netværk, selvom de andre virtuelle maskiner på forskellige undernet. Hvorfor det er muligt årsagen er, at der er en samling af system omdirigerer, der er aktiveret som standard, der tillader denne type kommunikation. Disse standard omdirigerer Tillad virtuelle maskiner på det samme Azure virtuelle netværk til at starte forbindelser med hinanden og med internettet (for udgående kommunikation til internettet kun).

Mens standard system omdirigerer er nyttige mange installationer, er der tidspunkter, hvor du vil tilpasse den konfigurationen af routing til dine installationer. Disse tilpasninger, så du kan konfigurere den næste hopadresse for at få specifikke destinationer.

Vi anbefaler, at du konfigurerer brugerdefineret omdirigerer, når du installerer et virtuelt netværk sikkerhed maskinen, som vi kommer ind i en nyere bedste fremgangsmåde.

> [AZURE.NOTE] bruger defineret omdirigerer er ikke påkrævet og standard system omdirigerer virker i de fleste tilfælde.

Du kan få mere at vide om brugerdefineret omdirigerer og hvordan du konfigurerer dem ved at læse artiklen [Hvad er brugerdefineret omdirigerer og IP-videresendelse](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Aktivere tvunget tunnelføring

For bedre at forstå tvunget tunnelføring, er det nyttigt at forstå, hvilke "opdelte tunnelføring" er.
De mest almindelige eksempel på opdelte tunnelføring kan ses med VPN-forbindelser. Forestil dig, at du opretter en VPN-forbindelse fra din hotelværelse til virksomhedens netværk. Denne forbindelse gør det muligt at oprette forbindelse til ressourcer på virksomhedens netværk og al kommunikation til ressourcer på dit firmanetværk gennemfører VPN-tunnel.

Hvad sker der, når du vil oprette forbindelse til ressourcer på internettet? Når opdelt tunnelføring er aktiveret, gå forbindelserne direkte til internettet og ikke via VPN-tunnel. Nogle sikkerhedseksperter Overvej dette skal være en potentiel risiko og derfor anbefaler at opdelt tunnelføring deaktiveres og alle forbindelser, bestemt til internettet og bestemt til virksomhedsressourcer, går igennem VPN-tunnel. Fordelen ved at gøre dette er, at forbindelser til internettet gennemtvinges derefter gennem virksomhedens netværk sikkerhedsenheder, der ville være sag, hvis VPN-klienten forbindelse til internettet uden for VPN-tunnel.

Nu Lad os tage dette tilbage til virtuelle maskiner i et virtuelt Azure-netværk. De standard forskellige muligheder for et virtuelt netværk med Azure Tillad virtuelle computere at starte trafik til internettet. Dette kan også repræsentere en sikkerhedsrisiko, som disse udgående forbindelser, der kan øge angrebsoverfladen af en virtuel maskine og udnyttes af hackere.
Derfor anbefaler vi, at du aktiverer tvunget tunnelføring på virtuelle maskiner, når du har tværs lokale forbindelse mellem din virtuelle Azure-netværk og dit lokale netværk. Vi vil tale om på tværs af lokale connectivity senere i dette Azure netværk bedste fremgangsmåder dokument.

Hvis du ikke har en forbindelse i tværs det lokale miljø, skal du kontrollere du drage fordel af netværk sikkerhedsgrupper (beskrevet tidligere) eller Azure virtuelle netværk sikkerhedsfunktioner (behandles næste) for at forhindre udgående forbindelser til internettet fra din virtuelle Azure-computere.

For at få mere at vide om tvunget tunnelføring og hvordan du aktiverer den, læse skal du artiklen [konfigurere tvunget tunnelføring ved hjælp af PowerShell og Azure ressourcestyring](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Brug virtuelt netværk udstyr

Mens netværk sikkerhedsgrupper og defineret Routing bruger kan angive et bestemt mål for netværkssikkerhed på netværk og transport lag [OSI-modellen](https://en.wikipedia.org/wiki/OSI_model), vil der være situationer, hvor du vil vil eller har brug for at aktivere sikkerhed på højt niveau af stablen. I disse situationer anbefaler vi, at du installerer virtuelt netværk sikkerhedsfunktioner, der leveres af Azure partnere.

Azure netværk sikkerhedsfunktioner kan levere betydeligt forbedret sikkerhedsniveauer i forhold til hvad leveres af netværk niveau kontrolelementer. Nogle af de netværk sikkerhedsfunktioner, der leveres af virtuelt netværk sikkerhedsfunktioner omfatter:

- Firewalling
- Uautoriseret adgang registrering/uautoriseret adgang forebyggelse
- Administration af sikkerhedsrisiko
- Programmet kontrolelement
- Netværk-baserede anomali registrering
- Web filtrering
- Antivirus
- Botnet beskyttelse

Hvis du kræver et højere niveau af netværkssikkerhed, end du kan få med niveau adgang til kontrolelementer for netværket, derefter anbefaler vi, at du undersøge og installere Azure virtuelt netværk sikkerhedsfunktioner.

Få at vide om, hvilke Azure virtuelt netværk sikkerhedsfunktioner er tilgængelige, og om deres funktioner, skal du gå til den [Azure Marketplace](https://azure.microsoft.com/marketplace/) og søge efter "sikkerhed" og "netværkssikkerhed".

##<a name="deploy-dmzs-for-security-zoning"></a>Installer netværksarkitekturen for sikkerhed inddeling
En DMZ eller "afskærmede netværk" er en fysisk eller logisk netværk målgruppe, som er udviklet til at give et ekstra sikkerhedslag mellem dine aktiver og internettet. Formålet med DMZ er at placere specielle access kontrolelement netværksenheder på kanten af DMZ-netværket, så kun ønskede trafik er tilladt tidligere IP-adresse, sikkerhed og ind i dit Azure virtuelle netværk.

Netværksarkitekturen er nyttige, fordi du kan fokusere din netværk administrationen af adgangskontrol, overvågning, logføring og rapportering på enheder på kanten af netværket Azure virtuelle. Her skal du typisk aktivere DDoS forebyggelse, beskyttelse mod uautoriseret adgang registrering/uautoriseret adgang systemer (ID'ER/IP-Adresser), firewallregler og politikker, web filtrering, netværk Antimalwareprogram og meget mere. Sikkerhed netværksenheder sidde mellem internettet og netværket Azure virtuelle og har en grænseflade på begge netværk.

Dette er den grundlæggende udformning af en DMZ, men der er mange forskellige DMZ-design, såsom sideløbende, hører til tre, hører til flere, og andre.

Vi anbefaler til alle høj sikkerhed installationer, at du overvejer at implementere en DMZ for at forbedre sikkerhedsniveauet netværk for dine Azure ressourcer.

Hvis du vil vide mere om netværksarkitekturen, og hvordan du installerer dem i Azure, skal du læse artiklen [Microsofts Skytjenester og netværkssikkerhed](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Undgå visning på internettet med dedikeret WAN links
Mange organisationer har valgt Hybrid IT ruten. I hybride IT, er nogle af virksomhedens oplysninger Aktiver er i Azure, mens andre forbliver i det lokale miljø. I mange tilfælde vil du kører visse komponenter i en tjeneste i Azure mens andre komponenter forbliver i det lokale miljø.

Hybrid IT scenarie er der ofte i en type af tværs lokale connectivity. Dette tværs lokale connectivity kan virksomhed tilsluttes deres lokale netværk til Azure virtuelle netværk. Der er to tværs lokale connectivity løsninger:

- VPN til-websted
- ExpressRoute

[Websted til websted VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) repræsenterer en virtuel privat forbindelse mellem dit lokale netværk og et virtuelt Azure-netværk. Denne forbindelse foregår via internettet og giver dig mulighed for at "tunnel" oplysningerne inden i en krypteret forbindelse mellem dit netværk og Azure. Websted til websted VPN er en sikker, fuldt teknologi, der er installeret ved virksomheder i alle størrelser i mange år. Tunnel kryptering udføres ved hjælp af [IPSec-tunneltilstand](https://technet.microsoft.com/library/cc786385.aspx).

Mens VPN til-websted er en teknologi, der er tillid til, pålidelig og eksisterende, trafik inden for tunnelen sker via internettet. Desuden er båndbredde relativt begrænset til maksimalt om 200Mbps.

Hvis du kræver en særlige niveau af sikkerheden eller ydeevnen for dine forbindelser i tværs det lokale miljø, anbefaler vi, at du bruger Azure ExpressRoute til tværs lokale forbindelsen. ExpressRoute er et dedikeret WAN forbindelse mellem din lokale placering eller en Exchange-udbyder. Da dette er en telco forbindelse, dine data overføres ikke via internettet, og derfor vises ikke på de potentielle risici Internetkommunikation.

Hvis du vil vide mere om hvordan Azure ExpressRoute fungerer, og hvordan du installerer, skal du læse artiklen [ExpressRoute teknisk oversigt](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Optimere oppetid og ydeevne
Omfatter Triade af dags mest indflydelse sikkerhedsmodel, fortrolighed, integritet og tilgængelighed (CIA). Fortrolighed er om kryptering og beskyttelse af personlige oplysninger, integritet handler om at sikre dig, at dataene ikke ændres af uautoriserede personer tilgængelighed handler om at sikre, at der kan få adgang til de oplysninger, de har tilladelse til at få adgang til autoriserede personer. Fejl i en af disse områder repræsenterer potentielle brud på sikkerhed.

Tilgængelighed kan betragtes som værende om oppetid og ydeevne. Hvis en tjeneste er nede, kan ikke åbnes oplysninger. Hvis ydeevnen er så dårlig som for at gøre dataene ubrugelig, kan vi overveje dataene, der ikke er tilgængelig. Derfor fra et sikkerhed perspektiv skal vi gøre hvad vi kan for at sikre at vores tjenester har optimal oppetid og ydeevne.
En populære og effektiv metode, der bruges til at forbedre tilgængelighed og ydeevne er at bruge belastning. Justering af belastning er en metode til at distribuere netværkstrafik på tværs af servere, der er en del af en tjeneste. Eksempelvis hvis du har front end-servere som en del af din tjeneste, kan du bruge belastning for at distribuere trafikken på tværs af din flere front end-servere.

Denne fordelingen af trafikken øges tilgængelighed, fordi Hvis en-webserverne bliver tilgængelig, justering af belastning vil ophøre med at sende trafik til den pågældende server og omdirigere trafikken til servere, der stadig er online. Justering af belastning også, at ydeevne, fordi den processor, netværk og hukommelse omkostninger for behandlingen af anmodninger distribueres på tværs af alle afkrydsningsfeltet Indlæs afstemmes servere.

Vi anbefaler, at du anvender belastning, hver gang du kan, og efter behov for dine tjenester. Vi vil løse hensigtsmæssige i de følgende afsnit.
Azure leverer du med tre primære indlæse indstillinger for justering af belastning på niveauet Azure virtuelt netværk:

- HTTP-baseret justering af belastning
- Eksterne justering af belastning
- Interne justering af belastning

## <a name="http-based-load-balancing"></a>HTTP-baseret justering af belastning
HTTP-baseret belastning baserer beslutninger om hvilke server til at sende forbindelser ved hjælp af egenskaber på HTTP-protokollen. Azure har en HTTP justering af belastning, der løber af navnet på programmet Gateway.

Vi anbefaler, at du os Azure Application Gateway når:

- Programmer, der kræver anmodninger fra den samme bruger/klient-session når den samme back end-virtuelle maskine. Eksempler på dette ville indkøb indkøbskurv apps og web mailservere.
- Programmer, der vil frigive webserver-farme fra SSL opsigelse før omkostninger ved at drage fordel af Application Gateway [SSL offload](https://f5.com/glossary/ssl-offloading) funktion.
- Programmer, som et netværk til levering af indhold, som kræver flere HTTP-anmodninger på samme længerevarende TCP-forbindelsen til dirigeres eller indlæse afstemmes til forskellige back end-servere.

Hvis du vil vide mere om, hvordan Azure Application Gateway fungerer, og hvordan du kan bruge det i dine installationer, skal du læse artiklen [Programmet Gateway oversigt](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Eksterne justering af belastning
Eksterne belastning skal udføres, når indgående forbindelser fra internettet er Indlæs fordeles blandt dine servere, der er placeret i et virtuelt Azure-netværk. Azure eksterne belastning kan give dig denne egenskab, og vi anbefaler, at du bruger den, når du ikke har brug for de sticky sessioner eller SSL offload.

I modsætning til HTTP-baseret belastning bruger den eksterne belastning oplysninger på netværk og transport lag af OSI netværk modellen tage beslutninger om på hvilken serveren for at indlæse balance forbindelse til.

Vi anbefaler, at du bruger eksterne belastning, når du har [uden status programmer](http://whatis.techtarget.com/definition/stateless-app) accepterer indgående anmodninger fra internettet.

Hvis du vil vide mere om, hvordan den Azure eksterne justering af belastning fungerer, og hvordan du kan installere det, skal du læser artiklen [Introduktion oprettelse af en Internet modstående justering af belastning i ressourcestyring ved hjælp af PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Interne justering af belastning
Interne belastning svarer til eksterne belastning og bruger den samme metode til at indlæse balance forbindelser til servere bagved. Den eneste forskel er, at justering af belastning i dette tilfælde er acceptere forbindelser fra virtuelle maskiner, der ikke er på internettet. I de fleste tilfælde, er de forbindelser, der er accepteret til justering af belastning startet af enheder i et virtuelt Azure-netværk.

Vi anbefaler, at du bruger interne belastningsjustering for scenarier, der kan få glæde af denne egenskab, som når du har brug at indlæse balance forbindelser til SQL-servere eller interne web-servere.

Hvis du vil vide mere om hvordan Azure interne belastning fungerer, og hvordan du kan installere det, skal du læse artiklen [Introduktion oprettelse af en intern justering af belastning ved hjælp af PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## <a name="use-global-load-balancing"></a>Brug af globale belastning
Offentlige cloud computing gør det muligt at installere globalt fordelt programmer, der har komponenter, der er placeret i datacentre over hele verden. Dette er muligt på Microsoft Azure på grund af Azures globale datacenter tilstedeværelse. I modsætning til teknologier, der er nævnt tidligere til belastningsjustering, gør global justering af belastning det muligt at foretage tjenester tilgængelige, selvom hele datacentre kan blive utilgængelige.

Du kan åbne denne type globale belastning i Azure ved at drage fordel af [Azure trafik Manager](https://azure.microsoft.com/documentation/services/traffic-manager/). Trafik Manager gør er muligt at indlæse balance forbindelser til dine tjenester, der er baseret på placeringen af brugeren.

Eksempelvis hvis brugeren foretager en anmodning om din tjeneste fra EU, dirigeres forbindelsen til dine tjenester, der er placeret i et EU-datacenter. Denne del af trafik Manager globale indlæse justering af belastning hjælper med at forbedre ydeevnen, fordi oprette forbindelse til det nærmeste datacenter er hurtigere end at oprette forbindelse til datacentre, der er langt væk.

På siden tilgængelighed sikrer, at, at din tjeneste er tilgængelig, selvom en hel datacenter skal være tilgængelige globale belastning.

Eksempelvis hvis en Azure datacenter skal bliver er ikke tilgængelig på grund af påvirkning årsager eller på grund af udfald (såsom internationale netværksfejl), forbindelser til din tjeneste vil blive omdirigeret til den nærmeste online datacenter. Denne globale belastning opnås ved at drage fordel af DNS-politikker, som du kan oprette i trafik Manager.

Vi anbefaler, at du bruger trafik Manager for en hvilken som helst cloud-løsning du udvikler, der indeholder et bredt fordelt omfang på tværs af flere områder og kræver det højeste niveau af oppetid mulige.

Hvis du vil vide mere om Azure trafik Manager, og hvordan du installerer det, skal du læse artiklen [Hvad er trafik Manager](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Deaktivere RDP/SSH adgang til Azure virtuelle maskiner
Det er muligt at nå virtuelle Azure-computere ved hjælp af [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) og [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH) protokoller. Disse protokoller gør det muligt at administrere virtuelle maskiner fra eksterne placeringer og er standard i datacenter computing.

De potentielle sikkerhedsproblem med brug af disse protokoller via internettet er hackere kan anvende forskellige [råstyrke](https://en.wikipedia.org/wiki/Brute-force_attack) teknikker til at få adgang til virtuelle Azure-computere. Når opfanger access, kan de bruge din virtuelle maskine som et startpunkt for bringe andre computere på netværket Azure virtuelle eller endda angreb netværksenheder uden for Azure.

På grund af dette anbefaler vi, at du deaktiverer direkte RDP og SSH adgang til din virtuelle Azure-computere fra internettet. Når direkte RDP og SSH adgang fra internettet er deaktiveret, har du andre indstillinger, du kan bruge til at få adgang til disse virtuelle maskiner til fjernadministration:

- Punkt-til-site VPN
- VPN til-websted
- ExpressRoute

[Punkt-til-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) er et andet udtryk for en fjernadgang til VPN-klient/server forbindelse. Et punkt-til-site VPN gør det muligt for en enkelt bruger til at oprette forbindelse til et virtuelt Azure-netværk via internettet. Når punkt-til-site forbindelsen er oprettet, bliver brugeren kan bruge RDP eller SSH til at oprette forbindelse til en hvilken som helst virtuelle maskiner, der er placeret på det virtuelle Azure-netværk, brugeren, der er forbindelse via VPN punkt-til-websted. Dette forudsætter, at brugeren har tilladelse til at nå disse virtuelle computere.

Punkt-til-site VPN er mere sikker end direkte RDP eller SSH forbindelser, fordi brugeren ikke har til at godkende to gange før der oprettes forbindelse til en virtuel maskine. Først skal brugeren skal godkende (og godkendes) til at oprette punkt-til-site VPN-forbindelse; andet, som brugeren skal godkende (og godkendes) til at etablere RDP eller SSH session.

En [-til-websted VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) forbinder en hele netværket til et andet netværk via internettet. Du kan bruge en VPN til-websted til at oprette forbindelse til dit lokale netværk til et virtuelt Azure-netværk. Hvis du installerer et websted til websted VPN, brugere på dit lokale netværk vil kunne oprette forbindelse til virtuelle maskiner på netværket Azure virtuelle ved hjælp af RDP eller SSH protokollen over til websted VPN-forbindelse og kræver ikke at tillade direkte RDP eller SSH adgang via internettet.

Du kan også bruge en dedikeret WAN-forbindelse til at levere funktionalitet, der ligner den VPN til-websted. De vigtigste forskelle er 1. dedikeret WAN-forbindelsen gennemgang ikke af internettet, og 2. dedikeret WAN links er typisk mere stabil og performant. Azure giver dig en dedikeret WAN-forbindelsen løsning i form af [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Aktivér Azure Security Center
Azure Sikkerhedscenter kan du forhindre, at registrere og besvare trusler og giver du større indsigt i, og kontrol over, sikkerheden for dine Azure ressourcer. Det giver integreret sikkerhed overvågning og politik for administration på tværs af dine abonnementer på Azure, hjælper med at registrere trusler, der ellers går sig og fungerer med et omfattende netværk af sikkerhedsløsninger.

Azure Sikkerhedscenter hjælper dig med at optimere og overvåge netværkssikkerhed ved at:

- Give netværk sikkerhedsanbefalinger
- Overvågning tilstanden for din sikkerhed netværkskonfiguration
- Advarer dig mod netværket baseret trusler, både på slutpunkt og netværk

Vi anbefaler, at du aktiverer Azure Sikkerhedscenter for alle dine Azure installationer.

Hvis du vil vide mere om Azure Sikkerhedscenter, og hvordan du aktivere den til dine installationer, skal du læse i artiklen [Introduktion til Azure Sikkerhedscenter](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Udvide sikkert dit datacenter til Azure
Mange enterprise IT organisationer leder for at udvide i skyen i stedet for voksende deres lokale datacentre. Denne udvidelse repræsenterer en udvidelse af eksisterende IT-infrastruktur i offentlige skyen. Det er muligt at behandle dine Azure virtuelle netværk som en anden lige undernet på din lokale netværksinfrastruktur ved at drage fordel af tværs lokale indstillinger for forbindelse.

Der er dog en masse planlægnings- og problemer, der skal løses først. Dette er især vigtigt i området af netværkssikkerhed. En af de bedste måder at forstå, hvordan du løse disse et design er at se et eksempel.

Microsoft har oprettet [Datacenter lokalnummer Reference arkitektur Diagram](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) og understøttende sikkerhed for at hjælpe dig med at forstå, hvordan disse filtypenavnet datacenter vil se ud. Dette giver en eksempel referenceimplementering, som du kan bruge til at planlægge og designe filtypenavnet sikker enterprise datacenter til skyen. Vi anbefaler, at du gennemgår dette dokument for at få en ide om de vigtigste komponenter i en sikker løsning.

Hvis du vil vide mere om, hvordan du sikkert udvide dit datacenter til Azure, skal du se video [Forlænge Datacenter til Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
