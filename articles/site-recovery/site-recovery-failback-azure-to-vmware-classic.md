<properties 
   pageTitle="Mislykkes tilbage VMware virtuelle maskiner og fysiske servere til det lokale websted | Microsoft Azure"
   description="Få mere at vide om fejl tilbage til webstedet for lokale efter failover af VMware FOS og fysiske servere til Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="08/22/2016"
   ms.author="ruturajd"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Fail tilbage VMware virtuelle maskiner og fysiske servere til det lokale websted

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-failback-azure-to-vmware.md)
- [Azure klassisk Portal](site-recovery-failback-azure-to-vmware-classic.md)
- [Azure klassisk Portal (ældre)](site-recovery-failback-azure-to-vmware-classic-legacy.md)



I denne artikel beskrives, hvordan mislykkes tilbage Azure virtuelle maskiner fra Azure til webstedet, i det lokale miljø. Følg vejledningen i denne artikel, når du er klar til at mislykkes tilbage VMware virtuelle-computere eller Windows/Linux fysiske servere, når de har mislykkedes fra lokale websted til at Azure ved hjælp af dette [selvstudium](site-recovery-vmware-to-azure-classic.md).



## <a name="overview"></a>Oversigt

Dette diagram viser failback arkitekturen for dette scenario.

Brug denne arkitektur, når processen serveren er i det lokale miljø, og du bruger en ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Brug denne arkitektur, når processen serveren er på Azure, og du har en VPN-forbindelse eller en ExpressRoute forbindelse.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Hvis du vil se den komplette liste over porte og failback architechture diagram referere til billedet nedenfor

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Sådan virker failback:

- Når du har ikke Azure, mislykkes du tilbage til dit lokale websted i et par faser:
    - **Trin 1**: reprotect Azure FOS, så de starter replikering tilbage til VMware VM'er, der kører på dit lokale websted. Aktivere reprotection flytter VM til en failback beskyttelse gruppe, der blev oprettet automatisk, når du oprindeligt har oprettet gruppen failover beskyttelse. Hvis du har tilføjet din failover beskyttelse gruppe til en gendannelse plan derefter failback beskyttelse gruppen også automatisk blev føjet til planen.  Under reprotect kan du angive, hvordan du planlægger at mislykkes tilbage.
    - **Trin 2**: Når din Azure FOS replikering til dit lokale websted, du kører en fejl mislykkes tilbage fra Azure.
    - **Trin 3**: Når dine data mislykkes tilbage, du reprotect de lokale FOS, som du ikke tilbage til, så de starter replikeres til Azure.

> [AZURE.VIDEO enhanced-vmware-to-azure-failback]


### <a name="failback-to-the-original-or-alternate-location"></a>Failback til den oprindelige eller alternative placering

Hvis du ikke over en VMware VM kan du ikke tilbage til den samme kilde VM, hvis den stadig findes i det lokale miljø. I dette scenarie bliver kun delta ændringer ikke gennemført tilbage. Bemærk, at:

- Hvis du ikke over fysiske servere er failback altid til en ny VMware VM.
    - Bemærk, at før ned tilbage en fysisk maskine
    - Fysisk computer beskyttet kommer tilbage som en virtuel maskine, når mislykkedes tilbage fra Azure VMware
    - Sørg for, at du opdager mindst én Master Target bryde sammen med de nødvendige ESX/ESXi værter, du vil failback.
- Hvis du ikke tilbage til den oprindelige VM er følgende påkrævet:
    - Hvis VM administreres af en vCenter server skal målet Master ESX host har adgang til FOS datastore.
    - Hvis VM er på en ESX vært, men ikke styres af vCenter være harddisken VM i en handicapvenlige datastore af af MT vært.
    - Hvis din VM findes på en ESX vært og ikke bruger en vCenter skal du udføre registrering af ESX vært for MT, før du reprotect. Dette gælder, hvis du ned tilbage fysiske servere for.
    - En anden indstilling er (hvis den lokale VM findes) at slette den, før du gør en failback. Derefter opretter failback derefter en ny VM på den samme vært som master target ESX vært.
    
- Når vil blive gendannet failback til en anden placering dataene i den samme datastore og samme ESX værten som dem, der bruges af lokale master destinationsadresse-serveren. 


## <a name="prerequisites"></a>Forudsætninger

