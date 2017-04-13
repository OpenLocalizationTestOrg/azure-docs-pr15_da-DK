<properties
    pageTitle="Gentage VMware virtuelle maskiner og fysiske servere til Azure med Azure gendannelse af websteder i portalen Azure | Microsoft Azure"
    description="Beskriver, hvordan du kan installere Azure gendannelse af websteder for at dirigerer gentagelse, failover og gendannelse af lokale VMware virtuelle computere og Windows/Linux fysiske servere til Azure ved hjælp af portalen Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>Gentage VMware virtuelle maskiner og fysiske maskiner til Azure med Azure gendannelse af websteder ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-vmware-to-azure.md)
- [Azure klassisk](site-recovery-vmware-to-azure-classic.md)
- [Azure klassisk (ældre)](site-recovery-vmware-to-azure-classic-legacy.md)

Velkommen til gendannelse af Azure websteder! Brug denne artikel, hvis du vil gentage lokale VMware virtuelle maskiner eller Windows/Linux fysiske servere til Azure ved hjælp af Azure gendannelse af websteder i portalen Azure.

> [AZURE.NOTE] Azure har to forskellige [installation modeller](../resource-manager-deployment-model.md) til oprettelse og arbejde med ressourcer: Azure ressource Manager (ARM) og klassisk. Azure har også to portaler – Azure klassisk portalen, der understøtter den klassiske implementeringsmodel og Azure portalen med understøttelse af begge installation-modeller.

Gendannelse af websteder i portalen Azure indeholder en række nye funktioner:

- Azure sikkerhedskopiering og gendannelse af websteder Azure tjenesterne kombineres til en enkelt gendannelse Services samling, så du kan konfigurere og administrere Forretningskontinuitet og nedbrud (BCDR) fra ét sted. Du kan overvåge og administrere handlinger på tværs af dine lokale websteder og Azure offentlige skyen i dashboardet samlet.
- Brugere med Azure abonnementer klargjort med programmet skyen løsning CSP'EN (Provider) kan nu administrere gendannelse af websteder handlinger i portalen Azure.
- Gendannelse af websteder i portalen Azure kan replikeres maskiner til ARM lagerplads konti. Gendannelse af websteder opretter på failover, ARM-baseret FOS i Azure.
- Gendannelse af websteder fortsat understøtter gentagelse til klassisk lagerplads konti. På failover opretter gendannelse af websteder FOS ved hjælp af klassisk modellen.

Når du har læst dette artikel indlæg kommentarer nederst i Disqus kommentarer. Bed tekniske spørgsmål på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Oversigt

Virksomheder har brug for en strategi for BCDR, der bestemmer, hvordan apps, arbejdsmængder og data holde køre og være tilgængelige under planlagte og ikke-planlagt nedetid, og gendanne til normal arbejdsbetingelser så tidligt som muligt. Din strategi for BCDR skal beholde forretningsdata sikre og kan gendannes, og sikre, at arbejdsbelastninger forbliver konstant er tilgængelig, når der udsendes nedbrud.

Gendannelse af websteder er en Azure-tjeneste, der bidrager til din strategi for BCDR ved orchestrating gentagelse af lokale fysiske servere og virtuelle maskiner til skyen (Azure) eller til en sekundær datacenter. Hvis der opstår afbrydelser i din primære placering, du ikke sekundær placeringen for at holde apps og arbejdsbelastninger, som er tilgængelige. Du ikke tilbage til din primære placering, når det vender tilbage til normal handlinger. Se mere i [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md)

Denne artikel indeholder alle de oplysninger, du vil gentage lokale VMware FOS og Windows/Linux fysiske servere til Azure. Den indeholder en oversigt over arkitekturen, planlægning oplysninger og installation trin til konfiguration af Azure, lokale servere, Replikeringsindstillinger og kapacitetsplanlægning af. Du kan aktivere gentagelse på computere, du vil beskytte, og Markér failover fungerer, når du har konfigureret infrastrukturen.

## <a name="business-advantages"></a>Business fordele

- Gendannelse af websteder giver beskyttelse for business arbejdsmængder og programmer, der kører på VMware FOS og fysiske servere.
- Gendannelse Services-portalen for at oprette, administrere og overvåge gentagelse, failover og genoprettelse på ét sted.
- Automatisk kan gendannelse af websteder opdage VMware FOS føjet til vSphere hosts.
- Du kan nemt køre failover fra din lokale infrastruktur Azure og failback (Gendan) fra Azure til VMware VM servere på dit lokale websted.
- Du kan aktivere flere VM og oprette gentagelse grupper, så arbejdsbelastninger lagdelt på tværs af flere computere replikeres på samme tid. Alle computere i en gentagelse gruppe har nedbrud ensartet og app-ensartede gendannelse punkter, når de mislykkes. Du kan samle flere computere i gendannelse planer for failover, så lagdelte arbejdsbelastninger mislykkes sammen.

## <a name="supported-operating-systems"></a>Understøttede operativsystemer

### <a name="windows64-bit-only"></a>Windows (kun 64 bit)
- Windows Server 2008 R2 SP1 +
- Windows Server 2012
- Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (kun 64 bit)
- Red Hat Enterprise Linux 6,7, 7.1, 7.2
- CentOS 6.5, 6.6, 6,7, 7.0, 7.1, 7.2
- Oracle Enterprise Linux 6.4, 6.5 kører Red Hat kompatible kernen eller Unbreakable Enterprise kernen version 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>Scenarie arkitektur

Dette er de scenarie komponenter:

- **Konfiguration af server**: en lokal computer, der koordinater kommunikation og administrerer data gentagelse og gendannelsesfiler processer. På denne computer skal du køre en enkelt installationsfilen for at installere server configuration og disse yderligere komponenter:
    - **Processen server**: fungerer som en gentagelse gateway. Den modtager gentagelse data fra beskyttet kilde maskiner, optimerer med cachelagring, komprimering og kryptering og sender den til Azure-lager. Det også håndterer opslagsnål installation af tjenesten mobilitet til beskyttet maskiner, og udfører automatisk registrering af VMware FOS. Processen standardserveren er installeret på konfigurationsserveren. Du kan installere flere enkeltstående proces servere for at tilpasse din installation.
    - **Mastersider target server**: håndterer gentagelse data under failback fra Azure.

- **Mobilitet service**: denne komponent er installeret på hver enkelt computer (VMware VM eller fysisk server), du vil gentage til Azure. Det registrerer data skriver på computeren og sender dem til processerveren.
- **Azure**: Du behøver ikke at oprette en hvilken som helst Azure FOS for at håndtere gentagelse og belastningsjustering til Azure.  Du har brug for et en Azure-lager-konto for at gemme replikerede data og en Azure virtuelt netværk, så Azure FOS er tilsluttet et netværk efter failover-Azure abonnement. Lagerplads konto og netværk skal være i samme område som gendannelse Services samling.
- **Failback**: Når du er klar til at mislykkes tilbage fra Azure til dit lokale websted efter en failover, skal du oprette en Azure VM som en midlertidig process-server. Du kan slette den, når failback er fuldført. For failback, skal du også kontakte en VPN (eller Azure ExpressRoute) forbindelse mellem dit lokale websted og Azure netværket, hvor din Azure FOS er placeret. Hvis failback trafik er tunge du muligvis også konfigurere en dedikeret master target server lokale computer. Standardserveren master target kører på konfigurationsserveren kan bruges til lysere trafik.


Grafikken viser, hvordan disse komponenter interagere.

