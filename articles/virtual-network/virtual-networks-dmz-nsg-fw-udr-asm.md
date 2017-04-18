<properties
   pageTitle="Eksempel på DMZ – oprette en DMZ at beskytte netværk med en Firewall, UDR og NSG | Microsoft Azure"
   description="Oprette en DMZ med en Firewall, brugerdefinerede Routing (UDR) og netværk-sikkerhedsgrupper (NSG)"
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
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Eksempel 3 – oprette en DMZ at beskytte netværk med en Firewall, UDR og NSG

[Gå tilbage til sikkerhed rammen bedste fremgangsmåder side][HOME]

I dette eksempel opretter en DMZ med en firewall, fire windows-servere, brugerdefineret Routing, IP-videresendelse og netværk sikkerhedsgrupper. Den fører også med hver af de relevante kommandoer, der giver en bedre forståelse af hvert enkelt trin. Der er også en trafik scenarie afsnit for at give en detaljeret trinvise hvordan trafik forløber gennem lag af beskyttelse i DMZ. Til sidst i referencer er sektion fuldført kode og instruktionerne til at oprette dette miljø for at teste og eksperimentere med forskellige scenarier. 

![Tovejs-DMZ med for NSG og UDR][1]

## <a name="environment-setup"></a>Konfiguration af miljø
I dette eksempel er der et abonnement, der indeholder følgende:

- Tre cloud services: "SecSvc001", "FrontEnd001" og "BackEnd001"
- Et virtuelt netværk "CorpNetwork" med tre undernet: "SecNet", "FrontEnd" og "Back end"
- Et netværk virtuelle produktet i dette eksempel en firewall, der er forbundet til SecNet undernettet
- En Windows-Server, der repræsenterer en programserver web ("IIS01")
- To vinduer servere, der repræsenterer program igen afslutte servere ("AppVM01", "AppVM02")
- En Windows-server, der repræsenterer en DNS-server ("DNS01")

I afsnittet referencer under er der en PowerShell-script, der bygger mest muligt ud af det miljø, der er beskrevet ovenfor. Opbygning af FOS og virtuelle netværk, selvom udføres af eksempel på et scriptet, der ikke er beskrevet mere detaljeret i dette dokument.

Sådan oprettes miljøet:

  1.    Gemme den netværk config XML-fil, der er inkluderet i afsnittet referencer (opdateret med navne, placering og IP-adresser, så det svarer til det angivne scenario)
  2.    Opdatere Brugervariabler i scriptet til at matche miljøet er scriptet til at blive kørt mod (abonnementer, tjenestenavne osv.)
  3.    Udførelse af script i PowerShell

**Bemærk**: det område, der er angivet i PowerShell-script skal passer til det område, der er angivet i netværk konfiguration XML-fil.

Når scriptet kører tages efter script følgende trin:

1.  Konfigurere firewallreglerne, er dette dækket i afsnittet nedenfor: Firewall regelbeskrivelsen.
2.  Du kan også er to scripts til at konfigurere webserver og app-server med et enkelt webprogram til at tillade test med denne DMZ konfiguration i sektionen referencer.

Når scriptet kører firewallen regler skal være fuldført, dette omtales i afsnittet: firewallregler.

## <a name="user-defined-routing-udr"></a>Brugerdefinerede Routing (UDR)
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

VNETLocal er altid defineret adresse prefix(es) af VNet for den specifikke netværk (ie ændres det fra VNet til VNet afhængigt af hvordan hver bestemte VNet er defineret). De resterende system omdirigerer er statisk og standard som ovenfor.

Som for prioritet, omdirigerer behandles via metoden længste præfiks Match (LPM), og som derfor mest specifikke ruten i tabellen skal gælde for en given destinationsadresse.

Derfor skal trafik (for eksempel til DNS01-serveren, 10.0.2.4) beregnet til det lokale netværk (10.0.0.0/16) sendes på tværs af VNet til sin destination på grund af 10.0.0.0/16 ruten. Med andre ord for 10.0.2.4 er 10.0.0.0/16 ruten den mest specifikke rute, selvom 10.0.0.0/8 og 0.0.0.0/0 også kan anvende, men fordi de er mindre bestemte de ikke påvirker denne trafik. Dermed vil trafik til 10.0.2.4 har en næste hop for den lokale VNet og blot omdirigere til destinationen.

Hvis trafik er beregnet til 10.1.1.1 for eksempel, 10.0.0.0/16 ruten vil ikke kunne anvende, men 10.0.0.0/8 ville være den mest specifikke og dette trafikken ville være ignoreret ("sort dig i bås"), da det næste trin er Null. 

Hvis destinationen ikke gælder for nogen af Null-præfikser eller VNETLocal præfikser og derefter den følge den mindst specifikke distribuere 0.0.0.0/0 og sendes til internettet, som det næste trin og dermed ud Azure's internet kant.

Hvis der er to identiske præfikser i rutetabellen, er følgende rækkefølgen for indstilling, der er baseret på attributten omdirigerer "kilde":

1.  "VirtualAppliance" = en bruger defineret rute, der er føjet til tabellen manuelt
2.  "VPNGateway" = en dynamisk rute (BGP når den bruges med hybrid netværk) tilføjet af en dynamisk netværksprotokol disse omdirigerer måske ændres med tiden som dynamiske protokollen automatisk afspejler ændringer i peered netværk
3.  "Standard" = System omdirigerer, lokale VNet og de statiske elementer, som vist i rutetabellen ovenfor.

>[AZURE.NOTE] Du kan nu bruge bruger defineret Routing (UDR) med ExpressRoute og VPN Gateways tvinge udgående og indgående kryds-premise-trafik skal dirigeres til en netværk virtuelle maskinen (for).

#### <a name="creating-the-local-routes"></a>Oprette lokale omdirigerer

I dette eksempel, der er behov for to routing tabeller, en for front end- og back end-undernet. Alle tabeller, er indlæst med statisk omdirigerer passer til det angivne undernet. I dette eksempel skal har hver tabel tre omdirigerer:

1. Lokale undernet trafik med ingen Next Hop defineret til at tillade lokale undernet trafik kan springe firewallen
2. Virtuel netværkstrafikken med en Next Hop defineret som firewall, dette tilsidesætter den standard-regel, der gør det muligt for lokale VNet trafik til at dirigere direkte
3. Alle resterende trafik (0/0) med en Next Hop defineret som firewallen

Når de routing-tabeller er oprettet, som de er bundet til deres undernet. For front end-undernettet ser routing-tabel, når oprettet og bundet til undernettet sådan ud:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Følgende kommandoer i dette eksempel skal bruges til at oprette rutetabellen, tilføje en brugerdefineret rute og derefter binde rutetabellen til et undernet (Note; eventuelle elementer under, der begynder med et dollartegn (f.eks.: $BESubnet) er brugerdefinerede variabler fra scriptet i afsnittet reference i dette dokument):

