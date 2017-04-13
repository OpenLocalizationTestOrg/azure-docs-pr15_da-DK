<properties
   pageTitle="Azure arkitektur Reference - IaaS: Udvide Active Directory til Azure | Microsoft Azure"
   description="Sådan implementere en sikker hybrid netværksarkitektur med Active Directory-godkendelse i Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
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
   ms.date="07/19/2016"
   ms.author="telmos"/>

# <a name="extending-active-directory-directory-services-adds-to-azure"></a>Hvis du vil udvide katalogtjenester Active Directory (tilføjer) til Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

I denne artikel beskriver bedste fremgangsmåder til at udvide dit miljø til Active Directory (AD) til Azure, til at levere fordelt godkendelsestjenester ved hjælp af [Active Directory-domænetjenester (AD DS)][active-directory-domain-services]. Denne arkitektur udvider, der er beskrevet i artiklerne [implementere en sikker hybrid netværksarkitektur i Azure] [ implementing-a-secure-hybrid-network-architecture] og [implementere en sikker hybrid netværksarkitektur med internetadgang i Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Ressourcestyring] [ resource-manager-overview] og klassisk. Denne referencearkitektur bruger Ressourcestyring, som Microsoft anbefaler til nye installationer.

Du kan bruge AD DS til at godkende identiteter. Disse identiteter kan være medlem af brugere, computere, programmer eller andre ressourcer, som indgår i et sikkerhedsdomæne. Du kan hoste lokale Active Directory-Domænetjenester, men i et hybridt scenarie, hvor elementer i et program er placeret i Azure kan det være mere effektivt at gentage denne funktionalitet og AD lager til skyen. Denne metode kan hjælpe med at reducere ventetid forårsaget ved at sende godkendelse og lokale godkendelse af anmodninger om fra skyen tilbage til AD DS kører i det lokale miljø. 

Der er to måder til at hoste din katalogtjenester i Azure:

1. Du kan bruge [Azure Active Directory (AAD)] [ azure-active-directory] til at oprette et nyt AD-domæne i skyen og knytte den til et lokalt AD-domæne. Derefter installationen [Azure AD-forbindelse] [ azure-ad-connect] på bekræfter at gentage identiteter i det lokale lager til skyen. Bemærk, at mappen i skyen er **ikke** en udvidelse af det lokale system, hellere det er en kopi, der indeholder de samme identiteter. Ændringer af disse lokale kopieres til skyen, men ændringer i skyen **, bliver der ikke** identiteter replikeres tilbage til det lokale domæne. For eksempel skal nulstilling af adgangskoder udføres lokalt og bruger Azure AD-forbindelse til at kopiere ændringen til skyen. Bemærk også, at den samme forekomst af AAD kan sammenkædes med mere end én forekomst af AD DS AAD skal indeholde identiteter af hver AD lager, den sammenkædes.

    AAD er nyttig i situationer, hvor de lokale netværk og Azure virtuelt netværk, der er vært for ressourcerne, der skyen ikke er direkte knyttet ved hjælp af en VPN-tunnel eller ExpressRoute kredsløb. Selvom denne løsning er enkel, det muligvis ikke passer til systemer hvor komponenter kan flyttes på tværs af grænsen på-lokale/sky, som dette kan kræve omstrukturering af AAD. AAD håndterer også kun brugergodkendelse i stedet for computergodkendelse. Nogle programmer og tjenester, såsom SQL Server, kan kræve computergodkendelse i så fald denne løsning ikke er relevant.

2. Du kan installere en VM kører AD DS som et domænenavn fra domænecontrolleren i Azure, hvis du vil udvide din eksisterende AD-infrastruktur fra dit lokale datacenter. Denne metode er mest almindelige scenarier, hvor de lokale netværk og Azure virtuelt netværk er forbundet via en VPN-og/eller ExpressRoute forbindelse. Denne løsning understøtter også tovejs-gentagelse, så foretage ændringer i skyen og lokalt, skal det sted, hvor det er mest relevante. Afhængigt af dine krav til sikkerhed, kan det være AD installationen i skyen:
    - en del af det samme domæne som de holdt lokalt
    - et nyt domæne i en delt område
    - et separat område

<!-- we might want to add info on how to choose from the three options above -->

Denne arkitektur fokuserer på løsning 2, ved hjælp af det samme domæne AD DS i Azure og lokale.

Typisk anvendelse sager for denne arkitektur omfatter:

- Hybrid programmer hvor arbejdsbelastninger Kør delvist i det lokale miljø og delvist i Azure.

- Programmer og tjenester, som udfører godkendelse ved hjælp af Active Directory.

## <a name="architecture-diagram"></a>Arkitektur diagram

