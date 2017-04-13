<properties 
    pageTitle="Integrere en app med en Azure virtuelt netværk" 
    description="Viser, hvordan du knytter en app i Azure App-tjeneste til et nyt eller eksisterende Azure virtuelt netværk" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor="cephalin"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="ccompy"/>

# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrere din app med en Azure virtuelt netværk #

Dette dokument beskrives funktionen Azure App Service virtuelt netværk integration og viser, hvordan du konfigurere det til apps i [Azure App-tjeneste](http://go.microsoft.com/fwlink/?LinkId=529714).  Hvis du har kendskab til Azure virtuelle netværk (VNETs), er dette en funktion, hvor du kan placere mange af dine Azure ressourcer i et ikke-internet routeable netværk, som du kan kontrollere adgang til.  Disse netværk kan derefter forbindelse til dine til en lokal netværk ved hjælp af en række forskellige VPN-teknologier.  For at lære mere om Azure virtuelle netværk starter med oplysningerne her: [Azure virtuelle netværk oversigt][VNETOverview].  

Tjenesten Azure App har to formularer.  

1. De med flere lejer systemer, der understøtter alle de priser planer
1. App Service miljø (ASE) premium funktionen som installerer til din VNET.  

Dette dokument gennemgår VNET Integration og ikke App Service-miljø.  Hvis du vil vide mere om funktionen ASE og derefter starte med oplysningerne her: [App servicemiljø Introduktion][ASEintro].

Integration af VNET giver web app adgang til ressourcer i netværket virtuel men giver ikke privat adgang til din online fra det virtuelle netværk.  Få adgang til private webstedet er kun tilgængelig med en ASE, der er konfigureret med en intern Indlæs belastningsjusteringstjenesten (ILB).  Få mere at vide om brug af en ILB ASE starter med artiklen her: [oprette og bruge en ILB ASE][ILBASE]. 

Et almindeligt scenarie, hvor du vil bruge VNET Integration aktivere adgang fra din online til en database eller en web services kører på en virtuel maskine i Azure virtuelle netværket.  Du behøver ikke at få vist et offentlige slutpunkt for programmer på din VM men kan bruge de private ikke-distribueret Internetadresser i stedet for med VNET Integration.  

Funktionen VNET Integration:

- kræver en Standard- eller Premium priser plan 
- fungerer sammen med Classic(V1) eller ressource Manager(V2) VNET 
- understøtter TCP- og UDP
- fungerer med internettet, Mobile og API apps
- gør det muligt for en app til at oprette forbindelse til kun 1 VNET ad gangen
- gør det muligt for op til 5 VNETs skal integreres med i planlægge en App-tjenesten 
- gør det muligt for den samme VNET der skal bruges i flere apps i planlægge en App-tjenesten
- understøtter en 99,9% SLA på grund af en afhængighed af gatewayen VNET

Der er nogle ting, som VNET Integration ikke understøtter herunder:

- tilslutte et drev
- AD-integration 
- NetBios
- adgang til privat gruppe

### <a name="getting-started"></a>Kom godt i gang ###
Her er nogle ting, du skal huske på, før der oprettes forbindelse til din online til et virtuelt netværk:

- Integration af VNET virker kun med apps i en **Standard** - eller **Premium** priser plan.  Hvis du aktiverer funktionen og derefter tilpasse din App-serviceaftale til en ikke-understøttede priser plan mister dine apps deres forbindelser til de VNETs, de bruger.  
- Hvis netværket target virtuelle allerede findes, skal der være punkt-til-site VPN aktiveret med en dynamisk routing gateway, før det kan være forbundet til en app.  Du kan ikke aktivere punkt-til-site virtuelt privat netværk (VPN), hvis din gateway er konfigureret med statisk routing.
- VNET skal være i samme abonnement som din App Service Plan(ASP).  
- De apps, der integreres med en VNET anvender DNS, der er angivet for VNET.
- Som standard vil dine integration af apps kun omdirigere trafikken til dit VNET, der er baseret på de omdirigerer, der er defineret i din VNET.  


## <a name="enabling-vnet-integration"></a>Aktivere VNET Integration ##

Dette dokument fokuserer primært på ved hjælp af portalen Azure til VNET Integration.  Hvis du vil aktivere VNET Integration med din app, ved hjælp af PowerShell, skal du følge vejledningen her: [forbinde din app til virtuelle netværk ved hjælp af PowerShell][IntPowershell].

Du har mulighed for at oprette forbindelse din app til et nyt eller eksisterende virtuelt netværk.  Hvis du opretter et nyt netværk som en del af din integration derefter ud over blot at oprette VNET, en dynamisk routing gateway er forudkonfigurerede for dig, og peg på webstedet VPN aktiveres.  

>[AZURE.NOTE] Konfigurere en ny virtuelt netværksintegration kan tage flere minutter.  

Aktivere VNET Integration Åbn din app indstillinger, og vælg derefter netværk.  Brugergrænsefladen, der åbnes viser tre netværk valgmuligheder.  Denne vejledning kun flyttes til Integration af VNET via Hybrid forbindelser og App-tjenesten miljøer gennemgås senere i dette dokument.  

Hvis din app ikke er i det korrekte priser plan gør Brugergrænsefladen formulardesignprocessen det muligt at skalere din plan til en højere priser plan efter eget valg.


![][1]
 
###<a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Aktivere VNET Integration med en eksisterende VNET###
VNET Integration Brugergrænsefladen kan du vælge på en liste over dine VNETs.  Klassisk VNETs indikerer, at de er sådanne med ordet "Klassisk" i parentes ud for navnet VNET.  Listen er sorteret, så ressourcestyring VNETs vises først.  Du kan se, at der kun én VNET kan vælges i det billede, der er vist nedenfor.  Der er flere årsager, at en VNET vil være nedtonet herunder:

- VNET er i et andet abonnement, som din konto har adgang til
- VNET har ikke punkt til websted, der er aktiveret
- VNET har ikke en dynamisk routing gateway


![][2]

Hvis du vil aktivere integration Klik på den VNET, du vil integrere med.  Når du vælger VNET, skal din app genstartes automatisk for at ændringerne kan træde i kraft.  

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Aktivere punkt-webstedet i en klassisk VNET #####
Hvis din VNET har ikke en gateway eller har punktforbindelse til websted derefter skal du konfigurere, første.  Hvis du vil gøre dette for en klassisk VNET, gå til [Azure Portal] [ AzurePortal] og få vist listen over virtuelle Networks(classic).  Her skal du klikke på på netværket, du vil integrere med, og klik på feltet stor under Essentials kaldet VPN-forbindelser.  Her kan du oprette dit budskab for at VPN-webstedet og endda har den oprette en gateway.  Det vil være om 30 minutter, før den er klar, når du går gennem punktet websted med gateway oprettelse af oplevelse.  

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Aktivere punkt-webstedet i en ressourcestyring VNET #####

Konfigurere en ressourcestyring VNET med en gateway, og peg på webstedet, skal du bruge PowerShell, som beskrevet her, [konfigureres en punkt-til-Site forbindelse til et virtuelt netværk ved hjælp af PowerShell][V2VNETP2S].  Brugergrænsefladen for at udføre denne funktion er ikke tilgængelig. 

### <a name="creating-a-pre-configured-vnet"></a>Oprette en forudkonfigurerede VNET ###
Hvis du vil oprette en ny VNET, der er konfigureret med en gateway og punkt-til-websted, har tjenesten App netværk Brugergrænsefladen muligheden for at gøre, men kun til en ressourceleder VNET.  Hvis du vil oprette en klassisk VNET med en gateway og punkt-til-websted skal du gøre det manuelt via brugergrænsefladen netværk. 

Hvis du vil oprette en ressourcestyring VNET gennem VNET Integration af Brugergrænsefladen, blot vælge **Opret ny virtuelle netværk** og angive den:

- Virtuel netværksnavn
- Virtuelt netværk Adresseblok
- Undernet navn
- Undernet Adresseblok
- Gatewayen Adresseblok
- Punkt-til-Site Adresseblok

Hvis du vil denne VNET til at oprette forbindelse til en af netværket andre bør derefter du ikke vælge IP-adresseområder, der overlapper med disse netværk.  

>[AZURE.NOTE] Oprettelse af Ressourcestyring VNET med en gateway tager om 30 minutter og kan aktuelt ikke integreres på VNET med din app.  Når din VNET er oprettet med gateway skal du vende tilbage til din app VNET Integration Brugergrænsefladen og vælge din nye VNET.

![][3]

Azure VNETs oprettes normalt i privat netværksadresser.  Som standard VNET integrationen dirigere funktion al trafik, der er beregnet til disse IP-adresseområder til din VNET.  Privat IP-adresseområder er:

- 10.0.0.0/8 - dette er den samme som 10.0.0.0 - 10.255.255.255
- 172.16.0.0/12 - dette er den samme som 172.16.0.0 - 172.31.255.255 
- 192.168.0.0/16 - dette er den samme som 192.168.0.0 - 192.168.255.255
 
Adresseområde VNET skal angives i CIDR notation.  Hvis du ikke kender CIDR notation, er det en metode til at angive adresseblokke ved hjælp af en IP-adresse og et heltal, der repræsenterer netværk masken. Overvej, 10.1.0.0/24 ville være 256 adresser og 10.1.0.0/25 ville være 128 adresser som en hurtig reference.  En IPv4-adresse med en /32 skal være lige 1 adresse.  

Hvis du angiver DNS-serveroplysninger her derefter, der angives for din VNET.  Du kan redigere oplysningerne fra VNET brugeroplevelser efter oprettelse af VNET.

Når du opretter en klassisk VNET ved hjælp af VNET Integration af Brugergrænsefladen, oprettes der en VNET i den samme ressourcegruppe som din app. 

## <a name="how-the-system-works"></a>Sådan fungerer systemet ##
Denne funktion opbygger under overfladen oven på punkt-til-Site VPN-teknologi til at oprette forbindelse til din app til din VNET.   Apps i Azure App Service har en med flere lejer systemarkitektur, hvilket betyder, at klargøring en app direkte i en VNET, som er udført med virtuelle maskiner.  Ved at bygge på punkt-til-site teknologi har vi begrænset netværksadgang til lige den virtuelle maskine, der er vært for appen.  Adgang til netværket er yderligere begrænset på disse app hosts, så dine apps kan kun få adgang til de netværk, som du konfigurere dem til at få adgang til.  

![][4]
 
Hvis du ikke har konfigureret en DNS-server med det virtuelle netværk skal du bruge IP-adresser.  Når du bruger IP-adresser, huske, overordnede fordelen ved denne funktion er, at det gør det muligt at bruge de private adresser i dit private netværk.  Hvis du angiver din app op til at bruge offentlige IP-adresser til en af dine FOS, så du ikke bruger funktionen VNET Integration og kommunikerer via internettet.


##<a name="managing-the-vnet-integrations"></a>Administrere VNET integrationer##

Er muligheden at oprette forbindelse og afbryde forbindelsen til en VNET på en app-niveau.  Handlinger, der kan påvirke VNET Integration på tværs af flere apps er på en ASP-niveau.  Du kan få mere at vide på din VNET fra Brugergrænsefladen, som vises på niveauet for app.  De fleste af de samme oplysninger vises også på niveauet for ASP.  

![][5]

Du kan se, hvis din app er tilsluttet din VNET fra siden Status for netværket funktion.  Hvis din VNET gateway er lukket ned eller anden grund derefter dette vil få vist som ikke-forbindelse.  

De oplysninger, du har nu tilgængelige for dig i niveau VNET Integration Brugergrænsefladen er den samme som den detaljerede oplysninger, du får fra ASP-appen.  Her er disse elementer:

- VNET Name - dette link åbnes på netværket brugergrænseflade
- Placering – dette afspejler placeringen af din VNET.  Det er muligt at integrere med en VNET i en anden placering.
- Der findes certifikatstatus - certifikater, der bruges til at sikre VPN-forbindelse mellem appen og VNET.  Dette afspejler en test for at sikre, at de er synkroniseret.
- Gatewaystatus for - din gateways skal ned uanset årsag derefter din app kan ikke få adgang til ressourcer i VNET.  
- VNET adresseområde - dette er IP-adresseområder for din VNET.  
- Peg på webstedet adresseområde - dette er punkt til websted IP-adresseplads til din VNET.  Din app viser kommunikation til at komme fra en af IP-adresser i denne adresseområde.  
- Websted til websted adresseområde - du kan bruge til websted VPN til at oprette forbindelse til din VNET, ressourcerne til en lokal eller andre VNETs.  Du Hvis har, konfigureret bliver IP-intervaller, der er defineret med, at VPN-forbindelse vises her.
- DNS-servere – Hvis du har DNS-servere, der er konfigureret med din VNET, og de er angivet her.
- IP'er distribueres til VNET - der er en liste over IP-adresser, har din VNET routing defineret for.  Adresserne, vises her.  

Handlingen kun du kan udføre i app-visningen i din VNET-Integration er at Afbryd VNET det aktuelt er tilsluttet din app.  Hvis du vil gøre dette blot klikke på Afbryd forbindelse øverst.  Denne handling ændre ikke din VNET.  Den VNET og det er konfiguration, herunder gateways forbliver uændret.  Hvis du derefter vil slette din VNET skal du først slette ressourcer i den herunder gateways.  

Visningen App Service planlægge har et antal flere handlinger.  Det er også adgang til anderledes end fra app.  Når Åbn ASP netværk Brugergrænsefladen blot ASP-brugergrænseflade, og Rul ned.  Der er en brugergrænseflade element kaldet netværksstatus funktion.  Det kan give nogle mindre detaljer omkring dine VNET Integration.  Klik på på denne brugergrænseflade åbnet netværk funktion Status Brugergrænsefladen.  Hvis du derefter klikker på "Klik her for at administrere" vil du åbne brugergrænseflade, der viser en liste over VNET integrationer i denne ASP.

![][6]

Placeringen af ASP er en god ide at huske, når de kigger på placeringen af VNETs du integrerer med.  Når VNET er i en anden placering sandsynligvis du langt mere skal se ventetid problemer.  

VNETs integreret med er en påmindelse på hvor mange VNETs dine apps er integreret med i denne ASP og hvor mange du kan have.  

For at se tilføjede oplysninger på hver VNET, klik på på VNET du er interesseret i.  Ud over oplysningerne, der er nævnt tidligere du kan også få vist en liste over de apps i denne ASP, der bruger VNET.  

Der findes to primære handlinger i forhold handlinger.  Først er muligheden for at tilføje omdirigerer, der styrer trafik fra din app til din VNET.  Den anden handling er muligheden for at synkronisere certifikater og oplysninger om netværket.

![][7]

**Routing** Som nævnt tidligere er omdirigerer, der er defineret i din VNET, hvad der bruges til at dirigere trafik til dit VNET fra din app.  Er der nogle bruger, men hvor kunder vil sende yderligere udgående trafik fra en app til VNET og for dem denne egenskab er angivet.  Hvad sker der med trafikken, når det er op til hvordan kunden konfigurerer deres VNET.  

**Certifikater** Certifikatstatus afspejler en kontrol, der udføres af tjenesten App til at validere, certifikater, som vi bruger til VPN-forbindelsen er stadig god.  Når VNET Integration er aktiveret, hvis dette er den første integration til VNET fra en hvilken som helst apps i ASP, er der en påkrævet udveksling af certifikater til at sikre sikkerheden for forbindelsen.  Sammen med certifikater få vi DNS-konfigurationen, omdirigerer og andre lignende ting, der beskriver netværket.
Hvis disse certifikater eller oplysninger om netværket er ændret skal du klikke på "Synkroniser netværk".  **Bemærk**: Når du klikker på "Synkroniser netværk", og du vil skabe en kort afbrydelse i forbindelse mellem din app og din VNET.  Mens din app ikke vil blive genstartet kan tab af forbindelse medføre, at dit websted for at funktionen ikke korrekt.  

##<a name="accessing-on-premise-resources"></a>Få adgang til lokale ressourcer##

En af fordelene ved at funktionen VNET-Integration er, hvis din VNET er tilsluttet netværket til en lokal med en VPN-websted til websted, og derefter dine apps kan har adgang til dine til lokale ressourcer fra din app.  Interval for denne indstilling for at arbejde, selvom du muligvis opdatere din til en lokal VPN-gateway med omdirigerer til dit budskab til IP-adresse til websted.  Når til websted VPN er konfigureret først skal derefter de scripts, der bruges til at konfigurere den konfigurere omdirigerer, herunder dit budskab til et websted VPN.  Hvis du føjer punktet til websted VPN efter din oprette dit websted til websted VPN, og derefter skal du opdatere omdirigerer manuelt.  Oplysninger om, hvordan du gør det, kan variere efter gateway og der ikke er beskrevet her.  

>[AZURE.NOTE] Mens funktionen VNET Integration fungerer sammen med et websted til websted VPN til at få adgang til lokale ressourcer fungerer det i øjeblikket ikke med en ExpressRoute VPN at gøre det samme.  Dette er sand, når integration med en klassisk eller Ressourcestyring VNET.  Hvis du har brug at få adgang til ressourcer via en ExpressRoute VPN kan du bruge en ASE som kan køre i din VNET. 

##<a name="pricing-details"></a>Prisoplysninger##
Der er nogle få priser små forskelle, du skal være opmærksom på, når du bruger funktionen VNET Integration.  Der er 3 relaterede gebyrer til brug af denne funktion:

- ASP priser niveau krav
- Dataoverførsel omkostninger
- VPN-Gateway omkostninger.

De skal være i en Standard- eller Premium App Service planlægge for dine apps til at kunne bruge denne funktion.  Du kan se flere oplysninger om disse omkostninger her: [App servicepriser][ASPricing]. 

På grund af måde punkt at websted VPN er håndteret, du har altid et gebyr for udgående data via forbindelsen VNET Integration selvom VNET er i den samme datacenter.  Se, hvilke disse gebyrer er kan du se her: [Overføre priser Datadetaljer][DataPricing].  

Det sidste element er omkostninger for VNET gateways.  Hvis du ikke behøver gateways efter noget andet som til websted VPN du betaler for gateways til at understøtte funktionen Integration af VNET.  Der er nogle oplysninger på disse omkostninger her: [VPN Gateway priser][VNETPricing].  

##<a name="troubleshooting"></a>Fejlfinding i forbindelse med##

Mens funktionen er nemt at konfigurere, betyder det ikke, at din oplevelse bliver problemet gratis.  Skal du støder på er problemer med adgang til dine ønskede slutpunkt, der nogle værktøjer, du kan bruge til at teste forbindelsen fra app-konsollen.  Der findes to console oplevelser, du kan bruge.  En er fra konsollen Kudu og den anden er konsollen, som du kan få fat i portalen Azure.  For at få adgang til konsollen Kudu fra din app-gå til Funktioner > Kudu.  Dette er den samme som gå til [sitename]. scm.azurewebsites.net.  Når, der åbnes blot gå til fanen fejlfinding konsol.  Hvis du vil have til Azure portalen hostet konsollen derefter fra din app-gå til Funktioner > Console.  


####<a name="tools"></a>Værktøjer####

Værktøjer ping, nslookup og tracert virker ikke gennem konsollen på grund af sikkerhedsbegrænsninger.  For at udfylde er ugyldig der to separate værktøjer, der er tilføjet.  For at teste DNS-funktionalitet tilføjet vi en værktøj, der hedder nameresolver.exe.  Syntaksen er:

    nameresolver.exe hostname [optional: DNS Server]

Du kan bruge nameresolver til at kontrollere den værtsnavne, der afhænger af din app.  Denne måde kan du teste Hvis du har noget forkert konfigureret med din DNS- eller måske ikke har adgang til din DNS-server.

Værktøjet næste kan du teste for TCP-forbindelse til en kombination af vært og port.  Dette værktøj kaldes tcpping.exe og syntaksen er:

    tcpping.exe hostname [optional: port]

Dette værktøj fortæller, hvis du kan få fat i en bestemt vært og port, men ikke udfører den samme opgave, du får med ICMP baseret ping til.  Kommandoen ICMP ping fortæller dig, hvis din vært er oprettet.  Med tcpping finde du ud af, hvis du har adgang til en bestemt port på en vært.  


####<a name="debugging-access-to-vnet-hosted-resources"></a>Fejlfinding af adgang til VNET hostet ressourcer####

Der er flere ting, der kan forhindre din app fra at kontakte en bestemt vært og port.  De fleste tilfælde er det en af tre ting:

- **Der er en firewall på måde**  Hvis du har en firewall i måden kan du trykke TCP-timeout.  Det er 21 sekunder i dette tilfælde.  Brug værktøjet tcpping til at teste forbindelsen.  TCP timeout kan skyldes mange ting ud over firewalls, men starter der.  
- **DNS er ikke tilgængelige**  DNS-timeout er 3 sekunder per DNS-server.  Hvis du har 2 DNS-servere, der er 6 sekunder.  Brug nameresolver til at se, om DNS fungerer.  Husk, at du ikke bruge nslookup som, der ikke bruger din VNET er konfigureret med DNS.
- **Ugyldige P2S IP-område** Peg på webstedet IP-område skal være i RFC 1918 privat IP-intervaller (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) Hvis området bruger IP'er uden for, og derefter ting virker ikke.  

Hvis disse elementer ikke besvare dit problem, skal du se første til enkle ting som:  

- Viser gatewayen som værende op i portalen?
- Vis certifikater som værende synkroniseret?
- Er alle ændret netværkskonfigurationen uden at gøre et "Synkroniser netværk" i de pågældende ASP'er? 

Hvis din gateway er nede derefter finde den frem igen.  Hvis dine certifikater er synkroniseret gå til ASP-visningen af din VNET Integration, og tryk på "Synkroniser netværk".  Hvis du har mistanke om, at der er blevet en ændring, der er foretaget i konfigurationen af VNET og den ikke blev synkronisere ville med dine ASP'er derefter gå til ASP-visningen af din VNET Integration og ramme "Synkroniser netværk" bare som en påmindelse, dette medfører en kort afbrydelse med VNET forbindelsen og dine apps.  

Hvis alle, der er fint skal du gå lidt dybere:

- Er der andre apps, der bruger VNET Integration når ressourcer i den samme VNET? 
- Kan du gå til app-konsollen og bruge tcpping når andre ressourcer i din VNET?  

Hvis en af ovenstående gælder derefter VNET-Integration er fint, og problemet, er en anden placering.  Dette er, hvor det kommer til at være mere af en udfordring, fordi der ikke er nogen enkelt metode til at se, hvorfor du ikke kan nå en vært: port.  Nogle af årsagerne omfatter:

- Du har en firewall på værten forhindre adgang til programmet port fra dit budskab til websted IP-område.  Krydser undernet ofte kræver offentlig adgang.
- din target host er nede
- dit program er nede
- Du havde forkert IP-adresse eller værtsnavn
- dit program lytter på en anden port end forventet.  Du kan kontrollere dette ved at gå til, der er vært og ved hjælp af "netstat - aon" fra cmd prompten.  Dette viser dig, hvilken proces ID lytter på hvad port.  
- sikkerhedsgrupper dit netværk er konfigureret i en sådan måde, at de forhindrer adgang til dit program vært og port dit budskab til websted IP-område

Husk, at du ikke kender hvilke IP-adresse i dit budskab til websted IP-område, vil bruge din app, så du har brug at give adgang fra hele området.  

Yderligere fejlfinding trin omfatter:

- Log på en anden VM i din VNET og forsøger at nå dine ressource vært: port derfra.  Der er nogle TCP ping-værktøjer, du kan bruge hertil eller kan også bruge telnet, hvis behøver at være.  Formålet her er blot at afgøre, om connectivity er der fra denne andre VM. 
- få vist et program på en anden VM og test adgang til denne vært og port fra konsollen fra din app  
####<a name="on-premise-resources"></a>På en lokal ressourcer####
Hvis din ikke kan nå ressourcer lokal, så det første, skal du kontrollere, hvis du kan få fat en ressource i din VNET.  Hvis, virker er de næste trin ret nemt.  Fra en VM i din VNET skal du forsøger at oprette forbindelse til en lokal programmet.  Du kan bruge telnet eller en TCP ping til.  Hvis din VM ikke kan oprette forbindelse til din til en lokal ressource skal du først sørge for arbejde til websted VPN-forbindelse.  Hvis den fungerer skal du kontrollere de samme ting, der er nævnt tidligere samt til en lokal gateway konfigurationen af og status.  

Hvis din VNET hostes VM kan få fat i systemet til en lokal nu men din app ikke så årsagen er sandsynligvis en af følgende:
- din omdirigerer ikke er konfigureret med dit budskab til websted IP-intervaller i din til en lokal gateway
- dit netværk sikkerhedsgrupper blokerer adgang til dit budskab til websted IP-område
- din til en lokal Firewall blokerer trafik fra dit budskab til websted IP-område
- Du har en brugerdefineret Route(UDR) i din VNET, der forhindrer dit budskab til websted baseret trafik fra at kontakte dit til en lokal netværk

## <a name="hybrid-connections-and-app-service-environments"></a>Hybrid forbindelser og App Service miljøer##
Der er 3 funktioner, som giver adgang til VNET hostet ressourcer.  De er:

- Integration af VNET
- Hybrid forbindelser
- App Service miljøer

Hybrid forbindelser skal du installere en relay agent kaldet Hybrid forbindelse Manager(HCM) i dit netværk.  HCM skal kunne oprette forbindelse til Azure og også i dit program.  Denne løsning er især velegnet fra et eksternt netværk som netværket til en lokal eller et andet skyen hostet netværk, fordi det ikke kræver internet handicapvenlige ark.  HCM kan kun køre på Windows, og du kan have op til 5 forekomster, der kører for at give høj tilgængelighed.  Hybrid forbindelser understøtter kun TCP via og hvert CH slutpunkt har så det svarer til en bestemt vært: port kombination.  

Funktionen App servicemiljø gør det muligt at køre en forekomst af tjenesten Azure-App i dit VNET.  Dette betyder, at dine apps få adgang til ressourcer i din VNET uden eventuelle ekstra trin.  Nogle af de andre fordele ved et App-tjenesten miljø er, at du kan bruge 8 core dedikeret medarbejdere med 14 GB RAM.  En anden fordel er, at du kan skalere systemet at opfylde dine behov.  I modsætning til afsnittet med flere lejer-miljøer, hvor din ASP er begrænset i størrelse, i en ASE styre du, hvor mange ressourcer, du vil give til systemet.  I forhold netværk fokus i dette dokument via, er en af de ting, du får med et ASE, som du ikke med VNET-Integration, at det kan bruges sammen med en ExpressRoute VPN.  

Mens der er nogle bruger tilfælde overlap, kan ingen af disse funktioner erstatte nogen af de andre.  Viden om hvilke funktion til at bruge er bundet til dine behov, og hvordan du vil bruge den.  Eksempel:

- Hvis du er en udvikler og blot vil køre et websted i Azure og har den få adgang til databasen på arbejdsstationen under skrivebordet er lettest at bruge Hybrid forbindelser.  
- Hvis du er en stor organisation, som ønsker at placere et stort antal web egenskaber i offentligheden i skyen og administrere dem i dit eget netværk, og du vil gå til App-tjenesten miljø.  
- Hvis du har et antal App Service hostet apps og blot vil have adgang til ressourcer i din VNET, så VNET Integration er den måde at gå.  

Ud over de use cases, der er nogle enkle relateret aspekter.  Hvis din VNET allerede er tilsluttet til en lokal netværket derefter bruge VNET Integration eller et App-tjenesten miljø er en nem måde at bruge lokale ressourcer.  Hvis din VNET ikke er forbundet til netværket til en lokal, så det er meget mere omkostninger til at konfigurere et websted til websted VPN med sammenlignes din VNET på den anden side med at installere HCM.  

Ud over de funktionelle forskelle der er også priser forskelle.  Funktionen App servicemiljø er en Premium servicetilbud tilbyder men mest netværk konfiguration muligheder ud over andre fantastiske funktioner.  Integration af VNET kan bruges med Standard eller Premium ASP'er og er perfekt til sikker forbrug ressourcer i din VNET fra flere lejeren App Service.  Hybrid forbindelser i øjeblikket afhænger af en konto, som har priser niveauer, der starter gratis og derefter få gradvist mere omfattende baseret på den ønskede afstand BizTalk.  Hvis du vil arbejde på tværs af mange netværk via, er der ingen anden funktion som Hybrid forbindelser, som gør det muligt at få adgang til ressourcer i godt over 100 separate netværk.    


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[IntPowershell]: http://azure.microsoft.com/documentation/articles/app-service-vnet-integration-powershell/
[ASEintro]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
