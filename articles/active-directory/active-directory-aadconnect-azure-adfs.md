<properties
    pageTitle="Active Directory Federation Services i Azure | Microsoft Azure"
    description="I dette dokument lærer du, hvordan du kan installere AD FS i Azure for høj tilgængelighed."
    keywords="installere AD FS i azure, implementere azure adfs, azure adfs, azure ad fs, installere adfs, installere ad fs adfs i azure, installere adfs i azure, installere AD FS i azure, adfs azure, Introduktion til AD FS, Azure, AD FS i Azure, iaas, ADFS, Flyt adfs til azure"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/03/2016"
    ms.author="anandy;billmath"/>

# <a name="ad-fs-deployment-in-azure"></a>AD FS installation i Azure 

AD FS indeholder forenklet, sikret identitetssammenslutninger og muligheder enkeltlogon (SSO) på internettet. Sammenslutning med Azure AD eller O365 giver brugerne mulighed at godkende ved hjælp af lokale legitimationsoplysninger og få adgang til alle ressourcer i skyen. Som et resultat, bliver det vigtigt at have en meget tilgængelige AD FS-infrastruktur til at sikre adgang til ressourcer både lokalt og i skyen. Implementering af AD FS i Azure kan hjælpe med at opnå den høje tilgængelighed, der er påkrævet med minimale anstrengelser.
Der er flere fordele ved med at udrulle AD FS i Azure, vises nogle af dem nedenfor:

* **Høj tilgængelighed** – med en potens af Azure tilgængelighed sæt, du sikre dig en meget tilgængelige infrastruktur.
* **Let at skalere** – har brug for mere ydeevne? Nemt overføre til mere avancerede maskiner ved blot nogle få klik i Azure
* **Tværs geografisk redundans** – med Azure geografisk redundans, der kan du være sikker på, at din infrastruktur er meget tilgængelig over hele verden
* **Let at administrere** – med indstillinger for yderst forenklet administration i Azure-portalen, administration af din infrastruktur er meget let og problemfri 

## <a name="design-principles"></a>Designprincipper

![Installation design](./media/active-directory-aadconnect-azure-adfs/deployment.png)

Diagrammet ovenfor viser den anbefalede grundlæggende topologi til at begynde at implementere infrastrukturen AD FS i Azure. Principper bag de forskellige dele af topologien vises nedenfor:

* **DC / ADFS-servere**: Hvis du har færre end 1000 brugere kan du blot installere AD FS rolle på dit domæne enheder. Hvis du ikke vil nogen indflydelse på ydeevnen på domænecontrollere, eller hvis du har mere end 1000 brugere, derefter installere AD FS på separate servere.
* **WAP Server** – det er nødvendigt at installere Web Application Proxy-servere, så brugerne kan få adgang til AD FS, når de ikke er på virksomhedens netværk også.
* **DMZ**: The Web Application proxyservere placeres i DMZ og kun TCP/443 adgang er tilladt mellem DMZ og det interne undernet.
* **Indlæse balancere**: for at sikre høj tilgængelighed af AD FS og Web Application proxyservere, det anbefales at bruge en intern justering af belastning for AD FS-servere og Azure belastning for Web Application Proxy-servere.
* **Tilgængelighed sæt**: for at give redundans til AD FS-installation, anbefales det, at du grupperer to eller flere virtuelle maskiner i en tilgængelighed, der er angivet for lignende arbejdsmængder. Denne konfiguration sikrer, at under enten en planlagt eller ikke-planlagt vedligeholdelse, mindst én virtuelt bliver tilgængelig
* **Lagerplads konti**: det anbefales at have to lagerplads konti. Har du en enkelt lagerplads konto kan føre til oprettelse af et enkelt punkt af fejl og kan medføre installationen skal kunne ikke være tilgængelige i en sandsynlighed scenarie, hvor kontoen lagerplads går ned. To lagerplads konti kan knytte en lagerplads konto for hver fejl linje.
* **Netværk opdeling**: Web Application proxyservere skal installeres i et separat DMZ-netværk. Du kan opdele et virtuelt netværk i to undernet og derefter installere om Web proxyen serverne i et isolerede undernet. Du kan blot konfigurere gruppe netværk sikkerhedsindstillinger for hvert undernet og Tillad kun påkrævet kommunikation mellem de to undernet. Få mere at vide får per installation eksemplet nedenfor