- Du skal bruge et VMware miljø for at mislykkes tilbage VMware FOS og fysiske servere. Fejlbehæftede tilbage til en fysisk server understøttes ikke.
- For at mislykkes tilbage skulle du have oprettet et netværk med Azure når du har konfigureret beskyttelse. Failback skal have en VPN- eller ExpressRoute forbindelse fra Azure netværket hvor Azure FOS befinder sig til webstedet, i det lokale miljø.
- Hvis FOS, du vil mislykkes tilbage til administreres af en vCenter server, skal du kontrollere, har du de nødvendige tilladelser til registrering af FOS på vCenter-servere. [Du finder flere oplysninger](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Hvis snapshots findes på en VM mislykkes reprotection. Du kan slette snapshot eller diskene. 
- Før du mislykkes igen skal du oprette et antal komponenter:
    - **Opret en process-server i Azure**. Dette er en Azure VM, skal du oprette og holde kører under failback. Du kan slette maskinen, når failback er fuldført.
    - **Opret en masterside target server**: master target serveren sender og modtager failback data. Indholdsstyringsserver, du oprettede i det lokale miljø en master target server er installeret som standard. Afhængigt af mængde mislykkedes tilbage trafik kan du dog skal til at oprette en separat master target server til failback.
    - Hvis du vil oprette en ekstra slidemaster mål-server, der kører på Linux, skal du konfigurere Linux VM før du installerer master destinationsadresse-server, som beskrevet nedenfor.
- Konfiguration af server er påkrævet lokalt, når du gør en failback. Under failback, skal den virtuelle maskine findes i konfiguration server-databasen, ned, hvilke failback kan ikke gennemføres. Derfor sikre, at du tager normalt planlagte sikkerhedskopi af din server. I tilfælde af nedbrud skal du gendanne den med den samme IP-adresse, så failback fungerer.

## <a name="set-up-the-process-server-in-azure"></a>Konfigurere processerveren i Azure

Du skal installere en process-server i Azure, så Azure FOS kan sende dataene tilbage til lokal master target server. 

1.  På portalen gendannelse af websteder > **Konfiguration servere** Vælg for at tilføje en ny proces-server.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)

2.  Angiv et servernavn proces, og Angiv et navn og en adgangskode, du skal bruge til at oprette forbindelse til Azure VM som administrator. Vælg den lokale management server i **Server Configuration** , og Angiv Azure netværket, der skal installeres processerveren. Dette skal være det netværk, hvor Azure FOS er placeret. Angive en entydig IP-adresse fra det vælge undernet og starte installationen.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

    Et job til at udrulle processerveren udløses

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

    Når processen serveren er distribueret i Azure kan du logger på den med de legitimationsoplysninger, du har angivet. Første gang du logger på dialogboksen proces server kører. Skriv IP-adressen for den lokale management server og dens adgangskoden. Lad standardindstillingen for port 443. Du kan også lade standard 9443 port til replikering af data, medmindre du specifikt ændret denne indstilling, når du konfigurerer indholdsstyringsserver i det lokale miljø. 

    >[AZURE.NOTE] Serveren er ikke synlige under **VM egenskaber**. Det er kun synligt under fanen **servere** management Server som den er blevet registreret. Det kan tage om 10-15 minutter for processen server skal vises.


## <a name="set-up-the-master-target-server-on-premises"></a>Konfigurere de overordnede mål server lokalt

Master target serveren modtager failback dataene. En overordnet mål server installeres automatisk på indholdsstyringsserver lokalt, men hvis du ned tilbage mange data det være nødvendigt at konfigurere en ekstra slidemaster mål-server. Gøre dette på følgende måde:

>[AZURE.NOTE] Hvis du vil installere en master target server på Linux, skal du følge vejledningen i den næste procedure.

1. Hvis du installerer master target server på Windows, kan du åbne siden Hurtig Start fra VM, hvorpå du installerer master destinationsadresse-server, og hente installationsfilen til Azure websted gendannelse Unified konfigurationsguiden.
2. Kør installationsprogrammet, og vælg **Tilføj flere proces servers skalere ud installation**ind, **før du går i gang** .
3. Fuldføre guiden på samme måde, du har, når du [konfigurere management-serveren](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Angiv denne master destinationsadresse-server, og en adgangskode til at få adgang til VM IP-adressen på siden **Oplysninger om konfiguration Server** .

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Konfigurere en Linux VM som master destinationsadresse-serveren
Konfigurere management-serveren kører master target server som en Linux VM skal du installere kr) operativsystem, skal du S 6.6 minimale hente SCSI-id'er for hver SCSI-harddisk, installere nogle ekstra pakker og anvende brugerdefinerede ændringer.

