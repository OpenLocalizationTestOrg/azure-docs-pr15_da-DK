<properties 
   pageTitle="Hybrid forbindelse med 2-niveau programmet | Microsoft Azure"
   description="Lær at implementere virtuelle anvendelser og UDR til at oprette et miljø med flere lag programmet i Azure"
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
   ms.date="05/05/2016"
   ms.author="jdial" />

# <a name="virtual-appliance-scenario"></a>Virtuel maskinen scenarie

Et almindeligt scenarie mellem større Azure kunde er nødvendigt at angive et to niveauer-program, der vises til internettet, mens du tillade adgang til det tilbage niveau fra et lokalt datacenter. Dette dokument fører dig gennem et scenarie, der bruger bruger defineret omdirigerer (UDR), en VPN-Gateway og netværk virtuelle udstyr til at implementere et miljø med to niveauer, der opfylder følgende krav:

- Webprogram, der skal kunne åbnes fra det offentlige Internet.
- Webserver, der er vært for programmet skal kunne få adgang til en back end-programmet på serveren.
- Alle trafik fra internettet til webprogrammet skal gå gennem en firewall virtuelle maskinen. Denne virtuelle maskinen der skal bruges til internettrafik kun.
- Al trafik til application server i gang skal gå gennem en firewall virtuelle maskinen. Denne virtuelle maskinen der skal bruges til adgang til back end-end-serveren, og Åbn kommer fra det lokale netværk via en VPN-Gateway.
- Administratorer skal kunne administrere de virtuelle firewall-anvendelser fra deres lokale computere, ved hjælp af en tredje firewall virtuelle maskinen bruges udelukkende til administration af formål.

Dette er et standard DMZ scenarie med en DMZ og et beskyttet netværk. Sådan scenarie kan være opbygget i Azure ved hjælp af NSGs, firewall virtuelle anvendelser eller en kombination af begge. Tabellen nedenfor viser nogle af fordele og ulemper mellem NSGs og firewall virtuelle udstyr.

||Medarbejdere|Ulemper|
|---|---|---|
|NSG|Gratis. <br/>Integreret i Azure RBAC. <br/>Regler kan oprettes i ARM skabeloner.|Kompleksitet kan variere i større miljøer. |
|Firewall|Fuld kontrol over dataplan. <br/>Central administration via firewall-konsollen.|Omkostninger for firewall maskinen. <br/>Ikke er integreret med Azure RBAC.|

Løsningen nedenfor bruger firewall virtuelle udstyr til at implementere et scenarie med DMZ/beskyttet netværk.

## <a name="considerations"></a>Overvejelser i forbindelse med

Du kan installere det miljø, der er beskrevet ovenfor i Azure med forskellige funktioner, der er tilgængelige i dag, på følgende måde.

- **Virtuel netværk (VNet)**. En Azure VNet fungerer på samme måde til et lokalt netværk og kan blive opdelt i en eller flere undernet til at levere trafik isolationsniveauet og adskillelse af problemer.
- **Virtuel maskinen**. Flere partnere tilbyder virtuelle anvendelser på Azure Marketplace, der kan bruges til disse tre firewalls er beskrevet ovenfor. 
- **Brugerdefinerede omdirigerer (UDR)**. Distribuere tabeller kan indeholde UDRs bruges ved Azure netværk til at styre strømmen af pakker inden for en VNet. Tabellerne distribuere kan anvendes på undernet. En af de nyeste funktioner i Azure er muligheden for at anvende en rutetabel til til GatewaySubnet, gør muligheden at videresende al trafik kommer til Azure VNet fra en hybrid-forbindelse til et virtuelt maskinen.
- **Videresendelse af IP-adresse**. Som standard Videresend Azure netværk programmet pakker til virtuelle netværkskort (NIC), kun, hvis pakke destination IP-adressen svarer til NIC IP-adressen. Hvis en UDR definerer, at en pakke skal sendes til en given virtuelle maskinen, vil Azure netværk programmet derfor slippe denne pakke. For at sikre pakken er leveret til en VM (i dette tilfælde et virtuelt maskinen), der ikke er den faktiske destination for pakken, skal du aktivere IP-videresendelse virtuelle maskinen.
- **Netværk sikkerhedsgrupper (NSGs)**. I eksemplet nedenfor gør ikke brug af NSGs, men du kan bruge NSGs, der er anvendt på undernet og/eller netværkskort i denne løsning til at filtrere trafik og disse undernet og netværkskort.


