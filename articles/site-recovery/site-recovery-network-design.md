<properties
    pageTitle="Designe din netværksinfrastruktur til nedbrud | Microsoft Azure"
    description="I denne artikel beskrives netværk Designovervejelser Azure gendannelse af websteder"
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/19/2016"
    ms.author="pratshar"/>

#  <a name="designing-your-network-infrastructure-for-disaster-recovery"></a>Designe din netværksinfrastruktur til nedbrud

I denne artikel henvender til IT-medarbejdere, der er ansvarlige for planlagde arkitektur, implementere, og understøttende Forretningskontinuitet og nedbrud gendannelse (BCDR) infrastruktur, og der vil udnytte Microsoft Azure websted gendannelse (ASR) til at understøtte og forbedre deres BCDR tjenester. Dette dokument omhandler praktiske overvejelser i forbindelse med System Center Virtual Machine Manager serverinstallation, fordele og ulemper ved strakt undernet kontra undernet failover, og hvordan du strukturere nedbrud til virtuelle Microsoft Azure-websteder.

## <a name="overview"></a>Oversigt

[Azure websted gendannelse (ASR)](https://azure.microsoft.com/services/site-recovery/) er en Microsoft Azure-tjeneste, som orchestrates beskyttelse og gendannelse af programmerne virtuelt til løbende nedbrud gendannelse (BCDR) erhvervsbrug. Dette dokument er beregnet til at hjælpe læseren gennem processen med at designe netværk, fokuserer på planlagde arkitektur IP-intervaller og undernet på webstedet nedbrud gendannelse ved replikering virtuelle maskiner (VM'er) ved hjælp af gendannelse af websteder.

I denne artikel beskrives, hvordan gendannelse af websteder kan planlagde arkitektur og implementere en af flere lokationer virtuelle datacenter for at understøtte BCDR services på tidspunktet for test eller nedbrud.

I en verden, hvor alle forventer 24/7 connectivity, er det mere vigtigt end nogensinde før at holde din infrastruktur og programmer at køre. Formålet med Forretningskontinuitet og genoprettelse efter genoprettelse (BCDR) er at gendanne mislykkedes komponenter, så organisationen hurtigt kan genoptage normal handlinger. Udvikling af nedbrud gendannelse strategier til at håndtere sandsynligt, ødelæggende hændelser er meget svære. Dette er på grund af den naturlige problemer i at forudsige fremtiden, især i forhold til usandsynligt begivenheder og høj omkostninger til at levere tilstrækkelig målinger af beskyttelse mod stor catastrophes. 

Afgørende for BCDR planlægning, gendannelse tid mål (RTO) og gendannelse punkt målsætning varer skal være defineret som en del af en nedbrud gendannelse plan. Når nedbrud rammer kundens datacenter, der bruger Azure gendannelse af websteder, kunder kan hurtigt (lav RTO) tage online deres replikerede virtuelle maskiner, der er placeret i det sekundære datacenter eller Microsoft Azure med mindste datatab (lav frigivne Produktionsordre). 

Failover er gjort muligt via ASR som indledningsvis kopierer udpegede virtuelle maskiner fra den primære datacenter til sekundær datacenteret eller til Azure (afhængigt af scenariet), og derefter opdaterer replikaerne med jævne mellemrum. Under planlægningen infrastruktur skal netværksdesign betragtes som potentiel flaskehals, der kan forhindre, at du fra mødet virksomhed RTO og frigivne Produktionsordre målsætninger.  

Når administratorer planlægger at implementere en løsning til genoprettelse efter genoprettelse, er en af de vigtigste spørgsmål i deres hjerner hvordan den virtuelle maskine ville være tilgængelig, når sekundære er fuldført. ASR kan administratoren for at vælge det netværk, som en virtuel maskine ville være forbundet med efter failover. Hvis det primære websted administreres af en VMM server, og derefter dette opnås ved hjælp af netværk tilknytning. Du kan finde flere oplysninger i [Klargør til kort over netværk](site-recovery-network-mapping.md) .

Når du designer netværk for webstedet gendannelse, har administratoren to muligheder:

- Bruge en anden IP-adresseområder for netværket på gendannelse websted. I dette scenarie virtuelt efter failover får en ny IP-adresse og administratoren skulle gøre en DNS-opdatering. Læs mere om, hvordan du gør DNS opdatere [her](site-recovery-vmm-to-vmm.md#test-your-deployment) 
- Brug samme IP-adresseområder for netværket, på webstedet gendannelse. I visse scenarier foretrækker administratorer at bevare de IP-adresser, de har på webstedet primære lige efter sekundære. I et scenarie med normal være en administrator til at opdatere omdirigerer for at angive den nye placering af IP-adresserne. Men i dette scenario, hvor et strakt adskilt installeres mellem primært og gendannelse websteder, bevarer IP-adresserne for virtuelle maskiner bliver en flot indstilling. Holde styr på det samme IP adresser forenkler gendannelse ved at tage væk netværk af enhver relateret indlæg failover trin.


Når administratorer planlægger at implementere en løsning til genoprettelse efter genoprettelse, er en af de vigtigste spørgsmål deres huske hvordan programmerne, der vil være tilgængelig, når sekundære er fuldført. Moderne programmer er næsten altid afhængige af netværk til en vis grad, så fysiske flytning af en tjeneste fra et websted til en anden repræsenterer en netværk udfordring. Der er to måder, som dette problem er behandlet i nedbrud gendannelse løsninger. Den første metode er at vedligeholde fast IP-adresser. På trods af de tjenester, flytte og de hosting servere, der på forskellige placeringer i fysisk tage programmer IP-adressekonfiguration med dem til den nye placering. Den anden metode omfatter helt ændring af IP-adresse under overgangen til webstedet gendannet. Hver enkelt metode har flere implementering variationer, som er beskrevet nedenfor.

Når du designer netværk for webstedet gendannelse, har administratoren to muligheder:

## <a name="option-1-retain-ip-addresses"></a>Mulighed 1: Bevare IP-adresser 

Bruger fast IP-adresser ser ud til at være den nemmeste metode til at implementere fra nedbrud gendannelse proces perspektiv, men det indeholder en række mulige udfordringer som i praksis gør det den mindst populære tilgang. Azure gendannelse af websteder giver mulighed for at bevare IP-adresserne i alle scenarier. Før en beslutter at bevare IP-adresse, bør relevante tanken lægges til den pågældende på failoveregenskaber betingelserne. Lad os se på de faktorer, der kan hjælpe dig med at sikre en beslutning om at bevare IP-adresser eller ej. Dette kan ske på to måder, ved hjælp af et strakt undernet eller ved at gøre en fuld undernet failover.

### <a name="stretched-subnet"></a>Strakt undernet

Her er undernettet gjort tilgængelige samtidig i både primær og DR placeringer. Kort fortalt betyder det, kan du flytte en server og dens IP-(Layer 3) konfiguration til det andet websted og netværket dirigere trafik til den nye placering automatisk. Dette er trivial håndtere fra en server perspektiv, men det har et antal udfordringer:

- Den kræver netværksudstyr, der kan administrere et strakt adskilt fra et lag 2 (data link layer) perspektiv, men det er blevet mindre problem som den er nu alment tilgængelig. Anden og sværere problemet er, ved at strække VLAN'ET potentielle fejl domænet er udvidet til begge steder grundlæggende blive for fejl fra ét sted. Det er et sandsynligt forekomst, er det sket, en broadcast storm startet, men ikke kunne isolerede. Vi har fundet blandede udtalelser om problemet sidste og har set mange vellykket implementering samt "Vi vil aldrig gennemføre denne teknologi her".
- Strakt undernet er ikke muligt, hvis du bruger Microsoft Azure som DR-websted.


### <a name="subnet-failover"></a>Undernet failover

Det er muligt at implementere undernet failover for at få fordelene ved løsningen strakt undernet er beskrevet ovenfor, uden at sprede undernettet på tværs af flere websteder. Her ville en given undernet være til stede på webstedet 1 eller 2, men aldrig på begge websteder samtidig. For at bevare IP-adresseområder i tilfælde af en failover, er det muligt at arrangere fra et program til router infrastruktur til at flytte undernet fra et websted til en anden. Beskyttet FOS i et scenarie med failover undernet ville flytte med den tilknyttede. Den primære ulempe denne fremgangsmåde er i tilfælde af en fejl, der er du nødt til at flytte det hele undernet, som kan være OK, men det kan påvirke failover granularitet overvejelser. 

Lad os undersøge, hvordan en fiktiv virksomhed med navnet Contoso er kan replikeres dens FOS til en gendannelse placering under fejlbehæftede over det hele undernet. Vi vil først se på hvordan Contoso er i stand til at administrere deres undernet, mens replikering FOS mellem to lokale placeringer, og derefter vil vi diskuterer, hvordan undernet failover fungerer hvornår [Azure bruges som webstedet nedbrud gendannelse](#failover-to-azure).

#### <a name="failover-to-a-secondary-on-premises-site"></a>Failover til et sekundært lokale websted

Lad os se på et scenarie hvor vi vil bevare IP-adresse for hver af FOS og fejl over det komplette undernet sammen. Det primære websted har programmer, der kører i undernet 192.168.1.0/24. Når sekundære sker, alle virtuelle maskiner, der er en del af dette undernet bliver ikke gennemført til webstedet, gendannelse og bevare deres IP-adresser. Omdirigerer nødt til at være korrekt ændret for at afspejle, at alle de tilhører undernet 192.168.1.0/24 virtuelle-computere har nu flyttes til webstedet, gendannelse. 

I nedenstående illustration har omdirigerer mellem primære websted og gendannelse websted, tredje websted og primære websted og tredje websted og gendannelse websted ændres korrekt. 

De følgende billeder viser undernet før sekundære. Undernet 192.168.0.1/24 er aktiv på det primære websted før sekundære og aktiveres webstedet gendannelse efter sekundære 

![Før Failover](./media/site-recovery-network-design/network-design2.png)

Før failover


Billedet nedenfor viser netværk og undernet efter failover.
    
![Efter Failover](./media/site-recovery-network-design/network-design3.png)

Efter failover

Sekundær på webstedet er i det lokale miljø og du bruger en VMM server til at administrere den derefter når du aktiverer beskyttelse til en bestemt virtuel maskine, ASR tildeler netværk ressourcer efter arbejdsprocessen for følgende:

- ASR tildeler en IP-adresse for hver netværksgrænseflade på den virtuelle maskine fra den statiske IP-adressegruppe, der er defineret i det relevante netværk for hver System Center VMM forekomst.
- Hvis administratoren definerer den samme IP-adressegruppe for netværket, på webstedet gendannelse som IP-adressegruppe af netværket på webstedet primære, mens tildeling af IP-adressen til den replika virtuelle maskine ASR vil tildele den samme IP-adresse som den primære virtuelle maskine.  IP-adresse er reserveret i VMM, men er angivet ikke som failover IP-adresse. Failover IP angives lige før sekundære.

![Bevare IP-adresse](./media/site-recovery-network-design/network-design4.png)
    
Figur 5

Figur 5 viser Failover TCP/IP-indstillingerne for virtuelt replika (på konsollen Hyper-V). Disse indstillinger vil blive udfyldt, lige før den virtuelle maskine startes efter en failover

Hvis den samme IP-adresse ikke er tilgængelig, vil ASR fordel nogle andre tilgængelige IP-adresse fra definerede IP-adressegruppe. 

Når VM er aktiveret for beskyttelse kan du bruge følgende eksempel på et script til at kontrollere den IP-adresse, der er afsat til den virtuelle maskine. Den samme IP-adresse er angivet som Failover IP og tildelt til VM samtidig ydelse:

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

>[AZURE.NOTE] I dette scenario, hvor virtuelle maskiner bruger DHCP, er for administration af IP-adresser helt uden for kontrol af ASR. En administrator har til at sikre, at DHCP-serveren fungerer IP-adresser på webstedet gendannelse kan fungere fra det samme område som det primære websted.

#### <a name="failover-to-azure"></a>Failover til Azure

Azure websted gendannelse (ASR) gør det muligt for Microsoft Azure skal bruges som et nedbrud gendannelse websted til din virtuelle computere. I dette tilfælde skal du til at løse en flere begrænsning. 

Lad os undersøge et scenarie, hvor et fiktivt firma med navnet Woodgrove Bank har lokalt infrastruktur vært deres line of business-applikationer, og de er vært for deres mobilprogrammer på Azure. Forbindelsen mellem Woodgrove Bank FOS i Azure og lokale servere, der er angivet af en-til-websted (S2S) VPN virtuelt privat netværk (). S2S VPN kan Woodgrove Bank virtuelt netværk i Azure til betragtes som en udvidelse af Woodgrove Bank lokale netværk. Denne meddelelse er aktiveret af S2S VPN mellem Woodgrove Bank kant og Azure virtuelt netværk. Nu ønsker Woodgrove at bruge ASR for at gentage dens arbejdsbelastninger, som kører i dets datacenter til Azure. Denne indstilling opfylder behovene hos Woodgrove, som ønsker en økonomisk DR-indstilling og er i stand til at gemme data i offentlige skyen miljøer. Woodgrove skal håndtere programmer og konfigurationer, som er afhænger af faste IP-adresser, så de har et krav til at bevare IP-adresser for deres programmer efter fejlbehæftede over til Azure.

Woodgrove har besluttet at tildele IP-adresser fra IP-adresseområder (172.16.1.0/24, 172.16.2.0/24) til dens ressourcer, der kører i Azure.

For Woodgrove skal kunne replikeres dens virtuelle maskiner til Azure samtidig bevare IP-adresserne, skal et virtuelt netværk med Azure oprettes. Det skal være en udvidelse af lokale netværk, så programmer kan skifte fra det lokale websted til Azure problemfrit. Azure gør det muligt at føje til websted samt punkt-til-site VPN-forbindelse til de virtuelle netværk, der er oprettet i Azure. Når du konfigurerer din forbindelse til websted, kan du omdirigere trafikken til den lokale placering (Azure ringer det lokale netværk), kun, hvis IP-adresseområder adskiller sig fra lokal IP-adresseområder, fordi Azure ikke understøtter strækning undernet Azure netværk.  Det betyder, at hvis du har en undernet 192.168.1.0/24 lokalt, du ikke kan tilføje en LAN 192.168.1.0/24 i Azure netværket. Dette er forventet, fordi Azure ikke ved, at der er ingen aktive FOS i undernettet og, at undernettet oprettes kun til DR formål. Hvis du vil kunne korrekt omdirigere netværkstrafik af et netværk med Azure undernet i netværket, og LAN må ikke være i konflikt. 

![Før undernet Failover](./media/site-recovery-network-design/network-design7.png)

Før failover

For at opfylde deres forretningsbehov Woodgrove, har vi brug at implementere de arbejdsprocesser, der er følgende:

- Oprette et ekstra netværk, skal du kontakte den Lad os gendannelse netværk, hvor mislykkedes over virtuelle maskiner skal oprettes.
- Gå til fanen Konfigurer under Egenskaber for VM for at sikre, at IP-adresse til en VM bevares efter en failover, angive den samme IP-adresse, hvor VM har lokalt, og klik derefter på Gem. Når VM er ikke, tildele Azure gendannelse af websteder den medfølgende IP-adresse til den virtuelle maskine. 

![Egenskaber for netværk](./media/site-recovery-network-design/network-design8.png)

Når sekundære udløses og virtuelle maskiner er oprettet i gendannelse netværket med den ønskede IP-adresse, kan forbindelsen til dette netværk oprettes ved hjælp af en [Vnet til Vnet forbindelse](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Hvis det er nødvendigt, kan udføres som script denne handling.  Som beskrevet i forrige afsnit om undernet failover, selv i tilfælde af failover til Azure, skulle ændres korrekt afspejler 192.168.1.0/24 nu er flyttet til Azure omdirigerer. 

![Efter undernet Failover](./media/site-recovery-network-design/network-design9.png)

Efter failover

Hvis du ikke har et 'Azure netværk', som vist på billedet ovenfor. Du kan oprette et websted til vpn-forbindelse mellem din 'Primary Site' og 'Gendannelse netværk' efter sekundære.  


## <a name="option-2-changing-ip-addresses"></a>Mulighed 2: Ændre IP-adresser

Denne metode ser ud til at være mest almindeligt baseret på hvad vi har set. Det tager form af ændring af IP-adressen for hver VM, der er involveret i sekundære. En ulempe ved denne fremgangsmåde kræver det indgående netværk til 'få', at det program, der var på IPx er nu på IPy. Selvom IPx og IPy er logiske navne, DNS-poster har typisk ændres eller trækkes i hele netværket, og cachelagrede poster i netværk tabeller skal opdateres eller trækkes, en nedetid kan derfor ses, afhængigt af hvordan DNS-infrastrukturen er konfigureret. Disse problemer kan mindskes ved ved hjælp af lave TTL-værdier i forbindelse med intranet-programmer og [Azure trafik Manager vha](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) til Internetbaserede programmer

### <a name="changing-the-ip-addresses---illustration"></a>Ændre IP-adresser - Illustration

Lad os se på dette scenario sted, hvor du planlægger at bruge forskellige IP'er på tværs af primært og steder, gendannelse. I følgende eksempel vi også har en tredje websted fra hvor programmerne hostes på primær eller gendannelse kan få adgang til webstedet.

![Forskellige IP - før Failover](./media/site-recovery-network-design/network-design10.png)

Figur 11

Der er nogle programmer, der er vært undernet 192.168.1.0/24 undernet på webstedet primære i figur 11, og de har konfigureret til at komme frem på webstedet gendannelse i undernet 172.16.1.0/24 efter en failover. VPN-forbindelser/netværk omdirigerer er blevet konfigureret korrekt, så alle tre websteder kan få adgang til hinanden.
 
Som figur 12 vises, efter at de mislykkedes over en eller flere programmer, vil de blive gendannet i gendannelse undernet. I dette tilfælde er vi ikke begrænset Sådan flytter du det hele undernet på samme tid. Der kræves ingen ændringer at omkonfigurere VPN- eller netværksproblemer omdirigerer. En failover og nogle DNS-opdateringerne sikrer, at programmer stadig er tilgængelig. Hvis DNS-Posterne er konfigureret til at tillade dynamiske opdateringer vil derefter virtuelle maskiner registrere sig selv ved hjælp af den nye IP-adresse, når de starter efter en failover. 

![Forskellige IP - efter Failover](./media/site-recovery-network-design/network-design11.png)

Figur 12

Virtuelt replika muligvis efter fejlbehæftede over en IP-adresse, der ikke er den samme som den primære virtuelle maskine IP-adresse. Virtuelle maskiner opdaterer DNS-serveren, de bruger, når de starter. DNS-poster har typisk ændres eller trækkes i hele netværket, og cachelagrede poster i netværk tabeller skal opdateres eller tømt, så det ikke er usædvanlige gælder med nedetid, mens ændringerne tilstand foregå. Dette problem kan mindskes ved at:

- Brug af lave TTL-værdier for intranetprogrammer.
- Ved hjælp af Azure trafik Manager vha til internet baserede programmer.
- Ved hjælp af følgende script i tidsplanen gendannelse opdatere DNS-serveren for at sikre en tiden opdatering (scriptet ikke er påkrævet, hvis dynamisk DNS-registrering er konfigureret)

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


### <a name="changing-the-ip-addresses--dr-to-azure"></a>Ændre IP-adresser – DR til Azure

Blogindlæg [Netværk infrastruktur installationen af Microsoft Azure som et websted, efter nedbrud gendannelse](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) forklarer, hvordan du konfigurerer den nødvendige Azure netværksinfrastruktur, når bevarelse IP-adresser er ikke et krav. Det starter med programmet, der indeholder en beskrivelse og derefter se på, hvordan du konfigurerer netværk lokalt og på Azure og derefter afsluttede med Sådan foretager en test failover og en planlagt belastningsjustering.



## <a name="next-steps"></a>Næste trin

[Få mere at vide](site-recovery-network-mapping.md) hvordan gendannelse af websteder kort kilde- og destinationswebsteder netværk når en VMM server der anvendes til at administrere det primære websted. 