##<a name="steps-to-deploy-ad-fs-in-azure"></a>Trin til at udrulle AD FS i Azure

De trin, der er nævnt i dette afsnit oprettes en disposition for vejledning til at installere den under vist AD FS-infrastruktur i Azure.

### <a name="1-deploying-the-network"></a>1. implementering af netværket

Som beskrevet ovenfor, kan du enten oprette to undernet i et enkelt virtuelt netværk ellers oprette to helt forskellige virtuelle netværk (VNet). I denne artikel vil fokusere på implementering af et enkelt virtuelt netværk og opdele den i to undernet. Dette er en nemmere metode som to separate VNets kræver en VNet til VNet gateway for kommunikation.

**1.1 oprette virtuelle netværk**

![Oprette virtuelle netværk](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)
    
Vælg virtuelt netværk, og du kan i portalen Azure installere det virtuelle netværk og ét undernet med det samme med bare ét klik. INT undernet er også defineret og er nu klar til FOS skal tilføjes.
Næste trin er at tilføje en anden undernet til netværket, det vil sige DMZ-undernettet. Oprette undernet DMZ blot

* Vælg det nyoprettede netværk
* I egenskaberne, Vælg undernet
* I undernettet panel klikke på knappen Tilføj
* Angiv undernet navn og adresse mellemrum oplysninger for at oprette undernettet

![Undernet](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)