#### <a name="install-centos-66"></a>Installere CentOS 6.6

1.  Installere CentOS 6.6 minimale operativsystemet på indholdsstyringsserver VM. Bevare ISO i DVD-drevet, og genstart systemet. Springe test af medier, markere engelsk på sproget, Vælg **Grundlæggende lagerenheder**, Kontrollér, at harddisken ikke har nogen vigtige data og klikker på **Ja**, Slet data. Angiv værtsnavnet på management-serveren, og vælg netværkskort server.  Vælge** Opret automatisk forbindelse** , og Tilføj en statisk IP-adresse-, Netværks- og DNS-indstillingerne i dialogboksen **Redigere System** . Angive en tidszone og en rod adgangskode for at få adgang til administration af serveren. 
2.  Du vil vælge **Oprette brugerdefinerede Layout** som partitionen, når du bliver bedt om installationstype. Når du klikker på **Næste** vælge **Frigør** og klikke på Opret. Oprette **/**, **/var/crash** og **/ home partitioner** med **FS Type:** **ext4**. Oprette Udskift partion som **FS Type: Udskift**.
3.  Hvis der findes allerede eksisterende enheder under en advarsel vises. Klik på **Formatér** for at formatere drevet med Partitionsindstillingerne for. Klik på **skrive Skift til disk** for at anvende ændringerne partition.
4.  Vælg **Installer Start loader** > **Næste** installere Start loader på rodpartition.
5.  Klik på **Genstart**, når installationen er fuldført.


#### <a name="retrieve-the-scsi-ids"></a>Hente SCSI-id'er

1. Hent SCSI-id'er for hver SCSI harddisk i VM efter installationen. Højreklik på posten VM for at udføre denne Luk indholdsstyringsserver VM skal i egenskaberne VM i VMware > **Rediger indstillinger for** > **Indstillinger**.
2. Vælg **Avanceret** > **generelle element** , og klik på **Konfiguration af parametre**. Denne indstilling, vil være de-active, når computeren kører. For at gøre det aktive skal maskinen lukkes.
3. Hvis rækken **disk. EnableUUID** findes Sørg for, at værdien indstilles til **Sand** (store og små bogstaver). Hvis det er allerede, du kan annullere og teste SCSI-kommandoen i et gæst operativsystem, når den er blevet startet. 
4.  Hvis rækken ikke eksisterende skal du klikke på **Tilføj række** - og tilføje den med værdien **Sand** . Brug ikke dobbelte anførselstegn.

#### <a name="install-additional-packages"></a>Installere flere pakker

Du skal hente og installere nogle ekstra pakker. 

1.  Kontrollér, at master target serveren er forbindelse til internettet.
2.  Kør denne kommando for at hente og installere 15-pakker fra CentOS lager: **# yum Installér – y xfsprogs perl lsscsi rsync wget kexec-værktøjer**.
3.  Hvis kilde-computere, du beskytte kører Linux henholdsvis Reiser eller XFS filsystemet for rod eller Start enheden, og derefter skal du hente og installere flere pakker på følgende måde:

    - # <a name="cd-usrlocal"></a>cd /usr/local
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
    - # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>rpm – ivh kmod-reiserfs-0,0-1.el6.elrepo.x86_64.rpm reiserfs-af websted-3.6.21-1.el6.elrepo.x86_64.rpm
    - # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
    - # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>rpm – ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>Anvende brugerdefinerede ændringer

Udføre følgende fremgangsmåder for at anvende brugerdefinerede ændringer, når du har fuldført trinnene efter installationen og installeret pakkerne:

1.  Kopiér den RHEL 6-64 Unified Agent binære til VM. Kør denne kommando for at untar binære: **tjære – zxvf <file name> **
2.  Kør denne kommando for at give tilladelser: **# chmod 755./ApplyCustomChanges.sh**
3.  Køre scriptet: **#./ApplyCustomChanges.sh**. Du skal kun køre scriptet én gang. Når scriptet kører korrekt, skal du genstarte serveren.


## <a name="run-the-failback"></a>Køre failback

### <a name="reprotect-the-azure-vms"></a>Reprotect Azure FOS

