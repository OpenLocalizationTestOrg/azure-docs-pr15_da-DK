<properties 
   pageTitle="Mislykkes tilbage VMware virtuelle maskiner og fysiske servere fra Azure til VMware (ældre) | Microsoft Azure" 
   description="I denne artikel beskrives, hvordan mislykkes tilbage en VMware virtuel maskine, der er blevet replikeres til Azure med Azure gendannelse af websteder." 
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
   ms.workload="storage-backup-recovery" 
   ms.date="10/05/2016"
   ms.author="ruturajd@microsoft.com"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-from-azure-to-vmware-with-azure-site-recovery-legacy"></a>Mislykkes tilbage VMware virtuelle maskiner og fysiske servere fra Azure til VMware med Azure gendannelse af websteder (ældre)

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-failback-azure-to-vmware.md)
- [Azure klassisk Portal](site-recovery-failback-azure-to-vmware-classic.md)
- [Azure klassisk Portal (ældre)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


Gendannelse af websteder Azure service bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR) strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere. Computere kan replikeres til Azure eller til et sekundært lokalt datacenter. En hurtig oversigt over læse [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md)

## <a name="overview"></a>Oversigt

I denne artikel beskrives, hvordan mislykkes tilbage VMware virtuelle computere og Windows/Linux fysiske servere fra Azure til dit lokale websted, når du har replikeres fra dit lokale websted til Azure.

>[AZURE.NOTE] I denne artikel beskrives et ældre scenarie. Du skal kun bruge instruktionerne i denne artikel, hvis du replikeres til Azure ved hjælp af [disse ældre instruktioner](site-recovery-vmware-to-azure-classic-legacy.md). Hvis du konfigurere gentagelse ved hjælp af den [forbedrede installation](site-recovery-vmware-to-azure-classic-legacy.md) , skal du følge vejledningen i [denne artikel](site-recovery-failback-azure-to-vmware-classic.md) for at mislykkes tilbage. 


## <a name="architecture"></a>Arkitektur

I dette diagram repræsenterer scenariet failover og failback. Blå linjerne er de forbindelser, der bruges under failover. De røde understregninger er de forbindelser, der bruges i failback. Linjer med pile gå via internettet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## <a name="before-you-start"></a>Før du starter 

- Du skal have mislykkedes over din VMware FOS eller fysiske servere, og de skal køres i Azure.
- Du skal være opmærksom på, at du kun kan mislykkes tilbage VMware virtuelle computere og Windows/Linux fysiske servere fra Azure VMware virtuelle maskiner i det lokale primære websted.  Hvis du tilbage ned en fysisk maskine, failover til Azure konvertere den til en Azure VM og failback til VMware konvertere den til en VMware VM.

Her er, hvordan du konfigurere failback:

1. **Konfigurere failback komponenter**: skal du konfigurere en vContinuum server lokalt, og peg på server configuration VM i Azure. Du kan også konfigurere en process-server som en Azure VM til at sende data tilbage til lokale master target serveren. Du har registreret proces server med konfigurationsserveren, behandles sekundære. Du installere en lokal master target server. Hvis du har brug for en Windows master target server er den konfigureret automatisk når du installerer vContinuum. Hvis du har brug for Linux skal du konfigurere det manuelt på en separat server.
2. **Aktivér beskyttelse og failback**: Når du har konfigureret komponenterne, i vContinuum skal du aktivere beskyttelse af mislykkedes over Azure FOS. Du skal udføre en parathedskontrol af FOS og køre en failover fra Azure til dit lokale websted. Når failback afsluttes reprotect lokale computere, så de starter replikeres til Azure.



## <a name="step-1-install-vcontinuum-on-premises"></a>Trin 1: Installere vContinuum lokalt

Du skal installere en vContinuum server lokalt, og peg server configuration.

