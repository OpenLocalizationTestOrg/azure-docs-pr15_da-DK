<properties
   pageTitle="Microsoft cloud services og netværk sikkerhed | Microsoft Azure"
   description="Lær nogle af de vigtigste funktioner, der er tilgængelige i Azure for at oprette sikker netværksmiljøer"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jonor;sivae"/>

# <a name="microsoft-cloud-services-and-network-security"></a>Microsoft cloud services og netværk sikkerhed

Microsofts skytjenester levere store services og infrastruktur, professionel og-funktioner og mange muligheder for hybride connectivity. Kunder kan vælge at få adgang til disse tjenester via internettet eller med Azure ExpressRoute, som indeholder privat netværksforbindelse. Microsoft Azure-platform gør det muligt at udvide deres infrastruktur i skyen og oprette modulopdelt arkitekturer problemfrit. Desuden kan tredjeparter aktivere forbedrede funktioner ved at tilbyde sikkerhedstjenester og virtuelle udstyr. Denne hvidbog indeholder en oversigt over arkitektonisk problemer, kunder, bør overveje, når du bruger Microsofts skytjenester adgang via ExpressRoute og sikkerhed. Det dækker også oprette mere sikker tjenester i Azure virtuelle netværk.

## <a name="fast-start"></a>Hurtig start
Følgende logik diagrammet kan føre dig til et specifikt eksempel på mange sikkerhed teknikker tilgængelige med Azure-platformen. Find det eksempel, der bedst passer til din sag for hurtig reference. Finde mere komplet beskrivelser, fortsætte med at læse gennem papiret.
![Sikkerhed indstillinger rutediagram][0]