1.  Først skal routing Basistabellen oprettes. Denne kodestykke viser oprettelse af tabellen til back end-undernet. I scriptet oprettes en tilsvarende tabel også til front end-undernet.

        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"

2.  Når rutetabellen er oprettet, kan du tilføje bestemte brugerdefinerede omdirigerer. I denne eksempelboks, sendes al trafik (0.0.0.0/0) via virtuelle maskinen (en variabel, $VMIP [0], der bruges til at videregive IP-adressen, der er tildelt, da virtuelle maskinen blev oprettet tidligere i script). I scriptet oprettes der også en tilsvarende regel i tabellen front end.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

3. Posten ovenfor distribuere tilsidesætter standard "0.0.0.0/0" ruten, men stadig eksisterende standard 10.0.0.0/16 reglen, vil tillade trafik i VNet til at dirigere direkte til destinationen og ikke til netværket virtuel maskinen. At rette problemet Følg reglen skal tilføjes.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

4. På dette tidspunkt er der et valg skal foretages. Med de ovenfor to omdirigerer dirigere al trafik til firewallen for vurdering, lige trafik inden for et enkelt undernet. Dette muligvis ønske, men der tillader trafik inden for et undernet til at dirigere lokalt uden at involvere fra firewallen en tredje, meget bestemte reglen kan tilføjes. Dette stater, som alle adresserne destine til det lokale undernet bare kan distribuere der direkte (NextHopType = VNETLocal).

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal

5.  Til sidst med routing tabellen oprettet og udfyldt med en brugerdefineret omdirigerer, skal tabellen nu være bundet til et undernet. I scriptet er front-end distribuere tabellen også bundet til front end-undernet. Her er binding scriptet til back-end-undernet.

        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
           -SubnetName $BESubnet `
           -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>IP-videresendelse
En companion funktion til UDR, er IP-videresendelse. Dette er en indstilling på et virtuelt enhed, der gør det muligt at modtage trafik ikke specifikt er adresseret til maskinen og derefter videresende pågældende trafik til destinationen ultimate.

Som et eksempel, hvis trafik fra AppVM01 giver en anmodning til serveren DNS01 vil UDR omdirigere dette til firewallen. Med IP-videresendelse aktiveret, bliver trafik til destinationen DNS01 (10.0.2.4) accepteres ved maskinen (10.0.0.4) og derefter videresendt til destinationen ultimate (10.0.2.4). Uden IP-videresendelse er aktiveret i firewallen, vil trafik ikke accepteres ved maskinen, selvom rutetabellen har firewallen som det næste trin. 

>[AZURE.IMPORTANT] Det er vigtigt at huske at aktivere IP-videresendelse sammen med brugerdefineret Routing.

Konfigurere videresendelse af IP-adresse er en enkelt kommando og kan udføres på klokkeslættet for oprettelsen af VM. For strømmen af dette eksempel kodestykket mod slutningen af scriptet og grupperet med kommandoerne UDR:

1.  Ringe til den VM-forekomst, der er virtuelle anvendelsen, firewallen i dette tilfælde, og Aktivér IP-viderestilling af (Note; et element i rød, der begynder med et dollartegn (f.eks.: $VMName[0]) er en brugerdefineret variabel fra scriptet i afsnittet reference i dette dokument. Nul i kantede parenteser, [0], repræsenterer den første VM i matrixen af FOS, for eksempel scriptet til at fungere uden ændringer, hvor første VM (VM 0) skal være firewallen):

        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
           Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Netværk sikkerhedsgrupper (NSG)
I dette eksempel er en NSG gruppe indbygget og derefter indlæst med en enkelt regel. Denne gruppe er derefter bundet kun til front end- og back end-undernet (ikke SecNet). Med en erklæring er der oprettet på følgende regel:

1.  Al trafik (alle porte) fra internettet til hele VNet (alle undernet) er nægtet

Selvom NSGs bruges i dette eksempel, er det er Hovedformålet som en sekundær lag af beskyttelse mod forkert manuel konfiguration. Vi vil blokere alle indgående trafik fra internettet til enten Frontend eller back end-undernet, trafik skal kun passerer gennem SecNet undernet til firewallen (og hvis det er relevant på Frontend eller back end-undernet). Desuden med UDR regler på plads, ville al trafik, der lave dem til Frontend eller back end-undernet dirigeret ud til firewallen (sammen takket være UDR). Firewallen skal se ud som en asymmetrisk flow og vil slippe den udgående trafik. Derfor er der tre sikkerhedslag beskytte front end- og back end-undernet 1) nogen Åbn slutpunkter på FrontEnd001 og BackEnd001 cloud services, 2) NSGs nægte trafik fra internettet, 3) firewall slippe asymmetrisk trafikken.

Et interessant punkt vedrørende sikkerhedsgruppen netværk i dette eksempel er, at den indeholder kun én regel, som er vist nedenfor, som er nægte internettrafik til hele virtuelle netværket, medtages sikkerhed undernet. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Da NSG er kun bundet til front end- og back end-undernet, reglen ikke behandles på trafik indgående til sikkerhed undernet. Som et resultat, selvom NSG reglen står ingen internettrafik til en anden adresse på VNet, fordi NSG blev aldrig bundet til sikkerhed undernet, bliver trafik dataflow til sikkerhed undernet.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName
    
    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Firewallregler
På firewallen skal regler for videresendelse oprettes. Da firewallen blokerer eller viderestilling af alle indgående, udgående og handel VNet trafik mange firewallregler er det er nødvendigt. Desuden påløber al indgående trafik Security Service offentlige IP-adresse (på forskellige porte), hvis du vil blive behandlet af firewallen. Den bedste fremgangsmåde er til at oprette diagrammer logiske flyder før du konfigurerer undernet og firewallregler for at undgå at arbejde med senere. I følgende figur vises er en logisk visning af firewallregler for dette eksempel:
 
![Logiske visning af firewallreglerne][2]

>[AZURE.NOTE] Baseret på netværk maskinen virtuel bruges varierer management porte. I dette eksempel, der refereres til en Barracuda NextGen Firewall som bruger port 22, 801 og 807. Se i dokumentationen til maskinen leverandør for at finde ud af præcis porte, der bruges til styring af den enhed, der bruges.

### <a name="logical-rule-description"></a>Logiske regelbeskrivelse
I den logiske diagrammet ovenfor vises sikkerhed undernet ikke siden firewallen er den eneste ressource på det pågældende undernet, og dette diagram vises firewallreglerne, og hvordan de logisk tillade eller nægte trafik-flow og ikke den aktuelle distribuerede sti. Også de eksterne porte, der er valgt for RDP-trafik er placeret højere varierede porte (8014 – 8026) og blev valgt til at justere noget med de sidste to oktetter i nemmere læsbarhed lokal IP-adresse (f.eks. lokale serveradressen 10.0.1.4 er knyttet til eksterne port 8014), men en hvilken som helst højere ikke er i konflikt porte kan bruges.

I dette eksempel skal vi skal bruge 7 typer regler, disse regeltyper er beskrevet på følgende måde:

- Eksterne regler (for indgående trafik):
  1.    Firewall Management regel: Denne App Omdiriger regel tillader trafik til at overføre til management porte af netværk virtuelle maskinen.
  2.    RDP regler (for hver windows server): disse fire regler (én for hver server) vil tillade styring af de enkelte servere via RDP. Dette kan også samlet i én regel afhængigt af funktionerne i netværk maskinen virtuel bruges.
  3.    Programmet trafikregler: Der er to program trafikregler for først til front-end webtrafik, og andet til back-end-trafik (f.eks webserver til data niveau). Konfiguration af disse regler, afhænger af netværk arkitekturen (hvor serverne er placeret) og trafik flyder (hvilken retning trafik-flow, og hvilke porte bruges).
      - Den første regel, så den faktiske anvendelse trafik når application server. Mens de andre regler tillader til sikkerhed, administration, osv., programmet regler, der er, hvad give eksterne brugere eller tjenester til at få adgang til program(mer). I dette eksempel, der er en enkelt webserver på port 80, og som derfor en enkelt firewall programmet regel omdirigerer indgående trafik til den eksterne IP til web-servere interne IP-adresse. Omdirigeret trafik sessionen ville være NAT skulle den interne server.
      - Den anden programmet trafik regel er back-end-reglen, der tillader webserveren til at tale med AppVM01 server (men ikke AppVM02) via en hvilken som helst port.
- Interne regler (for handel VNet trafik)
  4.    Udgående til Internet regel: denne regel vil tillade trafik fra en hvilken som helst netværk, der skal overføres til de valgte netværk. Denne regel er som regel en standard-regel allerede på firewallen, men i deaktiveret tilstand. Denne regel skal være aktiveret i dette eksempel.
  5.    DNS-regel: Denne regel kan kun DNS (port 53) trafik til at overføre til DNS-serveren. For denne-miljø de fleste trafik fra front end til backend-version er blokeret, kan denne regel specifikt DNS fra en hvilken som helst lokale undernet.
  6.    Undernet til undernet regel: denne regel er at tillade en hvilken som helst server på back-end-undernet at oprette forbindelse til en hvilken som helst server i front-end-undernettet (men ikke omvendt).
- Fejlsikret regel (for trafik, der ikke opfylder en af ovenstående):
  7.    Afvise alle trafik regel: Dette skal altid være den endelige regel (med hensyn til prioritet), og som sådan Hvis en trafik flyder mislykkes, så det svarer til de foregående regler, der fjernes af denne regel. Dette er en standard-regel og normalt aktiveret nogen ændringer er normalt nødvendige.

>[AZURE.TIP] En hvilken som helst port er tilladt for nem i dette eksempel, i et scenarie med reelle den mest specifikke port på den anden programmet trafik regel og -adresseområder skal bruges til at reducere angrebsoverfladen af denne regel.

<br />

>[AZURE.IMPORTANT] Når alle ovenstående reglerne er oprettet, er det vigtigt at gennemse prioriteten af hver regel for at sikre, at trafik kan tillades eller afvises efter behov. I dette eksempel er reglerne i prioritetsrækkefølge. Det er nemt at være låst af firewallen på grund af forkert bestilte regler. Som minimum skal sikre, at administration for firewallen selve er altid reglen absolutte højeste prioritet.

### <a name="rule-prerequisites"></a>Forudsætninger for regel
En betingelse for den virtuelle maskine kører firewallen er offentlige slutpunkter. I firewallen til at behandle trafik skal relevante offentlige slutpunkterne være åben. Der findes tre typer trafik i dette eksempel 1) management trafik til kontrol af firewallen og firewallregler, 2) RDP trafik til at styre windows-servere og 3) programmet trafik. Dette er kolonnerne tre typer trafik i øverst del af logiske visning af firewallregler over.

>[AZURE.IMPORTANT] En vigtige takeway er at huske, at **al** trafik kommer gennem firewallen. Så til Fjernskrivebord til IIS01-serveren, vil selvom det er i den Front End-skytjeneste og på Front End-undernet, for at få adgang til denne server vi skal RDP til firewallen på port 8014 og derefter tillade firewall til at dirigere RDP anmodningen internt til IIS01 RDP-Port. Portalen Azure "Connect" knappen virker ikke, fordi der ikke er nogen direkte RDP-sti til IIS01 (den udstrækning portalen kan se). Det betyder, at alle forbindelser fra internettet vil være til tjenesten sikkerhed og en Port, f.eks. secscv001.cloudapp.net:xxxx (reference diagrammet ovenfor til tilknytning af eksterne Port og interne IP-adresse og Port).

Et slutpunkt kan åbnes enten på tidspunktet for oprettelse af VM eller stille build, som er udført i eksempel scriptet og vist nedenfor i denne kodestykke (Note; en hvilken som helst element, der begynder med et dollartegn (f.eks.: $VMName[$i]) er en brugerdefineret variabel fra scriptet i afsnittet reference i dette dokument. "$I" i parenteser, [$i] repræsenterer matrix antallet af en bestemt VM i en matrix med FOS):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Selvom ikke klart vises her på grund af brugen af variabler, men slutpunkter er **kun** åbnet på Skytjenesten sikkerhed. Dette er at sikre, at al indgående trafik håndteres (distribueret, NAT havde, sænket) ved firewallen.

En management-klienten skal være installeret på en PC til at administrere firewallen og oprette de konfigurationer, det er nødvendigt. Se dokumentationen fra din firewall (eller andre for) leverandør om til styring af enheden. Resten af dette afsnit og næste afsnit, Firewall oprettelse af regler, som beskriver konfigurationen af sig selv firewallen gennem leverandører management-klienten (det vil sige ikke Azure portal eller PowerShell).

Vejledning til klienten download og oprette forbindelse til Barracuda i dette eksempel kan findes her: [Barracuda viser administrator](https://techlib.barracuda.com/NG61/NGAdmin)

Når du er logget på firewallen, men før du opretter firewallregler, er der to nødvendige objekt klasser, som kan gøre oprettelse af reglerne nemmere; Netværk og Service objekter.

I dette eksempel skal skal tre navngivet netværksobjekter være defineret (én for front end-undernet og back end-undernet, også et netværk objekt som IP-adressen til DNS-serveren). Oprette et navngivet netværk. begyndende fra klient Barracuda viser administratordashboard, gå til fanen konfiguration, klik på regelsæt i sektionen funktionel konfiguration, derefter skal du klikke på "Netværk" under menuen Firewall objekter og derefter klikke på ny i menuen Rediger netværk. Netværksobjektet kan nu oprettes, ved at tilføje navnet og præfikset:

![Oprette et netværk front end-objekt][3]
 
Dette vil oprette et navngivet netværk for front end-undernet, skal der oprettes et lignende objekt for back end-undernettet. Nu undernet kan være nemmere refereres til af navnet på firewallreglerne.

For objektet DNS-Server:

![Oprette en DNS-serverobjekt][4]

Denne enkelt IP-adresse reference bruges i en regel for DNS-senere i dokumentet.

De anden nødvendige objekter er Services-objekter. Disse repræsenterer RDP forbindelse porte for hver server. Da eksisterende RDP serviceobjekt er bundet til RDP standardporten, kan 3389, nye tjenester, der oprettes for at tillade trafik fra de eksterne porte (8014-8026). De nye porte kan også tilføjes til eksisterende RDP-tjenesten, men for at lette demonstration, der kan oprettes en enkelt regel for hver server. Oprette en ny RDP regel for en server. Start fra klient Barracuda viser administratordashboard, og gå til fanen konfiguration, i sektionen funktionel konfiguration Klik regelsæt, og klik derefter på "Services" under menuen Firewall objekter, Rul ned på listen over tjenester og vælg tjenesten, "RDP". Højreklik på og vælg Kopiér, højreklik, og vælg Sæt ind. Der er nu et RDP-Copy1 Service-objekt, der kan redigeres. Højreklik på RDP-Copy1, og klik på Rediger Service Rediger objekt-vinduet åbnes op, som vist her:

![Kopi af standard RDP regel][5]

Værdierne, der kan derefter redigeres for at repræsentere tjenesten RDP til en specifik server. For AppVM01 skal være ændret afspejler en ny tjenestenavn, beskrivelse og eksterne RDP Port, der bruges i figur 8 diagrammet ovenfor standard RDP reglen (Bemærk: portene, ændres fra RDP standardværdien på 3389 til den eksterne port anvendes på denne bestemte server, hvis det er AppVM01 den eksterne Port er 8025) ændret tjenesten er vist nedenfor :

![AppVM01 regel][6]
 
Denne proces skal gentages, til at oprette RDP tjenester for de resterende servere AppVM02, DNS01 og IIS01. Oprettelse af disse tjenester vil gøre oprettelse regel nemmere og mere tydelig i næste afsnit.

>[AZURE.NOTE] En RDP-tjeneste til firewallen ikke er nødvendigt af to årsager 1) første firewallen VM er en Linux baseret billede, så SSH vil blive brugt på port 22 for VM administration i stedet for RDP og 2) port 22 og to andre management-porte er tilladt i den første management-regel, der er beskrevet nedenfor til at tillade styring connectivity.

### <a name="firewall-rules-creation"></a>Oprettelse af firewall regler
Der findes tre typer firewallregler i dette eksempel bruges, de alle har entydige ikoner:

Programmet omdirigere reglen: ![Omdirigere programikon][7]

Destination NAT reglen: ![Destination NAT-ikon][8]

Gang reglen: ![Overfører ikon][9]

Flere oplysninger om disse regler kan findes på webstedet Barracuda.

For at oprette følgende regler (eller bekræfte eksisterende standardregler), begyndende fra Barracuda viser klient administratordashboard, gå til fanen konfiguration, afsnit Klik på regelsæt i funktionel konfiguration. Et gitter der hedder "Main regler" viser de eksisterende aktive og deaktiverede regler på denne firewall. I øverste højre hjørne af dette gitter er en lille, grøn "+" knappen, Klik her for at oprette en ny regel (Bemærk: din firewall muligvis "låst" for ændringer, hvis du kan se knappen "Låse" er markeret, og du kan ikke oprette eller redigere regler, skal du klikke på denne knap for at "låse" regelsættet og Tillad redigering). Hvis du vil redigere en eksisterende regel, vælg reglen, højreklik, og vælg Rediger regel.

Når reglerne er blevet oprettet og/eller ændret, de skal overføres til firewallen og derefter aktiveret, hvis dette ikke er gjort reglen ændres ikke træder i kraft. Processen opslagsnål og aktivering er beskrevet nedenfor detaljer regel beskrivelserne.

Detaljerne for hver regel, der er nødvendige for at fuldføre dette eksempel er beskrevet på følgende måde:

- **Firewall Management regel**: denne App omdirigere regel tillader trafik til at overføre til af administrationsporte af netværk virtuelle produktet i dette eksempel Barracuda NextGen Firewall. Administrationsporte er 801, 807 og eventuelt 22. De eksterne og interne porte er de samme (det vil sige ingen Portoversættelse). Dette regel adgang-MGMT-konfiguration, er en standard-regel og aktiveret som standard (i Barracuda NextGen Firewall version 6.1).

    ![Firewall Management regel][10]

>[AZURE.TIP] Kilde-adresseområde i denne regel er Any, hvis det er blevet konstateret management IP-adresseområder, reduceres dette område vil også formindske angrebsoverfladen til af administrationsporte.

- **RDP regler**: disse Destination NAT regler vil tillade styring af de enkelte servere via RDP.
Der er fire kritiske felter, der bruges til at oprette denne regel:
  1.    Kilde – for at tillade RDP hvor som helst, referencen "En" bruges i feltet kilde.
  2.    Tjenesten – bruge det relevante Service-objekt, der har oprettet tidligere i dette tilfælde "AppVM01 RDP", de eksterne porte omdirigere til den lokale servere IP-adresse og til port 3386 (standardporten for RDP). Denne bestemt regel er til RDP adgang til AppVM01.
  3.    Destination – skal være "den *lokale port firewallen*, DCHP 1 lokal IP-adresse" eller eth0, hvis bruger statiske IP-adresser. Ordenstal (eth0, eth1 osv.) kan være forskellige, hvis dit netværk maskinen har flere lokale grænseflader. Dette er den port firewallen udsender fra (kan være den samme som den modtagelse port), er den faktiske distribuerede destination i feltet mållisten.
  4.    Omdirigering – i dette afsnit beskrives virtuelle maskinen til at omdirigere denne trafik i sidste ende. Den nemmeste omdirigering er at placere den IP-adresse og Port (valgfrit) i feltet mållisten. Hvis ingen port ikke bruges destinationsporten på indgående anmodning bliver brugt (ie ingen oversættelse), hvis en port er udpeget port vil også være NAT ville sammen med IP-adresse adresse.

    ![Firewall RDP regel][11]

    Op til fire RDP regler skal oprettes: 

  	|   Regelnavn    | Server  |   Tjenesten   |  Målliste  |
  	|----------------|---------|-------------|---------------|
  	| RDP-IIS01   | IIS01   | IIS01 RDP   | 10.0.1.4:3389 |
  	| RDP-DNS01   | DNS01   | DNS01 RDP   | 10.0.2.4:3389 |
  	| RDP-AppVM01 | AppVM01 | AppVM01 RDP | 10.0.2.5:3389 |
  	| RDP-AppVM02 | AppVM02 | AppVm02 RDP | 10.0.2.6:3389 |
  
>[AZURE.TIP] Indsnævre ned omfanget af felterne kilde- og vil reducere angrebsoverfladen. Den mest begrænset omfang, der tillader funktionalitet skal bruges.

- **Programmet trafikregler**: der er to program trafikregler for først til front-end webtrafik, og andet til back-end-trafik (f.eks webserver til data niveau). Disse regler, som er afhængige af netværk arkitekturen (hvor serverne er placeret) og trafik flyder (hvilken retning trafik-flow, og hvilke porte bruges).

    Front-end reglen for webtrafik er først diskuteres:

    ![Firewall Web regel][12]
 
    Denne regel Destination NAT tillader faktiske anvendelse trafikken når application server. Mens de andre regler tillader til sikkerhed, administration, osv., programmet regler, der er, hvad give eksterne brugere eller tjenester til at få adgang til program(mer). I dette eksempel, der er en enkelt webserver på port 80, og som derfor enkelt firewall programmet reglen omdirigerer indgående trafik til den eksterne IP til web-servere interne IP-adresse.

    **Bemærk**:, der er ingen port, der er tildelt i feltet mållisten, og som derfor den indgående port 80 (eller 443 for tjenesten markeret) der skal bruges i omdirigering af webserveren. Hvis webserveren lytter på en anden port, for eksempel port 8080, kan feltet mållisten blev opdateret til 10.0.1.4:8080 til at tillade til Port omdirigering.

    Næste program trafik reglen er back-end-reglen, der tillader webserveren til at tale med AppVM01 server (men ikke AppVM02) via en tjeneste:

    ![Firewall AppVM01 regel][13]

    Denne gang regel giver mulighed for en hvilken som helst IIS-server på front end-undernet at nå AppVM01 (IP-adresse 10.0.2.5) på en hvilken som helst port ved hjælp af en vilkårlig protokol til access-data, der bruges til webprogrammet.

    I dette skærmbillede en "\<eksplicit dest\>" bruges i destinationsfeltet til at angive 10.0.2.5 som destination. Det kan være enten eksplicitte som vist eller et navngivet netværk objekt (som er blevet udført i forudsætninger for DNS-server). Dette er op til administratoren af firewallen om, hvilken metode skal anvendes. Hvis du vil tilføje 10.0.2.5 som en Explict Desitnation, skal du dobbeltklikke på den første tomme række under \<eksplicitte dest\> , og skriv adressen i det vindue, der vises.

    Med denne overfører regel, der er ingen NAT behov da dette er intern trafik, så metoden forbindelse kan være indstillet til "Ingen SNAT".

    **Bemærk**: den kilde netværk i denne regel er en ressource på front end-undernet, hvis der kun er en eller et bestemt kendte antal-servere, en ressource netværk objekt blev oprettet for at være mere specifikke for de nøjagtige IP-adresser i stedet for det hele front end-undernet.

>[AZURE.TIP] Denne regel bruger tjenesten "Alle" til at gøre det nemmere at konfigurere og bruge eksempelprogrammet, og det vil også tillade ICMPv4 (ping) i en enkelt regel. Dette er dog ikke anbefales. Porte og protokoller ("Services") skal være smallere til det mindst mulige, der gør det muligt for programmet handling til at reducere angrebsoverfladen over denne grænse.

<br />

>[AZURE.TIP] Selvom denne regel viser en eksplicit dest reference, der bruges, skal en ensartet måde bruges i hele konfigurationen af firewallen. Det anbefales, at objektet navngivet netværk bruges i hele til nemmere læsbarheden og support. Eksplicitte dest bruges her kun til at vise en alternativ referencemetode og anbefales ikke Generelt (især til komplekse konfigurationer).

- **Udgående til Internet regel**: denne overfører regel vil tillade trafik fra en hvilken som helst kilde netværk til at overføre til de valgte Destination netværk. Denne regel er en regel standard, der normalt allerede på Barracuda NextGen firewallen, men er i en deaktiveret tilstand. Hvis du højreklikker på denne regel kan få adgang til kommandoen aktivere regel. Den regel, der er vist her er blevet ændret for at tilføje de to lokale undernet, der er oprettet som referencer i afsnittet nødvendige i dette dokument til attributten kilde for denne regel.

    ![Firewall udgående regel][14]

- **DNS-regel**: denne overfører regel tillader kun DNS (port 53) trafik til at overføre til DNS-serveren. For denne-miljø de fleste trafik fra front end til backend-version er blokeret, kan denne regel specifikt DNS.

    ![Firewall DNS-regel][15]

    **Bemærk**: I dette skærmbillede skærmbillede metoden forbindelse er inkluderet. Da denne regel er interne IP-adresse til interne IP-adresse trafik, ingen NATing er påkrævet, dette metoden forbindelse er indstillet til "Ingen SNAT" for denne regel i gang.

- **Undernet til undernet regel**: denne overfører regel er en standard-regel, der er aktiveret og ændret for at tillade en hvilken som helst server på back-end-undernet til at oprette forbindelse til en hvilken som helst server på front-end-undernet. Denne regel er alle intern trafik, så metoden forbindelse kan være indstillet til Nej SNAT.

    ![Firewall handel VNet regel][16]

    **Bemærk**: afkrydsningsfeltet tovejs-ikke er markeret (er heller ikke markeret i de fleste regler), dette er vigtig for denne regel, fordi det giver dette regel "én envejs", en forbindelse kan startes fra back-end-undernettet front-end netværket, men ikke omvendt. Hvis dette afkrydsningsfelt er markeret, vil denne regel aktivere tovejs-trafik, som fra vores logisk diagram ikke som ønskes.

- **Afvise alle trafik regel**: Dette altid skal være den endelige regel (med hensyn til prioritet), og som sådan Hvis en trafik flyder ikke matcher nogen af den foregående regler fjernes af denne regel. Dette er en standard-regel og normalt aktiveret nogen ændringer er normalt nødvendige. 

    ![Firewall nægte regel][17]

>[AZURE.IMPORTANT] Når alle ovenstående reglerne er oprettet, er det vigtigt at gennemse prioriteten af hver regel for at sikre, at trafik kan tillades eller afvises efter behov. I dette eksempel er reglerne i den rækkefølge, de skal vises i gitteret Main af viderestilling af regler i den Barracuda Management-klient.

## <a name="rule-activation"></a>Regel aktivering
Med den regelsæt, ændres til angivelse af logik diagrammet, regelsæt overført til firewallen og derefter aktiveret.

![Firewall regel aktivering][18]
 
Er en klynge af knapperne i øverste højre hjørne af management-klienten. Klik på knappen "Send ændringer" for at sende de ændrede regler til firewallen, og derefter klikke på knappen "Aktiver".
 
Dette eksempel miljø build er fuldført med aktivering af firewall regelsæt.

## <a name="traffic-scenarios"></a>Trafik scenarier
>[AZURE.IMPORTANT] En vigtige takeway er at huske, at **al** trafik kommer gennem firewallen. Så til Fjernskrivebord til IIS01-serveren, vil selvom det er i den Front End-skytjeneste og på Front End-undernet, for at få adgang til denne server vi skal RDP til firewallen på port 8014 og derefter tillade firewall til at dirigere RDP anmodningen internt til IIS01 RDP-Port. Portalen Azure "Connect" knappen virker ikke, fordi der ikke er nogen direkte RDP-sti til IIS01 (den udstrækning portalen kan se). Det betyder, at alle forbindelser fra internettet vil være til tjenesten sikkerhed og en Port, f.eks. secscv001.cloudapp.net:xxxx.

De følgende firewallregler skal være på plads for disse scenarier:

1.  Administration af Firewall
2.  RDP til IIS01
3.  RDP til DNS01
4.  RDP til AppVM01
5.  RDP til AppVM02
6.  App-trafik til internettet
7.  App-trafik til AppVM01
8.  Udgående til internettet
9.  Front end til DNS01
10. Handel-undernet trafik (back-end til front-end kun)
11. Afvise alle

Den faktiske firewall regelsæt højst sandsynligt har mange andre regler ud over disse, regler for en given firewall får også forskellige prioriteringstal end dem, der vises her. Denne liste og tilknyttede tal er at give relevans mellem disse elleve regler og den relative prioritet blandt dem. Med andre ord; på den faktiske firewall kan den "RDP til IIS01" være regel tal 5, men så længe den er under "Firewall Management" reglen og over reglen "RDP til DNS01" det vil justere med henblik på denne liste. Listen også hjælpe med den under scenarier tillade pladshensyn; FX "vs regel 9 (DNS)". Også af pladshensyn, de fire RDP regler bliver samlet kaldt, "RDP-regler" Når scenariet trafik ikke vedrører RDP.

Også tilbagekalde, netværk sikkerhedsgrupper er direkte til indgående internettrafik på alle front end- og back end-undernet.

#### <a name="allowed-internet-to-web-server"></a>(Tilladt) Internet til webserver
1.  Internet bruger HTTP siden serviceanmodninger fra SecSvc001.CloudApp.Net (Internet modstående skybaseret tjeneste)
2.  Skybaseret tjeneste gennemløb trafik via Åbn slutpunkt på port 80 til firewall brugergrænsefladen på 10.0.0.4:80
3.  Ingen NSG, der er tildelt til sikkerhed undernet, så system NSG regler tillader trafik til firewall
4.  Trafik rammer interne IP-adresse i firewall (10.0.1.4)
5.  Firewall begynder regel behandling:
  1.    VS regel 1 (vs Mgmt) ikke anvende, flytte til næste regel
  2.    VS regler 2-5 (RDP regler) kan ikke anvende skal du gå til næste regel
  3.    VS regel 6 (App: Web) gælder, trafik er tilladt, firewall NAT-enheder at 10.0.1.4 (IIS01)
6.  Front end-undernet begynder indgående regel behandling:
  1.    Gælder ikke for NSG regel 1 (Bloker Internet) (denne trafik blev NAT ville ved firewallen, og derfor kilde-adresse er er nu firewallen som er på sikkerhed undernet og ses af front end-undernet NSG skal være "lokale" trafik og dermed er tilladt), flytte til næste regel
  2.    Standard NSG regler tillader undernet til undernet trafik, trafik er tilladt, skal du stoppe NSG regel behandling
7.  IIS01 lytter efter webtrafik, modtager mødeindkaldelsen og starter behandlingen af anmodningen
8.  IIS01 forsøg på at starter et FTP-sessionen til AppVM01 på back end-undernet
9.  UDR ruten på front end-undernet gør firewallen det næste trin
10. Ingen udgående regler på front end-undernet trafik er tilladt
11. Firewall begynder regel behandling:
  1.    VS regel 1 (vs Mgmt) ikke anvende, flytte til næste regel
  2.    VS regel 2-5 (RDP regler) kan ikke anvende skal du gå til næste regel
  3.    VS regel 6 (App: Web) ikke anvende, skal du gå til næste regel
  4.    VS regel 7 (App: backend-version) gælder, trafik er tilladt, firewall videresender trafik til 10.0.2.5 (AppVM01)
12. Back end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (Bloker Internet) ikke anvende, flytte til næste regel
  2.    Standard NSG regler tillader undernet til undernet trafik, trafik er tilladt, skal du stoppe NSG regel behandling
13.  AppVM01 modtager anmodningen og starter sessionen og reagerer
14. UDR ruten på back end-undernet gør firewallen det næste trin
15. Da der ikke er nogen udgående NSG regler på back end-undernet svaret er tilladt
16. Fordi dette returnerer trafik på en eksisterende session sender firewallen svar tilbage til webserveren (IIS01)
17. Front end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (Bloker Internet) ikke anvende, flytte til næste regel
  2.    Standard NSG regler tillader undernet til undernet trafik, trafik er tilladt, skal du stoppe NSG regel behandling
18. IIS-serveren modtager svaret, afsluttes posteringen med AppVM01, og færdiggør derefter bygge HTTP-svaret, sendes denne HTTP-svar til person
19. Da der ikke er nogen udgående NSG regler på front end-undernet svaret er tilladt
20. HTTP-svaret rammer firewallen og fordi det er svar på en eksisterende NAT-session er godkendt af firewallen
21. Firewallen omdirigerer derefter svar tilbage til den bruger, Internet
22. Da der ikke er ikke udgående modtager NSG regler eller UDR hop på front end-undernet svaret er tilladt og Internetbrugeren den webside, der anmodes om.

#### <a name="allowed-internet-rdp-to-backend"></a>(Tilladt) Internet RDP til backend-version
1.  Server administrator på internettet, som RDP-session til AppVM01 via SecSvc001.CloudApp.Net:8025, hvor 8025 er portnummeret bruger, der er tildelt til "RDP til AppVM01" firewallreglen
2.  Skytjenesten passerer trafik via Åbn slutpunktet på port 8025 til firewall brugergrænsefladen på 10.0.0.4:8025
3.  Ingen NSG, der er tildelt til sikkerhed undernet, så system NSG regler tillader trafik til firewall
4.  Firewall begynder regel behandling:
  1.    VS regel 1 (vs Mgmt) ikke anvende, flytte til næste regel
  2.    VS regel 2 (RDP IIS) ikke anvende, flytte til næste regel
  3.    VS regel 3 (RDP DNS01) ikke anvende, flytte til næste regel
  4.    VS regel 4 (RDP AppVM01) gælder, trafik er tilladt, firewall NAT-enheder at 10.0.2.5:3386 (RDP port på AppVM01)
5.  Back end-undernet begynder indgående regel behandling:
  1.    Gælder ikke for NSG regel 1 (Bloker Internet) (denne trafik blev NAT ville ved firewallen, og derfor kilde-adresse er er nu firewallen som er på sikkerhed undernet og ses af back end-undernet NSG skal være "lokale" trafik og dermed er tilladt), flytte til næste regel
  2.    Standard NSG regler tillader undernet til undernet trafik, trafik er tilladt, skal du stoppe NSG regel behandling
6.  AppVM01 lytter efter RDP trafik og reagerer
7.  Standardregler i kraft med nogen udgående NSG regler og returnerede trafik er tilladt
8.  UDR dirigerer udgående trafik til firewallen som det næste trin
9.  Fordi dette returnerer trafik på en eksisterende session sender firewallen svar tilbage til brugeren, internet
10. RDP-session er aktiveret
11. AppVM01 beder om brugernavn adgangskode

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Tilladt) Web DNS-Server opslag på DNS-server
1.  Web Server, IIS01, behov et datafeed på www.data.gov, men skal løse adressen.
2.  Netværkskonfigurationen for listerne VNet DNS01 (10.0.2.4 på back end-undernet) som den primære DNS-server, IIS01 sender DNS-anmodningen til DNS01
3.  UDR dirigerer udgående trafik til firewallen som det næste trin
4.  Ingen udgående NSG regler er bundet til front end-undernet, er trafik tilladt
5.  Firewall begynder regel behandling:
  1.    VS regel 1 (vs Mgmt) ikke anvende, flytte til næste regel
  2.    VS regel 2-5 (RDP regler) kan ikke anvende skal du gå til næste regel
  3.    Anvende vs regler 6 og 7 (App regler) kan ikke skal du gå til næste regel
  4.    VS regel 8 (Internet) ikke anvende, flytte til næste regel
  5.    VS regel 9 (DNS) gælder, trafik er tilladt, firewall videresender trafik til 10.0.2.4 (DNS01)
6.  Back end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (Bloker Internet) ikke anvende, flytte til næste regel
  2.    Standard NSG regler tillader undernet til undernet trafik, trafik er tilladt, skal du stoppe NSG regel behandling
7.  DNS-server modtager anmodningen
8.  DNS-server har ikke den adresse, der er cachelagret og beder om en rod DNS-server på internettet
9.  UDR dirigerer udgående trafik til firewallen som det næste trin
10. Ingen udgående NSG regler på back end-undernet, trafik er tilladt
11. Firewall begynder regel behandling:
  1.    VS regel 1 (vs Mgmt) ikke anvende, flytte til næste regel
  2.    VS regel 2-5 (RDP regler) kan ikke anvende skal du gå til næste regel
  3.    Anvende vs regler 6 og 7 (App regler) kan ikke skal du gå til næste regel
  4.     VS regel 8 (Internet) gælder, trafik er tilladt, session er SNAT ud af, at rod DNS-server på internettet
12. Internet DNS-server reagerer, da denne session er blevet startet fra firewallen, svaret besvares af firewallen
13. Som det er en eksisterende session, videresender firewallen svar til starter serveren, DNS01
14. Back end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (Bloker Internet) ikke anvende, flytte til næste regel
  2.    Standard NSG regler tillader undernet til undernet trafik, trafik er tilladt, skal du stoppe NSG regel behandling
15. DNS-server modtager og gemmer svaret og derefter reagerer på den oprindelige anmodning tilbage til IIS01
16. UDR ruten på back end-undernet gør firewallen det næste trin
17. Ingen udgående NSG bestemmelser ikke findes på back end-undernet, er trafik tilladt
18. Dette er en eksisterende session firewallen, svaret sendes af firewall tilbage til IIS-serveren
19. Front end-undernet begynder indgående regel behandling:
  1.    Der er ingen NSG regel, der gælder for indgående trafik fra back end-undernet til front end-undernet, så ingen af NSG regler gælder
  2.    Standard system reglen at tillade trafik mellem undernet vil tillade denne trafik, så trafikken er tilladt
20. IIS01 modtager svaret fra DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Tilladt) Back end-serveren til front end-server
1.  En administrator, der er logget på AppVM02 via RDP anmoder om en fil direkte fra serveren IIS01 via windows Stifinder
2.  UDR ruten på back end-undernet gør firewallen det næste trin
3.  Da der ikke er nogen udgående NSG regler på back end-undernet svaret er tilladt
4.  Firewall begynder regel behandling:
  1.    VS regel 1 (vs Mgmt) ikke anvende, flytte til næste regel
  2.    VS regel 2-5 (RDP regler) kan ikke anvende skal du gå til næste regel
  3.    Anvende vs regler 6 og 7 (App regler) kan ikke skal du gå til næste regel
  4.    VS regel 8 (Internet) ikke anvende, flytte til næste regel
  5.    VS regel 9 (DNS) ikke anvende, flytte til næste regel
  6.    VS regel 10 (handel-undernet) gælder, trafik er tilladt, firewall overfører trafik til 10.0.1.4 (IIS01)
5.  Front end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (Bloker Internet) ikke anvende, flytte til næste regel
  2.    Standard NSG regler tillader undernet til undernet trafik, trafik er tilladt, skal du stoppe NSG regel behandling
6.  Hvis du allerede stort godkendelse og autorisation, IIS01 accepterer anmodningen og reagerer
7.  UDR ruten på front end-undernet gør firewallen det næste trin
8.  Da der ikke er nogen udgående NSG regler på front end-undernet svaret er tilladt
9.  Dette er en eksisterende session i firewallen for dette svar er tilladt og firewallen returnerer svar til AppVM02
10. Back end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (Bloker Internet) ikke anvende, flytte til næste regel
  2.    Standard NSG regler tillader undernet til undernet trafik, trafik er tilladt, skal du stoppe NSG regel behandling
11. AppVM02 modtager svaret

#### <a name="denied-internet-direct-to-web-server"></a>(Nægtet) Internet direkte til webserver
1.  Internet bruger forsøger at få adgang til webserveren, IIS01, via tjenesten FrontEnd001.CloudApp.Net
2.  Da der ikke er nogen slutpunkter åben for HTTP-trafik, dette vil ikke gå igennem Skytjenesten og vil ikke kunne oprette forbindelse til serveren
3.  Hvis slutpunkterne var åbne eller anden grund, ville NSG (Bloker Internet) på front end-undernet blokere denne trafik
4.  Til sidst skal Frontend undernet UDR ruten vil sende en hvilken som helst udgående trafik fra IIS01 til firewallen som det næste trin, og firewallen skal se ud som asymmetrisk trafik og slippe udgående svaret dermed der ikke er mindst tre uafhængige lag forsvarslinjer mellem internet- og IIS01 via dens skybaseret tjeneste, forhindre uautoriseret/upassende adgang.

#### <a name="denied-internet-to-backend-server"></a>(Nægtet) Internet til back end-serveren
1.  Internet-bruger forsøger at få adgang til en fil på AppVM01 via tjenesten BackEnd001.CloudApp.Net
2.  Da der ikke er nogen slutpunkter åben for filshare, dette vil ikke bestået Skytjenesten og vil ikke kunne oprette forbindelse til serveren
3.  Hvis slutpunkterne var åbne eller anden grund, vil NSG (Bloker Internet) blokere denne trafik
4.  Til sidst skal UDR ruten vil sende en hvilken som helst udgående trafik fra AppVM01 til firewallen som det næste trin, og firewallen ville se ud som asymmetrisk trafik og slippe den udgående svar, og som derfor der ikke er mindst tre uafhængige lag forsvarslinjer mellem internet- og AppVM01 via dens skybaseret tjeneste, forhindre uautoriseret/upassende adgang.

#### <a name="denied-frontend-server-to-backend-server"></a>(Nægtet) Front end-server til back end-serveren
1.  Antag IIS01 er blevet afsløret og kører skadelig kode, der forsøger at scanne undernet back end-servere.
2.  Front end undernet UDR ruten vil sende en hvilken som helst udgående trafik fra IIS01 til firewallen som det næste trin. Dette er ikke noget, som kan ændres af kompromitteret VM.
3.  Firewallen vil behandle trafikken, hvis anmodningen blev AppVM01 eller DNS-server til DNS-opslag, trafik kan potentielt tillades af firewall (på grund af vs regler 7 og 9). Alle andre trafik ville være blokeret af vs regel 11 (Afvis alle).
4.  Hvis avancerede truslen registrering er aktiveret på firewallen (som ikke er omfattet i dette dokument, i dokumentationen leverandør til bestemte netværk anvendelsen avancerede truslen funktioner), kan det forhindre lige trafik, der ville være tilladt i de grundlæggende viderestilling af regler, der er beskrevet i dette dokument, hvis trafikken indeholdt kendte signaturer eller mønstre, markere en regel for avancerede truslen.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Nægtet) Internet DNS-opslag på DNS-server
1.  Internet-bruger forsøger at slå en interne DNS-post på DNS01 gennem BackEnd001.CloudApp.Net 
2.  Da der ikke er nogen slutpunkter Åbn DNS-trafik, dette vil ikke gå igennem Skytjenesten og vil ikke kunne oprette forbindelse til serveren
3.  Hvis slutpunkterne var åbne eller anden grund, blokerer NSG regel (Bloker Internet) på front end-undernet denne trafik
4.  Til sidst skal back end-undernet UDR ruten vil sende en hvilken som helst udgående trafik fra DNS01 til firewallen som det næste trin, og firewallen vil se ud som asymmetrisk trafik og slippe den udgående svar, og som derfor der ikke er mindst tre uafhængige lag forsvarslinjer mellem internet- og DNS01 via dens skybaseret tjeneste, forhindre uautoriseret/upassende adgang.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Nægtet) Internet SQL Access via Firewall
1.  Internetbrugeren anmoder om SQL-data fra SecSvc001.CloudApp.Net (Internet modstående skybaseret tjeneste)
2.  Da der ikke er nogen slutpunkter åben for SQL, dette vil ikke bestået Skytjenesten og vil ikke kunne oprette forbindelse til firewallen
3.  Hvis SQL slutpunkter var åbne eller anden grund, vil firewallen begynder regel behandling:
  1.    VS regel 1 (vs Mgmt) ikke anvende, flytte til næste regel
  2.    VS regler 2-5 (RDP regler) kan ikke anvende skal du gå til næste regel
  3.    Anvende vs regel 6 og 7 (programmet regler) kan ikke skal du gå til næste regel
  4.    VS regel 8 (Internet) ikke anvende, flytte til næste regel
  5.    VS regel 9 (DNS) ikke anvende, flytte til næste regel
  6.    VS regel 10 (handel-undernet) ikke anvende, flytte til næste regel
  7.    VS regel 11 (Afvis alle) gælder, trafik er blokeret, standse regel behandling


## <a name="references"></a>Referencer
### <a name="main-script-and-network-config"></a>Primære Script og netværk Config
Gemme fuld scriptet i en PowerShell-script-fil. Du kan gemme Config netværk i en fil med navnet "NetworkConf2.xml".
Ændre de brugerdefinerede variabler efter behov. Køre scriptet og derefter følge instruktionerne til at Firewall regel konfiguration ovenfor.

#### <a name="full-script"></a>Fuld Script
Dette script skal, baseret på brugerdefinerede variabler:

1.  Oprette forbindelse til et Azure-abonnement
2.  Oprette en ny firmapost lagerplads
3.  Oprette en ny VNet og tre undernet som defineret i netværk konfigurationsfil
4.  Opbygge fem virtuelle maskiner; 1 firewall og 4 windows server FOS
5.  Konfigurer UDR, herunder:
  1.    Oprette to nye distribuere tabeller
  2.    Tilføje omdirigerer til tabeller.
  3.    Binde tabeller til relevante undernet
6.  Aktivere IP Forwarding på for
7.  Konfigurer NSG, herunder:
  1.    Oprette en NSG
  2.    Tilføje en regel
  3.    Binder på NSG til de relevante undernet

Denne PowerShell-script skal køres lokalt på en internet forbindelse PC eller server.

>[AZURE.IMPORTANT] Når dette script køres, kan der være advarsler eller andre oplysninger, der pop i PowerShell. Kun fejlmeddelelser i rød er anledning.

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA
    
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.
    
      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4
     
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"
    
    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #
    
      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }
    
    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan
    
      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"
    
      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal
    
      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName
    
     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
    
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
    

#### <a name="network-config-file"></a>Netværk konfigurationsfil
Gem denne XML-fil med opdaterede placering og tilføje linket til denne fil til $NetworkConfigFile variabel i scriptet ovenfor.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Eksempel på program-Scripts
Hvis du vil installere en Northwind til dette og andre DMZ eksempler, en har fået på følgende link: [Eksempel programmet Script][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Tovejs-DMZ med for NSG og UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Logiske visning af firewallreglerne"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Oprette et netværk front end-objekt"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Oprette en DNS-serverobjekt"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Kopi af standard RDP regel"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "AppVM01 regel"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Omdiriger programikon"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Destination NAT-ikon"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Overføre ikon"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Firewall Management regel"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Firewall RDP regel"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Firewall Web regel"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Firewall AppVM01 regel"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Firewall udgående regel"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Firewall DNS-regel"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Firewall handel VNet regel"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Firewall nægte regel"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Firewall regel aktivering"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