![Undernet DMZ](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. oprettelse af netværket sikkerhedsgrupper**

En netværk sikkerhedsgruppe (NSG) indeholder en liste over listen ACL (Access Control) regler, der tillade eller nægte netværkstrafik til din VM forekomster i et virtuelt netværk. NSGs kan være knyttet til undernet eller enkelte VM forekomster i pågældende undernet. Når en NSG er knyttet til et undernet, regler ACL til alle VM forekomsterne i pågældende undernet.
Til denne vejledning, skal du oprette to NSGs: en for et internt netværk og en DMZ. De vil være mærket NSG_INT og NSG_DMZ henholdsvis.

![Oprette NSG](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Når NSG er oprettet, vil der være 0 indgående og 0 udgående regler. Når rollerne på de respektive servere er installeret og fungere, kan de indgående og udgående regler foretages efter det ønskede niveau af sikkerhed.

![Initialiseret NSG](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Når NSGs er oprettet, kan du knytte NSG_INT til undernet HEL og NSG_DMZ med undernet DMZ. Et skærmbillede af eksempel er gengivet nedenfor:

![Konfigurere NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Klik på undernet at åbne panelet for undernet
* Vælg undernettet skal knyttes til NSG 

Panelet for undernet efter konfigurationen, skal ligne nedenfor:

![Undernet efter NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. oprette forbindelse til en lokal installation**

Vi skal bruge en forbindelse til en lokal installation for at kunne installere domænecontrolleren (DC) i azure. Azure indeholder forskellige mulighed for at oprette forbindelse infrastrukturen i det lokale miljø til infrastrukturen Azure.

* Punkt-til-websted
* Virtuelt netværk websted til websted
* ExpressRoute

Det anbefales at bruge ExpressRoute. ExpressRoute kan du oprette private forbindelser mellem Azure datacentre og infrastruktur, der er i din virksomhed eller i et miljø med samtidig placering. ExpressRoute forbindelser går ikke via offentlige internettet. De tilbyder flere pålidelighed, hastigheden, nederste latenstider og højere sikkerhed end typisk forbindelser via internettet.
Mens det anbefales at bruge ExpressRoute, kan du vælge en forbindelsesmetode, der passer bedst til din organisation. Læs [ExpressRoute teknisk oversigt](https://aka.ms/Azure/ExpressRoute)for at lære mere om ExpressRoute og de forskellige connectivity indstillinger ved hjælp af ExpressRoute.

### <a name="2-create-storage-accounts"></a>2. Opret lagerplads konti

Hvis du vil vedligeholde høj tilgængelighed og undgå afhængighed af en enkelt lagerplads-konto, kan du oprette to lagerplads konti. Opdele maskiner i hvert sæt tilgængelighed i to grupper, og derefter tildele hver gruppe en separat lagerplads konto.

![Oprette lagerplads konti](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. Opret tilgængelighed sæt

Oprette tilgængelighed sæt, der skal indeholde 2 maskiner som minimum for hver rolle (DC/AD FS og WAP). Dette vil hjælpe med at opnå større tilgængelighed til hver rolle. Mens oprette tilgængeligheden angiver, er det vigtigt at beslutte, om følgende:
* **Fejl domæner**: virtuelle maskiner i det samme domæne fejl dele den samme power kilde- og fysisk netværks-switch. Et minimum af 2 fejl domæner anbefales. Standardværdien er 3, og du kan lade den som det er til denne installation
* **Opdatere domæner**: computere, der hører til det samme domæne opdatering genstartes sammen under en opdatering. Du vil have mindst 2 update domæner. Standardværdien er 5, og du kan lade den som det er til denne installation

![Tilgængelighed sæt](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Oprette følgende tilgængelighed sæt

| Tilgængelighed sæt | Rolle | Fejl domæner | Opdatere domæner |
|:----------------:|:----:|:-----------:|:-----------|
| contosodcset | DC/ADFS | 3 | 5 |
| contosowapset | WAP | 3 | 5 |

### <a name="4--deploy-virtual-machines"></a>4. installere virtuelle maskiner
Næste trin er at implementere virtuelle maskiner, der er vært for de forskellige roller i infrastrukturen. Mindst to maskiner anbefales i hvert sæt tilgængelighed. Oprette seks virtuelle maskiner til de grundlæggende installation.

| Maskine | Rolle | Undernet | Tilgængelighed sæt | Lagerplads konto | IP-adresse |
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|contosodc1|DC/ADFS|HELTAL|contosodcset|contososac1|Statisk|
|contosodc2|DC/ADFS|HELTAL|contosodcset|contososac2|Statisk|
|contosowap1|WAP|DMZ|contosowapset|contososac1|Statisk|
|contosowap2|WAP|DMZ|contosowapset|contososac2|Statisk|

Når du har måske bemærket, er ikke angivet nogen NSG. Dette skyldes, at azure lader dig bruge NSG på niveauet for undernet. Derefter kan du styre maskine netværkstrafik ved hjælp af den enkelte NSG, der er knyttet til enten undernettet ellers NIC objektet. Læs mere om [Hvad er et netværk sikkerhed gruppe (NSG)](https://aka.ms/Azure/NSG).
Statiske IP-adresse anbefales, hvis du administrerer DNS-Posterne. Du kan bruge Azure DNS og i stedet i DNS-posterne for dit domæne, referere til de nye maskiner ved deres Azure FQDN'er.
Virtuelt ruden skal se ud som under, når installationen er fuldført:

![Virtuelle maskiner, der er installeret](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. konfigurere domænecontrolleren / AD FS-servere
 For at godkende indgående anmodninger, skal AD FS kontakte domænecontrolleren. For at gemme den dyrt forretningsrejse fra Azure på lokale DC til godkendelse, anbefales det at installere en kopi af domænecontrolleren i Azure. For at nå høj tilgængelighed, anbefales det at oprette en tilgængelighed sæt over mindst 2 domæne enheder.

|Domænecontrolleren|Rolle|Lagerplads konto|
|:-----:|:-----:|:-----:|
|contosodc1|Replika|contososac1|
|contosodc2|Replika|contososac2|

* Hæve de to servere som replika domæne enheder med DNS
* Konfigurere AD FS-servere ved at installere rollen AD FS ved hjælp af server manager.

###<a name="6---deploying-internal-load-balancer-ilb"></a>6. implementering interne justering af belastning (ILB)

**6.1. Opret ILB**

Du skal installere en ILB, Tilføj Vælg Indlæs balancere i Azure portalen og klik på (+).
>[AZURE.NOTE] Hvis du ikke kan se **Indlæse balancere** i menuen, skal du klikke på **Gennemse** i den nederste venstre del af portalen og Rul, indtil du ser **Indlæs balancere**.  Klik derefter på den gule stjerne for at føje det til menuen. Vælg ny Indlæs belastningsjusteringstjenesten for at åbne panelet for at starte konfigurationen af justering af belastning.

![Gennemse justering af belastning](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Navn**: Give et passende navn til justering af belastning
* **Farveskema**: da denne justering af belastning skal placeres foran AD FS-servere og er beregnet til interne netværksforbindelser kun vælge "Interne"
* **Virtuelt netværk**: Vælg det virtuelle netværk, hvor du installerer AD FS
* **Undernet**: Vælg det interne undernet her
* **Tildeling af IP-adresser**: dynamisk

![Interne justering af belastning](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)
 
Når du klikker på oprette og ILB er installeret, skal du se den på listen over Indlæs balancere:

![Indlæse balancere efter ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)
 
Næste trin er at konfigurere back end-puljen og back end-efterprøvning af af.

**6.2. Konfigurer ILB back end-puljen**

Vælg det nyoprettede ILB i panelet Indlæs balancere. Panelet indstillinger, åbnes. 
1.  Vælg back end-grupper i panelet indstillinger
2.  Klik på Tilføj virtuel maskine i panelet Tilføj back end-puljen
3.  Du der præsenteres med et panel, hvor du kan vælge tilgængelighed sæt
4.  Vælge de AD FS tilgængelighed

![Konfigurere ILB back end-puljen](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)
 
**6.3. konfiguration efterprøvning af af**

Vælg sonder i panelet ILB indstillinger.
1.  Klik på Tilføj
2.  Vise detaljer for efterprøvning af af et. **Navn**: navnet b sende forespørgsler. **Protokol**: TCP c. **Port**: 443 (HTTPS) d. **Interval**: 5 (standardværdien) – dette er det interval, hvormed ILB vil sende forespørgsler maskiner i back end-puljen e. **Beskadiget grænseværdier**: 2 (standard val ue) – dette er grænseværdi antal mislykkede forsøg på hinanden følgende efterprøvning af af efter hvilket ILB erklære en maskine i back end-puljen, ikke at svare, og stop sende trafik til den.

![Konfigurere ILB efterprøvning af af](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)
 
**6.4. oprette regler til justering af belastning**

For at kunne effektivt saldo trafikken, skal være konfigureret på ILB med regler justering af belastning. Hvis du vil oprette en regel til justering af belastning 
1.  Vælg regel fra panelet indstillinger i ILB justering af belastning
2.  Klik på Tilføj i regel panel af belastning
3.  I regel panel af belastning på Tilføj en. **Navn**: Angiv et navn til reglen b. **Protocol**: Vælg TCP c. **Port**: 443 d. **Back end-port**: 443 e. **Back end-puljen**: Vælg den gruppe, du har oprettet til AD FS klynge tidligere f. **Efterprøvning af af**: Vælg den efterprøvning af af tidligere har oprettet for AD FS-servere

![Konfigurere ILB justering af regler](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. opdatere DNS med ILB**

Gå til din DNS-server, og Opret en CNAME for ILB. CNAME skal være for tjenesten sammenslutning med IP-adresse peger på ILB IP-adresse. For eksempel, hvis adressen, ILB DIP er 10.3.0.8 og sammenslutning tjenesten installeret er fs.contoso.com, derefter oprette en CNAME for fs.contoso.com, der peger på 10.3.0.8.
Dette sikrer, at al kommunikation vedrørende fs.contoso.com slutningen op på ILB og distribueres korrekt.

###<a name="7---configuring-the-web-application-proxy-server"></a>7. konfigurere proxyserveren Web-program

**7.1. konfiguration af proxyservere Web Application når AD FS-servere**

For at sikre at Web Application proxyservere kan nå AD FS-servere bag ILB, skal du oprette en post i %systemroot%\system32\drivers\etc\hosts for ILB. Bemærk, at det entydige navn (DN) skal være sammenslutning tjenestenavne, for eksempel fs.contoso.com. Og IP-adresse skal være, på den ILB IP-adresse (10.3.0.8 som i eksemplet).

**7.2. installerer rollen Web proxyen**

Når du sikre, at Web Application proxyservere kan nå AD FS-servere bag ILB, kan du derefter installere Web Application proxyservere. Web Application proxyservere ikke knyttet til domænet. Installere rollerne Web proxyen på to proxyservere til Web-program ved at vælge rollen fjernadgang. Server manager vejleder dig for at fuldføre installationen WAP.
Du kan finde flere oplysninger om, hvordan du installerer WAP Læs [installere og konfigurere Web Application Proxy-Server](https://technet.microsoft.com/library/dn383662.aspx).

###<a name="8---deploying-the-internet-facing-public-load-balancer"></a>8. implementering af internettet modstående (offentlige) justering af belastning

**8.1. Opret Internet modstående (offentlige) justering af belastning**
 
Markér Indlæs balancere i Azure-portalen, og klik på Tilføj. Angiv følgende oplysninger i oprette Indlæs belastningsjusteringstjenesten panel
1. **Navn**: navn til justering af belastning
2. **Farveskema**: offentlige – denne indstilling fortæller Azure, denne justering af belastning skal bruge en offentlig adresse.
3. **IP-adresse**: oprette en ny IP-adresse (dynamisk)

![Internet modstående justering af belastning](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Efter installation vises justering af belastning på listen Indlæs balancere.

![Indlæse belastningsjusteringstjenesten liste](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)
 
**8.2. tildele et DNS-navn til den offentlige IP-adresse**

Klik på posten nyoprettede Indlæs belastningsjusteringstjenesten i panelet Indlæs balancere for at få vist panelet for konfiguration. Følg nedenstående trin for at konfigurere DNS-navn til den offentlige IP-Adresser:
1.  Klik på den offentlige IP-adresse. Dette åbner panelet for den offentlige IP-adresse og dets indstillinger
2.  Klik på konfiguration
3.  Angive en DNS-etiket. Det bliver det offentlige DNS-navn, du kan få adgang til overalt, for eksempel contosofs.westus.cloudapp.azure.com. Du kan tilføje en post i den eksterne DNS for tjenesten sammenslutning (som fs.contoso.com), der oversættes til DNS-navnet på den eksterne justering af belastning (contosofs.westus.cloudapp.azure.com).

![Konfigurere justering af belastning via internettet](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Konfigurere justering af belastning (DNS) via internettet](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. Konfigurer back end-puljen for Internet modstående (offentlig) justering af belastning** 

Følg de trin, som opretter den interne justering af belastning, for at konfigurere back end-puljen til Internet modstående (offentlige) belastning som tilgængeligheden indstille for WAP-servere. For eksempel contosowapset.

![Konfigurere back end-puljen af Internet modstående justering af belastning](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)
 
**8,4. Konfigurer efterprøvning af af**

Følg de trin, som konfigurerer den interne justering af belastning for at konfigurere efterprøvning af af til back end-puljen af WAP-servere.

![Konfigurere efterprøvning af af af Internet modstående justering af belastning](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)
 
**8.5. oprette regler til justering af belastning**

Følg de trin, som ILB til at konfigurere belastningsjustering regel for TCP 443.

![Konfigurere justering af regler for Internet modstående justering af belastning](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)
 
###<a name="9---securing-the-network"></a>9. sikring af netværket

**9.1. sikring af det interne undernet**

Overordnede, du har brug for følgende regler til at sikre effektivt dit interne undernet (i rækkefølgen, som angivet nedenfor)

|Regel|Beskrivelse|Flow|
|:----|:----|:------:|
|AllowHTTPSFromDMZ| Tillade HTTPS-kommunikation fra DMZ | Indgående |
|DenyInternetOutbound| Ingen adgang til internettet | Udgående |

![INT adgangsregler (indgående)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[kommentar]: <> (![INT adgangsregler (indgående)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [kommentar]: <> (![INT adgangsregler (udgående)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))
 
**9.2. sikring af DMZ-undernet**

|Regel|Beskrivelse|Flow|
|:----|:----|:------:|
|AllowHTTPSFromInternet| Tillad HTTPS fra internettet DMZ | Indgående|
|DenyInternetOutbound|  Alt undtagen HTTPS internettet er blokeret | Udgående |

![LOKAL adgangsregler (indgående)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[kommentar]: <> (![ekstern adgangsregler (indgående)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [kommentar]: <> (![ekstern adgangsregler (udgående)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

>[AZURE.NOTE] Hvis klientbruger godkendelse af certifikat (med klient-TLS godkendelse ved hjælp af X509 brugercertifikater) er påkrævet, og derefter AD FS kræver TCP port 49443 være aktiveret for indgående adgang.

###<a name="10--test-the-ad-fs-sign-in"></a>10. test AD FS-logon

Den nemmeste måde er at teste AD FS er ved at bruge siden IdpInitiatedSignon.aspx. For at kunne gøre, at det er påkrævet for at aktivere IdpInitiatedSignOn AD FS-egenskaber. Følg nedenstående trin for at bekræfte din AD FS-konfiguration
1.  Køre den under cmdlet på AD FS-server, ved hjælp af PowerShell, aktiveret angivet det til.
    Sæt AdfsProperties - EnableIdPInitiatedSignonPage $true 
2.  Fra en ekstern maskine access https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx  
3.  Skal du se afsnittet siden AD FS som nedenfor:

![Test logonside](./media/active-directory-aadconnect-azure-adfs/test1.png)

På vellykket logon får du med en meddelelse som vist nedenfor:

![Test succes](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Skabelon til implementering af AD FS i Azure

Skabelonen installerer en 6 maskine opsætning, 2 til domænecontrollere, AD FS og WAP.

[AD FS i skabelon Azure-installation](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

Du kan bruge en eksisterende virtuelt netværk eller oprette en ny VNET under installation af denne skabelon. De forskellige parametre, der er tilgængelige for tilpasning af installationen vises nedenfor med en beskrivelse af brugen af parameteren i forbindelse med installationsprocessen. 

| Parameter | Beskrivelse |
|:--------|:-----|
|Placering| Område for at installere ressourcerne til fx Øst os. |
|StorageAccountType| Typen kontoen lagerplads, der er oprettet|
|VirtualNetworkUsage| Angiver, om et nyt virtuelt netværk oprettes eller bruge en eksisterende|
|VirtualNetworkName| Navnet på det virtuelle netværk til at oprette, obligatorisk for både eksisterende eller nye virtuelt netværk brugen|
|VirtualNetworkResourceGroupName| Angiver navnet på den ressourcegruppe, hvor det eksisterende virtuelle netværk er placeret. Når du bruger et eksisterende virtuelt netværk, bliver det en obligatorisk parameter, så installationen kan finde det eksisterende virtuelle netværk ID|
|VirtualNetworkAddressRange| Adresseområde på den nye VNET obligatorisk hvis opretter et nyt virtuelt netværk|
|InternalSubnetName| Navnet på den interne undernet, obligatorisk for begge virtuelt netværk brugerindstillinger (nye eller eksisterende)|
|InternalSubnetAddressRange| Adresseområde på den interne undernet, som indeholder den domæne enheder og ADFS-Server, obligatorisk, hvis opretter et nyt virtuelt netværk.|
|DMZSubnetAddressRange| Adresseområde på dmz-undernet, som indeholder de Windows-program proxyservere, obligatorisk, hvis opretter et nyt virtuelt netværk.|
|DMZSubnetName| Navnet på den interne undernet, obligatorisk for begge virtuelt netværk brugerindstillinger (nye eller eksisterende). |
|ADDC01NICIPAddress| Den interne IP-adresse første domænenavn fra domænecontrolleren, denne IP-adresse skal tildeles statisk DC og skal være en gyldig IP-adresse i det interne undernet|
|ADDC02NICIPAddress| Den interne IP-adresse anden domænenavn fra domænecontrolleren, denne IP-adresse skal tildeles statisk DC og skal være en gyldig IP-adresse i det interne undernet|
|ADFS01NICIPAddress| Den interne IP-adressen på den første ADFS-server, denne IP-adresse skal tildeles statisk ADFS-server og skal være en gyldig IP-adresse i det interne undernet|
|ADFS02NICIPAddress| Den interne IP-adressen på den anden ADFS-server, denne IP-adresse skal tildeles statisk ADFS-server og skal være en gyldig IP-adresse i det interne undernet|
|WAP01NICIPAddress| Den interne IP-adressen på den første WAP-server, denne IP-adresse tildeles statisk til WAP-serveren og skal være en gyldig IP-adresse i DMZ-undernet|
|WAP02NICIPAddress| Den interne IP-adressen på den anden WAP-server, denne IP-adresse tildeles statisk til WAP-serveren og skal være en gyldig IP-adresse i DMZ-undernet|
|ADFSLoadBalancerPrivateIPAddress| Den interne IP-adressen på ADFS justering af belastning, denne IP-adresse skal tildeles statisk belastning og skal være en gyldig IP-adresse i det interne undernet|
|ADDCVMNamePrefix| Virtuelt navnepræfiks ved domæne enheder|
|ADFSVMNamePrefix| Virtuelt navnepræfiks for ADFS-servere|
|WAPVMNamePrefix| Virtuelt navnepræfiks for WAP servere|
|ADDCVMSize| Vm størrelsen på domænecontrollerne|
|ADFSVMSize| Vm størrelsen af ADFS-serverne|
|WAPVMSize| Vm størrelsen på WAP-servere|
|AdminUserName| Navnet på den lokale Administrator af virtuelle maskiner|
|AdminPassword| Adgangskoden til den lokale administratorkonto af virtuelle maskiner|

## <a name="additional-resources"></a>Yderligere ressourcer
* [Tilgængelighed sæt](https://aka.ms/Azure/Availability ) 
* [Azure justering af belastning](https://aka.ms/Azure/ILB)
* [Interne justering af belastning](https://aka.ms/Azure/ILB/Internal)
* [Internet med adgang til justering af belastning](https://aka.ms/Azure/ILB/Internet)
* [Lagerplads konti](https://aka.ms/Azure/Storage )
* [Azure virtuelle netværk](https://aka.ms/Azure/VNet)
* [ADFS- og Web Application Proxy Links](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Næste trin

* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
* [Konfiguration og administration af din AD FS ved hjælp af Azure AD-forbindelse](active-directory-aadconnectfed-whatis.md)
* [Høj tilgængelighed kryds-geografiske AD FS installation i Azure med Azure trafik Manager](active-directory-adfs-in-azure-with-azure-traffic-manager.md)