I det følgende diagram fremhæver de vigtige komponenter i denne arkitektur (*Klik for at zoome ind*). Du kan finde flere oplysninger om de elementer, nedtonet læse [implementere en sikker hybrid netværksarkitektur i Azure] [ implementing-a-secure-hybrid-network-architecture] og [implementere en sikker hybrid netværksarkitektur med internetadgang i Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Lokale netværk.** Lokale netværk omfatter lokale AD-servere, der kan udføres godkendelse og autorisation for komponenter, der er placeret i det lokale miljø.

- **AD-servere.** Dette er domænecontrollere implementere katalogtjenester (AD DS), der kører som FOS i skyen. Disse servere kan levere godkendelse af komponenter, der kører i din Azure virtuelt netværk.

- **Active Directory-undernet.** Active Directory-Domænetjenester servere er hostet i et separat undernet. NSG regler beskytte AD DS servere og kan give en firewall mod trafik fra uventede kilder.

- **Azure Gateway og AD-synkronisering.**. Gatewayen Azure indeholder en forbindelse mellem lokale netværk og Azure VNet. Det kan være en [VPN-forbindelse] [ azure-vpn-gateway] eller [Azure ExpressRoute][azure-expressroute]. Alle synkroniseringsanmodninger mellem AD-servere i skyen og lokale passerer gennem gatewayen. Brugerdefinerede omdirigerer (UDRs) håndtere routing til synkroniseringstrafik som passerer direkte til AD-server i skyen og ikke går igennem de eksisterende netværk virtuelle anvendelser (NVAs) bruges i dette scenarie.

    Du kan finde flere oplysninger om konfiguration af UDRs og NVAs, se [implementere en sikker hybrid netværksarkitektur i Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Anbefalinger

Dette afsnit indeholder en oversigt over anbefalinger til implementering af Active Directory-Domænetjenester i Azure, som dækker:

- VM anbefalinger.

- Netværk anbefalinger.

- Anbefalinger til sikkerhed. 

- Active Directory-område anbefalinger.

- Active Directory FSMO-placeringen anbefalinger.

>[AZURE.NOTE] Dokumentet [retningslinjer for installation af Windows Server Active Directory på Azure virtuelle maskiner] [ ad-azure-guidelines] indeholder mere detaljerede oplysninger om mange af disse ting.

### <a name="vm-recommendations"></a>VM anbefalinger

Oprette FOS med tilstrækkelige ressourcer til at håndtere de forventede mængde trafik. Brug størrelsen på de computere, der er vært for AD DS lokalt som udgangspunkt. Overvåge ressource anvendelsen; Du kan ændre størrelsen på FOS og skalere, hvis de er for stor. Du kan finde flere oplysninger om sidetilpasning AD DS domæne enheder, se [Kapacitetsplanlægning af Active Directory Domain Services][capacity-planning-for-adds].

Oprette en separat virtuelle datadisk til lagring af databasen og logge på SYSVOL for AD. Gem ikke disse elementer på den samme disk som operativsystemet. Bemærk, at datadisce, der er knyttet til en VM bruges der som standard Skriv gennem cachelagring. Dog kan denne formular for cachelagring af i konflikt med Active Directory-Domænetjenester krav. Angiv indstillingen *Host Cache foretrukket* på harddisken data til *ingen*derfor. Du kan finde flere oplysninger, se [placeringen af Windows Server AD DS database og SYSVOL][adds-data-disks].

Installer mindst to VM'er, der kører AD DS som domænecontrollere til Azure virtuelle netværk [tilgængelighed](#Availability-considerations) årsager.

### <a name="networking-recommendations"></a>Netværk anbefalinger

Konfigurere netværksgrænsefladen for hver af de FOS vært AD DS med statisk private IP-adresser. Denne konfiguration understøtter bedre DNS på hver af AD FOS. Du kan finde flere oplysninger, se, [hvordan du angive en statisk privat IP-adresse i portalen Azure][set-a-static-ip-address].

> [AZURE.NOTE] Giv ikke AD DS FOS offentlige IP-adresser. Se [sikkerhedsovervejelser] [ security-considerations] kan finde flere oplysninger.

Skal du sikre dig, at trafik kan flyde frit mellem AD-servere i skyen og lokalt:

- Føje NSG regler til det AD undernet, som tillade indgående trafik fra det lokale. Yderligere oplysninger om de porte, der anvender Active Directory-Domænetjenester, se [Active Directory og Active Directory Domain Services-Portkravene][ad-ds-ports].

- Kontrollér, at UDR tabeller ikke omdirigere AD DS trafik via NVAs bruges i dette scenarie. Til dine egne installationer, afhængigt af hvilken NVAs, du bruger, kan du vil omdirigere pågældende trafik.

### <a name="security-recommendations"></a>Anbefalinger til sikkerhed

AD DS servere håndtere godkendelse og er derfor meget følsomme elementer. Forhindre direkte visning af AD DS-servere på internettet. Anbring AD DS servere i et separat undernet med sin egen firewall. Sørg for, at de porte, der er nødvendige for at bruge Active Directory-Domænetjenester for godkendelse og autorisation og for synchronzing servere forbliver åbent, men Luk alle andre porte. Du kan finde flere oplysninger i [Active Directory og Active Directory Domain Services-Portkravene][ad-ds-ports]. [Løsningskomponenter] [ solution-components] afsnit senere i dette dokument vises på NSG regler, der løsningen eksempel bruges til at åbne porte.

Du kan bruge NSG regler til at oprette en simpel firewall. Hvis du har brug for mere omfattende beskyttelse kan du implementere en ekstra sikkerhed kanten rundt om servere ved hjælp af et par undernet og NVAs, som beskrevet i dokumentet [implementere en sikker hybrid netværksarkitektur med internetadgang i Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

Brug enten BitLocker eller Azure disk kryptering til at kryptere disken vært for databasen, Active Directory-Domænetjenester.

### <a name="active-directory-site-recommendations"></a>Active Directory-område anbefalinger

Du kan bruge websteder i Active Directory-Domænetjenester til gruppen sammen domæne enheder, der er forbundet via et hurtigt link. Domænecontrollere på samme AD DS webstedet gentage deres ændringer i kataloget automatisk, og lille konfiguration er påkrævet til at håndtere replikering.

Du kan styre gentagelse trafik mellem Azure og dit lokale datacenter, anbefales det at tilføje en separat AD DS webstedet til at repræsentere den adresseområde, der bruges af Azure. Du kan konfigurere hyperlink til et websted mellem dine lokale Active Directory-Domænetjenester websteder og styre på tværs af websteder gentagelse mere effektivt.

Du kan også bruge websted adskillelsen anvende forskellige gruppepolitikobjekter (GPO'er) til joinforbundne computere i Azure, og at drage fordel af programmer, der er "websted opmærksom på", som System Center Configuration Manager.

### <a name="active-directory-fsmo-placement-recommendations"></a>Active Directory FSMO-placeringen anbefalinger

Fleksible enkelt Master handling (FSMO) servere er specialiserede domæne enheder reposnsible for data konsistens på tværs af forskellige indstillinger i AD DS, vises nedenfor.

- **Skema Master**. Dette er en skov hele rolle, der fører strukturen i skemaet bruges af Active Directory-Domænetjenester.

- **Navngive Master domæne**. Dette er en skov hele rolle, der administrerer oplysninger om domænenavne i dette område.

- **Primære domæne-Controller (PDC)**. Dette er en rolle til hele domænet. PDC'EN håndterer adgangskode opdateringer og spærringer. Det er høre andre DCs når godkendelse serviceanmodninger har uoverensstemmende adgangskoder. PDC'EN også håndterer Gruppepolitik opdateringer, og er destinationen DC for ældre programmer og nogle administratorværktøjer, som udfører nogle skrivbar handlinger.

- **Relative ID (RELATIVE) Master**. RID bruges til at hjælpe entydigt identificerer objekter i en mappe. Denne server er ansvarlig for oprettelse af en hele domænet samling af RID til brug af alle AD-servere i domænet.

- **Infrastruktur rolle**. Et objekt i ét domæne kan referere til et objekt i et andet domæne. Denne rolle i hele domænet er ansvarlig for at opdatere et objekts SID og entydigt navn i en objektreference i tværs af domæner. Bemærk, at en server, implementere denne rolle kan også fungere som en Global katalog (globalt katalog)-server.

Kan finde flere oplysninger i [Active Directory FSMO-roller i Windows][AD-FSMO-roles-in-windows].

Det anbefales dette scenarie skal du undgå anvender FSMO-roller for domænecontrollerne i Azure. 

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Oprette en tilgængeligheden af indstille for AD-servere. Sørg for, at der er mindst to servere i dialogboksen Angiv. AD-servere i skyen skal være domænecontrollerne i det samme domæne. Dette vil aktivere automatisk replikering mellem servere.

Overvej også at angive servere som [standby handlinger mastere] [ standby-operations-masters] i tilfælde af forbindelse til en server, der fungerer som en FSMO-rolle mislykkes.

## <a name="security-considerations"></a>Overvejelser om sikkerheden

Hvis alle domæne administratorens opgaver er sandsynligvis udføres ved hjælp af de lokale DCs, kan du overveje at skrivebeskytte DCs i skyen. En skrivebeskyttet DC kun fører et undersæt af brugernes legitimationsoplysninger (nok til at udføre godkendelse lokalt) og kan konfigureres til at kun cacheoplysninger om bestemte brugere. Derfor, hvis en skrivebeskyttet DC afsløret, kun oplysninger for cachelagrede brugere påvirkes, i stedet for detaljerne for hver konto i domænet. Du kan finde flere oplysninger, se [Og skrivebeskyttet domænecontrollere][read-only-dc].

For at minimere sikkerhedsrisikoen af individuelle brugerkonti, og at forhindre forsøg på at indbrud, skal du følge fremgangsmåde til at angive og vedligeholde brugernes adgangskoder i AD. Få mere at vide i [Bedste fremgangsmåder til sikres adgangskodepolitikker][best_practices_ad_password_policy]. Desuden være forsigtig, hvilke grupper du tildele brugere. For eksempel kan ikke gøre almindelige brugermedlemmer af virksomhedsadministratorer og skema administratorgruppen på domænets administratorgruppe.

## <a name="scalability-considerations"></a>Overvejelser i forbindelse med skalerbarhed

AD er automatisk SVG ved domæne enheder, der er en del af det samme domæne. Anmodninger om fordeles på tværs af alle enheder i et domæne. Du kan tilføje et andet domænenavn fra domænecontrolleren, og den synkroniseres automatisk med domænenavn. Konfigurer ikke en separat belastningsjustering at dirigere trafik til enheder inden for domænet. Sikre, at alle domænecontrollere har tilstrækkelig hukommelse og lagerplads ressourcer til at håndtere domænedatabasen. Ideelt set skal alle domænenavn fra domænecontrolleren FOS samme størrelse.

## <a name="management-considerations"></a>Overvejelser i forbindelse med administration

Kan ikke kopiere Virtuelle filer over domæne enheder i stedet for at udføre regelmæssige sikkerhedskopier, da gendanne dem kan medføre uoverensstemmelser i tilstand mellem domænecontrollere.

Luk og genstart en VM, der kører rollen domænecontroller i Azure i operativsystemet gæst i stedet for ved hjælp af indstillingen Luk Azure-portalen. Ved hjælp af portalen Azure for at lukke en VM får VM kan deallokeres. Denne handling nulstiller VM-GenerationID, som er uønskede for et domænenavn fra domænecontrolleren. Når VM-GenerationID nulstilles, invocationID af AD-lager, nulstilles også, RELATIVE puljen kasseret og SYSVOL er markeret som ikke-autoritative.

## <a name="monitoring-considerations"></a>Overvågning overvejelser i forbindelse med

Ned til at overvåge og vedligeholde et netværk AD serveres kan medføre problemer f.eks.:

- **Logonfejl**. Logonfejl kan opstå i hele det domæne eller område, hvis en sikkerhed og rettighedsadministration relation eller navn opløsning mislykkes, eller hvis en globalt katalogserver ikke kan finde ud af universal gruppemedlemskab.

- **Konto låsning**. Bruger og service-konti kan være låst ude, hvis den primære domænecontrolleren ikke er tilgængelig, eller gentagelse mislykkes mellem flere domænecontrollere.

- **Domænecontrolleren fejl**. Hvis det drev, der indeholder filen NTDS.dit øges løber tør for plads på harddisken, stoppe domænecontrolleren kan fungere.

- **Programfejl**. Programmer, der er vigtige for virksomheden, som Microsoft Exchange eller en anden e-mail-program, kan mislykkes, hvis address book forespørgsler til mappen mislykkes.

- **Inkonsekvent directory-data**. Hvis gentagelse mislykkes i et stykke tid, duplikerede objekter kan oprettes i mappen og kan kræve, at omfattende diagnosticering og klokkeslæt for at eliminere.

- **Fejl ved oprettelse af konto**. Et domænenavn fra domænecontrolleren kan ikke oprette bruger-eller computer, hvis den bruger alle dens levering af relative id'er og den RELATIVE master ikke er tilgængelig.

- **Sikkerhed politik mislykkedes**. Hvis den delte mappe SYSVOL ikke gentage korrekt, anvendes Gruppepolitik objekter og sikkerhedspolitikker korrekt ikke på klienter.

Overvåge AD servere omhyggeligt, og Vær klar til at handle korrigerende hurtigt. Oprette en tjekliste over overvåge opgaver skal udføres med et interval, behov. For eksempel kan du planlægge følgende kritiske opgaver dagligt:

- Undersøge og løse beskeder, der rapporteres af domæne enheder 

- Kontrollér, at alle domænecontrollere kan kommunikere og, at replikering virker.

- Sørg for, at SYSVOL forbliver delt.

- Løse problemer med synkronisering af klokkeslæt.

- Søg efter diskplads på de fysiske drev, der bruges af AD.

Du kan udføre andre rutinemæssige opgaver mindre ofte. Du kan for eksempel Gennemse tillidsforhold og kontrollere, om forældede eller brudte tillidsforhold ugentlig og bekræfte, at alle domænecontrollere kører ved hjælp af de samme servicepakker og programrettelse rettelser hver måned.

Finde flere oplysninger i [Overvågning Active Directory][monitoring_ad]. Du kan installere værktøjer som [Microsoft Systems Center] [ microsoft_systems_center] på overvågning serveren (se [arkitektur diagram][architecture]) til at udføre disse opgaver. 

## <a name="solution-components"></a>Løsningskomponenter

Den foreslåede løsning for denne arkitektur opretter en sikker hybrid netværk, som beskrevet i dokumenterne, [implementere en sikker hybrid netværksarkitektur i Azure] [ implementing-a-secure-hybrid-network-architecture] og [implementere en sikker hybrid netværksarkitektur med internetadgang i Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access], men med tilføjelse af følgende elementer:

- En Azure ressourcegruppe med navnet *basename*- dns-indbyggede rg, hvor *basename* er et præfiks, du angiver, når du installerer løsningen.

- To Azure FOS kaldet *basename*-ad1-vm og *basename*-ad2-vm, der er oprettet i gruppen *basename*- dns-indbyggede rg ressource. Disse FOS er konfigureret som AD servere med katalogtjenester og DNS installeret og konfigureret.

- En NSG med navnet *basename*-ad-nsg i gruppen *basename*- ntwk-indbyggede rg Azure ressource. Denne ressourcegruppe er en del af den infrastruktur, der udgør sikker hybrid netværket, men den nye NSG er en tilføjelse, der definerer indgående sikkerhedsregler for AD-servere, som vist i følgende tabel:


    Prioritet|Navn|Kilde|Destination|Tjenesten|Handling|
    --------|----|------|-----------|-------|------|
    170|vnet-port53|10.0.0.0/16|En hvilken som helst|Custom(any/53)|Tillad|
    180|vnet-port88|10.0.0.0/16|En hvilken som helst|Custom(any/88)|Tillad|
    190|vnet-port135|10.0.0.0/16|En hvilken som helst|Custom(any/135)|Tillad|
    200|vnet-til-port137-9|10.0.0.0/16|En hvilken som helst|Custom(any/137-139)|Tillad|
    210|vnet-port389|10.0.0.0/16|En hvilken som helst|Custom(any/389)|Tillad|
    220|vnet-port464|10.0.0.0/16|En hvilken som helst|Custom(any/464)|Tillad|
    230|vnet-til-rpc-dynamiske|10.0.0.0/16|En hvilken som helst|Custom(any/49152-65535)|Tillad|
    240|onprem ad til port53|192.168.0.0/24|En hvilken som helst|Custom(any/53)|Tillad|
    250|onprem ad til port88|192.168.0.0/24|En hvilken som helst|Custom(any/88)|Tillad|
    260|onprem ad til port135|192.168.0.0/24|En hvilken som helst|Custom(any/135)|Tillad|
    270|onprem ad til port389|192.168.0.0/24|En hvilken som helst|Custom(any/389)|Tillad|
    280|onprem ad til port464|192.168.0.0/24|En hvilken som helst|Custom(any/464)|Tillad|
    290|MGMT-rdp-Tillad|10.0.0.128/25|En hvilken som helst|Custom(any/3389)|Tillad|
    300|gateway-Tillad|10.0.255.224/27|En hvilken som helst|Custom(any/any)|Tillad|
    310|Tillad præsentation|10.0.255.192/27|En hvilken som helst|Custom(any/any)|Tillad|
    320|vnet-nægte|En hvilken som helst|En hvilken som helst|Custom(any/any)|Tillad|

    Active Directory-Domænetjenester bruger port 53, 89, 135, 389 og 464 til at acceptere indgående gentagelse og godkendelsestrafik. I denne tabel, er lokale domænenavn fra domænecontrolleren i adresse mellemrum 192.168.0.0/24 (din adresseområde kan variere – du angiver disse oplysninger som en parameter til de skabeloner, der er installeret af løsningen.

    NSG definerer også følgende sikkerhedsregler, aktivere synkronisering og godkendelse trafikken at flyde tilbage til det lokale netværk udgående:

    Prioritet|Navn|Kilde|Destination|Tjenesten|Handling|
    --------|----|------|-----------|-------|------|
    100|op-port53|En hvilken som helst|192.168.0.0/24|Custom(any/53)|Tillad|
    110|op-port88|En hvilken som helst|192.168.0.0/24|Custom(any/88)|Tillad|
    120|op-port135|En hvilken som helst|192.168.0.0/24|Custom(any/135)|Tillad|
    130|op-port389|En hvilken som helst|192.168.0.0/24|Custom(any/389)|Tillad|
    140|op-port 445|En hvilken som helst|192.168.0.0/24|Custom(any/445)|Tillad|
    150|op-port464|En hvilken som helst|192.168.0.0/24|Custom(any/464)|Tillad|
    160|op-rpc-dynamiske|En hvilken som helst|192.168.0.0/24|Custom(any/49152-65535)|Tillad|

Scriptet leveres med løsningen også udføre følgende opgaver:

- Den lægger *basename*-ad1-vm og *basename*-ad2-vm servere som at domænet. Du kan få vist disse servere i *Active Directory-brugere og computere* konsollen i domænecontrolleren lokalt:

![[1]][1]

- Det opretter et nyt undernet (10.0.0.0/16) for et AD-websted med navnet Azure-VNet-Ad-websted til domænet. Dette websted indeholder *basename*-ad1-vm og *basename*-ad2-vm-servere. 

- Det føjer IP-mellem netværksområder indstillinger, konfigurere gentagelse intervallet mellem det lokale websted og domænecontrollerne i skyen. Du kan se indstillingerne for undernet, websteder og indstillinger for transport i *Active Directory-områder og servere* konsollen i domænecontrolleren lokalt:

![[2]][2]

## <a name="deployment"></a>Installation

Eksempel løsningen har følgende prerequsites:

- Oprette forbindelse til gatewayen Azure VPN, du allerede har konfigureret domænet i det lokale miljø, og at du har konfigureret DNS, og installeret Routing og Remote Access services for at understøtte en VPN-forbindelse.


- Du har installeret den nyeste version af Azure CLI. [Følg disse instruktioner for oplysninger om][cli-install].

- Hvis du anvender løsningen fra Windows, skal du installere et værktøj, der indeholder en fest shell, såsom [GitHub skrivebord][github-desktop].

>[AZURE.NOTE] Hvis du ikke har adgang til et eksisterende lokalt domæne, kan du oprette et testmiljø ved hjælp af [onpremdeploy.sh] [ onpremdeploy] bash script. Dette script opretter en netværks- og VM i skyen, der simulerer en meget grundlæggende lokalt opsætning. Redigere dette script før du kører, og Angiv følgende variabler defineret i starten af filen:
>
> - **BASE_NAME**. Et brugerdefineret præfiks for ressourcegruppe og VM, der er oprettet af scriptet. Dette element skal være **længere end 5 tegn** ellers scriptet forsøger at oprette en VM med et ugyldigt navn og mislykkes.
> 
> - **Abonnement**. Dit Azure abonnement-ID. Ressourcegruppen oprettes i denne suscription.
> 
> - **Placering**. Den Azure placering, hvor til at oprette ressourcegruppen som *eastus* eller *westus*.
> 
> - **ADMIN_USER_NAME**. Navnet skal bruges til administratorkontoen i VM.
> 
> - **ADMIN_PASSWORD**. Adgangskode til administratorkontoen.

Udfør følgende trin for at opbygge løsningen eksempel:

1. hente og redigere [azuredeploy.sh] [ azuredeploy] scripts og angive følgende parametre i starten af filen:

    - **BASE_NAME**. Et brugerdefineret præfiks for grupper og VM'er, der er oprettet af scriptet. Som tidligere skal dette element ikke være **længere end 5 tegn**.

    - **Abonnement**. Dit Azure abonnement-ID.

    - **OS_TYPE**. Adgang til niveau FOS operativsystem (*Windows* eller *Linux*), der skal bruges til internettet, business og data. Bemærk, at alle AD-servere, der er oprettet af scriptet kører Windows Server 2012, uanset denne indstilling.

    - **Domænenavn**. Navnet på det lokale domæne. Bemærk, at hvis du bruger det miljø, der er oprettet af scriptet onpremdeploy.sh, skal det være *contoso.com*.

    - **Placering**. Den Azure placering, hvor til at oprette ressourcegruppe.

    - **ADMIN_USER_NAME**. Navnet skal bruges til administratorkonti i de forskellige FOS.

    - **ADMIN_PASSWORD**. Adgangskode til administratorkontoen.

    - **ON_PREMISES_PUBLIC_IP**. Den offentlige IP-adresse på den lokale VPN-computer.

    - **ON_PREMISES_ADDRESS_SPACE**. Den interne adresseplads i det lokale netværk. Hvis du bruger det miljø, der er oprettet af scriptet onpremdeploy.sh, kan det skal være 192.168.0.0/16.

    - **VPN_IPSEC_SHARED_KEY**. Den IPSec delte nøgle bruges til at oprette VPN-forbindelse mellem lokale netværk og Azure VPN gatewayen.

    - **ON_PREMISES_DNS_SERVER_ADDRESS**. IP-adressen på den lokale DNS-server. Hvis du bruger det miljø, der er oprettet af scriptet onpremdeploy.sh, skal det være 192.168.0.4

    - **ON_PREMISES_DNS_SUBNET_PREFIX** Adressepræfikset i det lokale undernet. Hvis du bruger det miljø, der er oprettet af scriptet onpremdeploy.sh, kan det skal være 192.168.0.0/24.

    >[AZURE.NOTE] Hvis du vil gemme ressourcer og det klokkeslæt, opretter scriptet ikke business eller data access niveauer. Hvis du har brug for disse elementer, kan du Fjern kommentar fra følgende afsnit i scriptet azuredeploy.sh:
    >
    >
    > ```
    > #### # create biz tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_BIZ_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${BIZ_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${BIZ_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_BIZ_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > #### 
    > #### # create db tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_DB_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${DB_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${DB_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_DB_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ```

2. Åbn en fest shell prompt, og flytte til den mappe, der indeholder scriptet azuredeploy.sh.

3. Log på din Azure-konto. Skriv følgende kommando i shell fest:

    ```
    azure login
    ```

    Følg vejledningen for at oprette forbindelse til Azure.

4. Kør kommandoen `./azuredeploy.sh`, og Vent, mens scriptet opretter netværksinfrastruktur.

5. Når du bliver spurgt *skal du kontrollere, at VNet er blevet oprettet*, du brug portalen Azure til at kontrollere, en ressourcegruppe med navnet *basename*- ntwk-indbyggede rg er blevet oprettet, og at den indeholder elementer, der ligner dem vist på følgende billede:

    ![[3]][3]

    >[AZURE.NOTE] *Basename* blev i eksempler vises indstillet til *skyen* , da scriptet blev udført.

    Klik *basename*- vnet VNet på *undernet*, og Bekræft, at der er oprettet de undernet, der er vist nedenfor:

    ![[4]][4]

6. Når du bliver spurgt i vinduet fest shell, trykke på en tast og Vent, mens der oprettes web niveau og justering af belastning.

7. Bruge Azure-portalen for kommandoprompten *skal du kontrollere, at Web niveauet er blevet oprettet korrekt*til at kontrollere, at en ressourcegruppe med navnet *basename*web-niveau-indbyggede rg er blevet oprettet, og, at den indeholder elementer, der svarer til dem, der er vist nedenfor:

    ![[5]][5]

8. Når du bliver spurgt i vinduet fest shell, trykke på en tast og Vent, mens NVAs oprettes.

9. Bruge portalen Azure til at kontrollere, at en ressourcegruppe med navnet *basename*- mgmt-indbyggede rg er blevet oprettet med følgende indholdet for kommandoprompten *skal du kontrollere, at for er blevet oprettet korrekt*:

    ![[6]][6]

10. Når du bliver spurgt i vinduet fest shell, trykke på en tast og Vent, mens jumpbox oprettes.

11. Bruge Azure-portalen for kommandoprompten *skal du kontrollere, at jumpbox er blevet oprettet korrekt*til at kontrollere, at følgende elementer er blevet føjet til gruppen *basename*- mgmt-indbyggede rg ressource:

    - Angiv en tilgængelighed kaldet *basename*- jb-som.

    - En VM med navnet *basename*- jb-vm.

    - Et netværk brugergrænseflade kaldet *basename*- jb-nic.

12. Når du bliver spurgt i vinduet fest shell, trykke på en tast og Vent, mens Azure VPN-gateway og forbindelsen er oprettet. Bemærk, at dette trin kan tage op til 30 minutter at gennemføre.

13. Bruge Azure-portalen for kommandoprompten *skal du kontrollere, at gatewayen VPN er blevet oprettet korrekt*til at kontrollere, at følgende elementer er blevet føjet til gruppen *basename*- ntwk-indbyggede rg ressource:

    - En gateway lokale netværk hedder på-lokale-lgw.
    
    - Et virtuelt netværksgateway hedder *basename*- vpngw.

    - En gateway-forbindelse med navnet *basename*- vnet-vpnconn. Bemærk, at status for denne forbindelse muligvis er *ikke forbundet* , hvis du endnu ikke har konfigureret lokalt slutningen af forbindelsen. Du kan løse dette senere.

14. Når du bliver spurgt i vinduet fest shell, trykke på en tast og Vent, mens FOS og andre ressourcer for DMZ oprettes.

15. Bruge portalen Azure til at kontrollere, at en ressourcegruppe med navnet *basename*-dmz-indbyggede rg er blevet oprettet med følgende indholdet for kommandoprompten *skal du kontrollere, at DMZ er blevet oprettet korrekt*:

    ![[7]][7]

16. Når du bliver spurgt i vinduet fest shell, skal du trykke på en nøgle. Følgende prompter skal vises:

    ```text
    Manual Step...

    Please configure your on-premises network to connect to the Azure VNet

    Make sure that you can connect to the on-premises AD server from the Azure VMs
    ```

    Log på din lokale computer, der opretter forbinder til gatewayen Azure og konfigurere forbindelsen korrekt. Tilføje statiske omdirigerer til den lokale gateway-enhed, der omdirigerer requestsfor adresseområde 10.0.0.0/16 gennem gatewayen til VNet. Trin til at udføre dette kan variere, alt efter hvordan du opretter forbindelse. Se [implementere en Hybrid netværksarkitektur med Azure og lokale VPN] [ implementing-a-hybrid-network-architecture-with-vpn] kan finde flere oplysninger.

    Bemærk, at du kan finde den offentlige IP-adresse gatewayen Azure VPN ved hjælp af portalen Azure undersøge gatewayen *basename*- vpngw i gruppen *basename*- ntwk-indbyggede rg ressource:

    ![[8]][8]

    Du kan bestemme, om forbindelsen er oprettet korrekt, ved at se på status for *basename*- vnet vpnconn forbindelsen. Det skal du vælge *tilsluttet*.

    For at teste forbindelsen, skal du åbne en forbindelse til Fjernskrivebord til jumpbox (10.0.0.254) fra en computer, der er placeret i dit lokale netværk.

17. Når du bliver spurgt i vinduet fest shell, skal du trykke på en nøgle. Spørg, *skal du trykke på en tast for at opdatere VNet indstillingen for VNet til at pege på lokale DNS*på Næste, trykke på en tast, og Vent, mens DNS-indstillingerne for VNet er opdateret til den værdi, du har angivet som parameteren **ON_PREMISES_DNS_SERVER_ADDRESS** i scriptet azuredeploy.sh.

18. Når du bliver spurgt, *skal du kontrollere, at indstillingen DNS-server på VNet er blevet opdateret*, skal du bruge Azure portal undersøge indstillingen *DNS-servere* af *basename*- vnet VNet i gruppen *basename*- ntwk-indbyggede rg ressource. Det skal du vælge *Custom DNS*, og den *primære DNS-server* skal være adressen på din lokale DNS-server:

    ![[9]][9]

19. Trykke på en tast, og Vent, mens ressourcegruppe ved tilbageholdelse AD-servere i skyen oprettes kommandoprompten *skal du trykke på en tast for at oprette ressourcegruppe AD-servere* i vinduet fest shell.

20. Trykke på en tast kommandoprompten *skal du trykke på en tast for at oprette FOS AD-servere* i vinduet fest shell, og vent FOS oprettet og tilføjes til ressourcegruppen.

21. Når den, *skal du trykke på en tast for at deltage i FOS til det lokale domæne* vises, gå til Azure-portalen, og Bekræft, at en gruppe med navnet *basename*- dns-indbyggede rg er blevet oprettet, og, at den indeholder to FOS (*basename*-ad1-vm og *basename*-ad2-vm):

    ![[10]][10]

22. Kontrollér, at en NSG er blevet oprettet kaldet i gruppen *basename*- ntwk-indbyggede rg ressource *basename*-ad-nsg. Undersøge indgående og udgående sikkerhed reglerne for denne NSG. De skal svare til dem er angivet i tabellerne i [løsningskomponenter] [ solution-components] sektion.

23. Når du bliver spurgt i vinduet fest shell, trykke på en tast og Vent, mens FOS føjes til det lokale domæne.

24. På den *skal du gå til lokale AD-serveren for at bekræfte, at computerne, der er blevet føjet til domænerne* Spørg, oprette forbindelse til din lokale computer og bruger *Active Directory-brugere og computere* konsollen til at kontrollere, at begge FOS er føjet til domænet:

    ![[11]][11]

25. Når du bliver spurgt i vinduet fest shell, trykke på en tast og Vent, mens webstedet AD replikering er oprettet i domænet.

26. På den *skal du gå til lokale AD-serveren for at bekræfte, at webstedet replikering er blevet oprettet* Spørg, skal du bruge konsollen *Active Directory-områder og -tjenester* til at kontrollere, at en gentagelse websted med navnet *Azure-Vnet-Ad-websted* er blevet oprettet korrekt, sammen med et link til IP-mellem netværksområder titlen *AzureToOnpremLink*og et undernet, der refererer til VNet:

    ![[12]][12]

27. Når du bliver spurgt i vinduet fest shell, trykke på en tast og Vent, mens scriptet installerer katalogtjenester og DNS på hver af AD FOS.

28. Når der vises prompten *skal du logge på hver Azure AD-serveren for at bekræfte, at katalogtjenester er blevet konfigureret korrekt* , Åbn en forbindelse til Fjernskrivebord fra en lokal maskine til jumpbox (*basename*- jb-vm), og Åbn derefter en anden forbindelse til Fjernskrivebord fra jumpbox til den første AD-server (*basename*-ad1-vm). Log på med det **domænenavn**, **ADMIN_USER_NAME**og **ADMIN_PASSWORD** , du angav i scriptet azuredeploy.sh. Bruger Server Manager skal kontrollere, at rollerne AD DS og DNS har begge er blevet tilføjet. Gentag denne proces for den anden AD-server (*basename*-ad2-vm).

29. Når du bliver spurgt i vinduet fest shell, skal du trykke på en nøgle. Når du bliver bedt om det *skal du trykke på en tast for at angive Azure VNet DNS-indstillingerne til at pege på DNS-Posterne i Azure* vises, trykke på en tast og tillade scriptet til at opdatere DNS-indstillingerne for VNet.

30. Når du bliver bedt om det *skal du kontrollere, at VNet DNS indstilling er blevet opdateret reference Azure VM DNS servers* vises, ved hjælp af Azure portalen Kontrollér indstillingen *DNS-servere* af *basename*- vnet VNet i gruppen *basename*- ntwk-indbyggede rg ressource. De to AD FOS skal nu henvise til de primære og sekundære DNS-servere:

    ![[13]][13]

31. Genstart hver af AD FOS før du fortsætter. Dette trin er nødvendige for at sikre, at de hver opfanger de korrekte DNS-indstillinger fra Azure. Vent, indtil begge FOS kører før du fortsætter.

32. Når du bliver spurgt i vinduet fest shell, skal du trykke på en nøgle. Trykke på en tast ved næste prompt, *skal du trykke på en tast for at anvende gatewayen UDR til det gateway undernet (det kan være fjernet)*, og det script til at opdatere gatewayen UDR.

33. Kontrollér, at scriptet er fuldført.

## <a name="next-steps"></a>Næste trin

- Lær de bedste fremgangsmåder til [oprettelse af et område, der tilføjer ressource] [ adds-resource-forest] i Azure.

- Lær de bedste fremgangsmåder til [oprettelse af en ADFS-infrastruktur] [ adfs] i Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[architecture]: #architecture_diagram
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[capacity-planning-for-adds]: http://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048(v=ws.11).aspx
[powershell-ad]: https://technet.microsoft.com/en-us/library/ee617195.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[cli-install]: https://azure.microsoft.com/documentation/articles/xplat-cli-install
[github-desktop]: https://desktop.github.com/
[sssd-and-active-directory]: https://help.ubuntu.com/lts/serverguide/sssd-ad.html
[set-a-static-ip-address]: https://azure.microsoft.com/documentation/articles/virtual-networks-static-private-ip-arm-pportal/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[adds-data-disks]: https://msdn.microsoft.com/library/azure/jj156090.aspx#BKMK_PlaceDB
[AD-FSMO-recommendations]: #active_directory_FSMO_placement_recommendations
[AD-FSMO-roles-in-windows]: https://support.microsoft.com/en-gb/kb/197132
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[transfer-FSMO-roles]: https://technet.microsoft.com/library/cc816946(v=ws.10).aspx
[view-fsmo-roles]: https://technet.microsoft.com/library/cc816893(v=ws.10).aspx
[read-only-dc]: https://technet.microsoft.com/library/cc732801(v=ws.10).aspx
[AD-sites-and-subnets]: https://blogs.technet.microsoft.com/canitpro/2015/03/03/step-by-step-setting-up-active-directory-sites-subnets-site-links/
[sites-overview]: https://technet.microsoft.com/library/cc782048(v=ws.10).aspx
[implementing-adfs]: ./guidance-iaas-ra-secure-vnet-adfs.md
[onpremdeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/onpremdeploy.sh
[azuredeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/azuredeploy.sh
[solution-components]: #solution_components

[0]: ./media/guidance-iaas-ra-secure-vnet-ad/figure1.png "Secure hybrid netværksarkitektur med Active Directory"
[1]: ./media/guidance-iaas-ra-secure-vnet-ad/figure2.png "Active Directory-brugere og computere konsollen listen over de to Azure FOS som servere"
[2]: ./media/guidance-iaas-ra-secure-vnet-ad/figure3.png "Konsollen Active Directory-områder og -tjenester, der viser indstillingerne for gentagelse for webstedet i skyen"
[3]: ./media/guidance-iaas-ra-secure-vnet-ad/figure4.png "Indholdet af den basename-ntwk-indbyggede rg ressourcegruppe"
[4]: ./media/guidance-iaas-ra-secure-vnet-ad/figure5.png "Alle undernet i basename vnet VNet"
[5]: ./media/guidance-iaas-ra-secure-vnet-ad/figure6.png "Elementer i web-lag"
[6]: ./media/guidance-iaas-ra-secure-vnet-ad/figure7.png "NVAs i gruppen basename-mgmt-indbyggede rg ressource"
[7]: ./media/guidance-iaas-ra-secure-vnet-ad/figure8.png "Ressourcer i gruppen basename-dmz-indbyggede rg ressource"
[8]: ./media/guidance-iaas-ra-secure-vnet-ad/figure9.png "Finde den offentlige IP-adresse gatewayen Azure VPN"
[9]: ./media/guidance-iaas-ra-secure-vnet-ad/figure10.png "DNS-serverindstillingerne for den * basename *-vnet VNet"
[10]: ./media/guidance-iaas-ra-secure-vnet-ad/figure11.png "Den * basename *-dns-indbyggede rg ressourcegruppe, der indeholder AD serveren FOS"
[11]: ./media/guidance-iaas-ra-secure-vnet-ad/figure12.png "Active Directory-brugere og computere konsollen listen over AD serveren FOS som medlemmer af domænet"
[12]: ./media/guidance-iaas-ra-secure-vnet-ad/figure13.png "Konsollen Active Directory-områder og -tjenester, der viser webstedet gentagelse Azure AD-servere"
[13]: ./media/guidance-iaas-ra-secure-vnet-ad/figure14.png "DNS-serverindstillingerne for den basename vnet VNet refererer til AD-servere i skyen"