1.  På portalen gendannelse af websteder > **maskiner** under fanen Vælg VM, der er blevet mislykkedes, og klik på **Beskyt igen**.
2.  Vælg lokalt master target serveren, og Azure VM proces server i **Master Target Server** og **Proces Server** .
3.  Vælg den konto, du har konfigureret til at oprette forbindelse til VM.
4.  Vælg failback versionen af gruppen beskyttelse. For eksempel hvis VM er beskyttet i navnet PG1, og du skal vælge PG1_Failback.
5.  Hvis du vil gendanne til en anden placering, Vælg det opbevaring drev og datastore konfigureret til den overordnede mål-server. Når du ikke tilbage til webstedet for lokale VMware FOS i failback beskyttelsesplan anvender de samme datastore som master destinationsadresse-serveren. Hvis du vil gendanne replikaen Azure VM til det samme lokale VM lokale VM skal allerede være af samme DataStore som master destinationsadresse-serveren. Hvis der ikke er nogen VM lokale oprettes en ny under reprotection.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)

6.  Når du klikker på begynder **OK** for at starte reprotection et job at gentage VM fra Azure til webstedet, i det lokale miljø. Du kan spore status på fanen **job** .

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>Køre en failover til det lokale websted

Når du reprotection VM flyttes til failback versionen af gruppen dens beskyttelse og føjes automatisk til den gendannelse plan, du har brugt til sekundære til Azure, hvis den findes.

1.  **Gendannelse af plan** på siden Vælg den gendannelse plan, der indeholder gruppen failback og klikke på **Failover** > **Ikke-planlagt Failover**.
2.  Kontrollér i **Bekræfte Failover** failover retning (til Azure), og vælg det gendannelsespunkt, du vil bruge til sekundære (nyeste). Hvis du har aktiveret **Flere VM** , når du har konfigureret gentagelse egenskaber kan du gendanne til den nyeste app eller nedbrud ensartet gendannelsespunkt. Klik på markeringen i afkrydsningsfeltet for at starte sekundære.
3.  Gendannelse af websteder lukkes under failover Azure FOS. Når du markerer denne failback er afsluttet, som forventet, du kan kan du kontrollere, at Azure FOS blevet lukket som forventet.

### <a name="reprotect-the--on-premises-site"></a>Reprotect webstedet lokalt

Når failback er fuldført dine data bliver tilbage på webstedet i det lokale miljø, men ikke er beskyttet. Starte replikeres til Azure igen gøre følgende:

1.  På portalen gendannelse af websteder > **maskiner** under fanen Vælg VM'er, der har mislykkedes igen, og klik på **Beskyt igen**. 
2.  Når du har bekræftet, at replikering til fungerer Azure som forventet i Azure, kan du slette den Azure FOS (i øjeblikket ikke kører), der blev mislykkedes tilbage.


### <a name="common-issues-in-failback"></a>Almindelige problemer i failback

1. Hvis du udfører og skrivebeskyttet bruger vCenter registrering og beskytte virtuelle maskiner, det lykkes og failover fungerer. På tidspunktet for Reprotect mislykkes det, da datastores ikke kan registreres. Et symptom se du ikke datastores er angivet under beskyttelse igen. Du kan løse problemet, kan du opdatere legitimationsoplysninger, der vCenter med relevante konto, der har tilladelser, og prøv igen jobbet. [Få mere at vide](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Når du failback en Linux VM og køre den lokalt, vil du se, at pakken Network Manager er fjernet fra computeren. Dette skyldes, at når VM gendannes i Azure, pakken Network Manager er fjernet.
3. Når en VM er konfigureret med statiske IP-adresse og er mislykkedes Azure, er IP-adressen hentet via DHCP. Når du ikke over tilbage til lokalt, hvor VM fortsætter med at bruge DHCP til at hente IP-adresse. Du skal manuelt logon til den computer, og sæt IP-adressen tilbage til statisk adresse, hvis det er nødvendigt.
4. Hvis du bruger enten ESXi 5.5 gratis edition eller vSphere 6 Hypervisor gratis edition, vil det lykkes failover, men failback lykkes ikke. Du kan blive ned for at opgradere til enten evaluering licens til at aktivere failback.

## <a name="failing-back-with-expressroute"></a>Svigter tilbage med ExpressRoute

Du kan ikke tilbage over en VPN-forbindelse eller Azure ExpressRoute. Hvis du vil bruge ExpressRoute Bemærk følgende:

- ExpressRoute skal konfigureres på Azure virtuelle netværket til hvilke kilde maskiner fejl over og i hvilken Azure FOS er placeret, når sekundære opstår.
- Data replikeres til en Azure lagerplads konto på et offentligt slutpunkt. Du skal konfigurere offentlige peering i ExpressRoute med target datacenter til gendannelse af websteder replikering bruge ExpressRoute.