1.  [Hente vContinuum](http://go.microsoft.com/fwlink/?linkid=526305). 
2.  Derefter kan du hente den [vContinuum opdatere](http://go.microsoft.com/fwlink/?LinkID=533813) version.
3. Installere den nyeste version af vContinuum. Klik på **Næste**på siden **Velkommen** .
    ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  Angiv CX IP-adressen og serverport CX på den første side i guiden. Vælg **Brug HTTPS**.

    ![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Finde server configuration IP-adresse under fanen **Dashboard** i server configuration VM i Azure.
    ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Finde server configuration HTTPS offentlige porten under fanen **slutpunkter** i server configuration VM i Azure.

    ![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. Angiv den adgangskode, du har noteret ned, når du har registreret server configuration på siden **CS adgangskoden** . Hvis du ikke kan huske den tjekker den ind **C:\\programfiler (x86)\\InMage systemer\\privat\\connection.passphrase** på server configuration VM.

    ![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  Angiv, hvor du vil installere vContinuum serveren, og klik på **Installer**, på siden **Vælg destinationen** .

    ![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. Når installationen er fuldført, kan du starte vContinuum.
    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## <a name="step-2-install-a-process-server-in-azure"></a>Trin 2: Installere en process-server i Azure 

Du skal installere en process-server i Azure, så FOS i Azure kan sende dataene tilbage til en lokal master target server. 

1.  Vælg for at tilføje en ny proces server på siden **Konfiguration af servere** i Azure.

    ![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Angive en server til proces navn, og et navn og en adgangskode for at oprette forbindelse til den virtuelle maskine som administrator. Vælg den konfiguration server, du vil registrere processerveren. Dette skal være den samme server, du bruger til at beskytte og mislykkes over virtuelle computere.
3.  Angiv Azure netværket, der skal installeres processerveren. Det skal være den samme netværk som server configuration. Angiv et entydigt IP-adresse, der vælges undernet og starte installationen.

    ![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  Et job udløses, hvis du vil installere processerveren.

    ![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Når processen serveren er distribueret i Azure kan du logge på serveren ved hjælp af de legitimationsoplysninger, du har angivet. Registrere proces server på samme måde, du har registreret lokal proces server. 

    ![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE] De servere, der er registreret under failback ikke synlige under VM egenskaber i gendannelse af websteder. De er kun synlig under fanen **servere** på den konfiguration server, som de er registreret. Det kan tage om 10-15 minutter, indtil de processerveren vises under fanen.


## <a name="step-3-install-a-master-target-server-on-premises"></a>Trin 3: Installer en master target server lokalt

Afhængigt af dit kilde virtuelle maskiner operativsystem, skal du installere en Linux eller en Windows master target server lokalt.

### <a name="deploy-a-windows-master-target-server"></a>Installere en Windows master target server

Et overordnet mål Windows følger allerede med vContinuum konfiguration. Når du installerer vContinuum, er en overordnet server også installeret på den samme maskine og registreret til af konfigurationsserveren.

1.  For at begynde installation skal du oprette en tom machine lokalt på værten ESX, som du vil gendanne FOS fra Azure.

2.  Sørg for, at der er knyttet til VM mindst to diske – en, der bruges til operativsystemet og den anden bruges til opbevaring drev.

3.  Installere operativsystemet.

4.  Du kan installere vContinuum på serveren. Dette også afslutter installation af mastersider destinationsadresse-server.

### <a name="deploy-a-linux-master-target-server"></a>Installere en Linux master target server

1.  For at begynde installation skal du oprette en tom machine lokalt på værten ESX, som du vil gendanne FOS fra Azure.

2.  Sørg for, at der er knyttet til VM mindst to diske – en, der bruges til operativsystemet og den anden bruges til opbevaring drev.

3.  Installere Linux-operativsystemet. Linux master target systemet skal ikke bruge LVM til rod- eller opbevaringspolitik lagerplads mellemrum. En Linux master target server er konfigureret til at undgå LVM partitioner/diske registrering som standard.
4.  Partitioner, som du kan oprette:

    ![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Udføre den nedenfor sende installationstrin før du begynder master target installationen.


#### <a name="post-os-installation-steps"></a>Send OS installationstrin

Hvis du vil have SCSI-id'er for hver af SCSI-harddisk i en Linux virtuel maskine, aktivere parameteren "disk. EnableUUID = TRUE "på følgende måde:

1. Lukke din virtuelle maskine.
2. Højreklik på posten VM i panelet til venstre > **Rediger indstillinger**.
3. Klik på fanen **Indstillinger** . Vælg **Avanceret\>generelle element** > **Konfiguration af parametre**. Indstillingen **Konfiguration af parametre** er kun tilgængelig, når maskinen lukkes.

    ![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Kontrollerer, om en række med **disk. EnableUUID** findes. Hvis den, og er indstillet til **Falsk** derefter angive den til **Sand** (ikke store og små bogstaver). Hvis der findes og er indstillet til sand, klik på **Annuller** og teste kommandoen SCSI inde i gæst operativsystemet, når den er blevet startet op. Hvis der findes ikke klikke på **Tilføj række**.
5. Tilføje disk. EnableUUID i kolonnen **navn** . Angive dens værdi som SAND. Tilføj ikke ovennævnte værdier sammen med dobbelte anførselstegn.

    ![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### <a name="download-and-install-the-additional-packages"></a>Hente og installere de ekstra pakker

Bemærk: Sørg for, at systemet har forbindelse til internettet før hente og installere de ekstra pakker.

\#yum Installér -y xfsprogs perl lsscsi rsync wget kexec-værktøjer

Denne kommando henter pakkerne 15 fra CentOS 6.6 lager og installerer dem:

BC-1.06.95-1.el6.x86\_64. rpm

busybox-1.15.1-20.el6.x86\_64. rpm

elfutils-libs-0.158-3.2.el6.x86\_64. rpm

kexec-værktøjer-2.0.0-280.el6.x86\_64. rpm

lsscsi-0,23-2.el6.x86\_64. rpm

lzo-2.03-3.1.el6\_5.1.x86\_64. rpm

Perl-5.10.1-136.el6\_6.1.x86\_64. rpm

Perl-modul-plug-3.90-136.el6\_6.1.x86\_64. rpm

Perl-styreanordning-skifter-1,04-136.el6\_6.1.x86\_64. rpm
    
Perl-styreanordning-enkle-3.13-136.el6\_6.1.x86\_64. rpm

Perl-libs-5.10.1-136.el6\_6.1.x86\_64. rpm

Perl-version-0,77-136.el6\_6.1.x86\_64. rpm

rsync-3.0.6-12.el6.x86\_64. rpm

snappy-1.1.0-1.el6.x86\_64. rpm

wget-1.12-5.el6\_6.1.x86\_64. rpm

Bemærk: Hvis kilde maskinen bruger Reiser eller XFS filsystemet for enheden rod eller Start, derefter følgende pakker skal hentes og installeres på Linux master målet før beskyttelse.

\#cd /usr/local

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\#rpm - ivh kmod-reiserfs-0,0-1.el6.elrepo.x86\_64. rpm reiserfs-af websted-3.6.21-1.el6.elrepo.x86\_64. rpm

\#wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\#rpm - ivh xfsprogs-3.1.1-16.el6.x86\_64. rpm

#### <a name="apply-custom-configuration-changes"></a>Anvende brugerdefinerede konfigurationsændringer

Sørg for, at du har færdiggjort det forrige afsnit før anvendelse af ændringerne og derefter følge disse trin:


1. Kopiere den RHEL 6-64 Unified Agent binære til det nyoprettede OS.

2. Kør denne kommando for at untar binære: **tjære - zxvf \<filnavn\> **

3. Kør denne kommando for at give tilladelser: \# **chmod 755./ApplyCustomChanges.sh**

4. Køre scriptet: ** \# ./ApplyCustomChanges.sh**. Kør scriptet kun én gang på serveren. Genstart serveren, når du har kørt scriptet.



### <a name="install-the-linux-server"></a>Installere Linux-server


1. [Hente](http://go.microsoft.com/fwlink/?LinkID=529757) installationsfilen.
2. Kopiere filen til den Linux Master Target virtuelle maskine, ved hjælp af en sftp klienten utility efter eget valg. At kan du logge på Linux master target virtuel maskine og bruge wget for at downloade installationspakken medfølgende link
3. Log på den Linux master target server virtuelt ved hjælp af en ssh klienten efter eget valg
4. Hvis du har forbindelse til Azure netværket, hvor du har installeret din Linux master target server via en VPN-forbindelse skal du bruge den interne IP-adresse for den server, som du kan finde i virtuelt under fanen **Dashboard** og port 22 til at oprette forbindelse til Linux master destination Server ved hjælp af Secure Shell.
5. Hvis du opretter forbindelse til Linux master target server over en offentlig internetforbindelse bruge Linux master target serverens offentlige virtuelle IP-adresse (fra fanen virtuelle maskiner **Dashboard** ) og offentlige slutpunktet oprettet til ssh for at logge på Linux servder.
6. Udtrække filerne fra gzippede Linux master target Server installer tjære arkiv ved at køre: *"tjære – xvzf Microsoft ASR\_UA\_8.2.0.0\_RHEL6 64\"* fra den mappe, der indeholder installationsfil,.

    ![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. Hvis du har pakket installationsprogrammet filer til en anden mappe ændres til mappen til blev som indholdet af tjære arkivere ud. Fra denne mappesti køre "sudo. / install.sh".

    ![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. Når du bliver bedt om at vælge Vælg en primær rolle **2 (Master mål)**. Lad andre interaktive installationsindstillinger til standardværdierne.
9. Vent for at fortsætte installationen og grænsefladen Host Config skal vises. Værktøjet Værtskonfigurationen for Linux master sarget Server er et værktøj i kommandolinjen. Ikke ændre størrelsen på den ssh client utility-vinduet. Brug piletasterne til at markere indstillingen **globale** og trykke på ENTER på tastaturet.

    ![](./media/site-recovery-failback-azure-to-vmware/image18.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. Angiv konfigurationsserveren (du kan finde det under fanen **Dashboard** i server configuration VM) interne IP-adressen i feltet **IP-adresse** , og tryk på ENTER. Angiv **22** i **Port** , og tryk på ENTER. 
11.  Lad **Brug HTTPS** som **Ja**, og tryk på ENTER.
12.  Angiv den adgangskode, der er oprettet i server configuration. Hvis du bruger en trykfarver klient fra en Windows-computer til at ssh til virtuelt Linux kan du bruge Skift + Insert til at indsætte indholdet af Udklipsholder. Kopiere adgangskoden til den lokale Udklipsholder ved hjælp af Ctrl + C, og brug SKIFT + Insert til at indsætte den. Tryk på ENTER.
13.  Brug tasten højre pil til at navigere for at afslutte, og tryk på ENTER. Vent for at fuldføre installationen.

    ![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Hvis du ikke lykkes eller anden grund til at registrere din Linux master target server til server configuration kan du gøre så igen ved at køre værten konfigurationsværktøj fra /usr/local/ASR/Vx/bin/hostconfigcli (først skal du angive adgangstilladelser til denne mappe ved at køre chmod som superbruger).


#### <a name="validate-master-target-registration"></a>Validere master target registrering

Kan du kontrollere, at master target serveren blev registreret til af konfigurationsserveren i Azure gendannelse af websteder samling > **Server Configuration** > **Serverdetaljer**.

>[AZURE.NOTE] Når du registrerer master destinationsadresse-server, hvis du modtager konfigurationsfejl, den virtuelle maskine måske er blevet slettet fra Azure, eller som slutpunkter er ikke konfigureret korrekt, skyldes det selvom master target konfigurationen registreres af Azure dndpoints, når master destinationen er installeret i Azure, det gælder ikke for en lokal master target server lokalt. Dette påvirker ikke failback, og du kan ignorere disse fejl. 



## <a name="step-4-protect-the-virtual-machines-to-the-on-premises-site"></a>Trin 4: Beskytte virtuelle maskiner til det lokale websted

Du har brug at beskytte FOS til webstedet, i det lokale miljø, før du mislykkes tilbage.

### <a name="before-you-begin"></a>Inden du går i gang

Når en VM er ikke kunne Azure, tilføjes der en ekstra temp drev for siden fil. Dette er en ekstra drev, der ikke er typisk kræves af din mislykkedes over VM, da det muligvis allerede har et drev, dedikeret til siden filen. Før du begynder modsat beskyttelse af virtuelle maskiner, skal du sikre dig, at dette drev er tilgængelig offline, så det ikke få beskyttet. Gøre dette på følgende måde:

1.  Åbn Computer Management og vælg Lagerstyring, så den viser en liste over diskene online og tilsluttet computeren.
2.  Vælg den midlertidige disk, der er knyttet til computeren, og vælg til at hente den offline. 

### <a name="protect-the-vms"></a>Beskytte FOS

1. Markér staterne virtuelt at sikre, at de er ikke i Azure-portalen.

    ![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. Start vContinuum på din computer.

    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. Klik på **Ny beskyttelse** , og Vælg systemtype handling på 

4.  I det nye vindue, Åbn, Vælg den **type OS** > **Få mere at vide** om FOS ønskede mislykkes tilbage. Identificere **primære serverdetaljer**, og vælg de virtuelle maskiner, som du vil beskytte. FOS er angivet under vCenter værtsnavnet, som de havde på, før failover.
5.  Når du markerer en virtuel maskine til at beskytte (og den mislykkedes allerede til Azure) indeholder to poster til den virtuelle maskine i et pop op-vindue. Dette skyldes, at server configuration registrerer to forekomster af de virtuelle maskiner, der er registreret til den. Du skal fjerne posten for den lokale VM, så du kan beskytte den korrekte VM. For at identificere den korrekte Azure VM post her skal du logge på Azure VM og gå til C:\Program Files (x86) \Microsoft Azure Site Recovery\Application Data\etc. I drscout.conf fil identificere host-ID. Hold det element, du har fundet host-ID på VM i dialogboksen vContinuum. Slette alle andre poster. Du kan referere til dens IP-adresse for at vælge den korrekte VM. De IP-adresse område i det lokale miljø, vil være den lokale VM.

    ![](./media/site-recovery-failback-azure-to-vmware/image22.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. Stoppe den virtuelle maskine på vCenter-serveren. Du kan også slette FOS i det lokale miljø.
7. Angiv derefter lokal MT server, du vil beskytte FOS. Oprette forbindelse til vCenter serveren, du vil mislykkes tilbage for at gøre dette.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. Vælg den master mål-server, der er baseret på værten, du vil gendanne VM.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. Angiv indstillingen gentagelse for hver af virtuelle maskiner. For at gøre dette skal du vælge gendannelse side datastore som FOS kan gendannes. Tabellen opsummerer de forskellige indstillinger, skal du angive for hver VM.

    ![](./media/site-recovery-failback-azure-to-vmware/image25.png)

    **Indstillingen** | **Indstillingen anbefales værdi**
    ---|---
    Procesfarver IP-adresse | Vælg proces-server, der er implementeret i Azure
    Opbevaring størrelse i MB| 
    Opbevaring værdi | 1
    Dage/timer | Dage
    Konsistens interval | 1
    Vælg destination datastore | Den datastore, der skal findes på webstedet gendannelse. Datalager skal nok ledig plads, og være tilgængelige til værten ESX, du vil gendanne den virtuelle maskine.


10. Konfigurere de egenskaber, som den virtuelle maskine opnår ved failover til lokale websted. Egenskaberne, der er opsummeret i følgende tabel.

    ![](./media/site-recovery-failback-azure-to-vmware/image26.png)

    **Egenskaben** | **Detaljer**
    ---|---
    Netværkskonfiguration| Markere det for hvert netværkskort, der er fundet, og klik på **Skift** for at konfigurere den virtuelle maskine failback IP-adresse. 
    Hardwarekonfiguration| Angiv CPU'EN og hukommelsen VM. Indstillinger kan anvendes på alle FOS, du forsøger at beskytte. For at identificere de korrekte værdier for CPU'EN og hukommelsen, kan du referere til IAAS FOS rolle størrelse og se antallet af kerner og hukommelse, der er tildelt.
    Vist navn | Efter failback til en lokal installation, kan du omdøbe de virtuelle maskiner, som de skal vises i oversigten vCenter. Standardnavnet er værtsnavnet virtuelt computer. Hvis du vil identificere VM navnet, kan du referere til listen VM i gruppen beskyttelse.
    Konfiguration af NAT | Det er gennemgået detaljeret nedenfor

    ![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### <a name="configure-nat-settings"></a>Konfigurere indstillinger for NAT

1. Hvis du vil aktivere beskyttelse af virtuelle maskiner, skal to kommunikationskanaler oprettes. Den første kanal er mellem den virtuelle maskine og processerveren. Denne kanal indsamler dataene fra VM og sender den til processerveren, som sender derefter dataene til master destinationsadresse-serveren. Hvis processen serveren og den virtuelle maskine skal beskyttes er i den samme Azure virtuelt netværk du ikke behøver at bruge NAT-indstillinger. Ellers specificere NAT-indstillinger. Få vist den offentlige IP-adresse på processerveren, i Azure. 

    ![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. Den anden kanal er mellem proces server og master destinationsadresse-server. Muligheden for at bruge NAT eller ej, afhænger af, om du bruger en VPN-baseret forbindelse eller kommunikere via internettet. Vælg ikke NAt, hvis du bruger VPN, men kun, hvis du bruger en internetforbindelse.

    ![](./media/site-recovery-failback-azure-to-vmware/image29.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. Hvis du ikke har slettet lokale virtuelle maskiner som angivet, og hvis datastore du mislykkes tilbage til stadig indeholder den gamle VMDK skal du sikre, at failback VM bliver oprettet i en ny placering. Hvis du vil vælge indstillingerne, **Avanceret** og angive en alternativ mappe til at gendanne til **Navnet Mappeindstillinger**. Lade de andre indstillinger med deres standardindstillinger. Anvende Mappeindstillinger navn på alle servere.

    ![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. Køre en parathed Kontroller, at de virtuelle computere er klar til at være beskyttet tilbage til en lokal installation. 

    ![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. Vent, til at fuldføre. Hvis det er korrekt på alle FOS kan du angive et navn til den beskyttelsesplan. Klik derefter på **Beskyt** skal begynde.

    ![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. Du kan overvåge statussen i vContinuum.

    ![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. Du kan overvåge VM beskyttelse i portalen gendannelse af websteder efter trinnet **Aktivering af beskyttelse planlægge** afsluttes.

    ![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. Du kan se den nøjagtige status at klikke på VM og overvågning af dens status.

    ![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## <a name="prepare-the-failback-plan"></a>Forberede failback planen

Du kan forberede en failback plan ved hjælp af vContinuum, så programmet kan være failed tilbage til webstedet, i det lokale miljø til enhver tid. Planerne gendannelse ligner meget de gendannelse planer i gendannelse af websteder.

1.  Start vContinuum, og vælg **Administrer planer** > **Gendan.** Du kan se på liste over alle dine planer, der er brugt til at mislykkes over FOS. Du kan bruge de samme planer skal gendannes.

    ![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. Vælg beskyttelsesplan og alle de FOS, du vil gendanne den. Når du vælger hver VM kan du se flere oplysninger, herunder target ESX server og kilde VM disken. Klik på **Næste** for at starte guiden gendanne, og vælg de FOS, du vil gendanne.

    ![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. Du kan gendanne baseret på flere indstillinger, men vi anbefaler, at du kan bruge **Seneste kode** , og vælg **Anvend for alle FOS** at sikre, at de nyeste data fra den virtuelle maskine, der skal bruges.
4. Køre den **parathed Kontrollér.** Dette vil kontrollere, om de rigtige parametre er konfigureret til at aktivere VM gendannelse. Klik på **Næste** , hvis alle Kontroller lykkes. Hvis ikke kontrollere logfilen og løse problemerne.

    ![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  Sørg for, at gendannelsesindstillingerne for er indstillet korrekt i **VM konfiguration** . Du kan ændre indstillingerne for VM, hvis det er nødvendigt.

    ![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. Gennemgå listen over virtuelle maskiner, der bliver gendannet, og Angiv en gendannelse rækkefølge. Bemærk, FOS vises ved hjælp af værtsnavn computer. Det kan være svært at knytte værtsnavn computer til den virtuelle maskine.
Gå til de virtuelle maskiner **Dashboard** i Azure for at tilknytte navnene, og Markér værtsnavn VM.

    ![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. Angiv et navn, plan, og vælg **Gendan senere**. Vi anbefaler for at gendanne senere, da den indledende beskyttelse ikke muligvis er fuldført. 
11. Klik på **Gendan** til at gemme planen eller udløse at genoprettelsen, hvis du har valgt for at gendanne nu og ikke senere. Du kan kontrollere status for gendannelse for at se, hvis planen er blevet gemt.

    ![](./media/site-recovery-failback-azure-to-vmware/image42.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## <a name="recover-virtual-machines"></a>Gendanne virtuelle maskiner

Når planen er blevet oprettet, kan du gendanne de virtuelle maskiner. Før du markerer, virtuelle maskiner har fuldført synkronisering. Hvis gentagelse status viser OK beskyttelsen er fuldført, og den frigivne Produktionsordre grænseværdi er opfyldt. Du kan kontrollere tilstand i egenskaberne VM.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Slå fra Azure virtuelle maskiner, før du kan starte at genoprettelsen. Dette sikrer, at der er ingen delt hjerneforskningen og, at brugerne kun skal have adgang til én kopi af programmet. 


1.  Starte den gemte plan. Vælg **skærm** planer i vContinuum. Dette viser en liste over alle dine planer, der er udført.

    ![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  Vælg planen, i **gendannelse** , og klik på **Start**. Du kan overvåge gendannelse. Når FOS har været slået på du kan oprette forbindelse til dem i vCenter.

    ![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## <a name="protect-to-azure-again-after-failback"></a>Beskytte til Azure igen efter failback

Når failback er fuldført skal du sandsynligvis vil beskytte virtuelle maskiner igen. Gøre dette på følgende måde:

1.  Kontrollér, at de virtuelle maskiner lokale arbejder og, programmer kan nås.
2.  Vælg de virtuelle maskiner i portalen Azure gendannelse af websteder, og slette dem. Vælg for at deaktivere beskyttelse af virtuelle maskiner. Dette sikrer, at FOS ikke længere er beskyttet.
3.  I Azure slette de fejlbehæftede over Azure virtuelle maskiner
4.  Slet den gamle virtuelle maskine på vSpehere. Dette er de VM'er, der tidligere mislykkedes Azure.
5.  Beskytte VM'er, der senest mislykkedes over i portalen gendannelse af websteder. Når de er beskyttet, kan du føje dem til en plan for gendannelse.
 
## <a name="next-steps"></a>Næste trin



- [Læs om](site-recovery-vmware-to-azure-classic.md) replikering VMware virtuelle maskiner og fysiske servere til Azure ved hjælp af den forbedrede installation.

 