![arkitektur](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figur 1: VMware/fysisk til Azure**

## <a name="azure-prerequisites"></a>Azure forudsætninger

Her er, hvad du skal bruge i Azure til at installere dette scenario.

**Nødvendige program** | **Detaljer**
--- | ---
**Azure-konto**| Du skal bruge en [Microsoft Azure](http://azure.microsoft.com/) -konto. Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). Du kan [Få mere at vide](https://azure.microsoft.com/pricing/details/site-recovery/) om gendannelse af websteder priser.
**Azure-lager** | Replikerede data er gemt i Azure-lager og Azure FOS oprettes, når der opstår failover. <br/><br/>For at gemme data skal du en standard eller førsteklasses lagerplads konto i det samme område som gendannelse Services samling.<br/><br/>Du kan bruge en LRS eller GRS lagerplads-konto. Vi anbefaler GRS, så dataene er tolerant, hvis der opstår en internationale afbrydelse, eller hvis det primære område ikke kan gendannes. [Få mere at vide](../storage/storage-redundancy.md).<br/><br/> [Premium lagerplads](../storage/storage-premium-storage.md) bruges typisk til virtuelle maskiner, der har brug for en ensartet måde høj EY ydeevne og lav ventetid til host EY intensivt arbejdsbelastninger.<br/><br/> Hvis du vil bruge en premium-konto til at gemme replikerede data, skal du også kontakte en standard-lager-konto for at gemme logfiler over gentagelse, hente igangværende ændringer til lokale data.<br/><br/> Bemærk, at lagerplads konti, der er oprettet i portalen Azure ikke kan flyttes på tværs af grupper. Også understøttes beskyttelse til premium lagerplads konti i Central Indien og Syd Indien ikke i øjeblikket.<br/><br/> [Læs om](../storage/storage-introduction.md) Azure-lager.
**Azure-netværk** | Du skal have et Azure virtuelle netværk, der Azure FOS opretter forbindelse til, når der opstår failover. Azure virtuelt netværk skal være i samme område som gendannelse Services samling.
**Failback fra Azure** | Du skal bruge en midlertidig process-server, der er angivet som en Azure VM. Du kan oprette, når du er klar til at mislykkes tilbage og slette det efter fejl tilbage er fuldført.<br/><br/> For at mislykkes skal igen du en VPN-forbindelse (eller Azure ExpressRoute) fra Azure netværket til webstedet, i det lokale miljø.

## <a name="configuration-server--scale-out-process-prerequisites"></a>Server Configuration / skalere ud proces forudsætninger

Du kan konfigurere en lokal computer som server configuration / skala op-proces server.

**Nødvendige program** | **Detaljer**
--- | ---
**Server Configuration**| Du har brug for en lokal fysiske eller virtuelt, der kører Windows Server 2012 R2. Alle de lokale gendannelse af websteder komponenter er installeret på computeren.<br/><br/>VMware VM gentagelse, anbefaler vi at du installerer serveren som en meget tilgængelige VMware VM. Hvis du replikering fysiske computere kan maskinen være en fysisk server.<br/><br/> Failback til det lokale websted fra Azure er altid at VMware FOS uanset om du mislykkedes over FOS eller fysiske servere. Hvis du ikke kan installere server configuration som en VMware VM skal du konfigurere en separat master target server som en VMware VM til at modtage failback trafik.<br/><br/>Hvis serveren er en VMware VM, skal netværk adapter typen være VMXNET3. Hvis du bruger en anden type netværkskort skal du installere en [VMware opdatere](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) på vSphere 5.5-serveren.<br/><br/>Serveren, der skal have en statisk IP-adresse.<br/><br/>Serveren må ikke være et domænenavn fra domænecontrolleren.<br/><br/>Værtsnavnet på serveren, der skal være på 15 tegn eller mindre.<br/><br/>Operativsystemet skal være engelsk kun.<br/><br/> Du skal installere VMware vSphere PowerCLI 6.0. på konfigurationsserveren.<br/><br/>Server configuration skal have adgang til internettet. Udgående adgang er påkrævet på følgende måde:<br/><br/>Midlertidig adgang på http-80 under installationen af komponenterne gendannelse af websteder (for at hente MySQL)<br/><br/>Igangværende udgående adgang via HTTPS 443 for gentagelse administration<br/><br/>Igangværende udgående adgang via HTTPS 9443 til gentagelse trafik (denne port kan ændres)<br/><br/>Serveren skal også adgang til de følgende URL-adresser, så den kan oprette forbindelse til Azure: *. hypervrecoverymanager.windowsazure.com; *. accesscontrol.Windows.NET; *. backup.windowsazure.com; *. BLOB.Core.Windows.NET; *. store.core.windows.net<br/><br/>Hvis du har IP-adresse-baserede firewallregler på serveren, kan du kontrollere, at reglerne, der tillader kommunikation til Azure. Du skal til at tillade [Azure Datacenter IP-områder](https://www.microsoft.com/download/confirmation.aspx?id=41653) og HTTPS (443)-protokollen.<br/><br/>Tillad IP-adresseområder for det Azure område for dit abonnement og for Vest USA.<br/><br/>Tillad denne URL-adresse til hentning af MySQL:.http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## <a name="vmware-vcentervsphere-host-prerequisites"></a>VMware vCenter/vSphere host forudsætninger

**Nødvendige program** | **Detaljer**
--- | ---
**vSphere**| Du har brug for en eller flere VMware vSphere hypervisors.<br/><br/>Hypervisors skal køre vSphere version 6.0, 5.5 eller 5.1 med de seneste opdateringer.<br/><br/>Vi anbefaler, at din vSphere værter og vCenter servere er placeret i det samme netværk som processerveren (dette er det netværk, hvor konfigurationsserveren er placeret, medmindre du har konfigureret en dedikeret process-server).
**vCenter** | Vi anbefaler, at du installerer en VMware vCenter server til at administrere din vSphere hosts. Det skal køre vCenter version 6.0 eller 5.5 med de seneste opdateringer.<br/><br/>Bemærk, at gendannelse af websteder ikke understøtter nye vCenter og vSphere 6.0 funktioner såsom cross vCenter vMotion, virtuelle enheder og lager DRS. Support for gendannelse af websteder er begrænset til funktioner, der er også tilgængelig i version 5.5.


## <a name="protected-machine-prerequisites"></a>Forudsætninger, der er beskyttet computer

**Nødvendige program** | **Detaljer**
--- | ---
**Lokal (VMware FOS)** | VMware FOS, du vil beskytte bør have VMware værktøjer installeret og kører.<br/><br/> Computere, du vil beskytte skal overholde med [Azure forudsætninger](site-recovery-best-practices.md#azure-virtual-machine-requirements) for oprettelse af Azure FOS.<br/><br/>Individuelle diskkapacitet på beskyttet maskiner ikke bør være mere end 1023 GB. En VM kan have op til 64 diske (dermed op til 64 TB). <br/><br/>Minimum 2 GB ledig plads på installationsdrev til Komponentinstallationen.<br/><br/>Beskyttelse af FOS med krypterede diske understøttes ikke.<br/><br/>Delt disk gæst klynger ikke understøttes.<br/><br/>**Port 20004** skal åbnes på den beskyttede virtuelt lokal firewall, hvis du vil aktivere **programmet konsistens**.<br/><br/>Maskiner, der har Unified Extensible Firmware Interface UEFI () / Extensible Firmware Interface(EFI) start understøttes ikke.<br/><br/>Computernavne på skal indeholde mellem 1 og 63 tegn (bogstaver, tal og bindestreger). Navnet skal starte med et bogstav eller tal og slutter med et bogstav eller et tal. Du kan ændre Azure navnet, når du har aktiveret replikering for en computer.<br/><br/>Hvis kilden VM har NIC samarbejde konverteres til en enkelt NIC efter failover til Azure.<br/><br/>Hvis beskyttet virtuelle maskiner har en iSCSI-disk derefter konverterer gendannelse af websteder beskyttet VM iSCSI disken til filen Virtuelle når VM skifter til Azure. Hvis iSCSI-destinationen kan nås fra Azure VM derefter den oprette forbindelse til den og grundlæggende se to diske – den Virtuelle disk på Azure VM og kilde iSCSI-disk. I dette tilfælde skal du afbryde forbindelsen til iSCSI-destinationen, der vises på Azure VM.
**Windows-computere (fysisk eller VMware)** | Computeren skal køre et understøttet 64-bit-operativsystem: Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2 med på minimum SP1.<br/><br/> Operativsystemet skal være installeret på C:\ drev. OS disken skal være en grundlæggende disk Windows og ikke dynamisk. Disken data kan være dynamisk.<br/><br/>Gendannelse af websteder understøtter FOS med en RDM disk. Gendannelse af websteder vil genbruge RDM disken under failback, hvis den oprindelige kilde VM og RDM disk er tilgængelig. Hvis de ikke er tilgængelig, under failback opretter gendannelse af websteder en ny VMDK-fil for hver disk.
**Linux-computere** (phyical eller VMware)|  Du skal bruge en understøttet 64-bit-operativsystem: Red Hat Enterprise Linux 6.7,7.1,7.2; Centos 6.5, 6.6,6.7,7.0,7.1,7.2; Oracle Enterprise Linux 6.4, 6.5 kører Red Hat kompatible kernen eller Unbreakable Enterprise kernen version 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>Fjernsystemet filer på beskyttet maskiner skal indeholde poster, der er tilknyttet IP-adresser, der er knyttet til alle netværkskort lokale værtsnavnet.<br/><br/>Hvis du vil oprette forbindelse til en Azure virtuelt kører Linux efter failover ved hjælp af en Secure Shell-klient (ssh), skal du sikre dig, Secure Shell-tjenesten på den beskyttede computer er indstillet til at starte automatisk på systemstart, og at firewallregler tillader en ssh forbindelse til den.<br/><br/>Værtsnavn, tilslutningspunkter, navne på enheder, og Linux system stier og filnavne (f.eks/osv /; /usr) skal være på engelsk kun.<br/><br/>Beskyttelse kan kun aktiveres for Linux-computere med følgende opbevaring: filsystem (EXT3, ETX4, ReiserFS, XFS) MultiPath software-enhed Mapper (multipath)). Lydstyrken manager: (LVM2). Fysiske servere med HP CCISS controller lagerplads understøttes ikke. ReiserFS filsystemet understøttes kun på SUSE Linux Enterprise Server 11 SP3.<br/><br/>Gendannelse af websteder understøtter FOS med en RDM disk.  Gendannelse af websteder genbruge ikke RDM disken under failback for Linux. I stedet for opretter det en ny VMDK-fil for hver tilsvarende RDM disk.<br/><br/>Sørg for, at du angiver indstillingen disk.enableUUID=true i konfigurationsparametrene af VM i VMware. Oprette posten, hvis den ikke findes. Der er brug at give et ensartet UUID til VMDK, så det tilslutter korrekt. Tilføje denne indstilling sikrer også, at kun delta ændringer overføres tilbage til en lokal installation under failback, og ikke en fuld replikering.
**Mobilitet Service** |  **Windows**: Du skal for at overføre automatisk tjenesten mobilitet til VM'er, der kører Windows, skal du angive en administratorkonto (lokal administrator på Windows-computer), så processen serveren kan gøre en push-installation.<br/><br/>**Linux**: til at overføre automatisk tjenesten mobilitet til VM'er, der kører Linux, skal du oprette en konto, der kan bruges af processen serveren til at foretage en push-installation.<br/><br/> Som standard replikeres alle diske på en computer. [Udelade en disk fra gentagelse](#exclude-disks-from-replication), skal tjenesten mobilitet være installeret manuelt på maskinen før du aktiverer gentagelse.<br/>

## <a name="prepare-for-deployment"></a>Forberede til installation

Sådan forbereder du dig til installation, skal du:

1. [Konfigurere et netværk med Azure](#set-up-an-azure-network) som Azure FOS vil være placeret, når de er spundet op efter failover. Desuden for failback skal du konfigurere en VPN-forbindelse (eller Azure ExpressRoute) fra Azure netværket til dit lokale websted.
2. [Konfigurere en konto til Azure lagerplads](#set-up-an-azure-storage-account) for replikerede data.
3. [Forberede en konto](#prepare-an-account-for-automatic-discovery) på vCenter server eller vSphere vært, så gendannelse af websteder kan automatisk registrere VMware VM'er, der er tilføjet.
4. [Forberede server configuration](#prepare-the-configuration-server) til at sikre, at det kan få adgang til påkrævede URL-adresser og installere vSphere PowerCLI 6.0.


### <a name="set-up-an-azure-network"></a>Konfigurere et netværk med Azure

- Netværket skal være i samme Azure område som, skal du installere samling af legitimationsoplysninger gendannelsestjenester.
- Afhængigt af ressource model, du vil bruge til mislykkedes over Azure FOS, skal du konfigurere det Azure netværk i [ARM tilstand](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) eller [tilstanden Klassisk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- For at mislykkes tilbage fra Azure til dit lokale VMware websted skal du en VPN-forbindelse (eller en Azure ExpressRoute forbindelse) fra Azure netværket som den replikerede Azure FOS befinder sig, til den lokale netværk, hvor konfigurationsserveren er placeret.
- [Få mere at vide om](../vpn-gateway/vpn-gateway-site-to-site-create.md) understøttede installationen modeller for VPN-til-websted forbindelser, og hvordan du kan [konfigurere en forbindelse](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network).
- Alternativt kan du konfigurere [Azure ExpressRoute](../expressroute/expressroute-introduction.md). Du kan [Få mere at vide](../expressroute/expressroute-howto-vnet-portal-classic.md) om konfiguration af et Azure netværk med ExpressRoute.

> [AZURE.NOTE] [Overførsel af netværk](../resource-group-move-resources.md) på tværs af grupper i det samme abonnement eller på tværs af abonnementer understøttes ikke af netværk, der bruges til implementering af gendannelse af websteder.

### <a name="set-up-an-azure-storage-account"></a>Konfigurere en konto til Azure-lager

- Du skal have en standard- eller en premium Azure lagerplads konto til at indeholde data replikeres til Azure. Kontoen skal være i samme område som gendannelse Services samling. Afhængigt af ressource model, du vil bruge til mislykkedes over Azure FOS, skal du konfigurere en konto i [ARM tilstand](../storage/storage-create-storage-account.md) eller [tilstanden Klassisk](../storage/storage-create-storage-account-classic-portal.md).
- Hvis du bruger en premium-konto til replikerede data, du vil oprette en ekstra standardkonto for at gemme logfiler over gentagelse, hente igangværende ændringer til lokale data.  

> [AZURE.NOTE] [Overførsel af lagerplads konti](../resource-group-move-resources.md) på tværs af grupper i det samme abonnement eller på tværs af abonnementer understøttes ikke for lagerplads konti, der bruges til implementering af gendannelse af websteder.

### <a name="prepare-an-account-for-automatic-discovery"></a>Forberede en konto til automatisk registrering

Gendannelse af websteder process-server kan automatisk opdage VMware FOS, på vSphere hosts eller på en vCenter-server, der administrerer hosts. For at udføre automatisk registrering legitimationsoplysninger for gendannelse af websteder, som kan få adgang til VMware-serveren. Dette er ikke relevant, hvis du replikering fysiske computere.

1. Hvis du vil bruge en dedikeret konto til automatisk registrering, skal du oprette en rolle (for eksempel Azure_Site_Recovery) på niveauet for vCenter med de [nødvendige tilladelser](#vmware-account-permissions).
2. Oprette en ny bruger på vSphere vært for eller vCenter serveren og tildele rollen til brugeren. Senere skal du lade vide om disse legitimationsoplysninger, så den kan udføre automatisk registrering gendannelse af websteder.

    >[AZURE.NOTE] En vCenter brugerkonto med en skrivebeskyttet rolle kan køre failover, men kan ikke lukke beskyttet kilde-computere. Hvis du vil lukke disse computere skal du rollen [Azure_Site_Recovery](#vmware-account-permissions) . Hvis du kun overføre FOS fra VMware til Azure og behøver ikke at failback derefter er rollen skrivebeskyttet tilstrækkeligt.

### <a name="prepare-the-configuration-server"></a>Forberedelse af konfigurationsserveren

1.  Sørg for, at den maskine, du bruger til server configuration opfylder [forudsætninger](#configuration-server-prerequisites). Især sørge for, at computeren har forbindelse til internettet med disse indstillinger:

    - Tillad adgang til disse URL-adresser: *. hypervrecoverymanager.windowsazure.com; *. accesscontrol.Windows.NET; *. backup.windowsazure.com; *. BLOB.Core.Windows.NET; *. store.core.windows.net
    - Tillad adgang til at hente MySQL [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) .
    - Tillad firewall kommunikation til Azure med [Azure datacenter IP-intervaller](https://www.microsoft.com/download/confirmation.aspx?id=41653) og HTTPS (443)-protokollen.

2.  Hent og Installer [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) på server configuration. (I øjeblikket andre versioner af PowerCLI understøttes ikke, herunder R versioner af version 6.0.)


## <a name="create-a-recovery-services-vault"></a>Oprette en samling af legitimationsoplysninger gendannelse

1. Log på [Azure-portalen](https://portal.azure.com).
2. Klik på **nyt** > **Management** > **Sikkerhedskopiering og gendannelse af websteder (OMS)**. Alternativt kan du klikke på **Gennemse** > **Gendannelse Services samling** > **Tilføj**.

    ![Ny samling](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. Angiv et navn til at identificere samling af legitimationsoplysninger i **navn** . Hvis du har mere end ét abonnement, skal du vælge en af dem.
4. [Opret en ny ressourcegruppe](../resource-group-template-deploy-portal.md) eller vælge en eksisterende. Angiv et Azure område. Computere kan replikeres til dette område. Bemærk, at Azure-lager og netværk, der bruges til gendannelse af websteder skal være i samme område. Understøttede områder se for at kontrollere geografiske tilgængelighed i [Azure websted gendannelse priser detaljer](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Hvis du vil hurtigt få adgang til samling af legitimationsoplysninger fra dashboardet klikke på **Fastgør til dashboard** , og klik derefter på **Opret**.

    ![Ny samling](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

Den nye samling af legitimationsoplysninger vises på **dashboardet** > **alle de ressourcer**, og primære **gendannelse Services vaults** blade.

## <a name="getting-started"></a>Kom godt i gang

Gendannelse af websteder indeholder en introduktion oplevelse, der er udviklet til at komme og køre så hurtigt som muligt. Det kontrollerer forudsætninger og vejleder dig gennem de trin, du har brug for at få installeret gendannelse af websteder.

Du skal vælge typen af maskiner, du vil gentage, og hvor du kan replikeres til. Du kan konfigurere infrastrukturen, herunder lokale servere, Azure indstillinger, gentagelse politikker og kapacitetsplanlægning. Når infrastrukturen er på plads kan du aktivere gentagelse til FOS og fysiske servere. Du kan derefter køre failover for bestemte computere eller oprette gendannelse planer mislykkes over flere computere.

Begynde at komme i gang ved at vælge, hvordan du vil installere gendannelse af websteder. Kom godt i gang strømmen ændres en smule afhængigt af dine krav til gentagelse.


## <a name="step-1-choose-your-protection-goals"></a>Trin 1: Vælg dine beskyttelse mål

Vælg, hvad du vil gentage og hvor du kan replikeres til.

1. Vælg din samling bladet **gendannelse Services vaults** og klik på **Indstillinger**.
2. I **Indstillinger for** > **Kom godt i gang** skal du klikke på **Gendannelse af websteder** > **trin 1: forberede infrastruktur** > **beskyttelse mål**.

    ![Vælg mål](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. Vælg **Til Azure**i **målet for beskyttelse** , og vælg **Ja, med VMware vSphere Hypervisor**. Klik derefter på **OK**.

    ![Vælg mål](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Trin 2: Konfigurere kildemiljøet

Konfigurere server configuration og registrere det i samling af legitimationsoplysninger gendannelsestjenester. Hvis du replikering VMware FOS Angiv VMware konto, du bruger til automatisk registrering.

1. Klik på **trin 1: forberede infrastruktur** > **kilde**. I **forberede kilden** Hvis du ikke har en server configuration klikke på **+ server Configuration** til at tilføje en.

    ![Konfigurere kilde](./media/site-recovery-vmware-to-azure/set-source1.png)

2. Den **Server Configuration** vises i **Tilføj Server** blade Kontrollér i **Server Skriv**.
3. Kontrollere [forudsætninger](#configuration-server-prerequisites), før du konfigurerer server configuration. I bestemt Kontrollér, at computeren kan få adgang til de påkrævede URL-adresser.
4.  Hente installationsfilen gendannelse Unified oprettelse af et sted.
5.  Hent tasten samling registrering. Du skal bruge dette, når du kører installationsprogrammet til Unified. Nøglen er gyldige for 5 dage, når du opretter den.

    ![Konfigurere kilde](./media/site-recovery-vmware-to-azure/set-source2.png)

6.  Kør Unified installationsprogrammet for at installere server configuration, processerveren og master target server på den computer, som du bruger som server configuration.


### <a name="run-site-recovery-unified-setup"></a>Gendannelse af køre websteder Unified konfiguration

1.  Kør installationsfilen Unified konfiguration.
2.  Vælg **Installer server configuration and proces server**ind, **før du går i gang** .

    ![Før du starter](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. Klik på **jeg accepterer** for at hente og installere MySQL **Fra tredjepart softwarelicensen** .

    ![Tredje = part software](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. Gennemse, og vælg tasten registrering, du har hentet fra samling af legitimationsoplysninger i **registrering** .

    ![Registrering](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. Angiv, hvordan den udbyder, der kører på konfigurationsserveren opretter forbindelse til Azure gendannelse af websteder via internettet i **Internetindstillinger** .

    - Hvis du vil oprette forbindelse til proxyen, der er i øjeblikket er konfigureret på computeren kan du vælge **Opret forbindelse til eksisterende proxyindstillinger**.
    - Vælg **Opret forbindelse direkte uden en proxy**, hvis du vil hos udbyderen for at oprette forbindelse direkte.
    - Hvis den eksisterende proxy kræver godkendelse, eller du vil bruge en brugerdefineret proxy for forbindelsen udbyder, kan du vælge **Opret forbindelse til brugerdefineret proxyindstillinger**.
        - Hvis du bruger en brugerdefineret proxy, skal du angive den adresse, port og legitimationsoplysninger
        - Hvis du bruger en proxy, du har allerede må URL-adresser, der er beskrevet i [forudsætninger](#configuration-server-prerequisites).

    ![Firewall](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. **Kontrollere forudsætninger, der** kører konfiguration Tjek at sikre dig, at installationen kan køre. Bekræft, at klokkeslættet på systemuret (indstillinger for**dato og klokkeslæt** ) er den samme som tidszonen, hvis der vises en advarsel om **synkronisering af globale klokkeslæt kontrollere** .

    ![Forudsætninger](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. Oprette legitimationsoplysninger for at logge på den MySQL server-forekomst, der skal installeres i **MySQL konfiguration** .

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. I Vælg **Miljø detaljer** om du vil gentage VMware FOS. Hvis du er, derefter kontrolleres installationen det, at PowerCLI 6.0 er installeret.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. I **Installationsplacering** Vælg, hvor du vil installere de binære filer og gemme cachen. Du kan vælge et drev, der har mindst 5 GB lagerplads, men vi anbefaler et cache drev med mindst 600 GB ledig diskplads.

    ![Installere placering](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. Angiv lytteren (netværkskort og SSL-port) som server configuration sender og modtager gentagelse data i **Valg af netværk** . Du kan ændre standard port (9443). Ud over denne port bruges port 443 af en webserver, orchestrates gentagelse handlinger. 443 ikke bør bruges til at modtage replikeringstrafik.


    ![Valg af netværk](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  Gennemse oplysningerne i **Oversigt** , og klik på **Installer**. Når installationen afsluttes genereres en adgangskode. Du skal bruge den, når du aktiverer gentagelse så kopiere det og beholde den i et sikkert sted.

    ![Oversigt](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  Når registrering afsluttes serveren, der vises i **Indstillinger for** > **servere** blade i samling af legitimationsoplysninger.



#### <a name="run-setup-from-the-command-line"></a>Kør installationsprogrammet fra kommandolinjen

Du kan konfigurere konfigurationsserveren fra kommandolinjen:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Parametre:

- / ServerMode: obligatorisk. Angiver, om konfiguration og proces servere skal installeres eller kun proces server. Inputværdier: CS, PS.
- InstallLocation: obligatorisk. Den mappe, hvor komponenterne er installeret.
- / MySQLCredsFilePath. Obligatorisk. Stien til filen, hvor MySQL server-legitimationsoplysninger er gemt. Filen skal være i dette format:
    - [MySQLCredentials]
    - MySQLRootPassword = "<Password>"
    - MySQLUserPassword = "<Password>"
- / VaultCredsFilePath. Obligatorisk. Placeringen af filen samling legitimationsoplysninger
- / EnvType. Obligatorisk. Typen installation. Værdier: VMware, NonVMware
- / PSIP og /CSIP. Obligatorisk. IP-adressen på proces server og server configuration.
- / PassphraseFilePath. Obligatorisk. Placeringen af filen adgangskoden.
- / BypassProxy. Valgfrit. Angiver, at server configuration opretter forbindelse til Azure uden en proxy.
- / ProxySettingsFilePath. Valgfrit. Indstillinger for proxyserver (standard proxyen kræver godkendelse, eller en brugerdefineret proxy). Filen skal være i dette format:
    - [ProxySettings]
    - ProxyAuthentication = "Ja/Nej"
    - Proxy IP = "IP-adresse >"
    - ProxyPort = "<Port>"
    - ProxyUserName = "<User Name>"
    - ProxyPassword = "<Password>"
- DataTransferSecurePort. Valgfrit. Portnummeret, der skal bruges til gentagelse data.
- SkipSpaceCheck. Valgfrit. Gå videre mellemrum Kontrollér, om cache.
- AcceptThirdpartyEULA. Obligatorisk. Flag indebærer accept af tredjepart slutbrugerlicensaftale (EULA).
- ShowThirdpartyEULA. Obligatorisk. Viser tredjeparters slutbrugerlicensaftale (EULA). Hvis angivet som input ignoreres alle andre parametre.

### <a name="add-the-vmware-account-used-for-automatic-discovery"></a>Tilføje kontoen VMware bruges til automatisk registrering

 Når du har forberedt til installation skal du har [oprettet en VMware-konto](#prepare-an-account-for-automatic-discovery) , gendannelse af websteder kan bruge til automatisk registrering. Tilføje denne konto på følgende måde:

1. Åbn **CSPSConfigtool.exe**. Det er tilgængeligt som en genvej på skrivebordet og placeret i mappen [INSTALLATIONSPLACERING] \home\svsystems\bin.
2. Klik på **Administrer konti** > **Tilføj konto**.

    ![Tilføj konto](./media/site-recovery-vmware-to-azure/credentials1.png)

3. Tilføje den konto, der skal bruges til automatisk registrering i **Account Details** . Bemærk, at det kan tage 15 minutter eller mere til kontonavnet vises i portalen. Hvis du vil opdatere med det samme, skal du klikke på **Konfiguration servere** > servernavn > **Opdater Server**.

    ![Detaljer](./media/site-recovery-vmware-to-azure/credentials2.png)

### <a name="connect-to-vsphere-hosts-and-vcenter-servers"></a>Oprette forbindelse til vSphere værter og vCenter servere

Hvis du replikering VMware FOS oprette forbindelse til vSphere værter og vCenter-servere.

1. Kontrollér, at server configuration har netværksadgang til vSphere værter og vCenter servere.
2. Klik på **Forbered infrastruktur** > **kilde**. Vælg server configuration **Forbered kilde** , og klik på **+ vCenter** for at tilføje en vSphere host eller vCenter server.
3. Angiv et fuldt navn til vSphere host eller vCenter serveren i **Tilføj vCenter** , og Angiv den IP-adresse eller det fulde Domænenavn på serveren. Lad port som 443, medmindre serverne VMware er konfigureret til at lytte til anmodninger på en anden port. Vælg derefter den konto, der skal bruges til at oprette forbindelse til VMware-serveren. Klik på **OK**.

    ![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

    >[AZURE.NOTE] Hvis du føjer vCenter serveren eller vSphere host med en konto, der ikke har administratorrettigheder på vCenter eller host-serveren, derefter skal du kontrollere, at kontoen har disse rettigheder, der er aktiveret: Datacenter, Datastore, mappe, Host, netværk, ressource, virtuel machine, vSphere fordelt Skift. Desuden skal vCenter serveren lagerplads visninger rettigheder.

Gendannelse af websteder opretter forbindelse til VMware servere ved hjælp af de indstillinger, du har angivet og opdager FOS.

## <a name="step-3-set-up-the-target-environment"></a>Trin 3: Konfigurere miljøet mål

Kontrollér, at du har en lagerplads konto for gentagelse og et Azure netværk, som opretter forbindelse Azure FOS efter failover.

1.  Klik på **Forbered infrastruktur** > **mål** og vælg det Azure abonnement, du vil bruge.
2.  Angiv den implementeringsmodel, du vil bruge til FOS efter failover.
3.  Gendannelse af websteder kontrollerer, at du har en eller flere kompatible Azure lagerplads konti og netværk.

    ![Mål](./media/site-recovery-vmware-to-azure/gs-target.png)

4.  Hvis du ikke har oprettet en lagerplads-konto, og du vil oprette en ved hjælp af ARM klikke på **+ lagerplads konto** til at gøre indbygget.  Angive et kontonavn, type, abonnement og placering på bladet **Opret lagerplads konto** . Kontoen skal være i samme område som gendannelse Services samling.

    ![Lagerplads](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

    Bemærk, at:

    - Hvis du vil oprette en lagerplads konto ved hjælp af klassisk modellen skal du gøre det på portalen Azure. [Lær mere](../storage/storage-create-storage-account-classic-portal.md)
    - Hvis du bruger en premium lagerplads konto til replikerede data, skal du konfigurere en ekstra standard lagerplads konto til registreres store gentagelse, capture igangværende ændringer til lokale data.

    > [AZURE.NOTE] Beskyttelse til premium lagerplads konti i Central Indien og Syd Indien understøttes ikke i øjeblikket.

4.  Vælg et netværk med Azure. Hvis du ikke har skabt et netværk, og du vil gøre det ved hjælp af ARM klikke på **+ netværk** til at gøre indbygget. Angiv et netværksnavn, adresseområde, undernet detaljer, abonnement og placering på bladet **Opret virtuelt netværk** . Netværket skal være i samme placering, som samling af legitimationsoplysninger gendannelsestjenester.

    ![Netværk](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

    Hvis du vil oprette et netværk med klassisk modellen skal du gøre det på portalen Azure. [Få mere at vide](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

## <a name="step-4-set-up-replication-settings"></a>Trin 4: Konfigurere indstillinger for gentagelse

1. Oprette en ny replikering politik skal du klikke på **Forbered infrastruktur** > **Replikeringsindstillinger** > **+ oprette og knytte**.
2. Angiv et navn på politik i **oprette og knytte politik** .
3. I **grænseværdi for frigivne Produktionsordre**: Angiver den frigivne Produktionsordre grænse. Beskeder genereres, når fortløbende gentagelse overskrider denne grænse.
5. I **gendannelse Peg opbevaring**, angive i timer hvor lang tid vinduet opbevaring bliver for hvert gendannelsespunkt. Beskyttet computere kan gendannes til et punkt i et vindue. Op til 24 timer opbevaring er understøttet på computere replikeres til premium-lager.
6. Angiv i **App-ensartet øjebliksbillede hyppighed**, hvor ofte (i minutter) gendannelse punkter, der indeholder programmet ensartet snapshots oprettes.
7. Når du opretter en politik for gentagelse, som standard oprettes en tilsvarende politik automatisk til failback. For eksempel hvis politikken replikering er **sælger-politik** og derefter politikken failback bliver **sælger-politik-failback**. Denne politik bruges ikke, før du starter en failback.  
8. Klik på **OK** for at oprette politikken.

    ![Politikken til replikering](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. Når du opretter en ny politik er den automatisk knyttet til af konfigurationsserveren. Klik på **OK**.

    ![Politikken til replikering](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## <a name="step-5-capacity-planning"></a>Trin 5: Kapacitetsplanlægning

Nu hvor du har dit basic infrastruktur konfigurere du kan synes om kapacitetsplanlægning af og finde ud af, om du har brug for yderligere ressourcer.

Gendannelse af websteder giver en kapacitet planlægger for at hjælpe dig med at tildele de rette ressourcer til kilde-miljø, websted gendannelse komponenter, netværk og opbevaring. Du kan køre planlæggeren i hurtigtilstand for skøn baseret på en gennemsnitlige antal FOS, diske og lagerplads eller i detaljeret tilstand, skal du angive figurer på niveauet for arbejdsbyrde. Før du går i gang skal du:

- Indsamle oplysninger om det gentagelse miljø, herunder FOS, diske per FOS og lager per disk.
- Anslå daglige ændring (transportspand) du har for replikerede data. Du kan bruge [vSphere kapacitet, planlægning maskinen](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) kan hjælpe dig med at gøre dette.

1.  Klik på **Hent** for at hente værktøjet og derefter køre den. [Læse artiklen](site-recovery-capacity-planner.md) , der følger med værktøjet.
2.  Når du er færdig Vælg **Ja** **har du udført kapacitetsplanlægning?**

    ![Planlægning af kapacitet](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

Tabellen nedenfor registrerer et antal point kan hjælpe dig med kapacitet, planlægning af dette scenario.


**Komponent** | **Detaljer**
--- | --- | ---
**Gentagelse** | **Maksimal dagligt ændring rente**– en beskyttet maskine kan kun bruge én proces server, og en enkelt proces server kan håndtere en daglig Skift bedømme op til 2 TB. Dermed er 2 TB de maksimale daglige data ændres rente, der understøttes til en beskyttet maskine.<br/><br/> **Maksimum overførselshastighed**– en replikerede maskine kan tilhøre én lagerplads konto i Azure. En almindelig lagerplads konto kan håndtere op til 20.000 anmodninger sekundet, og vi anbefaler, at du beholder antallet af IOP'ER på tværs af en kilde maskine med 20.000. For eksempel hvis du har en kilde maskine med 5 diske og hver disk genererer 120 IOP'ER (8K størrelse) på kilden vil det være i Azure per disk IOP'ER grænsen på 500. Antallet af lagerplads konti påkrævet = samlet kilde IOP'er/20000.
**Server Configuration** | Server configuration bør kunne håndtere daglige Skift rente kapaciteten på tværs af alle arbejdsbelastninger, som kører på beskyttet computere og skal tilstrækkelig båndbredde til løbende gentage data til Azure-lager.<br/><br/> Vi anbefaler, at server configuration findes på den samme netværks- og LAN-segment som de computere, du vil beskytte som en bedste fremgangsmåde. Det kan være placeret på et andet netværk, men computere, du vil beskytte bør have L3 netværkets synlighed til den.<br/><br/> Størrelse anbefalinger til af konfigurationsserveren er opsummeret i tabellen nedenfor.
**Process-server** | Den første proces server er installeret som standard på server configuration. Du kan installere yderligere processer servere for at tilpasse dit miljø. Bemærk, at:<br/><br/> Processen serveren modtager gentagelse data fra beskyttet computere og optimerer med cachelagring, komprimering og kryptering inden du sender til Azure. Servercomputeren proces har tilstrækkelige ressourcer til at udføre disse opgaver.<br/><br/> Processen serveren bruger diskbaserede cache. Vi anbefaler, at en separat cache disk af 600 GB eller mere til at håndtere dataændringer, der er gemt i tilfælde af netværk flaskehals eller -afbrydelse.

### <a name="size-recommendations-for-the-configuration-server"></a>Størrelse anbefalinger til af konfigurationsserveren

**CPU** | **Hukommelse** | **Cache diskstørrelse** | **Dataene ændres rente** | **Beskyttet computere**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 kerner @ 2,5 GHz) | 16 GB | 300 GB | 500 GB eller mindre | Gentage mindre end 100 computere.
12 vCPUs (2 sockets * 6 kerner @ 2,5 GHz) | 18 GB | 600 GB | 500 GB til 1 TB | Gentage mellem 100-150 maskiner.
16 vCPUs (2 sockets * 8 kerner @ 2,5 GHz) | 32 GB | 1 TB | 1 TB til 2 TB | Gentage mellem 150-200 maskiner.
Installere en anden proces-server | | | > 2 TB | Installere yderligere processer servere, hvis du replikering mere end 200 maskiner, eller hvis daglig dataene ændre hastigheden overskrider 2 TB.

Hvor:

- Hver kildecomputeren er konfigureret med 3 diske på 100 GB.
- Vi bruges benchmarking lagringen af 8 SAS-drev på 10 K RPM med RAID 10 til cache disk mål.

### <a name="size-recommendations-for-the-process-server"></a>Størrelse anbefalinger til processerveren

Du kan tilføje yderligere processer servere til at håndtere gentagelse indlæsning, hvis du har brug at beskytte mere end 200 computere eller daglige Skift rente er større end 2 TB. Hvis du vil skalere ud af, du kan:

- Øge antallet af konfiguration servere. Du kan for eksempel beskytte op til 400 maskiner med to konfiguration-servere.
- Tilføje yderligere processer servere og bruge disse til at håndtere trafik i stedet for (eller ud over) server configuration.

I denne tabel beskriver et scenarie, hvor:

- Du ikke planer om at bruge server configuration som en process-server.
- Du har konfigureret en server til yderligere processer.
- Du har konfigurere beskyttet virtuelle computere at bruge flere processerveren.
- Hver kildecomputer beskyttet er konfigureret med tre diske på 100 GB.

**Server Configuration** | **Yderligere processer server**| **Cache diskstørrelse** | **Dataene ændres rente** | **Beskyttet computere**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 kerner @ 2,5 GHz), 16 GB hukommelse | 4 vCPUs (2 sockets * 2 kerner @ 2,5 GHz), 8 GB hukommelse | 300 GB | 250 GB eller mindre | Gentage 85 eller mindre computere.
8 vCPUs (2 sockets * 4 kerner @ 2,5 GHz), 16 GB hukommelse | 8 vCPUs (2 sockets * 4 kerner @ 2,5 GHz), 12 GB hukommelse | 600 GB | 250 GB til 1 TB | Gentage mellem 85-150 maskiner.
12 vCPUs (2 sockets * 6 kerner @ 2,5 GHz), 18 GB hukommelse | 12 vCPUs (2 sockets * 6 kerner @ 2,5 GHz) 24 GB hukommelse | 1 TB | 1 TB til 2 TB | Gentage mellem 150-225 maskiner.


Den måde, hvori du skalere dine servere, der afhænger af din præference for en skala op eller model kan skalere.  Du skalere op ved at installere et par Avanceret konfiguration og proces servere eller skalere ud ved at installere flere servere med mindre ressourcer. For eksempel: Hvis du har brug at beskytte 220 computere kan du gøre et af følgende:

- Konfigurere server configuration med 12vCPU, 18 GB hukommelse, en yderligere processer server med 12vCPU, 24 GB hukommelse, og Konfigurer beskyttet maskiner for at bruge ekstra proces server.
- Alternativt kan du konfigurere to konfiguration (2 x 8vCPU, 16 GB RAM) og to yderligere processer servere (1 x 8vCPU) og 4vCPU x 1 til at håndtere 135 + 85 (220) computere og konfigurere beskyttet maskiner for at bruge de yderligere processer servere.

[Følg disse instruktioner](#deploy-additional-process-servers) til konfiguration af en server til yderligere processer.

### <a name="network-bandwidth-considerations"></a>Overvejelser i forbindelse med netværksbåndbredde

Du kan bruge værktøjet kapacitet teamplanlægning til at beregne den båndbredde, du skal bruge til gentagelse (indledende gentagelse og derefter delta). Hvis du vil styre mængden af båndbredde til replikering har du et par muligheder:

- **Begrænse båndbredde**: VMware trafik, der kopieres til Azure gennemgår en bestemt process-server. Du kan begrænse båndbredden på de maskiner, der kører som proces-servere.
- **Påvirke båndbredde**: Du kan påvirke den båndbredde, der bruges af replikering ved hjælp af et par registreringsdatabasenøgler:
    - Værdien i registreringsdatabasen **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** angiver antallet tråde, der bruges til dataoverførsel (de oprindelige eller delta replikering) på en disk. En højere værdi forøger netværksbåndbredde for gentagelse.
    - **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** angiver antallet tråde, der bruges til dataoverførsel under failback.

#### <a name="throttle-bandwidth"></a>Begrænse båndbredde

1. Åbn snap-in Microsoft Azure sikkerhedskopi MMC på den computer, der fungerer som processerveren. En genvej til Microsoft Azure sikkerhedskopiering er tilgængelige på skrivebordet eller på C:\Program Files\Microsoft Azure gendannelse Services Agent\bin\wabadmin som standard.
2. Klik på **Skift egenskaber**i snap-in.

    ![Begrænse båndbredde](./media/site-recovery-vmware-to-azure/throttle1.png)

3. Under fanen **Throttling** Vælg **Aktiver brug af internetbåndbredde (throttling) for sikkerhedskopiering**og angive begrænsninger for arbejde og ikke-arbejde timer. Gyldige områder er fra 512 Kbps til 102 Mbps sekundet.

    ![Begrænse båndbredde](./media/site-recovery-vmware-to-azure/throttle2.png)

Du kan også bruge cmdlet'en [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) til at angive (throttling). Her er et eksempel:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Angiv OBMachineSetting NoThrottle** angiver, at ingen (throttling) er påkrævet.


#### <a name="influence-network-bandwidth"></a>Påvirke netværksbåndbredde

1. Gå til **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**i registreringsdatabasen.
    - Ændre værdien **UploadThreadsPerVM**for at påvirke den båndbredde trafik på en kopierer disk, eller Opret nøgle, hvis den ikke findes.
    - Hvis du vil påvirke båndbredden til failback trafik fra Azure, at ændre værdien **DownloadThreadsPerVM**.
2. Standardværdien er 4. I en "overprovisioned" netværk, bør disse registreringsdatabasenøgler ændres fra standardværdierne. Maksimum er 32. Overvåge trafik til at optimere værdien.

## <a name="step-6-replicate-applications"></a>Trin 6: Kontraprøverne programmer

Sørg for, at computere, du vil gentage er forberedt til mobilitet service installation, og derefter aktivere gentagelse.

### <a name="install-the-mobility-service"></a>Installere tjenesten mobilitet

Det første trin i aktivere beskyttelse til virtuelle computere og fysiske servere er at installere tjenesten mobilitet. Du kan gøre dette på flere måder:

- **Processen server opslagsnål**: Når du aktiverer gentagelse på en computer, push og installere komponenten mobilitet service fra processerveren. Bemærk, at push installationen ikke opstå, hvis maskiner allerede kører en op-todate version af komponenten.
- **Enterprise opslagsnål**: automatisk at installere komponenten ved hjælp af din virksomhed opslagsnål proces som WSUS eller System Center Configuration Manager eller [Azure automatisering og ønsket tilstand konfiguration](./site-recovery-automate-mobility-service-install.md). Konfigurere server configuration, før du gør dette.
- **Manuel installation**: installere komponenten manuelt på hver maskine, du vil gentage. Konfigurere server configuration, før du gør dette.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Forberede automatisk opslagsnål på Windows-computere

Her er, hvordan du forbereder Windows-computere, så tjenesten mobilitet kan installeres automatisk af processen serveren.

1.  Oprette en konto, der kan bruges af processen serveren til at få adgang til computeren. Kontoen skal have administratorrettigheder (lokale eller domæne), og den bruges kun til opslagsnål installationen.

    >[AZURE.NOTE] Hvis du ikke bruger en domænekonto, skal du deaktivere Remote brugeradgang kontrolelement på den lokale computer. Sådan gør du i journalen under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System tilføje DWORD-posten LocalAccountTokenFilterPolicy med værdien 1. At tilføje posten i registreringsdatabasen fra en CLI type **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  Vælg **Tillad en app eller en funktion passerer gennem Firewall**på Windows Firewall på computeren, du vil beskytte. Aktivere **fil- og printerdeling** og **Windows Management Instrumentation**. Du kan konfigurere indstillingerne for firewallen med et Gruppepolitikobjekt for maskiner, der hører til et domæne.

    ![Firewall-indstillinger](./media/site-recovery-vmware-to-azure/mobility1.png)

2. Tilføje den konto, du har oprettet:

    - Åbn **cspsconfigtool**. Det er tilgængeligt som en genvej på skrivebordet og placeret i mappen [INSTALLATIONSPLACERING] \home\svsystems\bin.
    - Fanen **Administrer konti** , skal du klikke på **Tilføj konto**.
    - Tilføje den konto, du har oprettet. Når du har tilføjet kontoen skal du angive legitimationsoplysningerne, når du aktiverer gentagelse til en computer.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Forberede automatisk opslagsnål på Linux-servere

1.  Sørg for, at den Linux maskine, du vil beskytte er understøttet, som beskrevet i [forudsætninger, der er beskyttet computer](#protected-machine-prerequisites). Sikre, at der er netværk forbindelsen mellem Linux maskinen og processerveren.

2.  Oprette en konto, der kan bruges af processen serveren til at få adgang til computeren. Kontoen skal være en rodbruger på Linux kildeserveren og den bruges kun til opslagsnål installationen.

    - Åbn **cspsconfigtool**. Det er tilgængeligt som en genvej på skrivebordet og placeret i mappen [INSTALLATIONSPLACERING] \home\svsystems\bin.
    - Fanen **Administrer konti** , skal du klikke på **Tilføj konto**.
    - Tilføje den konto, du har oprettet. Når du har tilføjet kontoen skal du angive legitimationsoplysningerne, når du aktiverer gentagelse til en computer.

3.  Kontrollér, at filen fjernsystemet på Linux kildeserveren indeholder posterne, der er tilknyttet IP-adresser, der er knyttet til alle netværkskort lokale hostname.
4.  Installere den nyeste openssh, openssh-server, openssl pakker på den computer, du vil gentage.
5.  Sørg for SSH er aktiveret og kører på port 22.
6.  Aktivere SFTP undersystem og din adgangskode godkendelse i filen sshd_config på følgende måde:

    - Log på som rod.
    - Find den linje, der begynder med **PasswordAuthentication**i filen fil /etc/ssh/sshd_config.
    - Fjern kommentar fra linjen, og Skift værdien fra **ingen** til **Ja**.
    - Find den linje, der begynder med **undersystem** , og fjern kommentar fra linjen.

        ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Installere tjenesten mobilitet manuelt

Installer er tilgængelige på serveren konfiguration i **C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

Kilde-operativsystem | Mobilitet service installationsfilen
--- | ---
Windows Server (kun 64 bit) | Microsoft-ASR_UA_9. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (kun 64 bit) | Microsoft-ASR_UA_9. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (kun 64 bit) | Microsoft-ASR_UA_9. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (kun 64 bit) | Microsoft-ASR_UA_9. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-mobility-service-on-a-windows-server"></a>Installere mobilitet Service på en Windows-Server


1. Hente og køre den relevante installer.
2. Vælg **tjeneste, mobilitet**ind, **før du går i gang** .

    ![Mobilitet service](./media/site-recovery-vmware-to-azure/mobility3.png)

3. Angiv IP-adressen på server configuration og den adgangskode, der blev oprettet, når du kørte installationsprogrammet til Unified i **Konfigurationsoplysninger Server** . Du kan hente adgangskoden ved at køre: ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – v** på server configuration.

    ![Mobilitet service](./media/site-recovery-vmware-to-azure/mobility6.png)

4. Bevar standardindstillingen i **Installationsplacering** , og klik på **Næste** for at starte installationen.
5. Igangværende **Installation** overvåge installationen, og genstart computeren, hvis du bliver bedt om. Når du har installeret tjenesten kan det tage omkring 15 minutter, før status for at opdatere på portalen.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Installere mobilitet Service på en Windows server ved hjælp af kommandoprompten

1. Kopiere installationsprogrammet til en lokal mappe (sig C:\Temp) på den server, du vil beskytte. Installationsprogrammet kan findes på serveren konfiguration under **[installationsplacering] \home\svsystems\pushinstallsvc\repository**. Pakken til Windows-operativsystemer har et navn, der svarer til Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. **Omdøb** filen til MobilitySvcInstaller.exe
3. Køre følgende kommando til at uddrage MSI-installer </br>

        C:\> cd C:\Temp
        C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted
        C:\Temp> cd Extracted
        C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>

#####<a name="full-command-line-syntax"></a>Syntaks for fulde kommandolinje

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Parametre**

- **/Role:** Obligatorisk. Angiver, om tjenesten mobilitet skal installeres. Inputværdier Agent | MasterTarget
- **/InstallLocation:** Obligatorisk. Angiver, hvor tjenesten skal installeres.
- **/PassphraseFilePath:** Obligatorisk. Adgangskoden til konfigurationen server.
- **/LogFilePath:** Obligatorisk. Placering, hvor installationen logfiler skal oprettes.



#### <a name="uninstall-mobility-service-manually"></a>Manuelt fjerne mobilitet service

Mobilitet tjenesten kan fjernes ved hjælp af Tilføj fjerne programmet fra Kontrolpanel eller ved hjælp af kommandolinjen.

Kommandoen til at fjerne tjenesten mobilitet kommandolinje er

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### <a name="install-mobility-service-on-a-linux-server-using-command-line"></a>Installere mobilitet Service på en Linux-server, ved hjælp af kommandolinjen

1. Kopiere det relevante tjære arkiv, der er baseret på tabellen ovenfor for at den Linux maskine, du vil gentage.
2. Åbn et shell-program og udtrække zippede tjære arkiv til en lokal sti ved at køre:`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Oprette en passphrase.txt fil i den lokale mappe, hvor du har pakket indholdet af tjære arkiv. Gøre dette Kopiér adgangskoden fra C:\ProgramData\Microsoft Azure websted Recovery\private\connection.passphrase på server configuration og gemme den i passphrase.txt ved at køre *`echo <passphrase> >passphrase.txt`* i shell.
4. Installere tjenesten mobilitet ved at køre *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Angiv den interne IP-adresse på konfigurationsserveren,, og Sørg for, at port 443 er markeret. Når du har installeret tjenesten kan det tage omkring 15 minutter, før status for at opdatere på portalen.

**Du kan også installere fra kommandolinjen**:

1. Kopiér adgangskoden fra C:\Program Files (x86) \InMage Systems\private\connection på konfigurationsserveren, og Gem den som "passphrase.txt" på server configuration. Kør derefter disse kommandoer. I vores eksempel konfiguration serverens IP-adresse er 104.40.75.37 og HTTPS-port skal være 443:

Sådan installeres på en fremstilling server:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Sådan installeres på master serveren:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### <a name="enable-replication"></a>Aktivere gentagelse

#### <a name="before-you-start"></a>Før du starter

Hvis du replikering VMware virtuelle opmærksom maskiner på følgende:

- VMware FOS er blevet fundet hver 15 minutter, og det kan tage 15 minutter eller længere tid, før de kan vises i portalen efter registrering. Tillader kan registrering tage 15 minutter eller mere når du tilføjer en ny vCenter server eller vSphere vært.
- Miljøændringer i den virtuelle maskine (såsom VMware værktøjer installation) kan også tage 15 minutter eller mere for at blive opdateret på portalen.
- For server/vSphere vCenter vært på bladet **Konfiguration servere** , kan du kontrollere opdaget sidst for VMware FOS i feltet **Sidste kontakt på** .
- For at tilføje computere til replikering uden at vente planlagte registrering skal du fremhæve server configuration (ikke klikke på den), og klik på knappen **Opdater** .
- Når du aktiverer gentagelse, hvis computeren er forberedt process-server automatisk installerer tjenesten mobilitet på den.

#### <a name="exclude-disks-from-replication"></a>Udelade diske fra gentagelse

Når du aktiverer gentagelse, som standard replikeres alle diske på en computer. Du kan udelade diske fra gentagelse. For eksempel kan du ikke vil gentage diske med midlertidige data eller data, der er blevet opdateret hver gang en maskine eller program genstarter (for eksempel pagefile.sys eller SQL Server tempdb). Hvis du vil udelade diske Bemærk, at:

- Du kan kun udelade diske, der allerede har installeret mobilitet tjenesten. Du skal installere [manuelt tjenesten mobilitet](#install-the-mobility-service-manually) , da tjenesten mobilitet er kun installeret ved hjælp af push-funktion, når replikering er aktiveret.
- Kun grundlæggende disk kan udelades fra gentagelse. Du kan ikke udelades OS eller dynamiske diske.
- Når replikering er aktiveret kan du ikke tilføje eller fjerne diske for gentagelse. Hvis du vil tilføje eller udelade en disk, skal du deaktivere beskyttelse til computeren og derefter genaktivere den.
- Hvis du udelader en disk, der har behov for et program til at betjene, efter failover til Azure skal du oprette den manuelt i Azure så replikerede programmet kan køre. Alternativt kan du integrerer Azure automatisering i en gendannelse for at oprette disken under failover på computeren.
- Du opretter manuelt i Azure diske bliver ikke gennemført tilbage. Eksempelvis hvis mislykkes over tre diske, kan du oprette to direkte i Azure alle fem bliver ikke gennemført tilbage. Du kan ikke udelades diske oprettet manuelt fra failback.

**Aktiver nu replikering på følgende måde**:

1. Klik på **trin 2: gentage programmet** > **kilde**. Når du har aktiveret replikering for første gang skal du klikke på **+ gentage** i samling af legitimationsoplysninger til at aktivere gentagelse til flere computere.
2. I bladet **kilde** > **kilde** vælge server configuration.
3. Vælg **virtuelle maskiner** eller **Fysisk maskiner**i **maskine type** .
4. Vælg den vCenter server, der administrerer værten vSphere i **vCenter/vSphere Hypervisor** , eller Vælg vært. Denne indstilling er ikke relevant, hvis du replikering fysiske computere.
5. Vælg proces-server. Hvis du ikke har skabt eventuelle yderligere processer servere er dette navnet på konfigurationsserveren. Klik derefter på **OK**.

    ![Aktivere gentagelse](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. Vælg abonnementet, samling af legitimationsoplysninger i **destination** , og vælg den model (klassisk eller ressource management), du vil bruge i Azure efter failover i **indlæg failover implementeringsmodel** .
7. Vælg kontoen Azure-lager, du skal bruge til replikering af data. Bemærk, at:

    - Du kan vælge en premium eller standard lagerplads konto. Hvis du vælger en premium-konto, skal du angive en ekstra standard lagerplads konto for igangværende gentagelse loggene. Konti skal være i samme område som gendannelse Services samling.
    - Hvis du vil bruge en anden lagerplads konto end dem, har du kan [oprette en](#set-up-an-azure-storage-account). Klik på **Opret ny**for at oprette et lager konto ved hjælp af ARM modellen. Hvis du vil oprette en lagerplads konto ved hjælp af klassisk modellen, du bruger [i portalen Azure](../storage/storage-create-storage-account-classic-portal.md).

8. Vælg Azure Netværks- og undernet som Azure FOS opretter forbindelse til, når de er spundet op efter failover. Netværket skal være i samme område som gendannelse Services samling. Vælg **Konfigurer nu til markerede computere** at anvende indstillingen på alle computere, du vælger for beskyttelse. Vælg **Konfigurer senere** for at markere det Azure netværk per maskine. Hvis du ikke har et netværk skal du oprette [en](#set-up-an-azure-network). Klik på **Opret ny**for at oprette et netværk med ARM modellen. Hvis du vil oprette et netværk med klassisk modellen, du bruger [i portalen Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Vælg et undernet, hvis det er relevant. Klik derefter på **OK**.

    ![Aktivere gentagelse](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. I **virtuelle maskiner** > **Vælg virtuelle maskiner** skal du klikke på og Vælg hver maskine, du vil gentage. Du kan kun vælge maskiner, gentagelse kan aktiveres. Klik derefter på **OK**.

    ![Aktivere gentagelse](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. I **Egenskaber for** > **konfigurere egenskaber**, Vælg den konto, der automatisk skal bruges af processen serveren for at installere tjenesten mobilitet på maskinen. Som standard replikeres alle. Klik på **Alle** og fjerne alle diske, du ikke vil gentage. Klik derefter på **OK**. Du kan angive yderligere egenskaber, der senere.

    ![Aktivere gentagelse](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. I **Replikeringsindstillinger** > **Konfigurer replikeringsindstillinger** bekræfte, at den korrekte gentagelse politik er markeret. Du kan ændre gentagelse politikindstillinger i **Indstillinger for** > **Gentagelse politikker** > navn på politik > **Rediger indstillinger**. Ændringer, du anvender en politik, der skal anvendes til kopierer og nye computere.

12. Aktivere **flere VM konsistens** , hvis du vil indsamle computere i en gruppe, gentagelse, og Angiv et navn til gruppen. Klik derefter på **OK**. Bemærk, at:

    - Computere i gentagelse gruppere replikeres og har delt nedbrud ensartet og app-ensartede gendannelse punkter, når de ikke.
    - Vi anbefaler, at du samler FOS og fysiske servere at afspejle dine arbejdsmængder. Aktivere flere VM konsistens kan påvirke arbejdsbelastningen ydeevnen og bør kun bruges, hvis computere der kører samme arbejdsbelastningen, og du har brug for konsistens.

    ![Aktivere gentagelse](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. Klik på **Aktiver gentagelse**. Du kan følge statussen for **Aktivér beskyttelse** jobbet i **Indstillinger for** > **job** > **Websted gendannelse job**. Når du har kørt **Færdiggøre beskyttelse** jobbet maskinen er klar til failover.

> [AZURE.NOTE] Hvis computeren er forberedt på opslagsnål installation installeres komponenten mobilitet service, når beskyttelse er aktiveret. Når komponenten er installeret på maskinen et beskyttelse job starter og mislykkes. Når fejlen skal du genstarte manuelt hver computer. Efter genstart starter kørslen beskyttelse igen og indledende replikering forekommer.

### <a name="view-and-manage-vm-properties"></a>Få vist og administrere VM egenskaber

Vi anbefaler, at du kontrollerer egenskaber for datakilde maskinen. Husk, at Azure VM navn skal overholde med [Azure virtuelt krav](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Klik på **Indstillinger for** > **replikeret elementer** >, og vælg maskinen. Bladet **Essentials** viser oplysninger om indstillinger for computere og status.

2. Du kan få vist gentagelse og belastningsjustering oplysninger for VM i **Egenskaber** .

    ![Aktivere gentagelse](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. I **Beregn og netværk** > **beregne egenskaber** kan du angive Azure VM navn- og destinationswebsteder størrelse. Ændre navnet, der opfylder kravene Azure, hvis det er nødvendigt.
Du kan også få vist og Tilføj oplysninger om target netværk, undernet og IP-adresser, der skal tildeles til Azure VM. Bemærk følgende:

    - Du kan angive target IP-adresse. Hvis du ikke angiver en adresse, den mislykkedes over machine skal bruge DHCP. Hvis du angiver en adresse, der ikke er tilgængelig på failover, virker sekundære ikke. Den samme target IP-adresse kan bruges til test failover, hvis adressen, der er tilgængelige i test failover netværket.
    - Antallet af netværkskort, bestemmes af den størrelse, du angiver for virtuelt mål på følgende måde:

        - Hvis antallet af netværkskort på maskinen kilde er mindre end eller lig med antallet af kort, der er tilladt for target maskine størrelse, derefter får destinationen det samme antal kort som kilde.
        - Hvis antallet af kort til kilde virtual machine overstiger det antal, der er tilladt for mål størrelsen og derefter mål størrelsen maksimum der skal bruges.
        - Eksempelvis hvis en kilde maskine har to netværkskort og mål maskine størrelsen understøtter fire, target maskinen har to kort. Hvis kildecomputer har to kort, men understøttede mål størrelsen understøtter kun en har target maskinen kun ét kort.     
    - Hvis VM har flere netværkskort de kan alle oprette forbindelse til det samme netværk.

    ![Aktivere gentagelse](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. Du kan se operativsystem og datadisce på VM, der kan replikeres i **diske** .


## <a name="step-7-test-the-deployment"></a>Trin 7: Test installationen

Du kan køre en test sekundær server til en enkelt virtuel computer eller en gendannelse plan, der indeholder en eller flere virtuelle maskiner for at teste installationen.


### <a name="prepare-for-failover"></a>Forberede failover

- Hvis du vil køre en test failover anbefaler vi, at du opretter et nyt Azure netværk, der har identificeret fra netværket Azure fremstilling (dette er standardfunktionsmåden, når du opretter et nyt netværk i Azure). Du kan [Få mere at vide](site-recovery-failover.md#run-a-test-failover) om at køre test failover.
- For at få den bedste ydeevne, når du ikke Azure skal du installere Azure Agent på den beskyttede maskine. Det gør starte hurtigere og hjælper dig med fejlfinding. Installere [Linux](https://github.com/Azure/WALinuxAgent) eller [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) -agent.
- Hvis du vil teste fuldt implementeringen skal du bruge en infrastruktur for replikerede maskinen fungerer som forventet. Du kan oprette en virtuel maskine som et domænenavn fra domænecontrolleren med DNS og replikeres dette til Azure ved hjælp af Azure gendannelse af websteder, hvis du vil teste Active Directory og DNS. Læs mere i [test failover overvejelser i forbindelse med Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Sørg for, at konfigurationsserveren kører. Ellers mislykkes failover.
- Hvis du har udeladt diske fra gentagelse du muligvis oprette disketterne manuelt i Azure efter failover så programmet kører som forventet.
- Hvis du vil køre en ikke-planlagt failover i stedet for en test failover opmærksom på følgende:

    - Hvis det er muligt skal du lukke primære maskiner før du kører en ikke-planlagt failover. Dette sikrer, at du ikke har både kilde- og replika maskiner, der kører på samme tid. Hvis du replikering VMware FOS kan derefter du angive, at gendannelse af websteder skal nok få en bedste indsats for at lukke kilde-computere. Afhængigt af tilstanden for det primære websted dette muligvis eller fungerer muligvis ikke. Hvis du replikering fysiske servere gendannelse af websteder ikke tilbyder denne indstilling.
    - Når du kører en ikke-planlagt failover stopper replikering af data fra primære maskiner, så alle data delta ikke overføres, når en ikke-planlagt failover begynder. Desuden Hvis du kører en ikke-planlagt failover på en plan for gendannelse den kører, indtil fuldført, selvom der opstår en fejl.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Forberede dig på at oprette forbindelse til Azure FOS efter failover

Hvis du vil oprette forbindelse til Azure FOS af RDP-failover, skal du gøre følgende:

**På den lokale computer før failover**:

- Aktivere RDP for adgang via internettet, sikre, at TCP- og UDP-regler er føjet til den **offentlige**, og sikre, at RDP er tilladt i **Windows Firewall** -> **tilladte programmer og funktioner** for alle profiler.
- Aktivere RDP på computeren for at få adgang via en forbindelse til websted, og sikre, at RDP er tilladt i **Windows Firewall** -> **tilladte programmer og funktioner** for **domæne** og **Private** netværk.
- Installer [Azure VM agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) på den lokale computer.
- [Installer manuelt tjenesten mobilitet](#install-the-mobility-service-manually) på computere i stedet for ved hjælp af processen server til automatisk push-tjenesten. Dette skyldes, at opslagsnål installationen sker kun, når maskinen er aktiveret for gentagelse.
- Sørg for, at det operativsystem SAN politik er indstillet til OnlineAll. [Lær mere]( https://support.microsoft.com/kb/3031135)
- Deaktivere IPSec-tjenesten, før du kører sekundære.

**I feltet Azure VM efter failover**:

- Føje et offentlige slutpunkt for RDP-protokollen (port 3389) og angive legitimationsoplysninger for logon.
- Sikre, at du ikke har en hvilken som helst domæne politikker, der forhindrer dig i at oprette forbindelse til en virtuel maskine, ved hjælp af en offentlig adresse.
- Prøv at oprette forbindelse. Hvis du ikke kan oprette forbindelse kan du kontrollere, hvor VM kører. Læs denne [artikel](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)for flere tip til fejlfinding.


Hvis du vil have adgang til en Azure VM kører Linux efter failover ved hjælp af en Secure Shell-klient (ssh), kan du gøre følgende:

**På den lokale computer før failover**:

- Sørg for, at tjenesten Secure Shell på Azure VM er indstillet til at starte automatisk ved systemstart.
- Kontrollér, at firewallregler har givet en SSH forbindelse til den.

**I feltet Azure VM efter failover**:

- Netværk sikkerhed gruppe regler på mislykkedes over VM, og det Azure undernet, som den er tilsluttet skal tillade indgående forbindelser til SSH porten.
- Der skal oprettes et offentlige slutpunkt for at tillade indgående forbindelser på SSH porten (TCP-port 22 som standard).
- Hvis VM er tilgængelige via en VPN-forbindelse (Express rute eller til websted VPN) kan klienten bruges til at oprette forbindelse direkte til VM over SSH.

**I feltet Azure Windows/Linux VM efter failover**:

Hvis du har en netværk sikkerhedsgruppe, som er knyttet til den virtuelle maskine eller det undernet, som maskinen tilhører, skal du kontrollere, sikkerhedsgruppen netværk har en udgående regel, der tillader HTTP/HTTPS. Også sørge for, at DNS-Posterne på netværket, hvilket virtuelt er få ikke over er konfigureret korrekt. Sekundære kunne ellers timeout med fejlen-"PreFailoverWorkflow opgave WaitForScriptExecutionTask fik timeout". For at forstå dette detaljerede oplysninger om, at referere til afsnittet om gendannelse i [overvågnings- og fejlfindingsvejledningen](site-recovery-monitoring-and-troubleshooting.md#recovery).

## <a name="run-a-test-failover"></a>Køre en test failover

1. Mislykkes over en enkelt maskine, i **Indstillinger for** > **Replikeres elementer**, skal du klikke på VM > **+ Test Failover** ikon.

    ![Test failover](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. Mislykkes over en gendannelse plan i **Indstillinger for** > **Gendannelse plan**, skal du højreklikke på planen > **Test Failover**. Hvis du vil oprette planlægge en genoprettelse [skal du følge disse instruktioner](site-recovery-create-recovery-plans.md).

3. Vælg Azure netværket som Azure FOS forbindes til, når der opstår failover i **Test Failover** .
4. Klik på **OK** for at starte sekundære. Du kan registrere status for opgaver ved at klikke på VM til at åbne dens egenskaber eller på **Test Failover** jobbet i samling navn > **Indstillinger for** > **job** > **job gendannelse af websteder**.
5. Når sekundære når **færdigt test** status, kan du gøre følgende:

    1. Få vist virtuelt replika i portalen Azure. Kontrollér, at den virtuelle maskine startes korrekt.
    2. Du kan starte en Fjernskrivebord forbindelse til den virtuelle maskine, hvis du er sat op til access virtuelle maskiner fra dit lokale netværk.
    3. Klik på **udført test** for at afslutte den.

        ![Test failover](./media/site-recovery-vmware-to-azure/test-failover6.png)


    4. Klik på **noter** for at registrere og gemme de bemærkninger, der er knyttet til sekundære test.
    5. Klik på **sekundære test er fuldført** automatisk rydde op i testmiljøet. Når dette er gjort vise test sekundære **færdigt** status.
    6.  På dette tidspunkt slettes eventuelle elementer eller VM'er, der er oprettet automatisk af gendannelse af websteder under sekundære test. Du har oprettet for test failover ekstra elementer slettes ikke.

    > [AZURE.NOTE] Hvis en test failover fortsætter mere end to uger, den er fuldført af kraft.


6. Når sekundære er fuldført skal du også kunne se replikaen Azure maskine vises i portalen Azure > **virtuelle computere**. Du skal sikre dig, at VM har den ønskede størrelse, som den er tilsluttet til det pågældende netværk, og at den kører.
7. Hvis du [forberedt for forbindelser efter failover](#prepare-to-connect-to-azure-vms-after-failover) du bør kunne oprette forbindelse til Azure VM.

## <a name="monitor-your-deployment"></a>Overvåge installationen

Her er, hvordan du kan overvåge konfigurationsindstillinger, status og tilstand for implementeringen gendannelse af websteder:

1. Klik på navnet på samling af legitimationsoplysninger for at få adgang til dashboardet **Essentials** . I dette dashboard kan du gendannelse af websteder job, gentagelse status, gendannelse planer, servertilstand og begivenheder.  Du kan tilpasse Essentials for at få vist felter og layout, der er bedst egnede til dig, herunder status for andre vaults sikkerhedskopiering og gendannelse af websteder.<br>
![Essentials](./media/site-recovery-vmware-to-azure/essentials.png)

2. I feltet **tilstand** kan du overvåge webstedsservere (VMM eller konfiguration servere), der oplever problemet, og de hændelser, der er anledning gendannelse af websteder i de seneste 24 timer.
3. Du kan administrere og overvåge gentagelse i kolonnen **Replikeres elementer**, **Gendannelse planer**, og **Websted gendannelse job** felter. Du kan analysere ned i sager i **Indstillinger for** -> **job** -> **Websted gendannelse job**.


## <a name="deploy-additional-process-servers"></a>Installere yderligere processer servere

Hvis du har skalere ud af din installation ud over 200 kilde-computere eller en samlet daglige transportspand sats på mere end 2 TB, skal du yderligere processer servere til at håndtere trafik lydstyrken.

Kontrollere [størrelsen anbefalinger for processen servere](#size-recommendations-for-the-process-server) , og derefter følge disse instruktioner til konfiguration af processen serveren. Du skal overføre kilde maskiner for at bruge det efter konfigurering af serveren.

### <a name="install-an-additional-process-server"></a>Installere en server til yderligere processer

1. I **Indstillinger for** > **servere gendannelse af websteder** skal du klikke på server configuration > **Process-server**.

    ![Tilføje process-server](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. Klik på **proces server (lokalt)**i **Servertype** .

    ![Tilføje process-server](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Hente installationsfilen websted gendannelse Unified, og kør den for at installere processerveren og registrere det i samling af legitimationsoplysninger.
4. Vælg **Tilføj flere proces servers skalere ud installation**ind, **før du går i gang** .
5. Fuldføre guiden på samme måde, du har, når du [konfigurere](#step-2-set-up-the-source-environment) server configuration.

    ![Tilføje process-server](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. Angiv IP-adressen på server configuration og adgangskoden, i **Server konfigurationsoplysninger** . Til at hente adgangskoden køre ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** på server configuration.

    ![Tilføje process-server](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Overføre computere for at bruge den nye proces-server

1. I **Indstillinger for** > **gendannelse af websteder servere** på konfigurationsserveren, og udvid derefter **proces-servere**.

    ![Opdatere process-server](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. Højreklik på proces server i øjeblikket bruges, og klik på **Skift**.

    ![Opdatere process-server](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. I **Vælg target proces server**, skal du vælge den nye proces-server, du vil bruge, og vælg derefter de virtuelle maskiner, som den nye proces server håndterer. Klik på oplysningsikonet for at få oplysninger om serveren. For at hjælpe dig med at træffe indlæse beslutninger, vises det gennemsnitlige område, der er nødvendige for at gentage hver valgte virtuelt til den nye proces-server. Klik på markeringen i afkrydsningsfeltet for at starte replikering til den nye proces-server.

## <a name="vmware-account-permissions"></a>VMware kontotilladelser

Processen serveren kan automatisk opdage FOS på en vCenter server. Hvis du vil udføre automatisk registrering skal du tillade gendannelse af websteder til at få adgang til VMware serveren til at [definere en rolle (Azure_Site_Recovery)](#prepare-an-account-for-automatic-discovery) . Bemærk, at hvis du kun har brug at overføre VMware maskiner til Azure og ikke vil failback fra Azure, du kan definere en skrivebeskyttet rolle, der er tilstrækkeligt. Rolletilladelserne, der kræves er opsummeret i følgende tabel.

**Rolle** | **Detaljer** | **Tilladelser**
--- | --- | ---
Azure_Site_Recovery rolle | VMware VM registrering |Tildele disse rettigheder til v-Center-serveren:<br/><br/>DataStore -> fordel afstanden Gennemse datastore lav niveau fil handlinger., fjern fil, og Opdater virtuelt filer<br/><br/>Netværk -> netværk Tildel<br/><br/>Ressource -> Tildel virtuelt ressourcepulje, overføre slukket virtuel maskine, overføre tændt virtuel maskine<br/><br/>Opgaver -> Opret opgave, Opdater opgave<br/><br/>Virtual machine -> konfiguration<br/><br/>Virtual machine -> interakt.Skab -> spørgsmål svar enhed forbindelse. konfigurere CD-medier, og Konfigurer floppy medier, Power fra Power på, skal du installere VMware værktøjer<br/><br/>Virtual machine -> Lager -> Opret, Register, fjernelse af registrering<br/><br/>Virtual machine -> artikel Provisioning -> Tillad virtuelt download, overføre Tillad virtuelt filer<br/><br/>Virtual machine -> Snapshots -> Fjern øjebliksbilleder
vCenter brugerrolle | VMware VM registrering/Failover uden lukning af kilde VM | Tildele disse rettigheder til v-Center-serveren:<br/><br/>Data Center objekt –> Overfør til underordnede objekt rolle = skrivebeskyttet <br/><br/>Brugeren er tildelt på datacenter niveau og dermed har adgang til alle objekter i datacenteret.  Hvis du vil begrænse adgangen, skal du tildele rollen **Ingen adgang** med objektet **Overfør barn** til de underordnede objekter (vSphere hosts, datastores, FOS og netværk).
vCenter brugerrolle | Failover og failback | Tildele disse rettigheder til v-Center-serveren:<br/><br/>Datacenter objekt – Overfør til underordnede objekt rolle = Azure_Site_Recovery<br/><br/>Brugeren er tildelt på datacenter niveau og dermed har adgang til alle objekter i datacenteret.  Hvis du vil begrænse adgangen, skal du tildele rollen **Ingen adgang** med **Overfør til underordnede objekt** til det underordnede objekt (vSphere hosts, datastores, FOS og netværk).  
## <a name="next-steps"></a>Næste trin

- Du kan [Få mere at vide](site-recovery-failover.md) om forskellige typer ydelse.
- [Lær mere om failback](site-recovery-failback-azure-to-vmware.md) til at hente dine mislykkedes over maskiner, der kører i Azure tilbage til dit lokale miljø.

## <a name="third-party-software-notices-and-information"></a>Softwaremeddelelser fra tredjepart og oplysninger

Send ikke oversætte eller lokalisere

Softwaren og de firmware kører i Microsoft-produkt eller tjeneste er baseret på eller inkorporerer materiale fra projekter på listen nedenfor (samlet "fra tredjepart-kode").  Microsoft er den ikke oprindelige forfatter af koden fra tredjepart.  Den oprindelige meddelelse om ophavsret og licens, under hvilket Microsoft modtaget sådanne fra tredjepart kode, er indstillet tilbage nedenfor.

Oplysningerne i sektionen A drejer sig om fra tredjepart kode komponenter i de projekter, der vises nedenfor. Disse licenser og oplysninger, der leveres til orientering.  Denne kode fra tredjepart er der relicensed til dig af Microsoft under Microsoft-softwarelicensvilkårene for Microsoft-produkt eller tjeneste.  

Oplysningerne i afsnit B drejer sig om fra tredjepart kode komponenter, der gøres tilgængelige for dig af Microsoft under oprindelige licensvilkårene.

Hele filen kan findes på [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft forbeholder sig alle rettigheder, der ikke udtrykkeligt er tildelt denne aftale, uanset om det skyldes det vil sige, estoppel eller på anden måde.