![IPv6-forbindelse](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

I dette eksempel er der et abonnement, der indeholder følgende:

- 2 ressourcegrupper, ikke vises i diagrammet. 
    - **ONPREMRG**. Indeholder alle de ressourcer, der er nødvendige for at simulere en lokale netværk.
    - **AZURERG**. Indeholder alle de ressourcer, der er nødvendige for det Azure virtuelle netværksmiljø. 
- En VNet med navnet **onpremvnet** bruges til at efterligne et lokalt datacenter opdelt som angivet nedenfor.
    - **onpremsn1**. Undernet, der indeholder en virtuel maskine (VM), der kører Ubuntu til at efterligne en lokal server.
    - **onpremsn2**. Undernet, der indeholder en VM kører Ubuntu til at efterligne en lokal computer, der bruges af en administrator.
- Der er en firewall virtuelle maskinen med navnet **OPFW** på **onpremvnet** bruges til at bevare en tunnel til **azurevnet**.
- En VNet med navnet **azurevnet** opdelt som angivet nedenfor.
    - **azsn1**. Eksterne firewall undernet bruges udelukkende til den eksterne firewall. Alle internettrafik kommer gennem dette undernet. Dette undernet indeholder kun en NIC, der er knyttet til den eksterne firewall.
    - **azsn2**. Front-end-undernet er vært for en VM kører som en webserver, der skal have adgang til fra internettet.
    - **azsn3**. Back end-undernet er vært for en VM kører en back end-program-server, der kan åbnes af frontend webserveren.
    - **azsn4**. Administration af undernet bruges udelukkende til at levere management adgang til alle firewall virtuelle udstyr. Dette undernet indeholder kun en NIC for hver firewall virtuelle maskinen bruges i en løsning.
    - **GatewaySubnet**. Azure hybrid forbindelse undernet, der kræves til ExpressRoute og VPN-Gateway til at levere forbindelsen mellem Azure VNets og andre netværk. 
- Der er 3 firewall virtuelle anvendelser i **azurevnet** netværket. 
    - **AZF1**. Eksterne firewall være tilgængelig på offentlige internettet ved hjælp af en offentlige IP-adresseressource i Azure. Du har brug at sikre, at du har en skabelon fra Marketplace eller direkte fra din leverandør af maskinen, pågældende bestemmelser en 3-NIC virtuelle maskinen.
    - **AZF2**. Interne firewall, der bruges til at styre trafik mellem **azsn2** og **azsn3**. Dette er også en 3-NIC virtuelle maskinen.
    - **AZF3**. Administration af firewall tilgængelige til administratorer af grupper fra det lokale datacenter, og forbindelsen til et management undernet, der bruges til at administrere alle firewall udstyr. Du kan finde 2-NIC virtuelle maskinen skabeloner på markedet eller anmode om en direkte fra din leverandør af maskinen.

## <a name="user-defined-routing-udr"></a>Brugerdefinerede Routing (UDR)

Hvert undernet i Azure kan sammenkædes med en UDR tabel, der bruges til at definere, hvordan trafik startet i den valgte undernet er distribueret. Hvis ingen UDRs defineres, bruger Azure standard omdirigerer til at tillade trafikken at flyde fra ét undernet til en anden. For bedre at forstå UDRs, gå til [Hvad er brugerdefineret omdirigerer og IP-videresendelse](./virtual-networks-udr-overview.md#ip-forwarding).

For at sikre kommunikationen sker via højre firewall produktet baseret på sidste kravet ovenfor, skal du oprette følgende distribuere tabellen med UDRs i **azurevnet**.

### <a name="azgwudr"></a>azgwudr

I dette scenarie kun trafikken der flyder fra det lokale til Azure bruges til at administrere disse firewalls ved at oprette forbindelse til **AZF3**og pågældende trafik skal gå gennem den interne firewall, **AZF2**. Kun én rute er derfor nødvendigt i **GatewaySubnet** som vist nedenfor.

|Destination|Næste trin|FORKLARING|
|---|---|---|
|10.0.4.0/24|10.0.3.11|Gør det muligt for lokale trafik til administration af firewall **AZF3**|

### <a name="azsn2udr"></a>azsn2udr

|Destination|Næste trin|FORKLARING|
|---|---|---|
|10.0.3.0/24|10.0.2.11|Gør det muligt for trafik til det back end-undernet vært programserver gennem **AZF2**|
|0.0.0.0/0|10.0.2.10|Gør det muligt for alle andre trafik skal distribueres via **AZF1**|

### <a name="azsn3udr"></a>azsn3udr

|Destination|Næste trin|FORKLARING|
|---|---|---|
|10.0.2.0/24|10.0.3.10|Tillader trafik til **azsn2** at flyde fra app-serveren til webserver gennem **AZF2**|

Du skal også oprette distribuere tabeller for undernet i **onpremvnet** til at efterligne lokale datacenter.

### <a name="onpremsn1udr"></a>onpremsn1udr

|Destination|Næste trin|FORKLARING|
|---|---|---|
|192.168.2.0/24|192.168.1.4|Gør det muligt for trafik til **onpremsn2** via **OPFW**|

### <a name="onpremsn2udr"></a>onpremsn2udr

|Destination|Næste trin|FORKLARING|
|---|---|---|
|10.0.3.0/24|192.168.2.4|Tillader, at det sikkerhedskopierede undernet trafik i Azure gennem **OPFW**|
|192.168.1.0/24|192.168.2.4|Gør det muligt for trafik til **onpremsn1** via **OPFW**|

## <a name="ip-forwarding"></a>IP-videresendelse 

UDR og IP-videresendelse er funktioner, der kan bruges sammen for at tillade virtuelle anvendelser skal bruges til at styre strømmen af trafik i en Azure VNet.  Et virtuelt maskinen er ikke noget mere end en VM, der kører et program, der bruges til at håndtere netværkstrafik på en måde, som en firewall eller en NAT-enhed.

Denne virtuelle maskinen VM skal kunne modtage indgående trafik, der ikke er adresseret til sig selv. For at tillade en VM til at modtage trafik til andre destinationer, skal du aktivere IP-videresendelse af VM. Dette er en Azure konfiguration, ikke en indstilling i gæst-operativsystemet. Virtuel anvendelsen stadig skal køre nogle typer program tilladelse til at håndtere indgående trafikken og distribuere den korrekt.

Hvis du vil vide mere om IP-videresendelse, gå til [Hvad er brugerdefineret omdirigerer og IP-videresendelse](./virtual-networks-udr-overview.md#ip-forwarding).

Forestil dig du har følgende opsætning i en Azure vnet eksempelkuben:

- Undernet **onpremsn1** indeholder en VM med navnet **onpremvm1**.
- Undernet **onpremsn2** indeholder en VM med navnet **onpremvm2**.
- Et virtuelt maskinen med navnet **OPFW** er forbundet til **onpremsn1** og **onpremsn2**.
- En brugerdefineret rute, der er knyttet til **onpremsn1** angiver, at al trafik til **onpremsn2** skal sendes til **OPFW**.

Hvis **onpremvm1** forsøger at oprette en forbindelse med **onpremvm2**, skal anvendes på UDR på dette tidspunkt og trafik sendes til **OPFW** som det næste trin. Være opmærksom på, at den faktiske pakke destination ikke bliver ændret, der stadig står **onpremvm2** er destinationen. 

Uden IP-videresendelse aktiveret for **OPFW**, vil Azure virtuelle netværk logik slippe pakkerne, da der kun kan pakker skal sendes til en VM, hvis den VM IP-adresse er destinationen for pakken.

Med IP-videresendelse sender Azure virtuelt netværk logik pakkerne til OPFW, uden at ændre dets oprindelige destinationsadressen. **OPFW** skal håndtere pakkerne og bestemme, hvad skal jeg gøre med dem.

For ovenstående scenarie for at arbejde, skal du aktivere IP Forwarding på netværkskortene for **OPFW**, **AZF1**, **AZF2**og **AZF3** , der bruges til distribution (alle NIC bortset fra dem, der er knyttet til administration af undernet). 

## <a name="firewall-rules"></a>Firewallregler

Som beskrevet ovenfor, sikrer IP-videresendelse kun-pakker, der sendes til de virtuelle udstyr. Anvendelsen stadig skal beslutte, hvad skal jeg gøre med disse pakker. I dette scenario ovenfor skal du oprette følgende regler i din anvendelser:

### <a name="opfw"></a>OPFW

OPFW repræsenterer en lokal enhed, der indeholder følgende regler:

- **Distribuere**: al trafik til 10.0.0.0/16 (**azurevnet**) skal sendes igennem tunnel **ONPREMAZURE**.
- **Politik**: tillader al tovejs trafik mellem **port2** og **ONPREMAZURE**.
 
### <a name="azf1"></a>AZF1

AZF1 repræsenterer en Azure virtuelle anvendelses, der indeholder følgende regler:

- **Politik**: tillader al tovejs trafik mellem **port1** og **port2**.

### <a name="azf2"></a>AZF2

AZF2 repræsenterer en Azure virtuelle anvendelses, der indeholder følgende regler:

- **Distribuere**: al trafik til 10.0.0.0/16 (**onpremvnet**) skal sendes til Azure gateway IP-adresse (det vil sige 10.0.0.1) gennem **port1**.
- **Politik**: tillader al tovejs trafik mellem **port1** og **port2**.

## <a name="network-security-groups-nsgs"></a>Netværk sikkerhedsgrupper (NSGs)

I dette scenarie skal bliver NSGs ikke brugt. Du kan dog anvende NSGs til hver undernet til at begrænse indgående og udgående trafik. For eksempel kan du anvende følgende NSG regler til det eksterne vs-undernet.

**Indgående**

- Tillad, at al TCP-trafik fra internettet port 80 på en hvilken som helst VM i undernettet.
- Afvise alle andre trafik fra internettet.

**Udgående**
- Nægte al trafik til internettet.

## <a name="high-level-steps"></a>Høj niveau trin

Hvis du vil installere dette scenario, skal du benytte følgende højt niveau nedenfor.

1.  Log på dit Azure-abonnement.
2.  Hvis du vil installere en VNet for at efterligne lokale netværk, skal du klargør de ressourcer, der er en del af **ONPREMRG**.
3.  Klargør de ressourcer, der er en del af **AZURERG**.
4.  Klargøre tunnel fra **onpremvnet** til **azurevnet**.
5.  Når alle ressourcer, der er klargjort, log på **onpremvm2** og ping 10.0.3.101 for at teste forbindelsen mellem **onpremsn2** og **azsn3**.
