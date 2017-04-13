<properties
    pageTitle="Gentage VMware virtuelle maskiner og fysiske servere til Azure med Azure gendannelse af websteder | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du kan installere Azure gendannelse af websteder for at dirigerer gentagelse, failover og gendannelse af lokale VMware virtuelle computere og Windows/Linux fysiske servere til Azure."
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
    ms.date="09/29/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>Gentage VMware virtuelle maskiner og fysiske servere til Azure med Azure gendannelse af websteder

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-vmware-to-azure.md)
- [Klassisk Portal](site-recovery-vmware-to-azure-classic.md)
- [Klassisk Portal (ældre)](site-recovery-vmware-to-azure-classic-legacy.md)


Gendannelse af websteder Azure service bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR) strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere. Computere kan replikeres til Azure eller til et sekundært lokalt datacenter. En hurtig oversigt over læse [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md).

## <a name="overview"></a>Oversigt

Denne artikel beskrives, hvordan du:

- **Gentage VMware virtuelle maskiner til Azure**– installere gendannelse af websteder til at koordinere gentagelse, failover og gendannelse af lokale VMware virtuelle maskiner til Azure-lager.
- **Gentage fysiske servere til Azure**– installere Azure gendannelse af websteder til at koordinere gentagelse, failover og gendannelse af lokale fysiske Windows og Linux servere til Azure.

>[AZURE.NOTE] I denne artikel beskrives, hvordan du overfører til Azure. Hvis du vil gentage VMware FOS eller Windows/Linux fysiske servere til en sekundær datacenter, skal du følge vejledningen i [denne artikel](site-recovery-vmware-to-vmware.md).

Sende en hvilken som helst kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="enhanced-deployment"></a>Forbedret installation

Denne artikel indeholder indeholder instruktioner til en udvidet installation i klassisk Azure-portalen. Vi anbefaler, at du bruger denne version for alle nye installationer. Hvis du allerede har installeret bruger den tidligere version af ældre anbefaler vi, at du overfører til den nye version. Læs [mere](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment) om overførsel.

Den forbedrede installation er en overordnet opdatering. Her er en oversigt over de forbedringer, har vi gjort:

- **Ingen infrastruktur FOS i Azure**: Data kopieres direkte til en Azure-lager-konto. Desuden for gentagelse og belastningsjustering er der ingen grund til at konfigurere en hvilken som helst infrastruktur FOS (server configuration, mastersider target server), som vi det er nødvendigt i den ældre installation.  
- **Samlede installation**: en enkelt installation giver simpel opsætning og skalerbarhed for lokale komponenter.
- **Sikker implementering**: al trafik er krypteret og gentagelse management meddelelser sendes over HTTPS 443.
- **Gendannelse punkter**: understøttelse af nedbrud og program-ensartet gendannelse peger til Windows og Linux-miljøer, og understøtter begge enkelt VM og multi-VM ensartede konfigurationer.
- **Teste failover**: understøttelse af ikke-forstyrrende test failover til Azure, uden at påvirke fremstilling eller afbrydelsen gentagelse.
- **Ikke-planlagt failover**: understøttelse af ikke-planlagt failover til Azure med en forbedret mulighed for at lukke FOS automatisk før failover.
- **Failback**: integreret failback, kopieres kun delta ændringer tilbage til webstedet, i det lokale miljø.
- **vSphere 6.0**: begrænset understøttelse af VMware Vsphere 6.0 installationer.


## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>Hvordan gendannelse af websteder med at beskytte virtuelle maskiner og fysiske servere?


- VMware administratorer kan konfigurere beskyttelse til Azure af business arbejdsmængder og programmer, der kører på VMware virtuelle computere. Serveradministratorer kan replikeres fysisk lokalt Windows og Linux servere til Azure.
- Gendannelse af websteder Azure-konsollen indeholder enkle konfiguration og administration af replikering, failover og gendannelsesprocesser på ét sted.
- Hvis du gentage VMware virtuelle maskiner, der administreres af en vCenter server, kan gendannelse af websteder finde disse FOS automatisk. Hvis computere er på en ESXi vært opdager FOS på værtsplaceringen for gendannelse af websteder.
- Køre nemt failover fra din lokale infrastruktur til Azure og failback (Gendan) fra Azure på VMware VM servere i det lokale websted.
- Konfigurere gendannelse planer, der grupperer arbejdsbelastninger, som er udviklet på tværs af flere computere. Du kan ikke over disse planer og gendannelse af websteder giver flere VM konsistens, så maskiner, der kører de samme arbejdsmængder kan gendannes sammen til et ensartet datapunkt.


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

Scenarie komponenter:

- **En lokal management server**: management-serveren kører gendannelse af websteder komponenter:
    - **Konfiguration af server**: koordinaterne kommunikation og administrerer data gentagelse og gendannelsesfiler processer.
    - **Processen server**: fungerer som en gentagelse gateway. Den modtager data fra beskyttet kilde maskiner, optimerer med cachelagring, komprimering og kryptering og sender gentagelse data til Azure-lager. Det også håndterer opslagsnål installation af mobilitet tjeneste til beskyttede computere og udfører automatisk registrering af VMware FOS.
    - **Mastersider target server**: håndterer gentagelse data under failback fra Azure.
    Du kan også distribuere en management-server, der fungerer som en proces server kun, hvis du vil skalere installationen.
- **Feltet mobilitet service**: denne komponent er installeret på hver maskine (VMware VM eller fysisk server), du vil gentage til Azure. Det registrerer data skriver på computeren og sender dem til processerveren.
- **Azure**: Du behøver ikke at oprette en hvilken som helst Azure FOS for at håndtere replikering og belastningsjustering. Tjenesten gendannelse af websteder håndterer datastyring og data kopieres direkte til Azure-lager. Replikerede Azure FOS er spundet op automatisk kun, når der opstår failover til Azure. Men hvis du vil mislykkes tilbage fra Azure til det lokale websted skal du konfigurere en Azure VM skal fungere som en process-server.


Grafikken viser, hvordan disse komponenter interagere.

![arkitektur](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figur 1: VMware/fysisk til Azure** (oprettet med Henry Robalino)


## <a name="capacity-planning"></a>Planlægning af kapacitet

Når du planlægger kapacitet, hers hvad du bør overveje:

- **Kilde-miljø**– kapacitetsplanlægning eller VMware infrastruktur og kilde maskine krav.
- **Indholdsstyringsserver**– planlægning af de lokale management-servere, der kører gendannelse af websteder komponenter.
- **Netværksbåndbredde fra kilde til destination**-planlægning til netværksbåndbredde, der kræves til replikering mellem kilde- og Azure

### <a name="source-environment-considerations"></a>Overvejelser i forbindelse med kilde-miljø

- **Maksimal dagligt ændring rente**– en beskyttet maskine kan kun bruge én proces server, og en enkelt proces server kan håndtere op til 2 TB Dataændring dagen. Dermed er 2 TB de maksimale daglige data ændres rente, der understøttes til en beskyttet maskine.
- **Maksimum overførselshastighed**– en replikerede maskine kan tilhøre én lagerplads konto i Azure. En almindelig lagerplads konto kan håndtere op til 20.000 anmodninger sekundet, og vi anbefaler, at du beholder antallet af IOP'ER på tværs af en kilde maskine med 20.000. For eksempel hvis du har en kilde maskine med 5 diske og hver disk genererer 120 IOP'ER (8K størrelse) på kilden vil det være i Azure per disk IOP'ER grænsen på 500. Antallet af lagerplads konti påkrævet = samlet kilde IOP'er/20000.


### <a name="management-server-considerations"></a>Overvejelser i forbindelse med Management server

Management-serveren kører gendannelse af websteder komponenter, der håndterer data optimering, gentagelse og administration. Det bør kunne håndtere daglige Skift rente kapaciteten på tværs af alle arbejdsbelastninger, som kører på beskyttet maskiner, og har tilstrækkelig båndbredde til løbende gentage data til Azure-lager. Mere specifikt:

- Processen serveren modtager gentagelse data fra beskyttet computere og optimerer med cachelagring, komprimering og kryptering inden du sender til Azure. Management server har tilstrækkelige ressourcer til at udføre disse opgaver.
- Processen serveren bruger diskbaserede cache. Vi anbefaler, at en separat cache disk af 600 GB eller mere til at håndtere dataændringer, der er gemt i tilfælde af netværk flaskehals eller -afbrydelse. Under installationen kan du konfigurere cachen på et drev, der har mindst 5 GB ledig plads, men 600 GB er den mindste anbefaling.
- Vi anbefaler, at indholdsstyringsserver findes på samme netværks- og LAN-segment som de computere, du vil beskytte som en bedste fremgangsmåde. Det kan være placeret på et andet netværk, men computere, du vil beskytte bør have L3 netværkets synlighed til den.

Størrelse anbefalinger til management server er opsummeret i følgende tabel.

**Indholdsstyringsserver CPU** | **Hukommelse** | **Cache diskstørrelse** | **Dataene ændres rente** | **Beskyttet computere**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 kerner @ 2,5 GHz) | 16 GB | 300 GB | 500 GB eller mindre | Installere en indholdsstyringsserver med disse indstillinger for at gentage mindre end 100 computere.
12 vCPUs (2 sockets * 6 kerner @ 2,5 GHz) | 18 GB | 600 GB | 500 GB til 1 TB | Installere en indholdsstyringsserver med disse indstillinger for at gentage mellem 100-150 maskiner.
16 vCPUs (2 sockets * 8 kerner @ 2,5 GHz) | 32 GB | 1 TB | 1 TB til 2 TB | Installere en indholdsstyringsserver med disse indstillinger for at gentage mellem 150-200 maskiner.
Installere en anden proces-server | | | > 2 TB | Installere yderligere processer servere, hvis du replikering mere end 200 maskiner, eller hvis daglig dataene ændre hastigheden overskrider 2 TB.

