<properties
    pageTitle="Konfigurere altid på tilgængelighed gruppe i Azure VM - klassisk"
    description="Opret en konstant tilgængelighed gruppe med Azure virtuelle maskiner. Dette selvstudium bruger primært brugergrænsefladen og værktøjer i stedet for scripting."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm---classic"></a>Konfigurere altid på tilgængelighed gruppe i Azure VM - klassisk

> [AZURE.SELECTOR]
- [Ressourcestyring: skabelon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Ressourcestyring: Manuel](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Klassisk: Brugergrænsefladen](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Klassisk: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Selvstudiet til slut viser, hvordan du implementere tilgængelighed grupper ved hjælp af SQL Server altid på kører på Azure virtuelle computere.

I slutningen af selvstudiet, vil din SQL Server altid på løsning i Azure består af følgende elementer:

- Et virtuelt netværk, der indeholder flere undernet, herunder en front-end- og et back end-undernet

- Et domænenavn fra domænecontrolleren et domæne, Active Directory (AD)

- To SQL Server FOS udrulles til back end-undernet og tilsluttet AD-domæne

- En 3-node WSFC klynge med Node størstedelen quorum modellen

- En tilgængelighed gruppe med to synkron Anvend replikaer af en tilgængelighed-database

Figuren nedenfor er en grafisk repræsentation af løsningen.

![Teste øvelse arkitektur for AG i Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Bemærk, at dette er en mulig konfiguration. For eksempel kan du minimere antallet af FOS for en gruppe med to gengivne tilgængelighed for at gemme på Beregn timer i Azure ved hjælp af domænecontrolleren som quorum fil del bekræftelse i en 2-node WSFC klynge. Denne metode reducerer VM antallet af en fra ovenstående konfigurationen.

Dette selvstudium antages det følgende:

- Du har allerede en Azure-konto.

- Du ved allerede, hvordan du klargør et klassisk SQL Server VM fra galleriet virtuelt ved hjælp af den grafiske brugergrænseflade.

- Du har allerede en dækkende forståelse af altid på tilgængelighed-grupper. Du kan finde yderligere oplysninger finder [Altid på tilgængelighed grupper (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Hvis du er interesseret i at bruge altid på tilgængelighed-grupper med SharePoint, skal du også se [Konfigurere SQL Server 2012 altid på tilgængelighed grupper til SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Oprette virtuelle Netværks- og domænecontroller Server

Du starter med en ny Azure-prøveversion konto. Når du er færdig med konfiguration af din konto, skal du være i startskærmen på portalen Azure klassisk.

1. Klik på knappen **Ny** i det nederste venstre hjørne på siden, som vist nedenfor.

    ![Klik på ny i portalen](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)

1. **Netværkstjenester**, skal du klikke på og derefter skal du klikke på **Virtuelt netværk og** , og klik derefter på **Brugerdefineret oprette**, som vist nedenfor.

    ![Oprette virtuelle netværk](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)

1. I dialogboksen **Opret et VIRTUELT netværk** , du Opret et nyt virtuelt netværk ved væk gennem siderne med indstillingerne nedenfor. 

  	|Siden|Indstillinger|
|---|---|
|Virtuelt netværk detaljer|**NAVN = ContosoNET**<br/>**OMRÅDE = Vest dk**|
|DNS-servere og VPN-forbindelse|Ingen|
|Virtuelt netværk adresse mellemrum|Indstillinger er vist i skærmbilledet nedenfor: ![Oprette virtuelle netværk](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png)|

1. Derefter skal oprette du den VM, du vil bruge som domænecontrolleren (DC). Klik på **Ny** igen, og derefter **beregne**, og klik derefter **virtuelt**, og derefter **Fra galleriet**, som vist nedenfor.

    ![Oprette en VM](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)

1. Konfigurere en ny VM ved væk gennem siderne med nedenstående indstillinger i dialogboksen **Opret et VIRTUELT** . 

  	|Siden|Indstillinger|
|---|---|
|Vælg virtuelt operativsystemet|Windows Server 2012 R2 Datacenter|
|Virtuelt konfiguration|**VERSION UDGIVELSESDATO** = (nyeste)<br/>**VIRTUELT navn** = ContosoDC<br/>**Niveau** = STANDARD<br/>**Størrelse** = A2 (2 kerner)<br/>**Nye brugernavn** = AzureAdmin<br/>**Ny adgangskode** = Contoso! 000<br/>**Bekræft** = Contoso! 000|
|Virtuelt konfiguration|**SKYBASERET tjeneste** = Opret en ny tjeneste i skyen<br/>**SKYEN tjenesten DNS-navn** = et entydigt skyen service navn<br/>**DNS-navn** = et entydigt navn (ex: ContosoDC123)<br/>**Område/forbindelse gruppe/VIRTUELLE netværk** = ContosoNET<br/>**Virtuel NETVÆRKSUNDERNET** = Back(10.10.2.0/24)<br/>**LAGERPLADS konto** = Brug en automatisk oprettede lagerplads konto<br/>**Tilgængelighed angive** = (ingen)|
|Virtuelt indstillinger|Brug standarder|

Når du er færdig med at konfigurere den nye VM Vent VM skal være provsioned. Denne proces tager lidt tid at fuldføre, og hvis du klikker på fanen **virtuelt** i portalen Azure klassisk, kan du se ContosoDC gået stater fra **Start (artikel Provisioning)** til **showet afbrydes**, **Start**, **kører (artikel Provisioning)**og til sidst **køre**.

DC serveren er nu klargjort korrekt. Derefter skal konfigurere du Active Directory-domænet på denne DC-server.

## <a name="configure-the-domain-controller"></a>Konfigurere domænecontrolleren

I de følgende trin, kan du konfigurere ContosoDC maskinen som et domænenavn fra domænecontrolleren for corp.contoso.com.

1. Vælg **ContosoDC** maskinen på portalen. Klik på **Opret forbindelse** for at åbne en RDP-fil til adgang til Fjernskrivebord under fanen **Dashboard** .

    ![Oprette forbindelse til Vritual maskine](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)

1. Log på med din konfigurerede administratorkonto (**\AzureAdmin**) og din adgangskode (**Contoso! 000**).

1. Dashboardet **Server Manager** skal vises som standard.

1. Klik på linket **Tilføj roller og funktioner** på dashboardet.

    ![Server Explorer Tilføj roller](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)

1. Vælg **Næste** , indtil du kommer til afsnittet **Serverroller** .

1. Vælg rollerne, der **Active Directory-domænetjenester** og **DNS-Server** . Når du bliver bedt om det, kan du tilføje eventuelle yderligere funktioner, der kræves af disse roller.

    >[AZURE.NOTE] Du får en advarsel om, at der er ingen statiske IP-adresse validering. Hvis du tester konfigurationen, klik på Fortsæt. For fremstilling scenarier [ved hjælp af PowerShell for at angive den statiske IP-adresse domænecontroller maskinen](./virtual-network/virtual-networks-reserved-private-ip.md).

    ![Tilføje roller dialogboksen](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)

1. Klik på **Næste** , indtil du når sektionen **bekræftelse** . Marker afkrydsningsfeltet **Genstart destinationsserveren automatisk Hvis det er nødvendigt** .

1. Klik på **Installer**.

1. Når funktionerne færdiggøres, kan du gå tilbage til **Server Manager** dashboard.

1. Vælg indstillingen **AD DS** på den venstre rude.

1. Klik på linket **flere** på den gule bjælke advarsel.

    ![AD DS dialogboksen på DNS-Server VM](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)

1. Klik på **Hæv denne server til et domænenavn fra domænecontrolleren**i kolonnen **handling** af dialogboksen **Alle detaljer om opgave for Server** .

1. I **Active Directory Domain Services-konfigurationsguiden**, skal du bruge følgende værdier:

  	|Siden|Indstilling|
|---|---|
|Installationskonfiguration|**Tilføj en ny skov** = markeret<br/>**Rod domænenavn** = corp.contoso.com|
|Domænecontroller indstillinger|**Adgangskode** = Contoso! 000<br/>**Bekræft adgangskode** = Contoso! 000|

1. Klik på **Næste** til at gennemgå de andre sider i guiden. Kontrollér på siden **Forudsætninger kontrollere** , at du få vist følgende meddelelse: **alle nødvendige Kontroller overføres korrekt**. Bemærk, at du skal gennemgå alle relevante advarselsmeddelelser, men det er muligt at fortsætte med installationen.

1. Klik på **Installer**. Virtuelt **ContosoDC** genstartes automatisk.

## <a name="configure-domain-accounts"></a>Konfigurere domænekonti

De næste trin konfigurere kontiene Active Directory (AD) til senere brug.

1. Log tilbage på **ContosoDC** maskinen.

1. Vælg **Funktioner** i **Server Manager** , og klik derefter på **Active Directory administrativ Center**.

    ![Active Directory Administrative Center](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)

1. I den **Active Directory administrativ Center** Vælg **corp (lokalt)** i venstre rude.

1. Vælg **Ny** **opgaver** højre rude, og klik derefter på **bruger**. Brug følgende indstillinger:

  	|Indstilling|Værdi|
|---|---|
|**Fornavn**|Installere|
|**Bruger SamAccountName**|Installere|
|**Adgangskode**|Contoso! 000|
|**Bekræft adgangskode**|Contoso! 000|
|**Andre indstillinger for adgangskode**|Markeret|
|**Adgangskoden udløber aldrig**|Markeret|

1. Klik på **OK** for at oprette brugeren **installere** . Denne konto bruges til at konfigurere sekundær klynge og gruppen tilgængelighed.

1. Oprette to flere brugere med de samme trin: **CORP\SQLSvc1** og **CORP\SQLSvc2**. Disse konti bruges til SQL Server-forekomster. Derefter skal du give **CORP\Install** de nødvendige tilladelser til at konfigurere Windows Service Failover klynge (WSFC).

1. Vælg **corp (lokalt)** i venstre rude i **Active Directory administrativ Center**. Klik derefter på **Egenskaber**i ruden til højre **opgaver** .

    ![CORP brugeregenskaber](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)

1. Vælg **udvidelser**, og klik derefter på knappen **Avanceret** under fanen **sikkerhed** .

1. I dialogboksen **Avancerede sikkerhedsindstillinger for corp** . Klik på **Tilføj**.

1. Klik på **Vælg en sikkerhedskonto**. Søg efter **CORP\Install**. Klik på **OK**.

1. Vælg de **læse alle egenskaber** og **oprette Computer objekter** tilladelser.

    ![Corp brugertilladelser](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)

1. Klik på **OK**, og klik derefter på **OK** igen. Luk vinduet corp egenskaber.

Nu hvor du er færdig med konfiguration af Active Directory og user-objekter, du opretter tre SQL Server FOS og deltage i dem til dette domæne.

## <a name="create-the-sql-server-vms"></a>Oprette SQL Server FOS

Dernæst skal du oprette tre FOS, herunder en WSFC klyngenode og to SQL Server FOS. Hvis du vil oprette hver af FOS, gå tilbage til portalen Azure klassisk skal du klikke på **Ny**, **beregne**, **virtuelt**og derefter **Fra galleriet**. Brug derefter skabelonerne i den følgende tabel kan hjælpe dig med at oprette FOS.

|Siden|VM1|VM2|VM3|
|---|---|---|---|
|Vælg virtuelt operativsystemet|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|Virtuelt konfiguration|**VERSION UDGIVELSESDATO** = (nyeste)<br/>**VIRTUELT navn** = ContosoWSFCNode<br/>**Niveau** = STANDARD<br/>**Størrelse** = A2 (2 kerner)<br/>**Nye brugernavn** = AzureAdmin<br/>**Ny adgangskode** = Contoso! 000<br/>**Bekræft** = Contoso! 000|**VERSION UDGIVELSESDATO** = (nyeste)<br/>**VIRTUELT navn** = ContosoSQL1<br/>**Niveau** = STANDARD<br/>**Størrelse** = A3 (4 kerner)<br/>**Nye brugernavn** = AzureAdmin<br/>**Ny adgangskode** = Contoso! 000<br/>**Bekræft** = Contoso! 000|**VERSION UDGIVELSESDATO** = (nyeste)<br/>**VIRTUELT navn** = ContosoSQL2<br/>**Niveau** = STANDARD<br/>**Størrelse** = A3 (4 kerner)<br/>**Nye brugernavn** = AzureAdmin<br/>**Ny adgangskode** = Contoso! 000<br/>**Bekræft** = Contoso! 000|
|Virtuelt konfiguration|**SKYBASERET tjeneste** = tidligere oprettet entydige skyen tjenesten DNS-navn (ex: ContosoDC123)<br/>**Område/forbindelse gruppe/VIRTUELLE netværk** = ContosoNET<br/>**Virtuel NETVÆRKSUNDERNET** = Back(10.10.2.0/24)<br/>**LAGERPLADS konto** = Brug en automatisk oprettede lagerplads konto<br/>**Tilgængelighed angive** = Opret en tilgængelighed angive<br/>**Tilgængelighed SET NAME** = SQLHADR|**SKYBASERET tjeneste** = tidligere oprettet entydige skyen tjenesten DNS-navn (ex: ContosoDC123)<br/>**Område/forbindelse gruppe/VIRTUELLE netværk** = ContosoNET<br/>**Virtuel NETVÆRKSUNDERNET** = Back(10.10.2.0/24)<br/>**LAGERPLADS konto** = Brug en automatisk oprettede lagerplads konto<br/>**Tilgængelighed angive** = SQLHADR (du kan også konfigurere tilgængeligheden angive efter maskinen er blevet oprettet. Alle tre maskiner skal tildeles til sættet SQLHADR tilgængelighed.)|**SKYBASERET tjeneste** = tidligere oprettet entydige skyen tjenesten DNS-navn (ex: ContosoDC123)<br/>**Område/forbindelse gruppe/VIRTUELLE netværk** = ContosoNET<br/>**Virtuel NETVÆRKSUNDERNET** = Back(10.10.2.0/24)<br/>**LAGERPLADS konto** = Brug en automatisk oprettede lagerplads konto<br/>**Tilgængelighed angive** = SQLHADR (du kan også konfigurere tilgængeligheden angive efter maskinen er blevet oprettet. Alle tre maskiner skal tildeles til sættet SQLHADR tilgængelighed.)|
|Virtuelt indstillinger|Brug standarder|Brug standarder|Brug standarder|

<br/>

>[AZURE.NOTE] Den tidligere konfiguration foreslås STANDARD niveau virtuelle maskiner, grundlæggende niveau maskiner understøtter ikke netværksbelastningen slutpunkter, der kræves for at oprette en tilgængelighed gruppe lyttere senere. Desuden er maskine størrelsen forslag her beregnet til test tilgængelighed grupper i Azure FOS. Du kan finde den bedste ydeevne på fremstilling arbejdsbelastninger, anbefalinger til SQL Server machine størrelser og konfiguration af i [ydeevne bedste fremgangsmåder til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-performance.md).

Når tre FOS er fuldt klargjort, skal du tilslutte dem til **corp.contoso.com** domænet og Tildel CORP\Install administrative rettigheder til computerne. Brug følgende trin for hvert af de tre FOS til at udføre dette.

1. Først skal du ændre den foretrukne DNS-serveradresse. Start med at hente (hver VM remote desktop RDP)-fil til din lokale mappe ved at vælge VM på listen og klikke på knappen **Opret forbindelse** . Hvis du vil markere en VM, klik et vilkårligt sted undtagen den første celle i rækken, som vist nedenfor.

    ![Hente RDP-fil](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)

1. Start den RDP-fil, du har hentet, og log på ved hjælp af din konfigurerede VM administratorkonto (**BUILTIN\AzureAdmin**) og din adgangskode (**Contoso! 000**).

1. Når du er logget på, skal du se dashboardet **Server Manager** . Klik på **Lokalserver** i venstre rude.

1. Vælg linket **IPv4-adresse, der er tildelt af DHCP, IPv6-aktiveret** .

1. Vælg netværksikonet i vinduet **Netværksforbindelser** .

    ![Ændre VM foretrukne DNS-Server](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)

1. Klik på kommandolinjen skal **ændre indstillingerne for denne forbindelse** (afhængigt af størrelsen på vinduet kan det være nødvendigt at klikke på den højre dobbeltpil for at få vist denne kommando).

1. Vælg **Internet Protocol Version 4 (TCP/IPv4)** , og klik på Egenskaber.

1. Vælg Brug følgende DNS-server-adresser og angive **10.10.2.4** i **Foretrukken DNS-server**.

1. Adresse **10.10.2.4** er den adresse, der er tildelt til en VM i 10.10.2.0/24 undernettet i en Azure virtuelt netværk, og VM er **ContosoDC**. Brug **nslookup contosodc** i kommandoprompten, for at bekræfte **ContosoDC**IP-adresse, som vist nedenfor.

    ![Brug NSLOOKUP til at finde IP-adresse til DC](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)

1. Klik på O**K** og derefter **Luk** for at bekræfte ændringerne. Du kan nu til at deltage i VM til **corp.contoso.com**.

1. Klik på linket **arbejdsgruppe** tilbage i vinduet **Lokalserver** .

1. Klik på **Skift**i sektionen **Computernavn** .

1. Markér afkrydsningsfeltet **domæne** , og skriv **corp.contoso.com** i tekstfeltet. Klik på **OK**.

1. Angive legitimationsoplysninger for standard domæneadministratorkonto (**CORP\AzureAdmin**) og adgangskoden i dialogboksen **Windows sikkerhed** pop op (**Contoso! 000**).

1. Når du ser meddelelsen "Velkommen til corp.contoso.com domænet", skal du klikke på **OK**.

1. Klik på **Luk**, og klik derefter på **Genstart nu** i dialogboksen pop op-vindue.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-vm"></a>Tilføje Corp\Install bruger som administrator på hver VM:

1. Vent, indtil VM genstartes, og derefter Start RDP-filen igen for at logge på ved hjælp af kontoen **BUILTIN\AzureAdmin** VM.

1. Vælg **Funktioner**i **Server Manager** , og klik derefter på **Computer Management**.

    ![Computer Management](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)

1. Udvid **lokale brugere og grupper**i **Computer Management** -vinduet, og vælg derefter **grupper**.

1. Dobbeltklik på **administratorgruppen** .

1. Klik på knappen **Tilføj** i dialogboksen **Egenskaber for administratorer** .

1. Angiv brugeren, **CORP\Install**, og klik derefter på **OK**. Når du bliver bedt om legitimationsoplysninger, bruge kontoen **AzureAdmin** med den **Contoso! 000** adgangskode.

1. Klik på **OK** for at lukke dialogboksen **Egenskaber for Administrator** .

### <a name="add-the-failover-clustering-feature-to-each-vm"></a>Føj funktionen **Failoverklyngedannelse** til hver VM.

1. Klik på **Tilføj roller og funktioner**i dashboardet **Server Manager** .

1. Klik på **Næste** på siden **Tilføj roller og guiden funktioner**skal, indtil du kommer til siden **Funktioner** .

1. Vælg **Failoverklyngedannelse**. Når du bliver bedt om det, kan du tilføje andre funktioner, der er afhængige.

    ![Tilføje failoverklynge funktion til VM](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)

1. Klik på **Næste**, og klik derefter på **Installer** på siden **Confirmation** .

1. Klik på **Luk**, når **Failoverklyngedannelse** funktionsinstallationen er fuldført.

1. Log af VM.

1. Gentag trinnene i dette afsnit for alle tre servere – **ContosoWSFCNode**, **ContosoSQL1**og **ContosoSQL2**.

SQL Server FOS er nu klargjort og køre, men de er installeret med SQL Server med standardindstillingerne.

## <a name="create-the-wsfc-cluster"></a>Oprette WSFC klynge

I dette afsnit, skal oprette du den WSFC klynge, der er vært gruppen tilgængelighed, du vil oprette senere. På nuværende tidspunkt, skal du har gjort følgende for at hver af de tre FOS, du oftest bruger i WSFC klynge:

- Fuldt klargjort i Azure

- Joinforbundne VM til domænet

- Tilføjede **CORP\Install** til den lokale administratorgruppe

- Tilføjet funktionen Failoverklyngedannelse

Alle dette er forudsætninger på hver VM, før du kan deltage til WSFC klynge.

Bemærk også, at det Azure virtuelle netværk ikke fungerer på samme måde som et lokalt netværk. Du skal oprette klyngen i følgende rækkefølge:

1. Oprette en enkelt node klynge på en af noderne (**ContosoSQL1**).

1. Ændre klynge IP-adresse til en ubrugt IP-adresse (**10.10.2.101**).

1. Tage klyngenavnet online.

1. Tilføj de andre noder (**ContosoSQL2** og **ContosoWSFCNode**).

Følg nedenstående trin for at udføre disse opgaver, der konfigurerer klyngen fuldt ud.

1. Start RDP-filen til **ContosoSQL1** , og log på med domænekontoen **CORP\Install**.

1. Vælg **Funktioner**i dashboard **Server Manager** , og klik derefter på **Failoverklyngestyring**.

1. Højreklik på **Failoverklyngestyring**i venstre rude, og klik derefter på **Opret en klynge**, som vist nedenfor.

    ![Oprette klynge](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)

1. Oprette en en-node klynge i guiden Opret klynge ved væk gennem siderne med indstillingerne nedenfor:

  	|Siden|Indstillinger|
|---|---|
|Inden du begynder|Brug standarder|
|Vælg servere|Skriv **ContosoSQL1** **Enter servernavnet** og klik på **Tilføj**|
|Valideringsadvarsel|Vælg **Nej, jeg ikke kræver support fra Microsoft for denne klynge, og derfor ikke vil køre valideringstest. Når jeg klikker på Næste, kan du stadig oprette klyngen**.|
|Adgangspunkt til administration af klyngen|Skriv **Cluster1** i **klyngenavn**|
|Bekræftelse|Bruge standardindstillingerne, medmindre du bruger lagerplads mellemrum. Se den note, følge denne tabel.|

    >[AZURE.WARNING] Hvis du bruger [Lagerplads mellemrum](https://technet.microsoft.com/library/hh831739), der grupperer flere diske i lagerplads grupper, skal du fjerne markeringen af afkrydsningsfeltet **tilføje alle berettiget lagerplads til klyngen** på **bekræftelsessiden** . Hvis du ikke fjerne markeringen denne indstilling, vil de virtuelle disk frakoblet under processen klyngedannelse. Derfor kan de vises ikke også i Disk Manager eller Explorer indtil lagerplads mellemrummene er fjernet fra klynge og vedhæftet igen ved hjælp af PowerShell.

1. Udvid **Failoverklyngestyring**i venstre rude, og klik derefter på **Cluster1.corp.contoso.com**.

1. Rul ned til afsnittet **Klynge Core ressourcer** i den midterste rude, og udvid den **navn: Clutser1** detaljer. Du bør se både **navn** og **IP-adresse** ressourcer i tilstanden **mislykkedes** . IP-adresseressource kan gøres online, fordi klyngen er tildelt den samme IP-adresse som på computeren, som er en dublerede adresse.

1. Højreklik på ressourcen mislykkedes **IP-adresse** , og klik derefter på **Egenskaber**.

    ![Klynge egenskaber](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)

1. Vælg **Statiske IP-adresse** og angiv **10.10.2.101** i feltet adresse. Klik derefter på **OK**.

1. I afsnittet **Klynge Core ressourcer** skal du højreklikke på **navn: Cluster1** og klikke på **Få Online**. Derefter vente, indtil begge ressourcer, der er online. Når klynge navn ressourcen er online, opdaterer DC server med en ny AD computerkonto. Denne AD konto bruges til at køre tjenesten tilgængelighed gruppe grupperet senere.

1. Til sidst skal tilføje du de resterende noder til klyngen. Højreklik på **Cluster1.corp.contoso.com** i træet browser, og klik på **Tilføj Node**, som vist nedenfor.

    ![Tilføje Node til klyngen](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)

1. Klik på **Næste**i **Guiden Tilføj Node**. **Vælg servere** på siden skal du tilføje **ContosoSQL2** og **ContosoWSFCNode** til listen ved at skrive navnet på den server i **servernavn Enter** og derefter klikke på **Tilføj**. Når du er færdig, skal du klikke på **Næste**.

1. På siden **Valideringsadvarsel** skal du klikke på **Nej** (i et produktionsmiljø du skal udføre testene, der validering). Klik derefter på **Næste**.

1. Klik på **Næste** for at tilføje noderne **på bekræftelsessiden** .

    >[AZURE.WARNING] Hvis du bruger [Lagerplads mellemrum](https://technet.microsoft.com/library/hh831739), der grupperer flere diske i lagerplads grupper, skal du fjerne markeringen af afkrydsningsfeltet **tilføje alle berettiget lagerplads til klyngen** . Hvis du ikke fjerne markeringen denne indstilling, vil de virtuelle disk frakoblet under processen klyngedannelse. Derfor kan de vises ikke også i Disk Manager eller Explorer indtil lagerplads mellemrummene er fjernet fra klynge og vedhæftet igen ved hjælp af PowerShell.

1. Klik på **Udfør**, når noderne er føjet til klyngen. Failoverklyngestyring bør nu vise, at din klynge har tre noder og få dem vist i objektbeholderen **noder** .

1. Log af computeren fjernsession.

## <a name="prepare-the-sql-server-instances-for-availability-group"></a>Forberede dig på forekomster af SQL Server for tilgængelighed gruppe

I dette afsnit, skal du benytte følgende fremgangsmåde under både **ContosoSQL1** og **contosoSQL2**:

- Tilføje et logon for **NT AUTHORITY\System** med en nødvendige tilladelser, der er angivet til standard SQL Server-forekomsten

- Føje **CORP\Install** som en systemadministrator rolle til standard SQL Server-forekomsten

- Åbne firewall til fjernadgang til SQL Server

- Aktivere funktionen altid på tilgængelighed-grupper

- Ændre kontoen, SQL Server service **CORP\SQLSvc1** og **CORP\SQLSvc2**, henholdsvis

Disse handlinger kan udføres i en hvilken som helst rækkefølge. Nedenstående trin fører dog gennem dem i rækkefølge. Følg trinnene til både **ContosoSQL1** og **ContosoSQL2**:

1. Hvis du ikke har logget af pc-fjernsession af VM, skal du gøre det nu.

1. Start RDP-filer til **ContosoSQL1** og **ContosoSQL2** , og log på som **BUILTIN\AzureAdmin**.

1. Først skal du føje **NT AUTHORITY\System** for SQL Server-logon og sammen med de nødvendige tilladelser. Start **SQL Server Management Studio**.

1. Klik på **Opret forbindelse** til at oprette forbindelse til standard SQL Server-forekomsten.

1. Udvid **sikkerhed**i **Object Explorer**, og udvid derefter **logon**.

1. Højreklik på **NT AUTHORITY\System** logonforsøg, og klik på **Egenskaber**.

1. Vælg **bonus** for følgende tilladelser på siden **Securables** for den lokale server, og klik på **OK**.

    - Ændre en tilgængelighed gruppe

    - Oprette forbindelse SQL

    - Få vist server state

1. Tilføj derefter **CORP\Install** som en **systemadministrator** rolle til standard SQL Server-forekomsten. Højreklik på **logon** **Object Explorer**, og klik på **Nyt logon**.

1. Skriv **CORP\Install** i **Login-navn**.

1. Vælg **systemadministrator**i siden **Serverroller** . Klik derefter på **OK**. Når logon er oprettet, kan du se den ved at udvide **logon** i **Object Explorer**.

1. Derefter skal oprette du en regel til SQL Server. Start **Windows Firewall med Avanceret sikkerhed**, fra skærmbilledet **Start** .

1. Vælg **Indgående regler**i venstre rude. Klik på **Ny regel**i højre rude.

1. På siden **Regeltype** Vælg **Program**, og klik derefter på **Næste**.

1. **Program** på siden skal du markere **denne programsti** og skrive **%ProgramFiles%\Microsoft SQL Server\MSSQL12. MSSQLSERVER\MSSQL\Binn\sqlservr.exe** i tekstfeltet (Hvis du følger disse retningslinjer, men bruger SQL Server 2012, SQL Server-mappe er **MSSQL11. MSSQLSERVER**). Klik derefter på **Næste**.

1. Beholde **Tillad forbindelsen** markeret **handling** på siden, og klik på **Næste**.

1. Accepter standardindstillingerne **profilsiden,** og klik på **Næste**.

1. **Navn** på siden, Angiv et regelnavn, såsom **SQL Server (Program reglen)** i tekstboksen **navn** og derefter klikke på **Udfør**.

1. Derefter skal aktivere du funktionen **Altid på tilgængelighed-grupper** . Start **SQL Server Configuration Manager**fra skærmbilledet **Start** .

1. Klik på **SQL Server Services**, i træet browser Højreklik tjenesten **SQL Server (MSSQLSERVER)** , og klik på **Egenskaber**.

1. Klik på fanen **Altid på høj tilgængelighed** , og derefter vælge **Aktivér altid på tilgængelighed grupper**, som vist nedenfor, og klik derefter på **Anvend**. Klik på **OK** i pop op-vindue, og ikke lukker egenskabsvinduet for endnu. Du vil genstarte SQL Server-tjenesten, når du ændrer tjenestekontoen.

    ![Aktivér altid på tilgængelighed grupper](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)

1. Derefter skal ændre du SQL Server-tjenestekontoen. Klik på fanen **Log på** og derefter skrive **CORP\SQLSvc1** (for **ContosoSQL1**) eller **CORP\SQLSvc2** (for **ContosoSQL2**) i **Kontonavn**og derefter Udfyld og Bekræft adgangskoden, og klik derefter på **OK**.

1. Klik på **Ja** for at genstarte SQL Server-tjenesten i pop op-vinduet. Når tjenesten SQL Server er genstartet, er de ændringer, du har foretaget i egenskabsvinduet effektive.

1. Log af FOS.

## <a name="create-the-availability-group"></a>Oprette gruppen tilgængelighed

Du er nu klar til at konfigurere en tilgængelighed gruppe. Nedenfor finder du en oversigt over hvad du vil udføre:

- Oprette en ny database (**MyDB1**) på **ContosoSQL1**

- Tage både en fuld sikkerhedskopiering og sikkerhedskopiering af en transaktion af databasen

- Gendanne fuldt og log sikkerhedskopier **ContosoSQL2** med indstillingen **ingen genoprettelse**

- Oprette gruppen tilgængelighed (**AG1**) med synkron Anvend automatisk failover og læsevenligt sekundær replikaer

### <a name="create-the-mydb1-database-on-contososql1"></a>Oprette MyDB1 databasen på ContosoSQL1:

1. Hvis du ikke har allerede er logget af remote desktop sessioner for **ContosoSQL1** og **ContosoSQL2**, kan du gøre det nu.

1. Start RDP-filen til **ContosoSQL1** , og log på som **CORP\Install**.

1. I **Stifinder**, under * *C:\**, oprette en mappe med navnet * *Sikkerhedskopiering**. Du skal bruge denne mappe brug til at sikkerhedskopiere og gendanne databasen.

1. Højreklik på den nye mappe, pege på **del med**, og klik derefter på **bestemte personer**, som vist nedenfor.

    ![Oprette en sikkerhedskopimappe](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)

1. Tilføje **CORP\SQLSvc1** og give den tilladelsen **Læse-og skriveadgang** , og derefter tilføje **CORP\SQLSvc2** og give den **læsetilladelser,** som vist nedenfor, og klik derefter på **del**. Når fildeling proces er fuldført, skal du klikke på **færdig**.

    ![Tildele tilladelser til sikkerhedskopimappe](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)

1. Derefter skal oprette du databasen. Start **SQL Server Management Studio**fra menuen **Start** , og derefter klikke på **Opret forbindelse** til at oprette forbindelse til standard SQL Server-forekomsten.

1. Højreklik på **databaser** i **Object Explorer**, og klik på **Ny Database**.

1. Skriv **MyDB1**i **Databasenavn**, og derefter klikke på **OK**.

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Få en fuldstændig sikkerhedskopiering af MyDB1 og gendanne den på ContosoSQL2:

1. Derefter skal udføre du en fuldstændig sikkerhedskopi af databasen. I **Object Explorer**Udvid **databaser**, og derefter skal du højreklikke på **MyDB1**, og klik derefter peg på **opgaver**og klik derefter på **Sikkerhedskopier**.

1. Bevar **Backup type** er angivet til **fuld**i sektionen **kilde** . Klik på **Fjern** for at fjerne standardfilstien for sikkerhedskopien, i sektionen **Destination** .

1. Klik på **Tilføj**i sektionen **Destination** .

1. Skriv i boksen **filnavn** tekst ** \\ContosoSQL1\backup\MyDB1.bak**. Derefter skal du klikke på **OK**, og klik derefter på **OK** igen for at sikkerhedskopiere databasen. Når sikkerhedskopieringen er fuldført, skal du klikke på **OK** igen for at lukke dialogboksen.

1. Derefter skal tage du en transaktionslog sikkerhedskopien af databasen. I **Object Explorer**Udvid **databaser**, og derefter skal du højreklikke på **MyDB1**, og klik derefter peg på **opgaver**og klik derefter på **Sikkerhedskopier**.

1. Vælg **Transaktionslog til** **Backup** type. Bevar stien til **destinationsfilen** indstillet til den, du tidligere har angivet, og klik på **OK**. Når sikkerhedskopieringen er fuldført, skal du klikke på **OK** igen.

1. Derefter skal gendanne du sikkerhedskopier for fuld og transaction log på **ContosoSQL2**. Start RDP-filen til **ContosoSQL2** , og log på som **CORP\Install**. Lad den Fjernskrivebord-session for **ContosoSQL1** åben.

1. Start **SQL Server Management Studio**fra menuen **Start** , og derefter klikke på **Opret forbindelse** til at oprette forbindelse til standard SQL Server-forekomsten.

1. Højreklik på **databaser** **Object Explorer**, og klik på **Gendan Database**.

1. Vælg **enhed**i sektionen **kilde** , og klik på **...** knappen.

1. I **Vælg enheder til sikkerhedskopiering**, skal du klikke på **Tilføj**.

1. Skriv i sikkerhedskopi filplacering \\ContosoSQL1\backup, klik derefter på Opdater, og derefter vælge MyDB1.bak, og klik derefter på OK, og klik derefter på OK igen. Du bør nu se den komplette sikkerhedskopi, og log sikkerhedskopien i sikkerhedskopien angiver gendanne rude.

1. Gå til siden med indstillinger, og derefter vælge Gendan med ingen genoprettelse i gendannelse tilstand, og klik derefter på OK for at gendanne databasen. Når gendannelsen er fuldført, skal du klikke på OK.

### <a name="create-the-availability-group"></a>Oprette gruppen tilgængelighed:

1. Gå tilbage til pc-fjernsession for **ContosoSQL1**. Højreklik på **Altid på høj tilgængelighed** i **Object Explorer** i SSMS, og klik på **Ny tilgængelighed gruppe guiden**, som vist nedenfor.

    ![Start guiden Ny tilgængelighed gruppe](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)

1. Klik på **Næste**på siden **Introduktion** . Skrive **AG1** i **tilgængelighed gruppenavn**på siden **Angiv tilgængelighed gruppenavn** og derefter klikke på **Næste** igen.

    ![Guiden Nyt AG, Angiv AG navn](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)

1. Vælg **MyDB1** på siden **Vælg databaser** , og klik på **Næste**. Databasen opfylder forudsætningerne for en gruppe af tilgængelighed, fordi du har taget mindst én fuld sikkerhedskopiering på den tilsigtede primære replika.

    ![Guiden Nyt AG, Vælg databaser](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)

1. Klik på **Tilføj replika**på siden **Angiv replikaer** .

    ![Guiden Nyt AG, angive replikaer](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)

1. Dialogboksen **Opret forbindelse til Server** vises. Skriv **ContosoSQL2** i **servernavn**, og derefter klikke på **Opret forbindelse**.

    ![Guiden Nyt AG, oprette forbindelse til Server](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)

1. Du bør nu se **ContosoSQL2** er angivet i **Tilgængelige replikaer**tilbage på siden **Angiv replikaer** . Konfigurere replikaerne, som vist nedenfor. Når du er færdig, skal du klikke på **Næste**.

    ![Guiden Nyt AG, angive replikaer (fuldført)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)

1. Vælg **Deltag kun** på siden **Vælg indledende synkronisering af Data** , og klik på **Næste**. Du har allerede udført synkronisering af data manuelt, når du har taget sikkerhedskopier af fuld og transaktion med **ContosoSQL1** og gendannet dem på **ContosoSQL2**. I stedet kan du vælge ikke at udføre en sikkerhedskopiering af den og gendanne handlinger på databasen, og vælg **fuld** at lade tilgængelighed gruppe guiden Ny udføre synkronisering af data for dig. Dette er dog ikke anbefales til meget store databaser, der findes i nogle virksomheder.

    ![Guiden Nyt AG, Vælg synkronisering af indledende Data](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)

1. Klik på **Næste**på siden **Validering** . Denne side bør se nogenlunde sådan nedenfor. Er der en advarsel om Lytterkonfigurationen, fordi du ikke har konfigureret en tilgængelighed gruppe lytteren. Du kan ignorere en advarsel, da dette selvstudium ikke konfigurerer en lytter. For at konfigurere lytteren når du har fuldført dette selvstudium skal du se [konfigurere en ILB lytteren for altid på tilgængelighed grupper i Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

    ![Guiden Nyt AG, validering](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)

1. Klik på **Udfør**på siden **Oversigt** og derefter Vent, mens guiden konfigurerer gruppen ny tilgængelighed. Du kan klikke på **flere detaljer** for at få vist detaljerede status på siden **status** . Når guiden er færdig, skal du undersøge siden **resultater** for at bekræfte, at gruppen tilgængelighed er blevet oprettet, som vist nedenfor, og klik derefter på **Luk** for at lukke guiden.

    ![Guiden Nyt AG, resultater](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)

1. Udvid **Altid på høj tilgængelighed**i **Object Explorer**, og derefter Udvid **Tilgængelighed grupper**. Du bør nu se den nye gruppe tilgængelighed i denne beholder. Højreklik på **AG1 (primær)** og klikke på **Vis Dashboard**.

    ![Vis AG Dashboard](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)

1. Dit **Altid på dashboardet** skal ligne den, der vises nedenfor. Du kan se replikaerne, tilstanden failover af hver replika og synkroniseringstilstanden.

    ![AG Dashboard](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)

1. Gå tilbage til **Server Manager**, Vælg **Funktioner**og derefter starte **Failoverklyngestyring**.

1. Udvid **Cluster1.corp.contoso.com**, og udvid derefter **tjenester og programmer**. Vælg **roller** og noter, rollen **AG1** tilgængelighed gruppe er blevet oprettet. Bemærk, at AG1 ikke har en IP-adresse ved hvilken database klienter kan oprette forbindelse til gruppen tilgængelighed, fordi du ikke har konfigureret en lytter. Du kan oprette forbindelse direkte til den primære node for læse / skrive-handlinger og den sekundære node for skrivebeskyttede forespørgsler.

    ![AG i failoverklyngestyring](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

>[AZURE.WARNING] Forsøg ikke at mislykkes over gruppen tilgængelighed fra failoverklyngestyring. Alle Skift til sekundær server skal udføres fra i **Altid på Dashboard** i SSMS. Se [begrænsninger på ved hjælp af feltet WSFC failoverklyngestyring med tilgængelighed grupper](https://msdn.microsoft.com/library/ff929171.aspx)kan finde flere oplysninger.

## <a name="next-steps"></a>Næste trin
Du har nu korrekt implementeret SQL Server altid i ved at oprette en gruppe for tilgængelighed i Azure. For at konfigurere en lytter for denne tilgængelighed gruppe skal du se [konfigurere en ILB lytteren for altid på tilgængelighed grupper i Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Du kan finde andre oplysninger om brug af SQL Server i Azure [SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md).