[Eksempel 1: Oprette et afskærmede netværk (også kaldet DMZ og perimeternetværk) for at beskytte programmer med netværk sikkerhedsgrupper (NSGs).](#example-1-build-a-simple-dmz-with-nsgs)</br>
[Eksempel 2: Oprette en afskærmede netværk for at beskytte programmer med en firewall og NSGs.](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br>
[Eksempel 3: Oprette en afskærmede netværk for at beskytte netværk med en firewall, brugerdefinerede rute (UDR) og NSG.](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br>
[Eksempel 4: Tilføje en hybrid forbindelse til et websted til websted, virtuelt maskinen virtuelt privat netværk (VPN).](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Eksempel 5: Føj en hybrid forbindelse til en-til-websted, Azure gateway VPN.](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br>
[Eksempel 6: Føje en hybrid forbindelse med ExpressRoute.](#example-6-adding-a-hybrid-connection-with-expressroute)</br>
Eksempler for at tilføje forbindelser mellem virtuelle netværk, høj tilgængelighed og sammenkædning af tjenesten, føjes til dette dokument for de næste par måneder.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Microsoft-regulativ og infrastruktur beskyttelse
Microsoft har taget en førerskab placering, der understøtter overholdelse initiativer kræves af enterprise-kunder. Følgende er nogle af overholdelse certificering til Azure: ![Azure overholdelse badges][1]

Yderligere oplysninger finder du oplysninger om overholdelse i [Microsoft Center for sikkerhed og rettighedsadministration](https://azure.microsoft.com/support/trust-center/compliance/).

Microsoft har en omfattende metode til at beskytte skyinfrastruktur, der kræves for at køre store globale services. Microsoft cloud-infrastruktur omfatter hardware, software, netværk, og administrative og handlinger medarbejdere, ud over den fysiske datacentre.

![Azure sikkerhedsfunktioner][2]

Denne fremgangsmåde giver et mere sikkert fundament for til at installere deres tjenester i Microsoft cloud-kunder. Næste trin gælder for kunder til at designe og oprette en sikkerhedsarkitekturen for at beskytte disse tjenester.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Traditionelle sikkerhed arkitekturer og afskærmede netværk
Selvom Microsoft investerer stærkt i beskytte-skyinfrastruktur, skal kunder også beskytte deres skytjenester og grupper. En flere lag tilgang til sikkerhed giver de bedste forsvarslinjer. En afskærmede netværk sikkerhedszone beskytter interne netværksressourcer fra en upålidelige netværk. En afskærmede netværk refererer til kanter eller dele af netværket, sidde mellem internettet og beskyttet virksomheds IT-infrastruktur.

I typisk virksomhedsnetværk, er core infrastrukturen stærkt tilsat grænser med flere lag af sikkerhedsenheder. Grænsen for hvert lag, består af enheder og politikhåndhævelse punkter. Enheder kan omfatte følgende: firewalls, distribueret nægtelse af Service (DDoS) forebyggelse, registrering af uautoriseret adgang eller beskyttelsessystemer (ID'ER/IP'ER) og VPN-enheder. Politik for aktivering kan tage form af firewall politikker, adgangskontrollister (ACLs) eller bestemt rute. Den første linje i forsvarslinjer i netværket, direkte acceptere indgående trafik fra internettet, er en kombination af disse mekanismer til at blokere angreb og skadelige trafik samtidig gyldige anmodninger yderligere til netværket. Denne trafik dirigerer direkte til ressourcer i det afskærmede netværk. Denne ressource kan derefter "tale" til ressourcer dybere i det netværk skal anbringes i næste rammen til validering første. Det yderste lag kaldes det afskærmede netværk, fordi denne del af netværket er være tilgængelig på internettet, som regel med andre former for beskyttelse på begge sider. I følgende figur vises et eksempel på et enkelt undernet afskærmede netværk på en virksomheds netværk med to sikkerhedsgrænser.

![En afskærmede netværk på en virksomheds netværk][3]

Der findes mange arkitekturer, der bruges til at implementere et afskærmede netværk, fra en simpel belastningsjustering foran en webfarm til et flere undernet afskærmede netværk med varieret mekanismer på hver rammen at blokere trafik og beskytte dybere lagene i virksomhedens netværk. Hvordan det afskærmede netværk er oprettet, afhænger af organisationen og dens overordnede risikoen tolerance særlige behov.

Som kunder flytter deres arbejdsbelastninger til offentlige skyer, er det vigtigt at understøtte lignende funktioner for afskærmede netværksarkitektur i Azure til at opfylde krav til sikkerhed og overholdelse af angivne standarder. Dette dokument indeholder retningslinjer på hvordan kunder kan opbygge et sikkert netværksmiljø i Azure. Den fokuserer på det afskærmede netværk, men indeholder også en fyldestgørende beskrivelse af de mange aspekter af netværkssikkerhed. Følgende spørgsmål underrette denne diskussion:

- Hvordan kan et afskærmede netværk i Azure bygget?
- Hvad er nogle af de Azure funktioner, der er tilgængelige til at oprette det afskærmede netværk?
- Hvordan kan back end-arbejdsbelastninger beskyttet?
- Hvordan styres Internetkommunikation til arbejdsbelastninger i Azure?
- Hvordan kan de lokale netværk beskyttet fra installationer i Azure?
- Hvornår skal oprindelige Azure sikkerhedsfunktioner bruges kontra fra tredjepart anvendelser eller tjenester?

I det følgende diagram viser forskellige sikkerhedslag Azure leverer til kunder. Lagene er både oprindelig i Azure-platformen selve og brugerdefinerede funktioner:

![Azure sikkerhedsarkitekturen][4]

Indgående fra internettet, Azure DDoS hjælper med at beskytte mod store angreb mod Azure. Det næste lag er kunden har defineret offentlige slutpunkter, der bruges til at bestemme, hvilken trafik kan overføre gennem skytjenesten til det virtuelle netværk. Oprindelig Azure virtuelle netværkets isolation sikrer fuldført isolation fra alle andre netværk, og at trafik kun bevæger sig gennem brugeren er konfigureret kurver og metoder. Disse stier og metoder er det næste lag, hvor NSGs, UDR og netværk virtuelle anvendelser kan bruges til at oprette sikkerhedsgrænser for at beskytte programmet installationer i det beskyttede netværk.

Næste afsnit indeholder en oversigt over Azure virtuelle netværk. Disse virtuelle netværk er oprettet af kunder og er, hvad deres udløst arbejdsbelastninger har forbindelse til. Virtuelt Network er grundlaget for alle de netværk sikkerhedsfunktioner kræves for at oprette en afskærmede netværk for at beskytte kunde installationer i Azure.

## <a name="overview-of-azure-virtual-networks"></a>Oversigt over Azure virtuelle netværk
Før internettrafik kan få adgang til de Azure virtuelle netværk, er der to sikkerhedslag naturlige til Azure-platformen:

1.  **DDoS beskyttelse**: DDoS beskyttelse er et lag det Azure fysiske netværk, der beskytter Azure-platformen selve fra store internetbaserede angreb. Disse angreb bruge flere "bot" noder i et forsøg på at overvælde en Internet-tjeneste. Azure har en robust DDoS beskyttelse maske på alle indgående forbindelse til internettet. Dette DDoS beskyttelse lag indeholder ingen bruger konfigurerbar attributter og er ikke tilgængelig til kunde. Dette beskytter Azure som en platform fra store angreb, men beskytter ikke direkte individuelle customer-program. Flere lag af spændstighed kan konfigureres af kunden for en oversatte angreb. Eksempelvis hvis kunde A blev angreb med et omfattende DDoS angreb på et offentligt slutpunkt, blokerer Azure forbindelser i den pågældende tjeneste. Kunde A kan skifte til en anden virtuelt netværk eller en tjeneste slutpunkt ikke tilknyttet angreb at gendanne tjenesten. Bemærk, at selvom kunde A kan være påvirket på pågældende slutpunkt, ingen andre tjenester uden for pågældende slutpunkt berøres. Desuden vises andre kunder og -tjenester uden at påvirke fra angrebet.
2.  **Tjenesten slutpunkter**: slutpunkter Tillad cloud services eller ressource grupper, der skal have offentlige IP-adresser Internet og åbne porte. Slutpunktet anvender oversættelse (NAT) til at omdirigere trafikken til den interne adresse og port i Azure virtuelt netværk. Dette er den primære stien til eksterne trafik til at videregive til det virtuelle netværk. Tjenesten slutpunkterne er brugeren kan konfigureres til at afgøre, hvilken trafik der overføres i, og hvor og hvordan det oversættes til på det virtuelle netværk.

Når trafik når det virtuelle netværk, er der mange funktioner, der kommer ind i Afspil. Azure virtuelle netværk er grundlaget for kunder vedhæfte deres arbejdsmængder og anvendelse af grundlæggende netværk-sikkerhed på brugerniveau. Det er et privat netværk (et virtuelt netværk overlejring) i Azure for kunder med følgende funktioner og egenskaber:
 
- **Trafik isolationsniveauet**: et virtuelt netværk er grænsen for isolationsniveauet på Azure-platformen. Virtuelle maskiner (VM'er) i et virtuelt netværk kan ikke kommunikere direkte til FOS i et andet virtuelle netværk, selvom begge virtuelle netværk er oprettet i den samme kunde. Dette er en vigtige egenskab, der sikrer kunde FOS og kommunikation forbliver privat i et virtuelt netværk.
- **Multitier topologi**: virtuelle netværk tillader kunder til at definere multitier topologi ved tildeling af undernet og tildele separate adresseområder for forskellige elementer eller "lag" af deres arbejdsmængder. Disse logiske grupperinger og topologier aktivere kunder til at definere forskellige access-politik baseret på de arbejdsbelastningen, og også kontrollere trafik flyder mellem lag.
- **Tværs lokale connectivity**: kunder kan opnå tværs lokale forbindelse mellem et virtuelt netværk og flere lokale websteder eller andre virtuelle netværk i Azure. For at gøre dette, kan kunder bruge Azure VPN Gateways eller tredjeparts netværk virtuelle udstyr. Azure understøtter-til-websted (S2S) VPN'er ved hjælp af standard IPsec/IKE protokoller og ExpressRoute privat forbindelse.
- **NSG** giver mulighed for kunder til at oprette regler (ACLs) på det ønskede niveau af granularitet: netværk grænseflader, individuelle FOS eller virtuelle undernet. Kunder kan kontrollere adgangen ved at tillade eller nægte kommunikation mellem arbejdsmængder i et virtuelt netværk og fra systemer på kundens netværk via tværs lokale connectivity, eller direkte Internetkommunikation.
- **UDR** og **IP-videresendelse** vores kunder kan definere Kommunikationsstier mellem forskellige niveauer inden for et virtuelt netværk. Kunder kan installere en firewall, ID'ER/IP-Adresser og andre virtuelle anvendelser og distribuerer netværkstrafik via disse sikkerhedsfunktioner til sikkerhed rammen politikhåndhævelse, overvågning og inspektion.
- **Netværk virtuelle anvendelser** i Azure Marketplace: sikkerhedsfunktioner som firewalls, Indlæs balancere og ID'ER/IP-Adresser er tilgængelige i Azure Marketplace og galleriet VM billede. Du kan implementere disse anvendelser i deres virtuelle netværk og specielt, på deres sikkerhedsgrænser (herunder afskærmede netværksundernet) for at fuldføre en modulopdelte sikkert netværk-miljø.

Med disse funktioner og egenskaber er et eksempel på hvordan kunne være opbygget en afskærmede netværksarkitektur i Azure følgende:

![Et afskærmede netværk i en Azure virtuelt netværk][5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Afskærmede Netværksegenskaber og krav
Det afskærmede netværk er udviklet til at være front-end i netværket, direkte grænseflade kommunikation fra internettet. De indgående pakker skal passerer gennem sikkerhedsfunktioner, som firewall, ID'ER og IP-Adresser, før at kontakte back-end-servere. Internet-bundne pakker fra arbejdsmængder kan også bevæge sig gennem sikkerhedsfunktioner i det afskærmede netværk for politikhåndhævelse, kvalitetskontrol og overvågning, inden du forlader netværket. Det afskærmede netværk kan desuden være vært for tværs lokale VPN gateways mellem kunde virtuelle netværk og lokale netværk.

### <a name="perimeter-network-characteristics"></a>Afskærmede Netværksegenskaber
Refererer til den foregående figur, er nogle af egenskaberne for en god afskærmede netværk som følger:

- Internettet:
    - Det afskærmede netværksundernet selve er forbindelse til internettet, direkte kommunikation med internettet.
    - Offentlige IP-adresser, VIPs og/eller tjeneste slutpunkter overføre internettrafik til front end Netværks- og enheder.
    - Indgående trafik fra internettet passerer gennem sikkerhedsenheder før andre ressourcer på front end-netværket.
    - Hvis udgående sikkerhed er aktiveret, går trafik gennem sikkerhedsenheder, som det sidste trin, før du går til internettet.
- Beskyttet netværk:
    - Der er ingen direkte sti fra internettet til core infrastruktur.
    - Kanaler til core infrastruktur skal gennemgå gennem sikkerhedsenheder som NSGs, firewalls eller VPN-enheder.
    - Andre enheder skal ikke bygge bro mellem Internet- og central-infrastruktur.
    - Sikkerhedsenheder på både på internettet og beskyttet netværket modstående grænserne for det afskærmede netværk (for eksempel to firewall ikonerne vises i den foregående figur) kan faktisk være en enkelt virtuelle maskinen med opdelte regler eller grænseflader til hver rammen. (Det vil sige, en enhed, logisk adskilt håndtering indlæsning af begge grænserne for det afskærmede netværk.)
- Andre almindelige fremgangsmåder og begrænsninger:
    - Arbejdsbelastninger må ikke gemme vigtige oplysninger.
    - Få adgang til og opdateringer til afskærmede netværkskonfigurationer og installationer er begrænset til kun godkendte administratorer.

### <a name="perimeter-network-requirements"></a>Afskærmede netværkskrav
For at aktivere disse egenskaber skal du følge disse retningslinjer på virtuelt netværkskrav til at implementere en vellykket afskærmede netværk:

- **Undernet arkitektur:** Angiv det virtuelle netværk, så et helt undernet er dedikeret som det afskærmede netværk, der er adskilt fra andre undernet i det samme virtuelle netværk. Dette sikrer trafik mellem det afskærmede netværk og andre interne eller private undernet niveauer flyder gennem en firewall eller ID'ER/IP'ER virtuelle enhed på undernetsgrænserne med brugerdefinerede omdirigerer.
- **NSG:** Det afskærmede netværksundernet selve skal være åben for at tillade kommunikation med internettet, men, der betyder ikke kunder skal springe NSGs. Følg de almindelige sikkerhedsfremgangsmåder for at minimere netværk overflader være tilgængelig på internettet. Låse de remote adresseområder tilladelse til at få adgang til installationer eller specifikt programprotokoller og porte, der er åbne. Der kan være omstændigheder, men hvor det ikke er altid muligt. Eksempelvis hvis kunder har et eksternt websted i Azure, det afskærmede netværk skal tillade indgående webanmodninger fra en hvilken som helst offentlige IP-adresser, men bør kun åbne web application porte: TCP:80 og TCP:443.
- **Routing tabel:** Det afskærmede netværksundernet sig selv skal kunne kommunikere direkte til internettet, men skal ikke tillade direkte kommunikation til og fra de back end eller lokale netværk uden at gå gennem en firewall eller maskinen.
- **Sikkerhed anvendelseskonfiguration:** For at distribuere og undersøge pakker mellem det afskærmede netværk og resten af de beskyttede netværk, sikkerhedsfunktioner som firewall, ID'ER og IP'ER kan enheder være flere hører. De kan være nødvendigt separat netværkskort for det afskærmede netværk og back end-undernet. Netværkskort i det afskærmede netværk kommunikere direkte til og fra internettet, med de tilsvarende NSGs og afskærmede netværk routing tabellen. De netværkskort, oprette forbindelse til back end-undernet har mere begrænset, NSGs og routing tabeller for de tilsvarende back end-undernet.
- **Sikkerhed maskinen funktionalitet:** Sikkerhedsfunktioner, der er implementeret i det afskærmede netværk typisk udføre følgende funktioner:
    - Firewall: Aktivér firewallregler eller politik for adgangskontrol for de indgående anmodninger.
    - Trussel registrering og forebyggelse: registrering af og mindske skadelige angreb fra internettet.
    - Overvågning og -logføring: vedligeholde detaljerede logfiler til overvågning og analyse.
    - Omvendt proxy: omdirigere indgående anmodninger til de tilsvarende back end-servere. Dette omfatter tilknytning og oversætte destinationsadresse på front end-enheder, typisk firewalls, til adresserne, back end-server.
    - Videresende proxy: give NAT og udføre overvågning for kommunikation, der er startet fra inden for det virtuelle netværk til internettet.
    - Router: Viderestilling af indgående og kryds-undernet trafik i det virtuelle netværk.
    - VPN-enhed: som tværs lokale VPN gateways i tværs lokale VPN-forbindelsen mellem kunde lokale netværk og Azure virtuelle netværk.
    - VPN-serveren: acceptere VPN-klienter opretter forbindelse til Azure virtuelle netværk.

>[AZURE.TIP] Behold følgende to grupper separat: personerne, der er autoriseret til at få adgang til det afskærmede netværk sikkerhed gear og de personer, der er autoriseret som administratorer af udvikling, implementering eller handlinger. Holde styr på separate disse grupper gør det muligt for en opdeling af opgaver og forhindrer en enkelt person i springe både programmer sikkerhed og netværk sikkerhedskontrol.

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Spørgsmål, der stilles, når opbygning af netværk grænser
Medmindre specifikt nævnt refererer ordet "netværk" i dette afsnit til private Azure virtuelle netværk, der er oprettet af en administrator af abonnement. Ordet referere ikke til de underliggende fysiske netværk i Azure.

Azure virtuelle netværk bruges også ofte til at udvide traditionelle lokale netværk. Det er muligt at inkorporere-til-websted eller ExpressRoute hybrid netværk løsninger med afskærmede netværk arkitekturer. Dette er en vigtig overvejelse i opbygning af netværk sikkerhedsgrænser.

De følgende tre spørgsmål er meget vigtigt for at besvare, når du opretter et netværk med et afskærmede netværk og flere sikkerhedsgrænser.

#### <a name="1-how-many-boundaries-are-needed"></a>1) du skal bruge hvor mange grænser?
Den første Beslutningspunkt er at beslutte, hvor mange sikkerhedsgrænser der skal bruges i en given scenarie:

- En enkelt grænse: en på det front end afskærmede netværk mellem det virtuelle netværk og internettet.
- To grænser: en Internet side af det afskærmede netværk, og et andet mellem afskærmede netværksundernet og alle back end-undernet i Azure virtuelle netværk.
- Tre grænser: på siden Internet af det afskærmede netværk, en mellem afskærmede netværk og back end-undernet og en mellem de back end-undernet og lokale netværk.
- N grænser: et variabelt tal. Afhængigt af sikkerhedskrav er der virkelig et vilkårligt antal sikkerhedsgrænser, der kan anvendes i en given netværk.

Antallet og typen af grænser behov varierer afhængigt af virksomhedens risikoen tolerance og det scenarie, der implementeres. Dette er ofte et fælles beslutning, der er foretaget af flere grupper i en organisation, herunder ofte en risiko og overholdelse af angivne standarder team, et netværk og platform team og et program udviklingsteam. Personer med kendskab til sikkerhed, de data, der er involveret og bruges teknologier bør have en sig i denne beslutning om at sikre, at den relevante sikkerhed benstilling for hver implementering.

>[AZURE.TIP] Brug det mindste tal afgrænsninger, der opfylder sikkerhedskravene for en given situation. Med flere grænser den sværere handlinger og fejlfinding kan være, samt for administration af meget tid til at administrere flere rammen politikker over tid. Men utilstrækkelig grænser øger risikoen. Finde saldoen er meget vigtigt.

![Hybrid netværk med tre sikkerhedsgrænser][6]

Den foregående figur viser en overordnet oversigt over et tre sikkerhed rammen netværk. Der er grænserne mellem det afskærmede netværk og internettet, Azure front end- og back end-privat undernet, og det Azure back end-undernet og lokale virksomhedens netværk.

#### <a name="2-where-are-the-boundaries-located"></a>2) hvor er grænserne placeret?
Når antallet af grænser er besluttet, er hvor du kan implementere dem næste beslutning. Der er normalt tre valgmuligheder:
- Ved hjælp af en internetbaserede mellemliggende tjeneste (for eksempel en skybaseret firewall til webprogrammer, der ikke er beskrevet i dette dokument)
- Ved hjælp af indbyggede funktioner og/eller netværk virtuelle anvendelser i Azure
- Brug af fysiske enheder på det lokale netværk

På rent Azure netværk er indstillingerne indbyggede Azure funktioner (for eksempel Azure Indlæs balancere) eller netværk virtuelle enheder fra omfattende partner økosystemet af Azure (for eksempel, Kontrollér punkt firewalls).

Hvis der kræves en rammen mellem Azure og et lokalt netværk, kan sikkerhedsenheder findes på begge sider af forbindelsen (eller begge sider). En beslutning skal dermed foretages på placeringen placere sikkerhed gear.

I den foregående figur, det Internet-afskærmede netværk og forside-til-back-end grænserne er helt indeholdt i Azure og skal enten være oprindelige Azure funktioner eller netværket virtuel anvendelser. Sikkerhedsenheder på rammen mellem Azure (back end-undernet) og virksomhedens netværk kan være enten på i Azure side eller den lokale side eller endda en kombination af enheder på begge sider. Der kan være betydeligt fordele og ulemper ved begge indstillinger, som skal overvejes alvorligt.

For eksempel har med eksisterende fysisk sikkerhed tandhjul på lokale netværk side fordelen ved, at ingen nye gear skal bruges. Det kræver blot konfigureres igen. Ulempen er dog, at al trafik skal gå tilbage fra Azure til den lokale netværk, der skal ses af alle tandhjulet sikkerhed. Dermed Azure-Azure trafik kan betale betydeligt ventetid, og påvirker programmet ydeevne og bruger oplevelse, hvis det blev tvunget tilbage til det lokale netværk for sikkerhed politik aktivering.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) hvordan implementeres grænserne?
Hver sikkerhed rammen sandsynligvis muligheden for forskellige krav (for eksempel-ID'ER og firewallregler i Internet side af afskærmede netværk, men kun ACLs mellem afskærmede netværk og back end-undernet). Beslutte, hvilke enheder du bruger, afhænger af kravene scenarie og sikkerhed. I følgende afsnit beskriver eksempler 1, 2 og 3 nogle indstillinger, der kan bruges. Gennemgå de Azure oprindelige netværksfunktioner og de enheder, der er tilgængelige i Azure fra partner økosystemet viser de tilgængelige til at løse næsten alle scenarie et væld indstillinger.

En anden vigtige implementering Beslutningspunkt er hvordan du opretter forbindelse til lokale netværk med Azure. Skal du bruge Azure virtuelle gatewayen eller et netværk virtuelle enhed? Disse indstillinger er beskrevet mere detaljeret i følgende afsnit (eksempler 4, 5 og 6).

Desuden trafik mellem virtuelle netværk i Azure det kan være nødvendigt. Disse scenarier tilføjes på et senere tidspunkt.

Når du kender svarene på de forrige spørgsmål, kan sektionen [Hurtig Start](#fast-start) til at identificere, hvilke eksempler er mest relevante for et givet scenarie.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Eksempler: Opbygning af sikkerhedsgrænser med Azure virtuelle netværk
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Eksempel 1: Oprette en afskærmede netværk for at beskytte programmer med NSGs
[Tilbage til hurtig start](#fast-start) | [detaljeret opbygge instruktionerne i dette eksempel][Example1]

![Indgående afskærmede netværk med NSG][7]

#### <a name="environment-description"></a>Beskrivelse af miljø
I dette eksempel er der et abonnement, der indeholder følgende:

- To cloud services: "FrontEnd001" og "BackEnd001"
- Et virtuelt netværk og "CorpNetwork", med to undernet: "FrontEnd" og "Back end"
- En sikkerhedsgruppe til netværk, der anvendes til begge undernet
- En Windows-server, der repræsenterer en programserver web ("IIS01")
- To Windows-servere, der repræsenterer application back end-servere ("AppVM01", "AppVM02")
- En Windows-server, der repræsenterer en DNS-server ("DNS01")

Scripts og en skabelon til Azure Ressourcestyring, se [detaljerede build instruktioner][Example1].

#### <a name="nsg-description"></a>NSG beskrivelse
I dette eksempel er en NSG gruppe indbygget og derefter indlæst med seks regler.

>[AZURE.TIP] Generelt, skal du oprette bestemte "Tillad" regler først, efterfulgt af de mere generisk "Afvis" regler. Den angivne prioritet bestemmer, hvilke regler evalueres først. Når trafik er fundet skal gælde for en bestemt regel, skal evalueres ingen yderligere regler. NSG regler kan anvende i den indgående eller udgående retning (fra perspektivet undernettet).

Med en erklæring, er der opbygget følgende regler for indgående trafik:

1.  Interne DNS-trafik (port 53) det er tilladt.
2.  RDP-trafik (port 3389) fra internettet til en hvilken som helst virtuelt er tilladt.
3.  HTTP-trafik (port 80) fra internettet til webserver (IIS01) er tilladt.
4.  Al trafik (alle porte) fra IIS01 til AppVM1 er tilladt.
5.  Al trafik (alle porte) fra internettet til det hele virtuelle netværk (begge undernet) er nægtet.
6.  Al trafik (alle porte) fra front end-undernet til back end-undernet er nægtet.

Med disse regler, der er bundet til hvert undernet, hvis en HTTP-anmodning blev indgående fra internettet til webserveren, begge regler 3 (Tillad) og 5 (Afvis) vil anvende. Men fordi regel 3 har højere prioritet, kun den vil anvende, og regel 5 vil ikke kommer ind i Afspil. HTTP-anmodningen vil herefter til webserveren. Hvis den samme trafik blev prøver at nå DNS01 serveren, regel 5 (Afvis) skal være først anvende og trafikken vil ikke kunne overføre til serveren. Regel 6 (Afvis) blokerer det front end-undernet fra taler til back end-undernet (med undtagelse af tilladte trafik i regler 1 og 4). Dette beskytter back end-netværket, i tilfælde af en hacker bringer webprogrammet på front end. Hackeren ville har begrænset adgang til back end-"beskyttet" netværket (kun til ressourcer, der vises på AppVM01 serveren).

Der findes en udgående regel, der tillader trafik ud på internettet. I dette eksempel skal er vi at tillade udgående trafik og ikke at ændre alle udgående regler. Hvis du vil låse trafik i begge retninger, brugerdefinerede distributionen er påkrævet (se eksempel 3).

#### <a name="conclusion"></a>Konklusion
Dette er en relativt simple og enkle måde at isolere back end-undernet fra indgående trafik. Få mere at vide i [detaljerede build instruktioner][Example1]. Disse instruktioner, omfatter:

- Hvordan du opretter denne afskærmede netværk med PowerShell-scripts.
- Hvordan du opretter denne afskærmede netværk med en skabelon til Azure ressourcestyring.
- Detaljerede beskrivelser af hver NSG kommando.
- Detaljeret trafik flow scenarier, der viser, hvordan trafik er tilladt eller nægtet i hvert lag.


 ### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Eksempel 2: Oprette en afskærmede netværk for at beskytte programmer med en firewall og NSGs
[Tilbage til hurtig start](#fast-start) | [detaljeret opbygge instruktionerne i dette eksempel][Example2]

![Indgående afskærmede netværk med for og NSG][8]

#### <a name="environment-description"></a>Beskrivelse af miljø
I dette eksempel er der et abonnement, der indeholder følgende:

- To cloud services: "FrontEnd001" og "BackEnd001"
- Et virtuelt netværk og "CorpNetwork", med to undernet: "FrontEnd" og "Back end"
- En sikkerhedsgruppe til netværk, der anvendes til begge undernet
- Et netværk virtuelle produktet i dette tilfælde en firewall, forbundet til det front end-undernet
- En Windows-server, der repræsenterer en programserver web ("IIS01")
- To Windows-servere, der repræsenterer application back end-servere ("AppVM01", "AppVM02")
- En Windows-server, der repræsenterer en DNS-server ("DNS01")

Scripts og en skabelon til Azure Ressourcestyring, se [detaljerede build instruktioner][Example2].

#### <a name="nsg-description"></a>NSG beskrivelse
I dette eksempel er en NSG gruppe indbygget og derefter indlæst med seks regler.

>[AZURE.TIP] Generelt, skal du oprette bestemte "Tillad" regler først, efterfulgt af de mere generisk "Afvis" regler. Den angivne prioritet bestemmer, hvilke regler evalueres først. Når trafik er fundet skal gælde for en bestemt regel, skal evalueres ingen yderligere regler. NSG regler kan anvende i den indgående eller udgående retning (fra perspektivet undernettet).

Med en erklæring, er der opbygget følgende regler for indgående trafik:

1.  Interne DNS-trafik (port 53) det er tilladt.
2.  RDP-trafik (port 3389) fra internettet til en hvilken som helst virtuelt er tilladt.
3.  En hvilken som helst internettrafik (alle porte) til netværk virtuelle maskinen (firewall) er tilladt.
4.  Al trafik (alle porte) fra IIS01 til AppVM1 er tilladt.
5.  Al trafik (alle porte) fra internettet til det hele virtuelle netværk (begge undernet) er nægtet.
6.  Al trafik (alle porte) fra front end-undernet til back end-undernet er nægtet.

Med disse regler, der er bundet til hvert undernet, hvis en HTTP-anmodning blev indgående fra internettet til firewallen, begge regler 3 (Tillad) og 5 (Afvis) vil anvende. Men fordi regel 3 har højere prioritet, kun den vil anvende, og regel 5 vil ikke kommer ind i Afspil. HTTP-anmodningen vil herefter firewallen. Hvis den samme trafik blev prøver at nå server IIS01, selvom det er på front end-undernet, reglen 5 (Afvis) vil anvende, og trafikken vil ikke kunne overføre til serveren. Regel 6 (Afvis) blokerer det front end-undernet fra taler til back end-undernet (med undtagelse af tilladte trafik i regler 1 og 4). Dette beskytter back end-netværket, i tilfælde af en hacker bringer webprogrammet på front end. Hackeren ville har begrænset adgang til back end-"beskyttet" netværket (kun til ressourcer, der vises på AppVM01 serveren).

Der findes en udgående regel, der tillader trafik ud på internettet. I dette eksempel skal er vi at tillade udgående trafik og ikke at ændre alle udgående regler. Hvis du vil låse trafik i begge retninger, brugerdefinerede distributionen er påkrævet (se eksempel 3).

#### <a name="firewall-rule-description"></a>Beskrivelse af regel Firewall
På firewallen, skal regler for videresendelse oprettes. Da dette eksempel kun omdirigerer internettrafik i bundet til firewall og derefter til webserveren, kun én viderestilling af netværk, NAT (oversættelse af) bruges regel.

Reglen for videresendelse accepterer alle indgående kilde-adresser, der rammer firewallen forsøger at få HTTP (port 80 eller 443 til HTTPS). Der er sendt af en firewall lokale grænseflade og omdirigeret til webserveren med 10.0.1.5 IP-adresse.

#### <a name="conclusion"></a>Konklusion
Dette er en relativt enkle måde at beskytte dit program med en firewall, og isolerer det back end-undernet fra indgående trafik. Få mere at vide i [detaljerede build instruktioner][Example2]. Disse instruktioner, omfatter:

- Hvordan du opretter denne afskærmede netværk med PowerShell-scripts.
- Sådan oprettes i dette eksempel med en skabelon til Azure ressourcestyring.
- Detaljerede beskrivelser af hver NSG kommando- og firewall regel.
- Detaljeret trafik flow scenarier, der viser, hvordan trafik er tilladt eller nægtet i hvert lag.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-udr-and-nsg"></a>Eksempel 3: Oprette en afskærmede netværk for at beskytte netværk med en firewall, UDR og NSG
[Tilbage til hurtig start](#fast-start) | [detaljeret opbygge instruktionerne i dette eksempel][Example3]

![Tovejs-afskærmede netværk med for NSG og UDR][9]

#### <a name="environment-description"></a>Beskrivelse af miljø
I dette eksempel er der et abonnement, der indeholder følgende:

- Tre cloud services: "SecSvc001", "FrontEnd001" og "BackEnd001"
- Et virtuelt netværk og "CorpNetwork" med tre undernet: "SecNet", "FrontEnd" og "Back end"
- Et netværk virtuelle produktet i dette tilfælde en firewall, forbundet til SecNet undernettet
- En Windows-server, der repræsenterer en programserver web ("IIS01")
- To Windows-servere, der repræsenterer application back end-servere ("AppVM01", "AppVM02")
- En Windows-server, der repræsenterer en DNS-server ("DNS01")

Scripts og en skabelon til Azure Ressourcestyring, se [detaljerede build instruktioner][Example3].

#### <a name="udr-description"></a>UDR beskrivelse
Som standard defineres på følgende system omdirigerer som:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal altid er defineret adresse prefix(es) i det virtuelle netværk for denne specifikke netværk (det vil sige, den ændres fra virtuelt netværk til virtuelt netværk, afhængigt af hvordan hver bestemt virtuelt netværk er defineret). De resterende system omdirigerer er statisk og standard som angivet i tabellen.

I dette eksempel oprettes to routing tabeller, en for de front end- og back end-undernet. Alle tabeller, er indlæst med statisk omdirigerer passer til det angivne undernet. I dette eksempel skal hver tabel har tre omdirigerer, dirigere al trafik (0.0.0.0/0) via firewallen (næste hop = virtuelle maskinen IP-adresse):

1. Lokale undernet trafik med ingen Next Hop defineret for at tillade lokale undernet trafik kan springe firewallen.
2. Virtuel netværkstrafik med en Next Hop defineret som firewall; Dette tilsidesætter den standard-regel, der tillader lokale virtuelle netværkstrafik til at dirigere direkte.
3. Alle resterende trafik (0/0) med en Next Hop defineret som firewallen.

>[AZURE.TIP] Ikke at have lokale undernet indtastningen i UDR brydes lokale undernet kommunikation. 
> - I vores eksempel 10.0.1.0/24 pege på VNETLocal er meget vigtigt som på anden måde, pakke, der forlader webserver (10.0.1.4), der er beregnet til en anden lokal server (for eksempel) 10.0.1.25 mislykkes, når de sendes til for, der så vil sende det til undernettet, og undernettet igen sender den til for osv.
> - Risikoen for en routing løkke er typisk højere på multi-nic anvendelser, der er direkte forbundet med hvert undernet de kommunikerer med, som er ofte traditionelt, lokale, udstyr. 

Når de routing-tabeller er oprettet, er de bundet til deres undernet. Front end-undernet routing tabellen én gang oprettet og bundet til undernet, ville se således ud:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE] UDR kan nu anvendes til det gateway-undernet, som ExpressRoute kredsløbet er forbundet.
>
> Eksempler på, hvordan du aktiverer dit afskærmede netværk med ExpressRoute eller websted til websted netværk er vist i eksempler 3 og 4.


#### <a name="ip-forwarding-description"></a>Videresendelse af IP-beskrivelse
Videresendelse af IP-adresse er en companion funktion til UDR. Dette er en indstilling på et virtuelt enhed, der gør det muligt at modtage trafik ikke specifikt er adresseret til produktet, og derefter videresende pågældende trafik til destinationen ultimate.

Eksempelvis hvis trafik fra AppVM01 opretter en anmodning til DNS01-serveren, kan de UDR vil distribuere dette til firewallen. Med IP-videresendelse aktiveret, bliver trafik til destinationen DNS01 (10.0.2.4) accepteres ved maskinen (10.0.0.4) og derefter videresendt til destinationen ultimate (10.0.2.4). Uden IP-videresendelse er aktiveret i firewallen, vil trafik ikke accepteres ved maskinen, selvom rutetabellen har firewallen som det næste trin. Hvis du vil bruge et virtuelt maskinen, er det vigtigt at huske at aktivere IP-videresendelse sammen med UDR.

#### <a name="nsg-description"></a>NSG beskrivelse
I dette eksempel er en NSG gruppe indbygget og derefter indlæst med en enkelt regel. Denne gruppe er derefter bundet kun til de front end- og back end-undernet (ikke SecNet). Med en erklæring er der oprettet på følgende regel:

- Al trafik (alle porte) fra internettet til det hele virtuelle netværk (alle undernet) er nægtet.

Selvom NSGs bruges i dette eksempel, er dens Hovedformålet som en sekundær lag af beskyttelse mod forkert manuel konfiguration. Målet er at blokere al indgående trafik fra internettet til alle front end- eller back end-undernet. Trafik skal kun passerer gennem SecNet undernet til firewallen (og derefter, hvis det er relevant, på alle front end- eller back end-undernet). Desuden med UDR regler på plads, ville al trafik, der lave dem til de front end- eller back end-undernet dirigeret ud til firewallen (sammen takket være UDR). Firewallen skal se ud som en asymmetrisk flow og vil slippe den udgående trafik. Der er derfor tre sikkerhedslag beskytte undernet:
- Ingen åbne slutpunkterne på FrontEnd001 og BackEnd001 cloud services.
- NSGs nægte trafik fra internettet.
- Firewall slippe asymmetrisk trafikken.

Et interessant punkt vedrørende NSG i dette eksempel er, at den indeholder kun én regel, som er til at afvise internettrafik til hele virtuelle netværket, herunder sikkerhed undernet. Da NSG er kun bundet til front end- og back end-undernet, reglen ikke behandles på trafik indgående til sikkerhed undernet. Som et resultat teksten trafikken går til sikkerhed undernettet.

#### <a name="firewall-rules"></a>Firewallregler
På firewallen, skal regler for videresendelse oprettes. Da firewallen blokerer eller viderestilling af alle indgående, udgående og inden for samme virtuelle netværkstrafik, du skal bruge mange firewallregler. Desuden påløber al indgående trafik Security Service offentlige IP-adresse (på forskellige porte), hvis du vil blive behandlet af firewallen. Den bedste fremgangsmåde er til at oprette diagrammer logiske flyder før du konfigurerer undernet og firewallregler for at undgå at arbejde med senere. I følgende figur vises er en logisk visning af firewallregler for dette eksempel:
 
![Logiske visning af firewallreglerne][10]

>[AZURE.NOTE] Baseret på netværk maskinen virtuel bruges varierer management porte. I dette eksempel er en Barracuda NextGen Firewall refereres til, som bruger port 22, 801 og 807. I dokumentationen maskinen leverandør for at finde ud af præcis porte, der bruges til styring af den enhed, der bruges.

#### <a name="firewall-rules-description"></a>Firewall regler beskrivelse
Sikkerhed undernet vises ikke i det foregående logiske diagram. Dette skyldes, at firewallen er den eneste ressource på det pågældende undernet, og dette diagram vises firewallreglerne, og hvordan de logisk tillade eller nægte trafik-flow, ikke den aktuelle distribuerede sti. Desuden de eksterne porte valgt for RDP-trafik er placeret højere varierede porte (8014 – 8026) og blev valgt til at justere noget med de sidste to oktetter i nemmere læsbarhed lokal IP-adresse (for eksempel lokale serveradressen 10.0.1.4 er knyttet til eksterne port 8014). En hvilken som helst højere ikke er i konflikt porte, men kan bruges.

I dette eksempel skal bruge vi syv typer regler:

- Eksterne regler (for indgående trafik):
  1.    Firewall management regel: denne App omdirigere regel tillader trafik til at overføre til management porte af netværk virtuelle maskinen.
  2.    RDP regler (for hver Windows server): disse fire regler (én for hver server) Tillad styring af de enkelte servere via RDP. Dette kan også samlet i én regel, afhængigt af funktionerne i netværk maskinen virtuel bruges.
  3.    Programmet trafikregler: der er to af disse regler, først til front end-trafik, og andet til back end-trafik (for eksempel webserver til data niveau). Konfiguration af disse regler afhænger netværksarkitektur (hvor serverne er placeret) og trafik flyder (hvilken retning trafik-flow, og hvilke porte bruges).
      - Den første regel tillader faktiske anvendelse trafikken når application server. Mens de andre regler tillader for sikkerhed og administration, er programmet trafikregler hvad give eksterne brugere eller tjenester til at få adgang til program(mer). I dette eksempel er der en enkelt webserver på port 80. En enkelt firewall programmet regel omdirigerer dermed indgående trafik til den eksterne IP til web-servere interne IP-adresse. Omdirigeret trafik sessionen skal oversættes via NAT til den interne server.
      - Den anden regel er den back end-regel, der tillader webserveren til at tale med AppVM01 server (men ikke AppVM02) via en hvilken som helst port.
- Interne regler (for de virtuelle netværkstrafik)
  4.    Udgående til Internet regel: denne regel tillader trafik fra en hvilken som helst netværk, der skal overføres til de valgte netværk. Denne regel er som regel en standard-regel allerede på firewallen, men i deaktiveret tilstand. Denne regel skal være aktiveret i dette eksempel.
  5.    DNS-regel: denne regel kan kun DNS (port 53) trafik til at overføre til DNS-serveren. De fleste trafik fra front end til back-end er blokeret for denne-miljø. Denne regel kan specifikt DNS fra en hvilken som helst lokale undernet.
  6.    Undernet, at undernet regel: denne regel er at tillade en hvilken som helst server på back end-undernet at oprette forbindelse til en hvilken som helst server i det front end-undernet (men ikke omvendt).
- Fejlsikret regel (for trafik, der ikke opfylder en af de forrige):
  7.    Afvise alle trafik regel: Dette altid skal være den endelige regel (med hensyn til prioritet), og som sådan Hvis en trafikken ikke matcher nogen af den foregående regler fjernes af denne regel. Dette er en standard-regel og normalt aktiveret. Ingen ændringer er normalt det er nødvendigt.

>[AZURE.TIP] På den anden programmet trafik regel for at forenkle dette eksempel er en hvilken som helst port tilladt. I et reelt tal scenarie skal mest specifikke port og -adresseområder bruges til at reducere angrebsoverfladen af denne regel.

Når alle de regler, der tidligere er oprettet, er det vigtigt at gennemse prioriteten af hver regel for at sikre, at trafik kan tillades eller afvises efter behov. I dette eksempel er reglerne i prioritetsrækkefølge.

#### <a name="conclusion"></a>Konklusion
Dette er en mere kompleks, men mere komplet metode til at beskytte og isolerer netværk end de tidligere eksempler. (Eksempel 2 beskytter lige programmet, og eksempel 1 isolerer lige undernet). Dette design giver mulighed for overvågning trafik i begge retninger og beskytter ikke kun indgående application server men opretholder netværk sikkerhedspolitik for alle servere på dette netværk. Også, afhængigt af produktet bruges fuld trafik overvågning og tilstedeværelse kan der opnås. Få mere at vide i [detaljerede build instruktioner][Example3]. Disse instruktioner, omfatter:

- Sådan oprettes dette eksempel afskærmede netværk med PowerShell-scripts.
- Sådan oprettes i dette eksempel med en skabelon til Azure ressourcestyring.
- Detaljerede beskrivelser af hvert UDR, NSG kommandoen og firewallregel.
- Detaljeret trafik flow scenarier, der viser, hvordan trafik er tilladt eller nægtet i hvert lag.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-virtual-private-network-vpn"></a>Eksempel 4: Tilføje en hybrid forbindelse til et websted til websted, virtuelt maskinen virtuelt privat netværk (VPN)
[Tilbage til hurtig start](#fast-start) | Detaljeret build vejledning tilgængelige snart

![Afskærmede netværk med for forbundne hybrid netværk][11]

#### <a name="environment-description"></a>Beskrivelse af miljø
Hybrid netværk ved hjælp af et netværk virtuelle maskinen (for) kan føjes til en af de typer af afskærmede netværk er beskrevet i eksempler 1, 2 eller 3.

Som vist i den foregående figur, bruges en VPN-forbindelse via internettet (websted til websted) til at oprette forbindelse et lokalt netværk til en Azure virtuelt netværk via en for.

>[AZURE.NOTE] Hvis du bruger ExpressRoute med indstillingen Azure offentlige Peering er aktiveret, skal der oprettes en statisk rute. Dette bør omdirigere til for VPN-IP-adressen ud virksomhedens på internettet og ikke via ExpressRoute WAN. NAT påkrævet af indstillingen ExpressRoute Azure offentlige Peering kan bryde VPN-sessionen.

Når VPN er på plads, bliver for central hub for alle netværk og undernet. Viderestilling af firewallregler bestemme, hvilke trafik-flow er tilladt, oversættes via NAT, omdirigeres eller udelades, (lige for trafik flyder mellem lokale netværk og Azure).

Trafik-flow skal overvejes grundigt, som de kan optimeres eller nedsat ved dette design mønster, afhængigt af specifikt use case.

Ved hjælp af det miljø, der er indbygget i eksempel 3, og derefter tilføje et websted til websted VPN hybrid netværksforbindelse, giver det følgende design:

![Afskærmede netværk med for forbindelse ved hjælp af en VPN til-websted][12]

Lokalt router eller en anden netværksenhed, der er kompatibelt med din for for VPN, der ville være VPN-klienten. Enheden fysisk ville være ansvarlig for at starte og vedligeholde VPN-forbindelse med din for.

Logisk til for ser netværket Sådan fire separate "sikkerhedszoner" med reglerne på de bliver trafik mellem disse zoner primære direktør for:

![Logisk netværk fra for perspektiv][13]

#### <a name="conclusion"></a>Konklusion
Tilføjelse af en hybrid til websted VPN netværksforbindelse til en Azure virtuelt netværk kan udvide lokale netværk til Azure på en sikker måde. Brug af en VPN-forbindelse, din trafik er krypteret og omdirigerer via internettet. FOR i dette eksempel er en central placering for at gennemtvinge og administrere sikkerhedspolitikken. Du kan finde yderligere oplysninger finder detaljerede build-vejledningen (partiet). Disse instruktioner, omfatter:

- Sådan oprettes dette eksempel afskærmede netværk med PowerShell-scripts.
- Sådan oprettes i dette eksempel med en skabelon til Azure ressourcestyring.
- Detaljeret trafik flow scenarier, der viser, hvordan trafik bevæger sig gennem dette design.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn"></a>Eksempel 5: Tilføje en hybrid forbindelse til en-til-websted, Azure gateway VPN
[Tilbage til hurtig start](#fast-start) | Detaljeret build vejledning tilgængelige snart

![Afskærmede netværk med gateway forbundne hybrid netværk][14]

#### <a name="environment-description"></a>Beskrivelse af miljø
Hybrid netværk ved hjælp af en VPN-Azure gateway, kan føjes til enten afskærmede netværkstypen som beskrevet i eksempler 1 eller 2.

Som vist i den foregående figur, bruges en VPN-forbindelse via internettet (websted til websted) til at oprette forbindelse et lokalt netværk til en Azure virtuelt netværk via en VPN-Azure gateway.

>[AZURE.NOTE] Hvis du bruger ExpressRoute med indstillingen Azure offentlige Peering er aktiveret, skal der oprettes en statisk rute. Dette bør omdirigere til for VPN-IP-adressen ud virksomhedens på internettet og ikke via ExpressRoute WAN. NAT påkrævet af indstillingen ExpressRoute Azure offentlige Peering kan bryde VPN-sessionen.

I følgende figur vises de to netværk kanter i denne indstilling. Kontrollere trafik flyder inden for samme Azure netværk og mellem Azure og internettet på den første kant for og NSGs. Den anden kant er Azure VPN gatewayen, som er et helt separat og isolerede netværkskanten mellem lokale og Azure.

Trafik-flow skal overvejes grundigt, som de kan optimeres eller nedsat ved dette design mønster, afhængigt af specifikt use case.

Ved hjælp af det miljø, der er indbygget i eksempel 1, og derefter tilføje et websted til websted VPN hybrid netværksforbindelse, giver det følgende design:

![Afskærmede netværk med gateway, der er forbundet med en ExpressRoute-forbindelse][15]

#### <a name="conclusion"></a>Konklusion
Tilføjelse af en hybrid til websted VPN netværksforbindelse til en Azure virtuelt netværk kan udvide lokale netværk til Azure på en sikker måde. Ved hjælp af den oprindelige Azure VPN-gateway, din trafik er krypteret IPSec og sender via internettet. Desuden kan ved hjælp af gatewayen Azure VPN angive en lavere omkostninger indstilling (ingen yderligere licenser omkostninger som med tredjeparts NVAs). Dette er mest økonomisk i eksempel 1, hvor ingen for bruges. Du kan finde yderligere oplysninger finder detaljerede build-vejledningen (partiet). Disse instruktioner, omfatter:

- Sådan oprettes dette eksempel afskærmede netværk med PowerShell-scripts.
- Sådan oprettes i dette eksempel med en skabelon til Azure ressourcestyring.
- Detaljeret trafik flow scenarier, der viser, hvordan trafik bevæger sig gennem dette design.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Eksempel 6: Føje en hybrid forbindelse med ExpressRoute
[Tilbage til hurtig start](#fast-start) | Detaljeret build vejledning tilgængelige snart

![Afskærmede netværk med gateway forbundne hybrid netværk][16]

#### <a name="environment-description"></a>Beskrivelse af miljø
Hybrid netværk ved hjælp af en privat peering ExpressRoute-forbindelse kan føjes til enten afskærmede netværkstypen som beskrevet i eksempler 1 eller 2.

Som vist i den foregående figur, indeholder ExpressRoute privat peering en direkte forbindelse mellem dit lokale netværk og Azure virtuelt netværk. Trafik kører i transit kun service provider Netværks- og Microsoft Azure netværket, aldrig rører ved internettet.

>[AZURE.NOTE] Der er visse begrænsninger, når du bruger UDR med ExpressRoute, på grund af kompleksitet dynamisk routing bruges i Azure virtuelle gatewayen. Disse er som følger:
>
>- UDR bør ikke anvendes til det gateway-undernet, hvorpå ExpressRoute sammenkædede Azure virtuelle gatewayen er forbundet.
>- ExpressRoute sammenkædede Azure virtuelle gatewayen må ikke være NextHop enhed til andre UDR bundet undernet.
>
>
<br />

>[AZURE.TIP] Brug af ExpressRoute holder virksomhedens netværkstrafikken fra internettet for at øge sikkerheden og betydeligt forbedret ydeevne. Det kan også for serviceaftaler fra udbyderen af ExpressRoute. Gatewayen Azure kan overføre op til 2 Gb/s med ExpressRoute, mens med VPN til-websted, er det maksimale antal gennemløb Azure Gateway 200 Mb/s.

Som det fremgår i det følgende diagram, med denne indstilling har miljøet nu to netværk kanter. FOR og NSG styre trafik flyder inden for samme Azure netværk og mellem Azure og internettet, mens gatewayen er et helt separat og isolerede netværkskanten mellem lokale og Azure.

Trafik-flow skal overvejes grundigt, som de kan optimeres eller nedsat ved dette design mønster, afhængigt af specifikt use case.

Ved hjælp af det miljø, der er indbygget i eksempel 1, og derefter tilføje en ExpressRoute hybrid netværksforbindelse, giver det følgende design:

![Afskærmede netværk med gateway, der er forbundet med en ExpressRoute-forbindelse][17]

#### <a name="conclusion"></a>Konklusion
Tilføjelse af en ExpressRoute privat Peering netværksforbindelse kan udvide lokale netværk til Azure i en sikker, nederste ventetid, højere udføre måde. Desuden indeholder ved hjælp af den oprindelige Azure-Gateway, som i dette eksempel en lavere omkostninger indstilling (ikke flere licenser som i tredjeparts NVAs). Du kan finde yderligere oplysninger finder detaljerede build-vejledningen (partiet). Disse instruktioner, omfatter:

- Sådan oprettes dette eksempel afskærmede netværk med PowerShell-scripts.
- Sådan oprettes i dette eksempel med en skabelon til Azure ressourcestyring.
- Detaljeret trafik flow scenarier, der viser, hvordan trafik bevæger sig gennem dette design.

## <a name="references"></a>Referencer
### <a name="helpful-websites-and-documentation"></a>Nyttige websteder og dokumentation
- Access Azure med Azure ressourcestyring:
- Få adgang til Azure med PowerShell: [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- Virtuelt netværk dokumentation: [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- Netværk sikkerhed gruppe dokumentation: [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- Brugerdefinerede routing dokumentation: [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Azure virtuelle gateways: [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- Websted til websted VPN'er: [https://azure.microsoft.com/documentation/articles/vpn-gateway-create-site-to-site-rm-powershell](./vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- ExpressRoute dokumentation (Husk at tjekke afsnittene "Introduktion" og "Sådan"): [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Sikkerhed indstillinger rutediagram"
[1]: ./media/best-practices-network-security/compliancebadges.png "Azure overholdelse Badges"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Azure sikkerhedsfunktioner"
[3]: ./media/best-practices-network-security/dmzcorporate.png "En DMZ på en virksomheds netværk"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Azure sikkerhedsarkitekturen"
[5]: ./media/best-practices-network-security/dmzazure.png "En DMZ i en Azure virtuelt netværk"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Hybrid netværk med tre sikkerhedsgrænser"
[7]: ./media/best-practices-network-security/example1design.png "Indgående DMZ med NSG"
[8]: ./media/best-practices-network-security/example2design.png "Indgående DMZ med for og NSG"
[9]: ./media/best-practices-network-security/example3design.png "Tovejs-DMZ med for NSG og UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Logiske visning af firewallreglerne"
[11]: ./media/best-practices-network-security/example4designoptions.png "DMZ med for forbindelse Hybrid netværk"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ med for forbindelse ved hjælp af en VPN til-websted"
[13]: ./media/best-practices-network-security/example4networklogical.png "Logisk netværk fra for perspektiv"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ med Azure Gateway forbindelse til websted Hybrid netværk"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ med Azure Gateway ved hjælp af VPN til-websted"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ med Azure Gateway forbindelse ExpressRoute Hybrid netværk"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ med Azure Gateway ved hjælp af en ExpressRoute-forbindelse"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