Hvor:

- Hver kildecomputeren er konfigureret med 3 diske på 100 GB.
- Vi bruges benchmarking lagringen af 8 SAS-drev på 10 K RPM med RAID 10 til cache disk mål.

### <a name="network-bandwidth-from-source-to-target"></a>Netværksbåndbredde fra kilde til destination
Sørg for, at du beregner den båndbredde, der er nødvendige for indledende replikering og delta replikering ved hjælp af [kapacitet planner-værktøj](site-recovery-capacity-planner.md)

#### <a name="throttling-bandwidth-used-for-replication"></a>Begrænsning af båndbredde, der bruges til gentagelse

VMware trafik replikeres til Azure går gennem en server, bestemt proces. Du kan begrænse den båndbredde, der er tilgængelige for gendannelse af websteder gentagelse på den pågældende server på følgende måde:

1. Åbn Microsoft Azure sikkerhedskopi MMC snap-in på den primære indholdsstyringsserver eller på en management server, der kører yderligere klargjort proces-servere. Som standard en genvej til Microsoft Azure Backup er oprettet på skrivebordet, eller du kan finde det i: C:\Program Files\Microsoft Azure gendannelse Services Agent\bin\wabadmin.
2. Klik på **Skift egenskaber**i snap-in.

    ![Begrænse båndbredde](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. Angiv den båndbredde, der kan bruges til de relevante planlægning og gendannelse af websteder gentagelse under fanen **Throttling** .

    ![Begrænse båndbredde](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

Du kan eventuelt også angive (throttling) ved hjælp af PowerShell. Her er et eksempel:

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>Maksimere brugen af båndbredde
Du har brug at ændre en registreringsdatabasenøgle for at øge den båndbredde, der anvendes til replikering af Azure gendannelse af websteder.

Følgende nøgle styrer antallet af tråde per replikering disk, der bruges ved replikering

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 I en "overprovisioned" netværk skal denne registreringsdatabasenøgle ændres fra det er standardværdier. Vi understøtter maksimalt 32.  


Du kan [Få mere at vide](site-recovery-capacity-planner.md) om kapacitetsplanlægning af detaljerede.

### <a name="additional-process-servers"></a>Yderligere processer servere

Hvis du har brug at beskytte mere end 200 computere eller daglig Skift rente er større, 2 TB kan du tilføje yderligere servere til at håndtere afkrydsningsfeltet Indlæs. Hvis du vil skalere ud af, du kan:

- Øge antallet af management-servere. Du kan for eksempel beskytte op til 400 maskiner med to management-servere.
- Tilføje yderligere processer servere og bruge disse til at håndtere trafik i stedet for (eller ud over) management-serveren.

I denne tabel beskriver et scenarie, hvor:

- Du kan konfigurere den oprindelige management server til at bruge den som en konfiguration server.
- Du kan konfigurere en server til yderligere processer.
- Du konfigurerer beskyttet virtuelle maskiner, hvis du vil bruge flere processerveren.
- Hver kildecomputer beskyttet er konfigureret med tre diske på 100 GB.

**Oprindelige indholdsstyringsserver**<br/><br/>(konfiguration server) | **Yderligere processer server**| **Cache diskstørrelse** | **Dataene ændres rente** | **Beskyttet computere**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 kerner @ 2,5 GHz), 16 GB hukommelse | 4 vCPUs (2 sockets * 2 kerner @ 2,5 GHz), 8 GB hukommelse | 300 GB | 250 GB eller mindre | Du kan gentage 85 eller mindre computere.
8 vCPUs (2 sockets * 4 kerner @ 2,5 GHz), 16 GB hukommelse | 8 vCPUs (2 sockets * 4 kerner @ 2,5 GHz), 12 GB hukommelse | 600 GB | 250 GB til 1 TB | Du kan gentage mellem 85-150 maskiner.
12 vCPUs (2 sockets * 6 kerner @ 2,5 GHz), 18 GB hukommelse | 12 vCPUs (2 sockets * 6 kerner @ 2,5 GHz) 24 GB hukommelse | 1 TB | 1 TB til 2 TB | Du kan gentage mellem 150-225 maskiner.


Den måde, hvori du skalere dine servere, der afhænger af din præference for en skala op eller model kan skalere.  Du skalere op ved at installere nogle avancerede administration og proces servere eller skalere ud ved at installere flere servere med mindre ressourcer. For eksempel: Hvis du har brug at beskytte 220 computere kan du gøre et af følgende:

- Konfigurere den oprindelige management server med 12vCPU, 18 GB hukommelse, en yderligere processer server med 12vCPU, 24 GB hukommelse, og konfigurere beskyttet maskiner for at bruge ekstra proces server.
- Eller du kan også konfigurere to management (2 x 8vCPU, 16 GB RAM) og to yderligere processer servere (1 x 8vCPU) og 4vCPU x 1 til at håndtere 135 + 85 (220) computere og konfigurere beskyttet maskiner for at bruge de yderligere processer servere.


[Følg disse instruktioner](#deploy-additional-process-servers) til konfiguration af en server til yderligere processer.

## <a name="before-you-start-deployment"></a>Før du starter installation

Tabellerne Opsummer forudsætninger for installation af dette scenario.


### <a name="azure-prerequisites"></a>Azure forudsætninger

**Nødvendige program** | **Detaljer**
--- | ---
**Azure-konto**| Du skal bruge en [Microsoft Azure](https://azure.microsoft.com/) -konto. Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). Du kan [Få mere at vide](https://azure.microsoft.com/pricing/details/site-recovery/) om gendannelse af websteder priser.
**Azure-lager** | Du skal bruge en Azure-lager-konto til at gemme replikerede data. Replikerede data er gemt i Azure-lager og Azure FOS er spundet op, når der opstår failover. <br/><br/>Du har brug for en [standard geografisk overflødige lagerplads konto](../storage/storage-redundancy.md#geo-redundant-storage). Kontoen skal være i samme område som tjenesten gendannelse af websteder, og at blive knyttet til den samme abonnement. Bemærk, at replikering til premium lagerplads konti understøttes ikke i øjeblikket og ikke bør bruges.<br/><br/>Vi understøtter ikke flytning af lagerplads konti, der er oprettet ved hjælp af [nye Azure portal](../storage/storage-create-storage-account.md) på tværs af grupper. [Læs om](../storage/storage-introduction.md) Azure-lager.<br/><br/>
**Azure-netværk** | Du skal have et Azure virtuelle netværk, der Azure FOS opretter forbindelse til, når der opstår failover. Azure virtuelt netværk skal være i samme område som samling af legitimationsoplysninger gendannelse af websteder.<br/><br/>Bemærk, at mislykkes tilbage efter failover til Azure skal du en VPN-forbindelse (eller Azure ExpressRoute) konfigurere fra Azure netværket til webstedet, i det lokale miljø.


### <a name="on-premises-prerequisites"></a>Forudsætninger for lokalt

**Nødvendige program** | **Detaljer**
--- | ---
**Indholdsstyringsserver** | Du har brug for en lokal Windows 2012 R2 server, der kører på en virtuel computer eller fysisk server. Alle de lokale gendannelse af websteder komponenter er installeret på denne indholdsstyringsserver<br/><br/> Vi anbefaler, at du installerer serveren som en meget tilgængelige VMware VM. Failback til det lokale websted fra Azure er altid være til VMware FOS uanset om du mislykkedes over FOS eller fysiske servere. Hvis du ikke konfigurerer Management server som en VMware VM skal du konfigurere en separat master target server som en VMware VM til at modtage failback trafik.<br/><br/>Serveren må ikke være et domænenavn fra domænecontrolleren.<br/><br/>Serveren, der skal have en statisk IP-adresse.<br/><br/>Værtsnavnet på serveren, der skal være på 15 tegn eller mindre.<br/><br/>Landestandard af operativsystemet skal være engelsk kun.<br/><br/>Management-serveren kræver forbindelse til internettet.<br/><br/>Du skal have udgående adgang fra serveren på følgende måde: midlertidig adgang på http-80 under installationen af komponenterne til gendannelse af websteder (for at hente MySQL); Igangværende udgående adgang via HTTPS 443 for håndtering af replikering; Igangværende udgående adgang via HTTPS 9443 til gentagelse trafik (denne port kan ændres)<br/><br/> Sørg for, at disse URL-adresser er tilgængelige fra management server: <br/>- \*. hypervrecoverymanager.windowsazure.com<br/>- \*. accesscontrol.windows.net<br/>- \*. backup.windowsazure.com<br/>- \*. blob.core.windows.net<br/>- \*. store.core.windows.net<br/>-https://www.msftncsi.com/ncsi.txt<br/>- [https://dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi]( https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Hvis du har IP-adresse-baserede firewallregler på serveren, kan du kontrollere, at reglerne, der tillader kommunikation til Azure. Du skal til at tillade [Azure Datacenter IP-områder](https://www.microsoft.com/download/details.aspx?id=41653) og HTTPS (443)-port. Du skal også hvid liste IP-adresseområder for det Azure område for dit abonnement og for Vest USA. URL-adressen [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") er til at hente MySQL.
**VMware vCenter/ESXi host**: | Du har brug for en eller flere vMware vSphere ESX/ESXi hypervisors administrere VMware virtuelle maskiner, kører ESX/ESXi version 6.0, 5.5 eller 5.1 med de seneste opdateringer.<br/><br/> Vi anbefaler, at du installerer en VMware vCenter server til at administrere din ESXi hosts. Det skal køre vCenter version 6.0 eller 5.5 med de seneste opdateringer.<br/><br/>Bemærk, at gendannelse af websteder ikke understøtter nye vCenter og vSphere 6.0 funktioner såsom cross vCenter vMotion, virtuelle enheder og lager DRS. Support for gendannelse af websteder er begrænset til funktioner, der er også tilgængelig i version 5.5.
**Beskyttet maskiner**: | **AZURE**<br/><br/>Computere, du vil beskytte skal overholde med [Azure forudsætninger](site-recovery-best-practices.md#azure-virtual-machine-requirements) for oprettelse af Azure FOS.<br><br/>Hvis du vil oprette forbindelse til Azure FOS efter failover derefter skal du aktivere Fjernskrivebord forbindelser på lokale firewallen.<br/><br/>Individuelle diskkapacitet på beskyttet maskiner ikke bør være mere end 1023 GB. En VM kan have op til 64 diske (dermed op til 64 TB). Hvis du har overveje diske, der er større end 1 TB ved hjælp af databasereplikering såsom SQL Server altid på eller Oracle Data vagt.<br/><br/>Minimum 2 GB ledig plads på installationsdrev til Komponentinstallationen.<br/><br/>Delt disk gæst klynger ikke understøttes. Hvis du har en grupperet Overvej at bruge databasereplikering såsom SQL Server altid på eller Oracle Data vagt-installation.<br/><br/>Unified Extensible Firmware Interface UEFI () / Extensible Firmware Interface(EFI) start understøttes ikke.<br/><br/>Computernavne på skal indeholde mellem 1 og 63 tegn (bogstaver, tal og bindestreger). Navnet skal starte med et bogstav eller tal og slutter med et bogstav eller et tal. Du kan ændre navnet på Azure, når en maskine er beskyttet.<br/><br/>**VMware FOS**<br/><br>Du skal installere VMware vSphere PowerCLI 6.0. på indholdsstyringsserver (konfiguration server).<br/><br/>VMware FOS, du vil beskytte bør have VMware værktøjer installeret og kører.<br/><br/>Hvis kilden VM har NIC samarbejde konverteres til en enkelt NIC efter failover til Azure.<br/><br/>Hvis beskyttet FOS har en iSCSI-disk derefter konverterer gendannelse af websteder beskyttet VM iSCSI-disk til en Virtuelle fil når VM skifter til Azure. Hvis iSCSI-mål kan nås fra Azure VM derefter den oprette forbindelse til iSCSI-mål og grundlæggende se to diske – den Virtuelle disk på Azure VM og kilde iSCSI-disk. I dette tilfælde skal du afbryde forbindelsen til iSCSI-destinationen, der vises på den mislykkedes over Azure VM.<br/><br/>Du kan [Få mere at vide](#vmware-permissions-for-vcenter-access) om de VMware brugertilladelser, der er nødvendige for gendannelse af websteder.<br/><br/> **WINDOWS SERVER-computere (på VMware VM eller fysisk server)**<br/><br/>Serveren, der skal køre et understøttet 64-bit-operativsystem: Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2 med på minimum SP1.<br/><br/>Operativsystemet skal være installeret på C:\ drev og OS disken skal være en Windows grundlæggende disk (OS ikke bør være installeret på en Windows dynamisk disk).<br/><br/>For Windows Server 2008 R2 maskiner skal du have .NET Framework 3.5.1 til installeret.<br/><br/>Du skal angive en administratorkonto (skal være en lokal administrator på Windows-computer) for push installationen tjenesten mobilitet på Windows-servere. Hvis den angivne konto er en ikke-domænekonto skal du deaktivere Remote brugeradgang kontrolelement på den lokale computer. [Få mere at vide](#install-the-mobility-service-with-push-installation).<br/><br/>Gendannelse af websteder understøtter FOS RDM disk med.  Gendannelse af websteder genbruger under failback RDM disken, hvis den oprindelige kilde VM og RDM disk er tilgængelig. Hvis de ikke er tilgængelig, under failback opretter gendannelse af websteder en ny VMDK-fil for hver disk.<br/><br/>**LINUX-COMPUTERE**<br/><br/>Du skal bruge et understøttede 64-bit operativsystem: rød Hat Enterprise Linux 6,7; Centos 6.5, 6.6,6.7; Oracle Enterprise Linux 6.4, 6.5 kører Red Hat kompatible kernen eller Unbreakable Enterprise kernen version 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>Fjernsystemet filer på beskyttet maskiner skal indeholde poster, der er tilknyttet IP-adresser, der er knyttet til alle netværkskort lokale værtsnavnet. <br/><br/>Hvis du vil oprette forbindelse til en Azure virtuelt kører Linux efter failover ved hjælp af en Secure Shell-klient (ssh), skal du sikre dig, Secure Shell-tjenesten på den beskyttede computer er indstillet til at starte automatisk på systemstart, og at firewallregler tillader en ssh forbindelse til den.<br/><br/>Beskyttelse kan kun aktiveres for Linux-computere med følgende opbevaring: filsystem (EXT3, ETX4, ReiserFS, XFS) MultiPath software-enhed Mapper (multipath)). Lydstyrken manager: (LVM2). Fysiske servere med HP CCISS controller lagerplads understøttes ikke. ReiserFS filsystemet understøttes kun på SUSE Linux Enterprise Server 11 SP3.<br/><br/>Gendannelse af websteder understøtter FOS RDM disk med.  Gendannelse af websteder genbruge ikke RDM disken under failback for Linux. I stedet for opretter det en ny VMDK-fil for hver tilsvarende RDM disk.

Sørg for, at du angiver indstillingen disk.enableUUID=true i konfiguration af parametre VM i VMware kun for Linux VM. Hvis denne række ikke findes, kan du tilføje den. Dette er påkrævet for at give et ensartet UUID VMDK, så det tilslutter korrekt. Bemærk også, uden at denne indstilling, failback medfører en fuldstændig overførsel, selvom VM er tilgængelige på prem. Tilføje denne indstilling sikrer, at kun delta ændringer overføres tilbage under failback.

## <a name="step-1-create-a-vault"></a>Trin 1: Oprette en samling af legitimationsoplysninger

1. Log på [administrationsportalen til](https://manage.windowsazure.com/).
2. Udvid **Datatjenester** > **Gendannelsestjenester** , og klik på **Websted gendannelse samling**.
3. Klik på **Opret et nyt** > **Hurtig oprettelse**.
4. Angiv et fuldt navn til at identificere samling af legitimationsoplysninger i **navnet**.
5. Vælg det geografiske område for samling af legitimationsoplysninger i **område**. Understøttede områder se for at kontrollere geografiske tilgængelighed i [Azure websted gendannelse priser detaljer](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Klik på **Opret samling**.
    ![Ny samling](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Kontrollere vises på statuslinjen for at bekræfte, at samling af legitimationsoplysninger blev oprettet. Samling af legitimationsoplysninger, vises som **aktiv** på hovedsiden **Gendannelsestjenester** .

## <a name="step-2-set-up-an-azure-network"></a>Trin 2: Konfigurer et netværk med Azure

Konfigurere et netværk med Azure, så Azure FOS der skal knyttes til et netværk efter failover, og så failback til det lokale websted kan fungerer som forventet.

1. På portalen Azure > **Opret virtuelt netværk** angive navnet på netværk. IP-adresse område og undernet navn.
2. Du har brug at føje VPN/ExpressRoute til netværket, hvis du skal gøre failback. VPN/ExpressRoute kan føjes til netværket, selv efter failover.

[Læs mere](../virtual-network/virtual-networks-overview.md) om Azure netværk.

> [AZURE.NOTE] [Overførsel af netværk](../resource-group-move-resources.md) på tværs af grupper i det samme abonnement eller på tværs af abonnementer understøttes ikke af netværk, der bruges til implementering af gendannelse af websteder.

## <a name="step-3-install-the-vmware-components"></a>Trin 3: Installere VMware komponenter

Hvis du vil gentage VMware virtuelle installere maskiner følgende VMware komponenter på management-serveren:

1. [Hent](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) og Installer VMware vSphere PowerCLI 6.0.
2. Genstart serveren.


## <a name="step-4-download-a-vault-registration-key"></a>Trin 4: Hente en samling af legitimationsoplysninger registreringsnøgle

1. Ud over administrationen åbne server konsollen gendannelse af websteder i Azure. Klik på samling af legitimationsoplysninger for at åbne siden Hurtig Start på siden **Gendannelsestjenester** . Også du kan åbne Hurtig Start når som helst ved hjælp af ikonet.

    ![Ikon for Hurtig Start](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. Klik på **Forberede Target ressourcer**på siden **Hurtig Start**  > **hente en registreringsnøgle**. Registreringsdatabasefil genereres automatisk. Det er gyldige for 5 dage, efter den er oprettet.


## <a name="step-5-install-the-management-server"></a>Trin 5: Installere management server
> [AZURE.TIP] Sørg for, at disse URL-adresser er tilgængelige fra management server:
>
- *. hypervrecoverymanager.windowsazure.com
- *. accesscontrol.windows.net
- *. backup.windowsazure.com
- *. blob.core.windows.net
- *. store.core.windows.net
- https://dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi
- https://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. Download samlet installationsfilen til serveren på siden **Hurtig Start** .

2. Kør installationsfilen for at starte installationen i konfigurationsguiden websted gendannelse Unified.

3.  Vælg **Installer server configuration and proces server**ind, **før du går i gang** .

    ![Før du starter](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. Klik på **jeg accepterer** for at hente og installere MySQL **Fra tredjepart softwarelicensen** . 

    ![Tredje = part software](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)

5. Gennemse, og vælg tasten registrering, du har hentet fra samling af legitimationsoplysninger i **registrering** .

    ![Registrering](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

6. Angiv, hvordan den udbyder, der kører på konfigurationsserveren opretter forbindelse til Azure gendannelse af websteder via internettet i **Internetindstillinger** .

    - Hvis du vil oprette forbindelse til proxyen, der er i øjeblikket er konfigureret på computeren kan du vælge **Opret forbindelse til eksisterende proxyindstillinger**.
    - Vælg **Opret forbindelse direkte uden en proxy**, hvis du vil hos udbyderen for at oprette forbindelse direkte.
    - Hvis den eksisterende proxy kræver godkendelse, eller du vil bruge en brugerdefineret proxy for forbindelsen udbyder, kan du vælge **Opret forbindelse til brugerdefineret proxyindstillinger**.
        - Hvis du bruger en brugerdefineret proxy, skal du angive den adresse, port og legitimationsoplysninger
        - Hvis du bruger en proxy, du har allerede må følgende URL-adresser:
            - *. hypervrecoverymanager.windowsazure.com;    
            - *. accesscontrol.windows.net; 
            - *. backup.windowsazure.com; 
            - *. blob.core.windows.net; 
            - *. store.core.windows.net
            

    ![Firewall](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

7. **Kontrollere forudsætninger, der** kører konfiguration Tjek at sikre dig, at installationen kan køre. 

    
    ![Forudsætninger](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     Bekræft, at klokkeslættet på systemuret (indstillinger for**dato og klokkeslæt** ) er den samme som tidszonen, hvis der vises en advarsel om **synkronisering af globale klokkeslæt kontrollere** .

    ![TimeSyncIssue](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. Oprette legitimationsoplysninger for at logge på den MySQL server-forekomst, der skal installeres i **MySQL konfiguration** .

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

9. I Vælg **Miljø detaljer** om du vil gentage VMware FOS. Hvis du er, derefter kontrolleres installationen det, at PowerCLI 6.0 er installeret.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

10. I **Installationsplacering** Vælg, hvor du vil installere de binære filer og gemme cachen. Du kan vælge et drev, der har mindst 5 GB lagerplads, men vi anbefaler et cache drev med mindst 600 GB ledig diskplads.

    ![Installere placering](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

11. Angiv lytteren (netværkskort og SSL-port) som server configuration sender og modtager gentagelse data i **Valg af netværk** . Du kan ændre standard port (9443). Ud over denne port bruges port 443 af en webserver, orchestrates gentagelse handlinger. 443 ikke bør bruges til at modtage replikeringstrafik.


    ![Valg af netværk](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



12.  Gennemse oplysningerne i **Oversigt** , og klik på **Installer**. Når installationen afsluttes genereres en adgangskode. Du skal bruge den, når du aktiverer gentagelse så kopiere det og beholde den i et sikkert sted.

    ![Oversigt](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)



13.  Gennemse oplysningerne i **Oversigt** .

    ![Oversigt](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)

>[AZURE.WARNING]Microsoft Azure Service Betroets proxy skal være konfiguration.
>Start et program med navnet "Microsoft Azure gendannelse Services Shell" fra menuen Start i Windows, når installationen er fuldført. Kør følgende sæt af kommandoer til at konfigurere indstillingerne for proxyserveren i kommandovinduet, der åbnes.
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine



### <a name="run-setup-from-the-command-line"></a>Kør installationsprogrammet fra kommandolinjen

Du kan også køre guiden samlet fra kommandolinjen, på følgende måde:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Hvor:

- / ServerMode: obligatorisk. Angiver, om installationen skal installere konfiguration og proces servere eller proces server (bruges til at installere yderligere processer servere). Inputværdier: CS, PS.
- InstallDrive: obligatorisk. Angiver den mappe, hvor komponenterne er installeret.
- / MySQLCredFilePath. Obligatorisk. Angiver stien til en fil, hvor MySQL server-legitimationsoplysninger er historie. Få skabelonen til at oprette filen.
- / VaultCredFilePath. Obligatorisk. Placeringen af filen samling legitimationsoplysninger
- / EnvType. Obligatorisk. Installationstype. Værdier: VMware, NonVMware
- / PSIP og /CSIP. Obligatorisk. IP-adressen på den proces server og server configuration.
- / PassphraseFilePath. Obligatorisk. Placeringen af filen adgangskoden.
- / ByPassProxy. Valgfrit. Angiver indholdsstyringsserver opretter forbindelse til Azure uden en proxy.
- / ProxySettingsFilePath. Valgfrit. Angiver indstillingerne for en brugerdefineret proxy enten (standardproxy på serveren, der kræver godkendelse) eller brugerdefineret proxy




## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>Trin 6: Konfigurere legitimationsoplysninger for vCenter-server

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

Processen serveren kan automatisk opdage VMware VM'er, der administreres af en vCenter server. Skal have en konto og legitimationsoplysninger, som kan få adgang til vCenter server til automatisk registrering gendannelse af websteder. Dette er ikke relevant, hvis du replikering fysiske servere.

Gøre dette på følgende måde:

1. På vCenter server, skal du oprette en rolle (**Azure_Site_Recovery**) på niveauet for vCenter med de [nødvendige tilladelser](#vmware-permissions-for-vcenter-access).
2. Tildele rollen **Azure_Site_Recovery** til en vCenter bruger.

    >[AZURE.NOTE] En vCenter brugerkonto, der har rollen skrivebeskyttet tilstand kan køre failover uden at lukke beskyttet kilde-computere. Hvis du vil lukke disse computere skal du rollen Azure_Site_Recovery. Bemærk, at hvis du kun overføre FOS fra VMware til Azure og behøver ikke at failback derefter rollen skrivebeskyttet tilstrækkelige.

3. Hvis du vil tilføje kontoen, skal du åbne **cspsconfigtool**. Det er tilgængeligt som en genvej på skrivebordet og placeret i mappen [INSTALLATIONSPLACERING] \home\svsystems\bin.
2. fanen **Administrer konti** , skal du klikke på **Tilføj konto**.

    ![Tilføj konto](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. Tilføj legitimationsoplysninger, som kan bruges til at få adgang til vCenter serveren i **Account Details** . Bemærk, at det kan tage mere end 15 minutter, før kontonavnet, der skal vises på portalen. For at opdatere med det samme, skal du klikke på Opdater under fanen **Konfiguration servere** .

    ![Detaljer](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>Trin 7: Føje vCenter servere og ESXi hosts

Hvis du replikering VMware FOS skal du tilføje en vCenter server (eller ESXi host).

1. På **servere** > **Konfiguration servere** fanen skal du vælge server configuration > **Tilføj vCenter server**.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. Tilføj vCenter serveren eller ESXi host detaljer, navnet på den konto, du har angivet for at få adgang til vCenter server i det forrige trin, og proces-server, der skal bruges til at finde VMware VM'er, der administreres af vCenter-serveren. Bemærk, at vCenter serveren eller ESXi host skal være placeret i det samme netværk som den server, som er installeret på processerveren.

    >[AZURE.NOTE] Hvis du føjer vCenter serveren eller ESXi host med en konto, der ikke har administratorrettigheder på vCenter eller host-serveren, derefter Kontroller, at vCenter eller ESXi konti har disse rettigheder, der er aktiveret: Datacenter, Datastore, mappe, Jost, netværk, ressource, virtuel machine, vSphere fordelt Skift. Desuden skal vCenter serveren lagerplads visninger rettigheder.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. Når registrering er fuldført vCenter serveren, vises i fanen **Konfiguration servere** .

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)


## <a name="step-8-create-a-protection-group"></a>Trin 8: Oprette en beskyttelse gruppe

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


Beskyttelse grupper er logiske grupperinger af virtuelle maskiner eller de fysiske servere, du vil beskytte ved hjælp af de samme beskyttelsesindstillinger for. Du anvende indstillingerne for dokumentbeskyttelse til en gruppe med beskyttelse, og disse indstillinger anvendes på alle virtuelle maskiner/fysiske maskiner, du føjer til gruppen.

1. Åbne **Beskyttet elementer** > **Gruppen beskyttelse** og klikke på for at tilføje en beskyttelse gruppe.

    ![Oprette gruppen beskyttelse](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. Angiv et navn til gruppen, og vælg den konfiguration server, som du vil oprette en gruppe i **fra** på siden **Angiv indstillinger for beskyttelse** . **Målet** er Azure.

    ![Indstillinger for beskyttelse](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. Konfigurere indstillingerne for gentagelse, der skal bruges til alle maskiner i gruppen på siden **Angiv indstillinger for gentagelse** .

    ![Beskyttelse gruppen gentagelse](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

    - **Flere VM konsistens**: Hvis du deaktiverer denne programmet opretter delte program-ensartet gendannelse punkter på tværs af computere i gruppen beskyttelse. Denne indstilling er mest relevante, når alle maskiner i gruppen beskyttelse kører samme arbejdsbelastningen. Alle de computere, vil blive gendannet i det samme datapunkt. Dette er tilgængeligt, om du replikering VMware FOS eller Windows/Linux fysiske servere.
    - **Grænseværdi for frigivne Produktionsordre**: Angiver den frigivne Produktionsordre. Beskeder genereres, når sammenhængende databeskyttelse replikering overstiger den konfigurerede frigivne Produktionsordre tærskelværdi.
    - **Gendannelse Peg opbevaring**: Angiver vinduet opbevaring. Beskyttet computere kan gendannes til en hvilken som helst tidspunkt i dette vindue.
    - **Program-ensartet øjebliksbillede frekvens**: Angiver, hvor ofte gendannelse punkter, der indeholder programmet ensartet snapshots oprettes.

Når du klikker på markeringen oprettes en beskyttelse gruppe med det angivne navn. In addition der oprettes en anden beskyttelse gruppe med navnet < beskyttelse-gruppe-navn-Failback). Gruppen denne beskyttelse bruges, hvis du ikke tilbage til webstedet for lokale efter failover til Azure. Du kan overvåge grupperne beskyttelse, som de er oprettet på siden **Beskyttede elementer** .

## <a name="step-9-install-the-mobility-service"></a>Trin 9: Installere tjenesten mobilitet

Det første trin i aktivere beskyttelse til virtuelle computere og fysiske servere er at installere tjenesten mobilitet. Du kan gøre dette på to måder:

- Push- og installere tjenesten på hver maskine fra serveren processen automatisk. Bemærk, at når du føjer computere til en gruppe af beskyttelse og de allerede er kører en passende version af mobilitet service push-installationen ikke opstå.
- Automatisk installere tjenesten ved hjælp af din virksomhed push-metoden som WSUS eller System Center Configuration Manager. Sørg for, at du har konfigureret management-serveren, før du gør dette.
- Installer manuelt på hver maskine, du vil beskytte. Lav sikker på, at du har konfigureret indholdsstyringsserver før du gør dette.


### <a name="install-the-mobility-service-with-push-installation"></a>Installere tjenesten mobilitet med push-installation

Når du føjer computere til en gruppe af beskyttelse rykkes og installeret på hver maskine af processen serveren tjenesten mobilitet automatisk.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Forberede automatisk opslagsnål på Windows-computere

Her er, hvordan du forbereder Windows-computere, så tjenesten mobilitet kan installeres automatisk af processen serveren.

1.  Oprette en konto, der kan bruges af processen serveren til at få adgang til computeren. Kontoen skal have administratorrettigheder (lokale eller domæne). Bemærk, at disse legitimationsoplysninger anvendes kun for push installation af tjenesten mobilitet.

    >[AZURE.NOTE] Hvis du ikke bruger en domænekonto, skal du deaktivere Remote brugeradgang kontrolelement på den lokale computer. Tilføje DWORD-posten LocalAccountTokenFilterPolicy med værdien 1 i journalen under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System for at gøre dette, under. Kommandoen Åbn i post fra en CLI tilføje registreringsdatabasen eller ved hjælp af PowerShell Angiv **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  I Windows Firewall på computeren, du vil beskytte skal du vælge **Tillad en app eller en funktion passerer gennem Firewall** og aktivere **fil- og printerdeling** og **Windows Management Instrumentation**. Du kan konfigurere firewallpolitikken med et Gruppepolitikobjekt for maskiner, der hører til et domæne.

    ![Firewall-indstillinger](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. Tilføje den konto, du har oprettet:

    - Åbn **cspsconfigtool**. Det er tilgængeligt som en genvej på skrivebordet og placeret i mappen [INSTALLATIONSPLACERING] \home\svsystems\bin.
    - Fanen **Administrer konti** , skal du klikke på **Tilføj konto**.
    - Tilføje den konto, du har oprettet. Når du har tilføjet kontoen skal du angive legitimationsoplysningerne, når du føjer en computer til en beskyttelse gruppe.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Forberede automatisk opslagsnål på Linux-servere

1.  Sørg for, at den Linux maskine, du vil beskytte er understøttet, som beskrevet i [forudsætninger, der er i det lokale miljø](#on-premises-prerequisites). Sikre, at der er netværksforbindelse mellem den maskine, du vil beskytte, og indholdsstyringsserver til at køre processen serveren.

2.  Oprette en konto, der kan bruges af processen serveren til at få adgang til computeren. Kontoen skal være en rodbruger på Linux kildeserveren. Bemærk, at disse legitimationsoplysninger anvendes kun for push installation af tjenesten mobilitet.

    - Åbn **cspsconfigtool**. Det er tilgængeligt som en genvej på skrivebordet og placeret i mappen [INSTALLATIONSPLACERING] \home\svsystems\bin.
    - Fanen **Administrer konti** , skal du klikke på **Tilføj konto**.
    - Tilføje den konto, du har oprettet. Når du har tilføjet kontoen skal du angive legitimationsoplysningerne, når du føjer en computer til en beskyttelse gruppe.

3.  Kontrollér, at filen fjernsystemet på Linux kildeserveren indeholder posterne, der er tilknyttet IP-adresser, der er knyttet til alle netværkskort lokale hostname.
4.  Installere den nyeste openssh, openssh-server, openssl pakker på den computer, du vil beskytte.
5.  Sørg for SSH er aktiveret og kører på port 22.
6.  Aktivere SFTP undersystem og din adgangskode godkendelse i filen sshd_config på følgende måde:

    - Log på som rod.
    - Find den linje, der begynder med PasswordAuthentication i filen fil /etc/ssh/sshd_config.
    - Fjern kommentar fra linjen, og Skift værdien fra **ingen** til **Ja**.
    - Find den linje, der begynder med **undersystem** , og fjern kommentar fra linjen.

        ![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Installere tjenesten mobilitet manuelt

Installer er tilgængelige i C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository.

Kilde-operativsystem | Mobilitet service installationsfilen
--- | ---
Windows Server (kun 64 bit) | Microsoft-ASR_UA_9. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (kun 64 bit) | Microsoft-ASR_UA_9. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (kun 64 bit) | Microsoft-ASR_UA_9. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (kun 64 bit) | Microsoft-ASR_UA_9. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-manually-on-a-windows-server"></a>Installer manuelt på en Windows server


1. Hente og køre den relevante installer.
2. Vælg **tjeneste, mobilitet**ind, **før du går i gang **.

    ![Mobilitet service](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. Angiv IP-adressen på management server og den adgangskode, der blev oprettet, da du installerede management server-komponenter i **Konfigurationsoplysninger Server** . Du kan hente adgangskoden ved at køre: ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** på management-serveren.

    ![Mobilitet service](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. Lade standardplaceringen i **Installationsplacering** , og klik på **Næste** for at starte installationen.
5. Igangværende **Installation** overvåge installationen, og genstart computeren, hvis du bliver bedt om.

Du kan også installere fra kommandolinjen:

UnifiedAgent.exe [/ rolle < Agent/MasterTarget >] [/ InstallLocation <Installation Directory>] [/ CSIP <IP address of CS to be registered with>] [/ PassphraseFilePath <Passphrase file path>] [/ LogFilePath <Log File Path>]

Hvor:

- / Rolle: obligatorisk. Angiver, om tjenesten mobilitet skal installeres.
- / InstallLocation: obligatorisk. Angiver, hvor tjenesten skal installeres.
- / PassphraseFilePath: obligatorisk. Angiver server adgangskoden til konfigurationen.
- / LogFilePath: obligatorisk. Angiver placering af logfiler opsætning af filer

#### <a name="uninstall-mobility-service-manually"></a>Manuelt fjerne mobilitet service

Mobilitet tjenesten kan fjernes ved hjælp af Tilføj fjerne programmet fra Kontrolpanel eller ved hjælp af kommandolinjen.

Kommandoen til at fjerne tjenesten mobilitet kommandolinje er

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="modify-the-ip-address-of-the-management-server"></a>Ændre IP-adressen på indholdsstyringsserver

Når du har kørt guiden kan du ændre IP-adressen på serveren, management på følgende måde:

1. Åbn den fil hostconfig.exe (findes på computeren).
2. Du kan ændre IP-adressen på serveren, management under fanen **Global** .

    >[AZURE.NOTE] Du skal kun ændre IP-adressen på serveren, management. Portnummeret for management serverkommunikation skal være 443, og brug HTTPS skal aktiveres venstre. Adgangskoden må ikke ændres.

    ![Administration af IP-adresse](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-manually-on-a-linux-server"></a>Installer manuelt på en Linux-server:

1. Kopiere det relevante tjære arkiv, der er baseret på tabellen ovenfor for at den Linux maskine, du vil beskytte.
2. Åbn et shell-program og udtrække zippede tjære arkiv til en lokal sti ved at køre:`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Oprette en passphrase.txt fil i den lokale mappe, hvor du har pakket indholdet af tjære arkiv. Gøre dette Kopiér adgangskoden fra C:\ProgramData\Microsoft Azure websted Recovery\private\connection.passphrase på management-serveren og gemme den i passphrase.txt ved at køre *`echo <passphrase> >passphrase.txt`* i shell.
4. Installere tjenesten mobilitet ved at angive *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Angiv interne IP-adressen på serveren, administration, og Sørg for, at port 443 er markeret.

**Du kan også installere fra kommandolinjen**:

1. Kopiér adgangskoden fra C:\Program Files (x86) \InMage Systems\private\connection på management-serveren, og Gem den som "passphrase.txt" på management-serveren. Kør derefter disse kommandoer. I vores eksempel management server IP-adresse er 104.40.75.37 og HTTPS-port skal være 443:

Sådan installeres på en fremstilling server:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Sådan installeres på master serveren:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>Trin 10: Aktivér beskyttelse til en maskine

For at aktivere beskyttelse tilføjer du virtuelle maskiner og fysiske servere på en beskyttelse gruppe. Inden du starter, opmærksom på følgende, hvis du beskytte VMware virtuelle maskiner:

- VMware FOS er blevet fundet hver 15 minutter, og det kan tage mere end 15 minutter, før de kan vises i portalen gendannelse af websteder efter registrering.
- Miljøændringer i den virtuelle maskine (såsom VMware værktøjer installation) kan også tage mere end 15 minutter i gendannelse af websteder skal opdateres.
- For vCenter server/ESXi værten under fanen **Konfiguration servere** , kan du kontrollere opdaget sidst for VMware FOS i feltet **Sidste kontakt på** .
- Hvis du har en allerede oprettet gruppen for beskyttelse og tilføje en vCenter Server eller ESXi host bagefter, kan det tage mere end 15 minutter til portalen Azure gendannelse af websteder til at opdatere og virtuelle maskiner opført i dialogboksen **Tilføj computere til en beskyttelse gruppe** .
- Hvis du gerne vil fortsætte med det samme med at tilføje computere til gruppen for beskyttelse uden at vente planlagte registrering, fremhæve server configuration (ikke klikke på den), og klik på knappen **Opdater** .

Desuden Bemærk, at:

- Vi anbefaler, at du udvikle dine beskyttelse grupper, så de afspejler dine arbejdsmængder. For eksempel tilføje maskiner, der kører et bestemt program til den samme gruppe.
- Når du føjer computere til en gruppe med beskyttelse, processerveren automatisk skubber og installerer tjenesten mobilitet, hvis den ikke allerede er installeret. Bemærk, at du skal have opslagsnål ordningen forberede som beskrevet i ovenstående trin.


Føje computere til en beskyttelse gruppe:

1. Klik på **beskyttet elementer** > **Gruppen beskyttelse** > **maskiner** > Tilføj computere. \As en bedste fremgangsmåde
2. Hvis du beskytte VMware virtuelle maskiner, skal du i **Vælg virtuelle maskiner** Vælg en vCenter-server, der administrerer din virtuelle maskiner (eller værten EXSi, som de kører) og derefter vælge maskiner.

    ![Aktivér beskyttelse](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  Hvis du beskytte fysisk i **Vælg virtuelle maskiner** give-servere i guiden **Tilføj fysiske maskiner** IP-adresse og fulde navn. Vælg operativsystem familie.

    ![Aktivér beskyttelse](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)

4. Vælg kontoen lagerplads, du bruger til replikering, og vælg, om indstillingerne, der skal bruges til alle arbejdsbelastninger i **Angive mål ressourcer** . Bemærk, at premium lagerplads konti ikke understøttes i øjeblikket.

    >[AZURE.NOTE] 1. Vi understøtter ikke flytning af lagerplads konti, der er oprettet ved hjælp af [nye Azure portal](../storage/storage-create-storage-account.md) på tværs af grupper.                           2.[overførsel af lagerplads konti](../resource-group-move-resources.md) på tværs af grupper i det samme abonnement eller på tværs af abonnementer understøttes ikke for lagerplads konti, der bruges til implementering af gendannelse af websteder.

    ![Aktivér beskyttelse](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. Vælg kontoen i **Angive konti** du [konfigureret](#install-the-mobility-service-with-push-installation) til at bruge automatisk installation af tjenesten mobilitet.

    ![Aktivér beskyttelse](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. Klik på markering til færdig med at tilføje computere til gruppen for beskyttelse og for at starte første replikering til hver computer.

    >[AZURE.NOTE] Hvis opslagsnål installation er blevet forberedt tjenesten mobilitet installeres automatisk på computere, der ikke har det, som de er føjet til gruppen beskyttelse. Når tjenesten er installation et beskyttelse job starter og mislykkes. Efter fejlen skal du genstarte manuelt hver computer, der har haft tjenesten mobilitet installeret. Efter genstart starter kørslen beskyttelse igen og indledende replikering forekommer.

Du kan overvåge status på siden **job** .

![Aktivér beskyttelse](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

Desuden beskyttelsesstatus kan overvåges i **Beskyttet elementer** > <protection group name> > **virtuelle computere**. Når første replikering er fuldført, og dataene skal synkroniseres, ændres maskine status til** beskyttet**.

![Aktivér beskyttelse](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## <a name="step-11-set-protected-machine-properties"></a>Trin 11: Angive beskyttet maskine egenskaber

1. Du kan konfigurere egenskaberne failover, når en maskine har statussen **beskyttet** . Vælg maskinen, og Åbn fanen **Konfigurer** i gruppeoplysninger beskyttelse.
2. Gendannelse af websteder foreslår egenskaber til Azure VM og registrerer lokale netværksindstillinger automatisk.

    ![Angive virtuelt egenskaber](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. Du kan ændre disse indstillinger:

    -  **Azure VM navn**: Dette er det navn, der skal angives på computeren i Azure efter failover. Navnet skal overholde krav til Azure.

    -  **Azure VM størrelse**: antallet netværkskort, bestemmes af den størrelse, du angiver for virtuelt mål. [Læs mere](../virtual-machines/virtual-machines-linux-sizes.md/#size-tables) om størrelser og kort. Bemærk, at:

        - Når du ændrer størrelsen på til en virtuel maskine og gemme indstillingerne, ændres antallet netværkskort, når du åbner fanen **Konfigurer** næste gang. Antallet af netværkskort af target virtuelle maskiner er minimum af antallet netværkskort på kilde virtuel maskine og maksimale antal netværkskort, der understøttes af størrelsen på den virtuelle maskine, der er valgt.
            - Hvis antallet af netværkskort på maskinen kilde er mindre end eller lig med antallet af kort, der er tilladt for target maskine størrelse, derefter får destinationen det samme antal kort som kilde.
            - Hvis antallet af kort til kilde virtual machine overstiger det antal, der er tilladt for mål størrelsen og derefter mål størrelsen maksimum der skal bruges.
            - Eksempelvis hvis en kilde maskine har to netværkskort og mål maskine størrelsen understøtter fire, target maskinen har to kort. Hvis kildecomputer har to kort, men understøttede mål størrelsen understøtter kun en har target maskinen kun ét kort.
        - Hvis den virtuelle maskine har forbindelse flere netværkskort alle kort skal til det samme Azure netværk.
    - **Azure netværk**: Du skal angive et Azure netværk, der skal forbindes Azure FOS til efter failover. Hvis du ikke angiver en forbindelse Azure FOS ikke til en hvilken som helst netværk. Desuden skal du angive en Azure netværk, hvis du vil failback fra Azure til webstedet, i det lokale miljø. Failback kræver en VPN-forbindelse mellem en Azure netværk og et lokalt netværk.
    - **Azure IP-adresse/undernet**: For hvert netværkskort du markerer det undernet, som Azure VM skal oprette forbindelse. Bemærk, at:
        - Hvis netværkskort af kildecomputeren er konfigureret til at bruge en statisk IP-adresse, kan du angive en statisk IP-adresse til Azure VM. Hvis du ikke angiver en statisk IP-adresse allokeres alle tilgængelige IP-adresser. Hvis target IP-adressen er angivet, men det er allerede i brug af en anden VM i Azure mislykkes failover. Hvis netværkskort af kildecomputeren er konfigureret til at bruge DHCP skal du have DHCP som indstillingen for Azure.

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>Trin 12: Oprette en plan for gendannelse og køre en failover

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

Du kan køre en sekundær server til en enkelt maskine eller mislykkes over flere virtuelle maskiner, der udfører den samme opgave eller køre samme arbejdsbelastningen. Hvis du vil skifte over flere computere på samme tid føje dem til en plan for gendannelse.

### <a name="create-a-recovery-plan"></a>Oprette en plan for gendannelse

1. Klik på **Tilføj gendannelse Plan** på siden **Gendannelse planer** og tilføje en plan for gendannelse. Angiv oplysninger om planen, og vælg **Azure** som destination.

    ![Konfigurere en plan](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. Vælg gruppen, beskyttelse i **Vælg Virtual Machine** og derefter vælge maskiner i gruppen for at tilføje til planen, gendannelse.

    ![Tilføje virtuelle maskiner](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

Du kan tilpasse planen til at oprette grupper og sekvens den rækkefølge, hvori maskiner i gendannelse plan er mislykkedes over. Du kan også tilføje scripts og beder om manuel handlinger. Scripts kan oprettes manuelt eller ved at bruge [Azure automatisering Runbooks](site-recovery-runbook-automation.md). Du kan [Få mere at vide](site-recovery-create-recovery-plans.md) om at tilpasse gendannelse planer.

## <a name="run-a-failover"></a>Køre en failover

Bemærk, før du kører en failover:


- Sørg for, at management-serveren kører og tilgængelige – ellers failover mislykkes.
- Hvis du kører en ikke-planlagt failover note, der:

    - Hvis det er muligt skal du lukke primære maskiner før du kører en ikke-planlagt failover. Dette sikrer, at du ikke har både kilde- og replika maskiner, der kører på samme tid. Hvis du replikering VMware FOS kan derefter når du kører en ikke-planlagt failover du angive, at gendannelse af websteder skal gøre bedste for at lukke kilde-computere. Afhængigt af tilstanden for det primære websted dette muligvis eller fungerer muligvis ikke. Hvis du replikering fysiske servere gendannelse af websteder ikke tilbyder denne indstilling.
    - Når du udfører en ikke-planlagt failover stopper replikering af data fra primære maskiner, så alle data delta ikke overføres, når en ikke-planlagt failover begynder.

- Hvis du vil oprette forbindelse til replika virtuel maskine i Azure efter failover, skal du aktivere Fjernskrivebord på kilde maskine, før du kører sekundære og tillade RDP forbindelse via firewallen. Du skal også tillade RDP på offentlige slutpunktet på den Azure virtuelle maskine efter failover. Følg disse [bedste fremgangsmåder](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) for at sikre, at RDP fungerer efter en failover.

>[AZURE.NOTE] Sørg for, at du har installeret Azure Agent i den beskyttede computer for at få den bedste ydeevne, når du gør en failover til Azure. Dette kan i starte hurtigere og også, at i diagnosticering i tilfælde af problemer. Linux agent kan være fundet [her](https://github.com/Azure/WALinuxAgent) - og Windows agent kan findes [her](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="run-a-test-failover"></a>Køre en test failover

Køre en test failover for at efterligne et isolerede netværk, der ikke påvirker produktionsmiljøet failover og gendannelse processerne og almindelige gentagelse fortsætter som normalt. Test failover starter på kilden, og du kan køre på flere måder:

- **Ikke angive et netværk med Azure**: Hvis du kører en test failover uden et netværk, testen blot kontrollerer, at virtuelle maskiner starter og vises korrekt i Azure. Virtuelle maskiner forbindelse ikke til et Azure netværk efter failover.
- **Angiv et netværk med Azure**: denne type ydelse kontrollerer, at det hele gentagelse miljø kommer op som forventet, og at Azure virtuelle maskiner har forbindelse til det angivne netværk.


1. Vælg planen, og klik på **Test Failover**siden **Gendannelse planer** .

    ![Tilføje virtuelle maskiner](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. Vælg **ingen** Sådan angiver du ikke vil bruge et netværk med Azure til sekundære test, eller Vælg det netværk, der skal knyttes en test FOS efter failover i **Bekræfte teste Failover** . Klik på markeringen i afkrydsningsfeltet for at starte sekundære.

    ![Tilføje virtuelle maskiner](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. Overvåge failover statussen på fanen **job** .

    ![Tilføje virtuelle maskiner](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. Når sekundære er fuldført skal du også kunne se replikaen Azure maskine vises i Azure portal > **virtuelle computere**. Hvis du vil starte en RDP-forbindelse til Azure VM skal du åbne port 3389 på VM slutpunktet.

5. Når du er færdig, når failover Afslut test fase, skal du klikke på fuldført Test at afslutte. Registrér, i noter og gemme de bemærkninger, der er knyttet til sekundære test.

6. Klik på **sekundære test er fuldført** automatisk rydde op i testmiljøet. Når dette er gjort vise test sekundære **færdigt** status. Alle elementer eller FOS oprettes automatisk under sekundære test, der slettes. Bemærk, at hvis en test failover fortsætter længere end to uger er det fuldført ved kraft.



### <a name="run-an-unplanned-failover"></a>Køre en ikke-planlagt failover

Ikke-planlagt failover startes fra Azure og kan udføres, selvom det primære websted er ikke tilgængelig.


1. **Gendannelse af plan** på siden Vælg planen, og klikke på **Failover** > **Ikke-planlagt Failover**.

    ![Tilføje virtuelle maskiner](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. Hvis du replikering VMware virtuelle maskiner kan du vælge at forsøge at lukke lokale FOS. Dette er bedste evne og failover fortsætter om indsats lykkes eller ej. Hvis det ikke lykkes oplysninger om fejl vises på fanen **job **> **Ikke-planlagt Failover job**.

    ![Tilføje virtuelle maskiner](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

    >[AZURE.NOTE] Denne indstilling er ikke tilgængelig, hvis du replikering fysiske servere. Du skal prøve og lukke dem manuelt, hvis det er muligt.

3. Kontrollér i **Bekræfte Failover** failover retning (til Azure), og vælg det gendannelsespunkt, du vil bruge til sekundære. Hvis du har aktiveret flere VM, når du har konfigureret gentagelse egenskaber kan du gendanne til den seneste program eller nedbrud ensartet gendannelsespunkt. Du kan også vælge **brugerdefineret gendannelse pege** på Gendan til et tidligere tidspunkt. Klik på markeringen i afkrydsningsfeltet for at starte sekundære.

    ![Tilføje virtuelle maskiner](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. Vent et ikke-planlagt failover job skal fuldføre. Du kan overvåge failover statussen på fanen **job** . Bemærk, selvom der sker fejl under ikke-planlagt failover gendannelse planen afspilles, indtil den er fuldført. Du skal også kunne se replikaen Azure maskine vises i virtuelle maskiner i portalen Azure.

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>Oprette forbindelse til replikeres Azure virtuelle maskiner efter failover

Replikeres virtuelle maskiner i Azure når failover her er, hvad du skal bruge for at oprette forbindelse til:

1. Funktionen Fjernskrivebord skal være aktiveret på den primære computer.
2. Windows Firewall på den primære maskine tillade RDP.
3. Du skal føje RDP til det offentlige slutpunkt for Azure virtuelt efter failover.

[Læs mere](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) om at konfigurere dette.


## <a name="deploy-additional-process-servers"></a>Installere yderligere processer servere

Hvis du vil skalere ud installationen ud over 200 kilde-computere eller dit samlede daglige transportspand rente overstiger 2 TB, skal du yderligere processer servere til at håndtere trafik lydstyrken. Til at konfigurere en server til yderligere processer kontrollere systemkrav i [yderligere processer servere](#additional-process-servers) og derefter følge vejledningen her for at konfigurere processerveren. Du kan konfigurere kilde maskiner for at bruge det efter konfigurering af serveren.

### <a name="set-up-an-additional-process-server"></a>Konfigurere en server til yderligere processer

Du konfigurerer en server til yderligere processer på følgende måde:

- Køre guiden samlet for at konfigurere en management-server som en proces server.
- Hvis du vil administrere replikering af data ved hjælp af kun den nye proces server skal du overføre dine beskyttet maskiner for at gøre dette.

### <a name="install-the-process-server"></a>Installer processerveren

1. Download samlet installationsfilen for installationen af komponenten gendannelse af websteder på siden Hurtig Start. Kør installationsprogrammet.
2. Vælg **Tilføj flere proces servers skalere ud installation**ind, **før du går i gang** .

    ![Tilføje process-server](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. Fuldføre guiden på samme måde, du har, når du [konfigurere](#step-5-install-the-management-server) den første management server.

4. Angiv IP-adressen på den oprindelige management server, som du har installeret server configuration, og adgangskoden, i **Server konfigurationsoplysninger** . På den oprindelige indholdsstyringsserver køre ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** til at hente adgangskoden.

    ![Tilføje process-server](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Overføre computere for at bruge den nye proces-server

1. Åbn **Konfiguration servere** > **Server** > navnet på den oprindelige indholdsstyringsserver > **Serverdetaljer**.

    ![Opdatere process-server](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. Klik på **Skift proces Server** ud for den server, du vil ændre, på listen **Proces-servere** .

    ![Opdatere process-server](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. I **Skift proces Server** > **Target proces Server** Vælg den nye management server, og vælg derefter de virtuelle maskiner, som den nye proces server håndterer. Klik på oplysningsikonet for at få oplysninger om serveren. Det gennemsnitlige område, der er nødvendige for at gentage hver valgte virtuelt til den nye proces server vises for at hjælpe dig med at træffe indlæse beslutninger. Klik på markeringen i afkrydsningsfeltet for at starte replikering til den nye proces-server.

    ![Opdatere process-server](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)




## <a name="vmware-permissions-for-vcenter-access"></a>VMware adgangstilladelser for vCenter

Processen serveren kan automatisk opdage FOS på en vCenter server. Hvis du vil udføre automatisk registrering skal du definere en rolle (Azure_Site_Recovery) på niveauet vCenter for at tillade gendannelse af websteder til at få adgang til vCenter-serveren. Bemærk, at hvis du kun har brug at overføre VMware maskiner til Azure og ikke vil failback fra Azure, du kan definere en skrivebeskyttet rolle, der er tilstrækkeligt. Du konfigurerer tilladelserne, som beskrevet i [trin 6: konfigurere legitimationsoplysninger for vCenter server](#step-6-set-up-credentials-for-the-vcenter-server) rolletilladelser er opsummeret i følgende tabel.

**Rolle** | **Detaljer** | **Tilladelser**
--- | --- | ---
Azure_Site_Recovery rolle | VMware VM registrering |Tildele disse rettigheder til v-Center-serveren:<br/><br/>DataStore -> fordel afstanden Gennemse datastore lav niveau fil handlinger., fjern fil, og Opdater virtuelt filer<br/><br/>Netværk -> netværk Tildel<br/><br/>Ressource -> Tildel virtuelt ressourcepulje, overføre slukket virtuel maskine, overføre tændt virtuel maskine<br/><br/>Opgaver -> Opret opgave, Opdater opgave<br/><br/>Virtual machine -> konfiguration<br/><br/>Virtual machine -> interakt.Skab -> spørgsmål svar enhed forbindelse. konfigurere CD-medier, og Konfigurer floppy medier, Power fra Power på, skal du installere VMware værktøjer<br/><br/>Virtual machine -> Lager -> Opret, Register, fjernelse af registrering<br/><br/>Virtual machine -> artikel Provisioning -> Tillad virtuelt download, overføre Tillad virtuelt filer<br/><br/>Virtual machine -> Snapshots -> Fjern øjebliksbilleder
vCenter brugerrolle | VMware VM registrering/Failover uden lukning af kilde VM | Tildele disse rettigheder til v-Center-serveren:<br/><br/>Data Center objekt –> Overfør til underordnede objekt rolle = skrivebeskyttet <br/><br/>Brugeren er tildelt på datacenter niveau og dermed har adgang til alle objekter i datacenteret.  Hvis du vil begrænse adgangen, skal du tildele rollen **Ingen adgang** med objektet **Overfør barn** til de underordnede objekter (ESX hosts, datastores, FOS og netværk).
vCenter brugerrolle | Failover og failback | Tildele disse rettigheder til v-Center-serveren:<br/><br/>Datacenter objekt – Overfør til underordnede objekt rolle = Azure_Site_Recovery<br/><br/>Brugeren er tildelt på datacenter niveau og dermed har adgang til alle objekter i datacenteret.  Hvis du vil begrænse adgangen, skal du tildele rollen **Ingen adgang **med **Overfør til underordnede objekt** til det underordnede objekt (ESX hosts, datastores, FOS og netværk).  



## <a name="third-party-software-notices-and-information"></a>Softwaremeddelelser fra tredjepart og oplysninger

Send ikke oversætte eller lokalisere

Softwaren og de firmware kører i Microsoft-produkt eller tjeneste er baseret på eller inkorporerer materiale fra projekter på listen nedenfor (samlet "fra tredjepart-kode").  Microsoft er den ikke oprindelige forfatter af koden fra tredjepart.  Den oprindelige meddelelse om ophavsret og licens, under hvilket Microsoft modtaget sådanne fra tredjepart kode, er indstillet tilbage nedenfor.

Oplysningerne i sektionen A drejer sig om fra tredjepart kode komponenter i de projekter, der vises nedenfor. Disse licenser og oplysninger, der leveres til orientering.  Denne kode fra tredjepart er der relicensed til dig af Microsoft under Microsoft-softwarelicensvilkårene for Microsoft-produkt eller tjeneste.  

Oplysningerne i afsnit B drejer sig om fra tredjepart kode komponenter, der gøres tilgængelige for dig af Microsoft under oprindelige licensvilkårene.

Hele filen kan findes på [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft forbeholder sig alle rettigheder, der ikke udtrykkeligt er tildelt denne aftale, uanset om det skyldes det vil sige, estoppel eller på anden måde.

## <a name="next-steps"></a>Næste trin

[Lær mere om failback](site-recovery-failback-azure-to-vmware-classic.md) til at hente dine mislykkedes over maskiner, der kører i Azure tilbage til dit lokale miljø.
