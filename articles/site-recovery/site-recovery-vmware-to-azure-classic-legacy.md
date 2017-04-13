<properties
    pageTitle="Gentage VMware virtuelle maskiner og fysiske servere til Azure med Azure gendannelse af websteder (ældre) | Microsoft Azure" 
    description="Beskriver, hvordan du overfører lokale FOS og Windows/Linux fysiske servere til Azure bruge Azure gendannelse af websteder i en ældre installation i portalen klassisk." 
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

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy"></a>Gentage VMware virtuelle maskiner og fysiske servere til Azure med Azure gendannelse af websteder ved hjælp af portalen klassisk (ældre)

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-vmware-to-azure.md)
- [Klassisk Portal](site-recovery-vmware-to-azure-classic.md)
- [Klassisk Portal (ældre)](site-recovery-vmware-to-azure-classic-legacy.md)


Velkommen til gendannelse af Azure websteder! I denne artikel beskrives en ældre installation til replikering lokale VMware virtuelle maskiner eller Windows/Linux fysiske servere til Azure ved hjælp af Azure gendannelse af websteder i portalen klassisk.

## <a name="overview"></a>Oversigt

Virksomheder har brug for en strategi for BCDR, der bestemmer, hvordan apps, arbejdsmængder og data holde køre og være tilgængelige under planlagte og ikke-planlagt nedetid, og gendanne til normal arbejdsbetingelser så tidligt som muligt. Din strategi for BCDR skal beholde forretningsdata sikre og kan gendannes, og sikre, at arbejdsbelastninger forbliver konstant er tilgængelig, når der udsendes nedbrud.

Gendannelse af websteder er en Azure-tjeneste, der bidrager til din strategi for BCDR ved orchestrating gentagelse af lokale fysiske servere og virtuelle maskiner til skyen (Azure) eller til en sekundær datacenter. Hvis der opstår afbrydelser i din primære placering, du ikke sekundær placeringen for at holde apps og arbejdsbelastninger, som er tilgængelige. Du ikke tilbage til din primære placering, når det vender tilbage til normal handlinger. Se mere i [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md)


>[AZURE.WARNING] I denne artikel indeholder **ældre instruktioner**. Ikke bruge den til nye installationer. I stedet [følge disse instruktioner](site-recovery-vmware-to-azure.md) til at udrulle gendannelse af websteder i Azure portal eller [bruge disse instruktioner](site-recovery-vmware-to-azure-classic.md) til at konfigurere den forbedrede installation i portalen klassisk. Hvis du har allerede installeret ved hjælp af metoden beskrevet i denne artikel, anbefaler vi, at du overfører til den forbedrede installation i portalen klassisk.


## <a name="migrate-to-the-enhanced-deployment"></a>Overføre til den forbedrede installation

Dette afsnit er kun relevant, hvis du allerede har installeret gendannelse af websteder ved hjælp af vejledningen i denne artikel.

Du vil overføre din eksisterende installation, skal du:

1. Installer nye gendannelse af websteder komponenter i dit lokale websted.
2. Konfigurere legitimationsoplysninger til automatisk registrering af VMware FOS på den nye konfiguration-server.
3. Opdag VMware servere med den nye konfiguration-server.
3. Oprette en ny gruppe i beskyttelse med den nye konfiguration-server.


Før du går i gang:

- Vi anbefaler, at du konfigurerer et vedligeholdelsesvindue for overførsel.
- Indstillingen **Overføre computere** er kun tilgængelig, hvis du har eksisterende beskyttelse grupper, der er oprettet i en ældre installation.
- Når du har fuldført trinnene overførslen kan det tage 15 minutter eller længere tid at opdatere legitimationsoplysningerne, og for at finde og opdatere virtuelle maskiner, så du kan føje dem til en beskyttelse gruppe. Du kan opdatere manuelt i stedet for at vente. 

Overføre på følgende måde:

1. Læs mere om [udvidet installation i portalen klassisk](site-recovery-vmware-to-azure-classic.md#enhanced-deployment). Gennemse den forbedrede [arkitektur](site-recovery-vmware-to-azure-classic.md#scenario-architecture)og [forudsætninger](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment).
2. Fjerne tjenesten mobilitet fra computere, du i øjeblikket replikering. En ny version af tjenesten installeres på computerne, når du føjer dem til den nye gruppe beskyttelse.
3. Hente en [samling af legitimationsoplysninger registreringsnøgle](site-recovery-vmware-to-azure-classic.md#step-4-download-a-vault-registration-key) og [køre konfigurationsguiden af samlet](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server) installere server configuration, proces servernavn og master target server-komponenter. Læs mere om [Planlægning af kapacitet](site-recovery-vmware-to-azure-classic.md#capacity-planning).
4. [Konfigurere legitimationsoplysninger](site-recovery-vmware-to-azure-classic.md#step-6-set-up-credentials-for-the-vcenter-server) , der gendannelse af websteder kan bruge til at få adgang til VMware server for at se automatisk VMware FOS. Få mere at vide om [nødvendige tilladelser](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
5. Tilføj [vCenter servere eller vSphere hosts](site-recovery-vmware-to-azure-classic.md#step-7-add-vcenter-servers-and-esxi-hosts). Det kan tage 15 minutter, før mere til servere, der skal vises i portalen gendannelse af websteder.
6. Opret en [Ny beskyttelse gruppe](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Det kan tage op til 15 minutter til portalen for at opdatere så virtuelle maskiner er blevet fundet og vises. Hvis du ikke vil vente kan du fremhæve servernavnet management (ikke klikke på den) > **Opdater**.
7. Klik på **Overføre maskiner**under gruppen ny beskyttelse.

    ![Tilføj konto](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)

8. Vælg den beskyttelse gruppe, du vil overføre fra i **Vælg maskiner** , og computerne, du vil overføre.

    ![Tilføj konto](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)

9. Angiv om du vil bruge de samme indstillinger for alle de computere, og vælg den proces server og Azure lagerplads konto i **Konfigurere indstillinger for mål** . Hvis du ikke har en separat proces server dette er den IP-adressen på serveren, server configuration.


    ![Tilføj konto](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] [Overførsel af lagerplads konti](../resource-group-move-resources.md) på tværs af grupper i det samme abonnement eller på tværs af abonnementer understøttes ikke for lagerplads konti, der bruges til implementering af gendannelse af websteder.

10. I **Angiv konti**, skal du vælge den konto, du har oprettet for processen serveren for at få adgang til computeren for at overføre den nye version af tjenesten mobilitet.

    ![Tilføj konto](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)

11. Gendannelse af websteder kan overføre dine replikerede data til kontoen Azure-lager, som du angav. Du kan eventuelt genbruge kontoen lagerplads du har brugt i den ældre installation.
12. Virtuelle maskiner synkroniserer automatisk, når jobbet afsluttes. Når synkroniseringen er fuldført, kan du slette de virtuelle maskiner fra gruppen ældre beskyttelse.
13. Når alle de computere har overflyttet kan du slette gruppen ældre beskyttelse.
14. Husk at angive failover egenskaberne for computere, og Azure netværksindstillingerne, når synkroniseringen er fuldført.
15. Hvis du har eksisterende gendannelse planer, kan du overføre dem til den forbedrede installation med indstillingen **Overføre gendannelse planlægge** . Du skal kun gøre dette, når alle de beskyttede computere er blevet overført. 

    ![Tilføj konto](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

>[AZURE.NOTE] Når du er færdig med overførsel fortsætte med [udvidet artikel](site-recovery-vmware-to-azure-classic.md). Resten af artiklen ældre længere er ikke relevante, og du behøver ikke at følge mere over de trin, der er beskrevet i it **.




## <a name="what-do-i-need"></a>Hvad har jeg brug for?

Dette diagram viser komponenterne til installation.

![Ny samling](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

Her er, hvad du skal bruge:

**Komponent** | **Installation** | **Detaljer**
--- | --- | ---
**Server Configuration** | En Azure standard A3 virtuel maskine i det samme abonnement som gendannelse af websteder. | Konfiguration af server-koordinaterne kommunikationen mellem beskyttet maskiner, proces server og master target servere i Azure. Det angiver gentagelse og koordinater gendannelse i Azure, når der opstår failover.
**Master target server** | En Azure virtuelt – enten en Windows-server, som er baseret på en Windows Server 2012 R2 galleriet Hurtige (for at beskytte Windows-computere) eller som en Linux-server, der er baseret på en OpenLogic CentOS 6.6 galleriet Hurtige (for at beskytte Linux-computere).<br/><br/> Tre størrelseshåndtag indstillinger er tilgængelige – Standard A4, Standard D14 og Standard DS4.<br/><br/> Serveren er forbundet til det samme Azure netværk som server configuration.<br/><br/> Du konfigurerer i portalen gendannelse af websteder | Den modtager og bevarer replikerede data fra din beskyttet maskiner ved hjælp af vedhæftede virtuelle harddiske oprettet på blob-lager i kontoen Azure-lager.<br/><br/> Vælg Standard DS4 specifikt til konfiguration af beskyttelse af arbejdsbelastninger, der kræver ensartet høj ydeevne og lav ventetid ved hjælp af Premium lagerplads konto.
**Process-server** | En lokal virtuelle eller fysiske server, der kører Windows Server 2012 R2<br/><br/> Vi anbefaler, at det er placeret på samme netværk og LAN-segment som de computere, som du vil beskytte, men den kan køre på et andet netværk, så længe beskyttet maskiner har L3 netværkets synlighed til den.<br/><br/> Du konfigurere det og registrere den til af konfigurationsserveren i portalen gendannelse af websteder. | Beskyttet maskiner sender gentagelse data til lokal proces server. Den har en diskbaserede cache til cache gentagelse data, som den modtager. Et antal handlinger udfører på disse data.<br/><br/> Det optimerer data ved cachelagring, komprimering og kryptere den før du sender den på serveren, mastersider mål.<br/><br/> Push-installation af tjenesten mobilitet heltal.<br/><br/> Automatisk registrering af VMware virtuelle maskiner udfører.
**Lokale computere** | Lokalt VMware virtuelle maskiner, eller fysisk servere, der kører Windows eller Linux. | Du konfigurerer Replikeringsindstillinger, der anvendes en eller flere computere. Du kan ikke via en enkelt computer eller mere almindeligt, flere computere, som du har indsamlet sammen i en plan for gendannelse. 
**Mobilitet service** | Installeret på hver virtuel computer eller fysisk server, du vil beskytte<br/><br/> Kan installeres manuelt eller overføres og installeres automatisk af processen serveren, når du aktiverer gentagelse til en computer. | Tjenesten mobilitet sender data til processerveren under indledende replikering (synkroniseringen). Når maskinen befinder sig i en beskyttet tilstand (når synkroniseringen afsluttes) tjenesten mobilitet registrerer skriver til disken i hukommelsen og sender dem til processerveren. Applicationconsistency til Windows-servere opnås ved hjælp af VSS.
**Samling af Azure gendannelse af websteder** | Du opretter en samling af legitimationsoplysninger gendannelse af websteder med et Azure-abonnement og registrere servere i samling af legitimationsoplysninger. | Samling af legitimationsoplysninger koordinater og orchestrates data gentagelse, failover og genoprettelse mellem dit lokale websted og Azure.
**Replikering fungerer** | **Over internettet**– kommunikerer og replikater data fra beskyttet på lokale servere til Azure ved hjælp af sikker SSL/TLS kanal via internettet. Dette er standardindstillingen.<br/><br/> **VPN/ExpressRoute**– kommunikerer og replikater data mellem lokale servere og Azure over en VPN-forbindelse. Du skal konfigurere en VPN til-websted eller en ExpressRoute forbindelse mellem dit lokale websted og Azure netværk.<br/><br/> Du skal vælge, hvordan du vil gentage under installation af gendannelse af websteder. Du kan ikke ændre metode, når den er konfigureret uden at påvirke replikering af eksisterende computere. | Hverken indstilling kræver, at du kan åbne nogen indgående netværksporte på beskyttet computere. Al netværkskommunikation startes fra det lokale websted. 

## <a name="capacity-planning"></a>Planlægning af kapacitet

De vigtigste områder, skal du overveje:

- **Kilde-miljø**– feltet VMware infrastruktur, kilde computerindstillinger og krav.
- **Komponent servere**– processen servernavn, server configuration og master target server 

### <a name="considerations-for-the-source-environment"></a>Overvejelser i forbindelse med kilde-miljø

- **Maksimumstørrelsen for disk**– den aktuelle maksimale størrelse for den disk, kan knyttes til en virtuel maskine er 1 TB. Dermed er den maksimale størrelse på en kildedisk, der kan replikeres også begrænset til 1 TB.
- **Maksimumstørrelsen for hver datakilde**– den maksimale størrelse på en enkelt kilde maskine er 31 TB (med 31 diske) og med en forekomst af D14 klargjort til master destinationsadresse-serveren. 
- **Antal kilder per master target server**– flere kilde-computere kan være beskyttet med en enkelt master destinationsadresse-server. En enkelt kilde maskine kan dog beskyttet på tværs af flere master destinationsadresse-servere, fordi som diske gentage, der oprettes en virtuel harddisk, der afspejler størrelsen på disken på Azure blob-lager og vedhæftet som en datadisk til master destinationsadresse-serveren.  
- **Maksimal dagligt ændring i hver kilde**– der er tre faktorer, der skal betragtes som når du beslutter, anbefalede ændring i hver datakilde. Overvej target baseret kræves to IOP'ER på disken mål for hver operation på kilden. Dette skyldes, at en læsning af gamle data og en skrivning af de nye data sker på disken mål. 
    - **Daglig ændre rente, der understøttes af processerveren**– en kilde maskine ikke strækker sig over flere proces-servere. En enkelt proces server kan understøtte op til 1 TB daglige Skift rente. Derfor er 1 TB de maksimale daglige data ændres rente, der understøttes i forbindelse med en kilde maskine. 
    - **Maksimum overførselshastighed understøttes af target disken**– maksimum transportspand per kildedisk må ikke være mere end 144 GB/dag (med 8 K Skriv størrelse). Se tabellen i sektionen master mål for overførselshastighed og IOP'er af destinationen for forskellige skrive størrelser. Dette nummer skal være divideret med to, fordi hver kilde IOP genererer 2 IOP'ER på disken mål. Læs mere om [Azure skalerbarhed og ydeevne destinationer](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) , når du konfigurerer destinationen for premium lagerplads konti.
    - **Maksimum overførselshastighed understøttes af kontoen lager**– en kilde kan ikke strækker sig over flere lagerplads konti. Givet, en lagerplads konto tager op til 20.000 anmodninger sekundet og, at hver kilde IOP genererer 2 IOP'ER på master target serveren, anbefales det, at antallet af IOP'ER på tværs af kilden til 10.000. Læs mere om [Azure skalerbarhed og ydeevne destinationer](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) , når du konfigurerer kilden til premium lagerplads konti.

### <a name="considerations-for-component-servers"></a>Overvejelser i forbindelse med komponent servere

Tabel 1 opsummerer virtuelt størrelsen til konfiguration og master destinationsadresse-servere.

**Komponent** | **Udløst Azure forekomster** | **Kerner** | **Hukommelse** | **Maks diske** | **Diskstørrelse**
--- | --- | --- | --- | --- | ---
Server Configuration | Standard A3 | 4 | 7 GB | 8 | 1023 GB
Master target server | Standard A4 | 8 | 14 GB | 16 | 1023 GB
 | Standard D14 | 16 | 112 GB | 32 | 1023 GB
 | Standard DS4 | 8 | 28 GB | 16 | 1023 GB

**Tabel 1**

#### <a name="process-server-considerations"></a>Overvejelser i forbindelse med procesfarver server

Generelt afhænger proces server størrelseshåndtag af den daglige Skift rente på tværs af alle arbejdsbelastninger, som beskyttet.


- Du skal bruge tilstrækkelige Beregn til at udføre opgaver som indbygget komprimering og kryptering.
- Processen serveren bruger diskbaserede cache. Kontrollér, at anbefalede cache diskplads overførselshastigheden er tilgængelige for lette dataændringer, der er gemt i tilfælde af netværk flaskehals eller -afbrydelse. 
- Sikre tilstrækkelig båndbredde, så processen serveren kan overføre data til master target serveren for at levere kontinuerlig databeskyttelse. 

Tabel 2 indeholder en oversigt over processen server retningslinjer.

**Dataene ændres rente** | **CPU** | **Hukommelse** | **Cache diskstørrelse**| **Cache disk overførselshastighed** | **Båndbredde vandindtrængen/udgangspunkt**
--- | --- | --- | --- | --- | ---
< 300 GB | 4 vCPUs (2 sockets * 2 kerner @ 2,5 GHz) | 4 GB | 600 GB | 7 til 10 MB sekundet | 30 Mbps/21 Mbps
300 til 600 GB | 8 vCPUs (2 sockets * 4 kerner @ 2,5 GHz) | 6 GB | 600 GB | 11 til 15 MB sekundet | 60 Mbps/42 Mbps
600 GB til 1 TB | 12 vCPUs (2 sockets * 6 kerner @ 2,5 GHz) | 8 GB | 600 GB | 16 til 20 MB sekundet | 100 Mbps/70 Mbps
> 1 TB | Installere en anden proces-server | | | | 

**Tabel 2**

Hvor: 

- Vandindtrængen er download båndbredde (intranet mellem kilde- og proces server).
- Udgangspunkt er Overfør båndbredde (internet mellem proces server og master target server). Udgangspunkt tal antages gennemsnitlige 30% proces server komprimering.
- Disken en separat OS disk af minimum 128 GB anbefales for cache for alle proces-servere.
- For cache disk overførselshastighed for følgende lagringen blev brugt til udviklingskurver: 8 SAS-drev på 10 OMDR med RAID 10 konfiguration.

#### <a name="configuration-server-considerations"></a>Overvejelser i forbindelse med konfiguration server

Hver server configuration kan understøtte op til 100 kilde maskiner med 3-4 enheder. Hvis din installation er større anbefaler vi du installerer en anden konfiguration server. Se tabel 1 for standard virtuel maskine egenskaberne for server configuration. 

#### <a name="master-target-server-and-storage-account-considerations"></a>Master target server og lager konto overvejelser

Opbevaring for hver master target server omfatter en OS disk, en opbevaring lydstyrken og datadisce. Opbevaring drev vedligeholder journal over disk ændringer for varigheden af vinduet opbevaring, der er defineret i portalen gendannelse af websteder.  Se tabel 1 for egenskaberne virtuelt på serveren, mastersider mål. Tabel 3 viser, hvordan diske af A4 bruges.

**Forekomst** | **OS disk** | **Opbevaring** | **Datadisce**
--- | --- | --- | ---
 | | **Opbevaring** | **Datadisce**
Standard A4 | 1 disk (1 * 1023 GB) | 1 disk (1 * 1023 GB) | 15 diske (15 * 1023 GB)
Standard D14 |  1 disk (1 * 1023 GB) | 1 disk (1 * 1023 GB) | 31 diske (15 * 1023 GB)
Standard DS4 |  1 disk (1 * 1023 GB) | 1 disk (1 * 1023 GB) | 15 diske (15 * 1023 GB)

**Tabel 3**

Kapacitet, planlægning af mastersider target serveren afhænger:

- Azure-lager ydeevne og begrænsninger
    - Det maksimale antal, der bruges diske til et almindeligt lag VM meget, 40 om (20.000/500 IOP'ER per disk) på en enkelt lagerplads konto. Læs om [skalerbarhed mål for standard lagerplads sccounts](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts) og får vist [premium lagerplads sccounts](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts).
-   Ændre dagligt rente 
-   Opbevaring lydstyrken lagerplads.

Bemærk, at:

- Én kilde kan ikke strækker sig over flere lagerplads konti. Dette gælder for data disken, gå til lagerplads konti, der er markeret, når du konfigurerer beskyttelse. Operativsystemet og opbevaring diskene normalt gå til kontoen, der automatisk udløst lagerplads.
- Opbevaring lagerplads lydstyrken påkrævet afhænger af den daglige Skift hastighed og antallet af dage, opbevaring. Den nødvendige per master target server opbevaring lagerplads = samlet transportspand fra kilde dagen * antal opbevaring dage. 
- Hver master target server har kun én opbevaring lydstyrken. Opbevaring lydstyrken er fordelt over diskene tilknyttet master destinationsadresse-server. Eksempel:
    - Hvis der er en kilde maskine med 5 diske og hver disk genererer 120 IOP'ER (8K størrelse) på kilden, betyder det, at 240 IOP'ER per disk (2 handlinger på target disken per kilde EY). 240 IOP'ER er inden for Azure per disk IOP'ER grænsen på 500.
    - På lydstyrken opbevaring det bliver 120 * 5 = 600 IOP'ER og det kan være tubus flasker. I dette scenarie skal ville være en god strategi for at føje flere diske til opbevaring lydstyrken og strækker sig over den på tværs af, som en RAID stribe konfiguration. Dette vil forbedre ydeevnen, fordi IOP'ER fordeles på tværs af flere drev. Antallet af drev der skal føjes til opbevaring lydstyrken bliver på følgende måde:
        - Samlet IOP'ER fra kilde miljø / 500
        - Samlet transportspand om dagen fra kilde-miljø (ikke-komprimeret) / 287 GB. 287 GB er det maksimale antal gennemløb, der understøttes af en destination disk dagen. Denne metrikværdi varierer afhængigt af Skrivestørrelsen med en faktor på 8K, fordi i dette tilfælde 8K er der antages Skriv størrelse. Eksempelvis hvis Skriv størrelse er 4K bliver overførselshastighed 287/2. Og hvis Skriv størrelse er 16K overførselshastighed bliver 287 * 2.
- Antallet af lagerplads konti påkrævet = samlet kilde IOP'er/10000.


## <a name="before-you-start"></a>Før du starter

**Komponent** | **Krav** | **Detaljer**
--- | --- | --- 
**Azure-konto** | Du skal bruge en [Microsoft Azure](https://azure.microsoft.com/) -konto. Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
**Azure-lager** | Du skal bruge en Azure-lager-konto til at gemme replikerede data<br/><br/> Enten kontoen skal være en [Geografisk overflødige lagerplads standardkonto](../storage/storage-redundancy.md#geo-redundant-storage) eller [Premium lagerplads konto](../storage/storage-premium-storage.md).<br/><br/> Det skal i det samme område som tjenesten Azure gendannelse af websteder, og at blive knyttet til den samme abonnement. Vi understøtter ikke flytning af lagerplads konti, der er oprettet ved hjælp af [nye Azure portal](../storage/storage-create-storage-account.md) på tværs af grupper.<br/><br/> Læs [Introduktion til Microsoft Azure-lager](../storage/storage-introduction.md) for at få mere at vide
**Azure virtuelt netværk** | Du skal have en Azure virtuelt netværk, som server configuration og master target server som skal installeres. Det skal være i samme abonnement og område som samling af legitimationsoplysninger Azure gendannelse af websteder. Hvis du vil gentage data via en ExpressRoute eller VPN-forbindelse skal det Azure virtuelle netværk have forbindelse til dit lokale netværk over en ExpressRoute dataforbindelse eller en VPN-websted til websted.
**Azure ressourcer** | Sørg for, at du har tilstrækkelige Azure ressourcer til at installere alle komponenter. Læs mere i [Azure abonnement begrænsninger](../azure-subscription-service-limits.md).
**Azure virtuelle maskiner** | Virtuelle maskiner, du vil beskytte skal være i overensstemmelse med [Azure forudsætninger](site-recovery-best-practices.md).<br/><br/> **Disken Tæl**– maksimalt 31 diske kan understøttes på en enkelt beskyttet server<br/><br/> **Diskstørrelser**– individuelle diskkapacitet ikke bør være mere end 1023 GB<br/><br/> **Clustering**– grupperet servere understøttes ikke<br/><br/> **Start**– Unified Extensible Firmware Interface UEFI () / Extensible Firmware Interface(EFI) start understøttes ikke<br/><br/> **Enheder**– Bitlocker krypterede drev ikke understøttes<br/><br/> **Servernavne**– navne skal indeholde mellem 1 og 63 tegn (bogstaver, tal og bindestreger). Navnet skal starte med et bogstav eller tal og slutter med et bogstav eller et tal. Du kan ændre navnet på Azure, når en maskine er beskyttet.
**Server Configuration** | Standard A3 virtuelt, der er baseret på en Azure websted gendannelse Windows Server 2012 R2 galleriet Hurtige oprettes i dit abonnement på server configuration. Den er oprettet som den første forekomst på en ny tjeneste i skyen. Hvis du vælger offentlige Internet connectivity filtype for server configuration oprettes skytjenesten med en reserverede offentlige IP-adresse.<br/><br/> Installationsstien skal være i engelske tegn.
**Master target server** | Azure virtuelt, standard A4, D14 eller DS4.<br/><br/> Installationsstien skal være i engelske tegn. For eksempel være stien **/usr/local/ASR** til en masterside target server, der kører Linux.
**Process-server** | Du kan implementere processen server på fysiske eller virtuelt, der kører Windows Server 2012 R2 med de seneste opdateringer. Installere på C: /.<br/><br/> Vi anbefaler, at du placerer serveren på samme netværk og undernet som de computere, du vil beskytte.<br/><br/> Installere VMware vSphere CLI 5.5.0 på processerveren. Komponenten VMware vSphere CLI er påkrævet på serveren processen for at finde virtuelle maskiner, der administreres af en vCenter server eller virtuelle maskiner, der kører på en ESXi vært.<br/><br/> Installationsstien skal være i engelske tegn.<br/><br/> Referencer-filsystemet understøttes ikke.
**VMware** | En VMware vCenter server, der administrerer din VMware vSphere hypervisors. Det skal køre vCenter version 5.1 eller 5.5 med de seneste opdateringer.<br/><br/> En eller flere vSphere hypervisors med VMware virtuelle maskiner, du vil beskytte. Hypervisor skal køre ESX/ESXi version 5.1 eller 5.5 med de seneste opdateringer.<br/><br/> VMware virtuelle maskiner bør have VMware værktøjer installeret og kører. 
**Windows-computere** | Beskyttet fysiske servere eller VMware virtuelle maskiner, der kører Windows har en række krav.<br/><br/> A understøttes 64-bit operativsystem: **Windows Server 2012 R2**, **Windows Server 2012**, eller **Windows Server 2008 R2 med på minimum SP1**.<br/><br/> Værtsnavn, tilslutningspunkter, enhedsnavne, Windows system sti (f.eks: C:\Windows) skal være på engelsk kun.<br/><br/> Operativsystemet skal være installeret på C:\ drev.<br/><br/> Kun grundlæggende disk understøttes. Dynamiske diske understøttes ikke.<br/><br/> Firewallregler på beskyttet maskiner skal muligt at nå mål konfiguration og master serverne i Azure.p ><p>Du skal angive en administratorkonto (skal være en lokal administrator på Windows-computer) til opslagsnål Installer tjenesten mobilitet på Windows-servere. Hvis den angivne konto er en ikke-domænekonto skal du deaktivere Remote brugeradgang kontrolelement på den lokale computer. Hvis du vil gøre dette Tilføj posten LocalAccountTokenFilterPolicy DWORD registreringsdatabasen med værdien 1 under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Til at tilføje posten i registreringsdatabasen fra en CLI Åbn cmd eller powershell og skrive **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. Du kan [Få mere at vide](https://msdn.microsoft.com/library/aa826699.aspx) om adgangskontrol.<br/><br/> Efter failover, hvis du vil oprette forbindelse til Windows virtuelle maskiner i Azure med Fjernskrivebord skal du kontrollere, at Fjernskrivebord er aktiveret for den lokale computer. Hvis du ikke opretter forbindelse via VPN, bør firewallregler tillade forbindelser til Fjernskrivebord via internettet.
**Linux-computere** | Et understøttede 64-bit operativsystem: **Centos 6.4, 6.5, 6.6**; **Oracle Enterprise Linux 6.4, 6.5 kører Red Hat kompatible kernen eller Unbreakable Enterprise kernen version 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.<br/><br/> Firewallregler på beskyttet maskiner skal give vedkommende mulighed at gå til konfiguration og master target servere i Azure.<br/><br/> Fjernsystemet filer på beskyttet maskiner skal indeholde poster, der er tilknyttet IP-adresser, der er knyttet til alle netværkskort den lokale værtsnavn <br/><br/> Hvis du vil oprette forbindelse til en Azure virtuelt kører Linux efter failover ved hjælp af en Secure Shell-klient (ssh), skal du sikre dig, Secure Shell-tjenesten på den beskyttede computer er indstillet til at starte automatisk på systemstart, og at firewallregler tillader en ssh forbindelse til den.<br/><br/> Værtsnavn, tilslutningspunkter, navne på enheder, og Linux system stier og filnavne (f.eks/osv /; /usr) skal være på engelsk kun.<br/><br/> Beskyttelse kan aktiveres for lokale maskiner med følgende opbevaring:-<br>Filsystem: EXT3, ETX4, ReiserFS, XFS<br>MultiPath software-enhed Mapper (multipath)<br>Lydstyrken manager: LVM2<br>Fysiske servere med HP CCISS controller lagerplads understøttes ikke.
**Fra tredjepart** | Nogle installation komponenter i dette scenarie afhænger af software fra tredjepart til at fungere korrekt. Se en komplet liste [softwaremeddelelser fra tredjepart og oplysninger](#third-party)


### <a name="network-connectivity"></a>Netværksforbindelsen

Du har to muligheder, når du konfigurerer netværksforbindelser mellem dit lokale websted og det Azure virtuelle netværk, som infrastruktur komponenterne (server configuration, mastersider target servere) er installeret. Du skal beslutte, hvilken indstilling du netværk connectivity skal bruge, før du kan installere serverens konfiguration. Du skal vælge denne indstilling på tidspunktet for installationen. Det kan ikke ændres senere.

**Internet:** Der sker via en sikker forbindelse SSL/TLS fra det lokale til offentlige slutpunkterne på konfiguration og master target servere, kommunikation og replikering af data mellem de lokale servere (process-server, beskyttet computere) og Azure infrastruktur komponent servere (server configuration, mastersider target server). (Den eneste undtagelse er forbindelsen mellem processerveren og den overordnede mål server på TCP-port 9080 som er ikke-krypteret. Kun kontrolelement oplysninger i forbindelse med replikering protokollen til konfiguration af replikering er udvekslet på denne forbindelse.)

![Installation diagramvisning internet](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**: der sker via en VPN-forbindelse mellem dit lokale netværk og Azure virtuelle netværket som server configuration og master target servere er installeret, kommunikation og replikering af data mellem de lokale servere (process-server, beskyttet computere) og Azure infrastruktur komponent servere (server configuration, mastersider target server). Sørg for, at dit lokale netværk er tilsluttet Azure virtuelle netværk ved en ExpressRoute dataforbindelse eller en-til-websted VPN-forbindelse.

![Fordelingsdiagram VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)


## <a name="step-1-create-a-vault"></a>Trin 1: Oprette en samling af legitimationsoplysninger

1. Log på [administrationsportalen til](https://portal.azure.com).


2. Udvid **Datatjenester** > **Gendannelsestjenester** , og klik på **Websted gendannelse samling**.


3. Klik på **Opret et nyt** > **Hurtig oprettelse**.

4. Angiv et fuldt navn til at identificere samling af legitimationsoplysninger i **navnet**.

5. Vælg det geografiske område for samling af legitimationsoplysninger i **område**. Understøttede områder se for at kontrollere geografiske tilgængelighed i [Azure websted gendannelse priser detaljer](https://azure.microsoft.com/pricing/details/site-recovery/)

6. Klik på **Opret samling**.

    ![Ny samling](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

Kontrollere vises på statuslinjen for at bekræfte, at samling af legitimationsoplysninger blev oprettet. Samling af legitimationsoplysninger, vises som **aktiv** på hovedsiden **Gendannelsestjenester** .

## <a name="step-2-deploy-a-configuration-server"></a>Trin 2: Installere en server configuration

### <a name="configure-server-settings"></a>Konfigurer serverindstillinger

1. Klik på samling af legitimationsoplysninger for at åbne siden Hurtig Start på siden **Gendannelsestjenester** . Også du kan åbne Hurtig Start når som helst ved hjælp af ikonet.

    ![Ikon for Hurtig Start](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)

2. Vælg **mellem et lokalt websted med VMware/fysiske servere og Azure**på rullelisten.
3. Klik på **Installere Server Configuration**i **Forberede Target(Azure) ressourcer** .

    ![Installere server configuration](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)

4. Angiv i **Nye konfigurationsoplysninger Server** :

    - Et navn til den server configuration og legitimationsoplysninger for at oprette forbindelse til den.
    - I typen netværk connectivity du rullelisten Vælg **Offentlige internettet** eller **VPN**. Bemærk, at du ikke kan ændre denne indstilling, efter den er anvendt.
    - Vælg det Azure netværk, som serveren, der skal være placeret. Hvis du bruger VPN foretage er sikker Azure netværket forbundet til dit lokale netværk som forventet. 
    - Angive den interne IP-adresse og undernet, der skal tildeles til serveren. Bemærk, at de første fire IP-adresser i en hvilken som helst undernet er reserveret til interne Azure brugen. Brug en anden tilgængelige IP-adresse.
    
    ![Installere server configuration](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)

5. Når du klikker på **OK** en standard A3 virtuel maskine, der er baseret på en Azure websted gendannelse Windows Server 2012 R2 galleriet Hurtige oprettes i dit abonnement på server configuration. Den er oprettet som den første forekomst på en ny tjeneste i skyen. Hvis du har valgt at oprette forbindelse via internettet oprettes skytjenesten med en reserverede offentlige IP-adresse. Du kan overvåge statussen i fanen **job** .

    ![Overvåge forløbet](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)

6.  Hvis du opretter forbindelse via internettet, efter server configuration tildeles udløst note den offentlige IP-adresse til det på siden **virtuelle maskiner** i portalen Azure. Klik derefter på **slutpunkter** fanen note offentlige HTTPS-porten tilknyttet private port 443. Du skal bruge disse oplysninger senere, når du har registreret master mål og proces servere med af konfigurationsserveren. Af konfigurationsserveren er installeret med disse slutpunkter:

    - HTTPS: En offentlig port bruges til at koordinere kommunikationen mellem komponent servere og Azure via internettet. Private port 443 bruges til at koordinere kommunikationen mellem komponent servere og Azure over VPN.
    - Brugerdefineret: En offentlig port bruges til failback værktøjet kommunikation via internettet. Private port 9443 bruges til failback værktøjet kommunikation over VPN.
    - PowerShell: Private port 5986
    - Fjernskrivebord: Private port 3389
    
    ![VM slutpunkter](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

    >[AZURE.WARNING] Ikke slette eller ændre offentlig eller privat portnummeret på en hvilken som helst slutpunkter oprettes under konfiguration serverinstallation.

Server configuration skal være installeret i en automatisk oprettede Azure skybaseret tjeneste med en reserverede IP-adresse. Reserverede adressen skal bruges til at sikre, at konfigurationen server skyen service IP-adressen forbliver de samme på tværs af genstarter af de virtuelle maskiner (herunder server configuration) på skytjenesten. Reserverede offentlige IP-adressen skal være manuelt reserverede, når server configuration ikke er i funktion eller det kan være reserveret. Der er et standardgrænse på 20 reserveret offentlige IP-adresser per abonnement. Du kan [Få mere at vide](../virtual-network/virtual-networks-reserved-private-ip.md) om reserveret IP-adresser. 

### <a name="register-the-configuration-server-in-the-vault"></a>Registrere server configuration i samling af legitimationsoplysninger

1. Klik på **Forberede Target ressourcer**på siden **Hurtig Start**  > **hente en registreringsnøgle**. Filen vigtige genereres automatisk. Det er gyldige for 5 dage, efter den er oprettet. Kopiere den til af konfigurationsserveren.
2. Vælg konfigurationsserveren på virtuelle maskiner listen i **virtuelle maskiner** . Åbn fanen **Dashboard** , og klik på **Opret forbindelse**. **Åbne** den hentede RDP-fil til at logge på konfigurationsserveren ved hjælp af Fjernskrivebord. Hvis du bruger VPN, skal du bruge den interne IP-adresse (den adresse, du angav, da du installerede server configuration) til en Fjernskrivebord forbindelse fra det lokale websted. Azure websted gendannelse konfiguration Server konfigurationsguiden køres automatisk, når du logger på for første gang.

    ![Registrering](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)

3. Klik på **jeg accepterer** for at hente og installere MySQL i **Installation af Software fra tredjepart** .

    ![MySQL-installation](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)

4. Oprette legitimationsoplysninger for at logge på MySQL server-forekomsten i **Detaljer om MySQL-Server** .

    ![MySQL-legitimationsoplysninger](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)

5. Angiv, hvordan server configuration opretter forbindelse til internettet i **Internetindstillinger** . Bemærk, at:

    - Hvis du vil bruge en brugerdefineret proxy skal du konfigurere det, før du installerer udbyderen.
    - Når du klikker på **Næste** kører en test for at kontrollere proxy forbindelsen.
    - Hvis du ønsker at bruge en brugerdefineret proxy, eller din standardproxy kræver godkendelse, skal du angive proxy oplysninger, herunder den adresse, port og legitimationsoplysninger.
    - De følgende URL-adresser skal være tilgængeligt via proxyen:
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Hvis du har IP-adresse-baserede sikre firewallregler, reglerne, der er angivet til at tillade kommunikation fra server configuration til de IP-adresser, der er beskrevet i [Azure Datacenter IP-områder](https://msdn.microsoft.com/library/azure/dn175718.aspx) og HTTPS (443) protocol. Du skulle hvid liste IP-områder af det Azure område, du vil bruge, og som Vest US.

    ![Proxy-registrering](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)

6. Angiv på hvilke sprog du vil fejlmeddelelser skal vises i **Udbyder fejl lokalisering meddelelsesindstillingerne** .

    ![Fejl meddelelse registrering](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)

7. Gennemse, og vælg den vigtige fil, du kopierede til serveren i **Tilmelding til Azure webstedet gendannelse** .

    ![Vigtige fil registrering](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)

8. Vælg disse indstillinger på fuldførelsessiden af guiden:

    - Vælg **Start konto Management dialogboksen** for at angive, at dialogboksen Administrer konti skal åbnes, når du har afsluttet guiden.
    - Vælg **Opret et ikon for Cspsconfigtool på skrivebordet** for at føje en skrivebordsgenvej på konfigurationsserveren, så du kan åbne dialogboksen **Administrer konti** til enhver tid uden at skulle køre guiden igen.

    ![Fuldført registrering](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)

9. Klik på **Udfør** for at fuldføre guiden. Der genereres en adgangskode. Kopiere den til et sikkert sted. Du skal bruge den til at godkende og registrere processen og master target servere med af konfigurationsserveren. Det bruges også til at sikre kanal integritet i konfiguration serverkommunikation. Du kan gendanne adgangskoden, men derefter du er nødt til at registrere igen master destinationen og behandle servere, der bruger den nye adgangskode.

    ![Adgangskoden](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

Efter registrering vises server configuration på siden **Konfiguration af servere** i samling af legitimationsoplysninger.

### <a name="set-up-and-manage-accounts"></a>Konfigurere og administrere konti

Under installationen gendannelse af websteder, som legitimationsoplysninger for følgende handlinger:

- En VMware konto, så thatSite gendannelse kan automatisk registrering FOS på vCenter servere eller vSphere hosts. 
- Når du tilføjer maskiner for beskyttelse, så gendannelse af websteder kan installere tjenesten mobilitet på dem.

Når du har registreret server configuration kan du åbne dialogboksen **Administrer konti** for at tilføje og administrere konti, der skal bruges til disse handlinger. Der er flere måder at gøre dette:

- Åbn genvejen du valgt at være oprettet for dialogboksen på den sidste side i installationsprogrammet til konfiguration af server (cspsconfigtool).
- Åbn dialogboksen på Afslut af konfiguration server installation.

1. Klik på **Tilføj konto**i **Administrer konti** . Du kan også ændre og slette eksisterende konti.

    ![Administrere konti](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)

2. Angiv navnet på en konto skal bruges i **Account Details** i Azure og legitimationsoplysninger (domæne/brugernavn). 

    ![Administrere konti](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### <a name="connect-to-the-configuration-server"></a>Oprette forbindelse til konfigurationsserveren 

Der er to måder at oprette forbindelse til server configuration:

- Via en VPN-til-websted eller ExpressRoute forbindelse
- Via internettet 

Bemærk, at:

- En Internetforbindelse bruger slutpunkterne for den virtuelle maskine sammen med den offentlige virtuelle IP-adresse på serveren.
- En VPN-forbindelse bruger interne IP-adressen på serveren, sammen med de private porte slutpunkt.
- Det er en enkeltstående beslutning om at beslutte, om at oprette forbindelse (CTRL og gentagelse data) fra dine lokale servere til forskellige komponent servere (server configuration, mastersider target server) kører i Azure via en VPN-forbindelse eller på internettet. Du kan ikke ændre denne indstilling bagefter. Hvis du ikke gør skal du geninstallere dette scenario og reprotect dine maskiner.  


## <a name="step-3-deploy-the-master-target-server"></a>Trin 3: Installere master destinationsadresse-server

1. Klik på **Forberede Target(Azure) ressourcer** > **Implementer master target server**.
2. Angiv oplysninger om server master mål og legitimationsoplysninger. Serveren, som skal installeres i det samme Azure netværk som server configuration. Når du klikker på at fuldføre en Azure virtuelt oprettes med et billede af galleriet Windows eller Linux.

    ![Target serverindstillinger](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

Bemærk, at de første fire IP-adresser i en hvilken som helst undernet er reserveret til interne Azure brugen. Angiv eventuelle andre tilgængelige IP-adresse.

>[AZURE.NOTE] Vælg Standard DS4, når du konfigurerer beskyttelse til arbejdsbelastninger, der kræver ensartet høj i/o-ydeevne og lav ventetid for at være vært for i/o-intensivt arbejdsmængder ved hjælp af [Premium lagerplads konto](../storage/storage-premium-storage.md).


3. En Windows styre target server VM er oprettet med disse slutpunkter. Bemærk, at offentlige slutpunkter oprettes kun, hvis din forbindelse via internettet.

    - Brugerdefineret: Offentlige port bruges af processen serveren til at sende replikeringsdata via internettet. Private port 9443 bruges af processen serveren til at sende gentagelse data til master target serveren via VPN.
    - Bruger1: Offentlige port bruges af processen serveren til at sende metadata via internettet. Private port 9080 bruges af processen serveren til at sende metadata til master target serveren via VPN.
    - PowerShell: Private port 5986
    - Fjernskrivebord: Private port 3389

4. En Linux master target server VM er oprettet med disse slutpunkter. Bemærk, at offentlige slutpunkter oprettes kun, hvis du opretter forbindelse via internettet.

    - Brugerdefineret: Offentlige port bruges af processen server til at sende gentagelse data via internettet. Private port 9443 bruges af processen serveren til at sende gentagelse data til master target serveren via VPN.
    - Bruger1: Offentlige port bruges af processen serveren til at sende metadata via internettet. Private port 9080 bruges af processen serveren til at sende metadata til master target serveren via VPN
    - SSH: Private port 22

    >[AZURE.WARNING] Ikke slette eller ændre offentlig eller privat portnummeret for slutpunkterne oprettes under den overordnede mål serverinstallation.

5. I **virtuelle maskiner** Vent til den virtuelle maskine til at starte.

    - Hvis det er en Windows server note ned remote desktop oplysninger.
    - Hvis det er en Linux-server, og du opretter forbindelse via VPN note interne IP-adressen på den virtuelle maskine. Bemærk den offentlige IP-adresse, hvis du opretter forbindelse via internettet.

6.  Log på serveren for at fuldføre installationen og registrere den med af konfigurationsserveren. 
7.  Hvis du kører Windows:

    1. Starte en forbindelse til Fjernskrivebord til den virtuelle maskine. Første gang du logger på et script kører i et PowerShell-vindue. Lukke den, ikke. Når det er afsluttet åbnes værktøjet Host Agent Config automatisk for at registrere serveren.
    2. Angiv den interne IP-adressen på den server configuration og port 443 i **Host Agent Config** . Du kan bruge den interne adresse og private port 443, selvom du ikke opretter forbindelse via VPN, fordi den virtuelle maskine er knyttet til den samme Azure netværk som server configuration. Lad **Brug HTTPS** aktiveret. Angiv adgangskoden til konfigurationsserveren, som du tidligere har angivet. Klik på **OK** for at registrere serveren. Du kan ignorere NAT-indstillinger. De er ikke brugt.
    3. Hvis din anslåede opbevaring drev krav er mere end 1 TB kan du konfigurere opbevaring lydstyrken (fod:) ved hjælp af en virtuel disk og [lagerplads mellemrum](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
    
    ![Windows master target server](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)

8. Hvis du kører Linux:
    1. Sørg for, at du har installeret den nyeste Linux Integration Services (LIS) installeret, før du installerer master destinationsadresse-serveren. Du kan finde den nyeste version af LIS sammen med instruktioner til, hvordan du installerer [her](https://www.microsoft.com/download/details.aspx?id=46842). Genstarte computeren, når LIS installeret.
    2. Klik på **Hent og Installer yderligere software (kun for Linux Master Target Server)**i **forberede mål (Azure) ressourcer** . Kopiere filen hentede tjære til den virtuelle maskine ved hjælp af en sftp-klient. Du kan også du kan logge på udløst linux master target server og bruge *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* til at hente den filen.
    2. Log på serveren ved hjælp af en Secure Shell-klient. Hvis du har forbindelse til Azure netværket over VPN Brug den interne IP-adresse. Ellers skal du bruge den eksterne IP-adresse og SSH offentlige slutpunktet.
    3. Udtrække filerne fra gzippede installationsprogrammet ved at køre: **tjære – xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
    ![Linux master target server](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
    4. Kontrollér, at du er i den mappe, hvor du har pakket indholdet af tjære-fil.
    5. Kopiere server adgangskoden til konfigurationen til en lokal fil ved hjælp af kommandoen * *ekkoet* `<passphrase>` * > passphrase.txt**
    6. Kør kommandoen "**sudo. / installere -t begge - en hoste -R MasterTarget -d /usr/local/ASR -i* `<Configuration server internal IP address>` * -p 443 -s y - c https -P passphrase.txt**".

    ![Registrere target server](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)

9. Vent et par minutter (10-15) og på siden kontrol, mastersider target serveren er opført er registreret i **servere** > **Konfiguration servere** under fanen **Serverdetaljer** . Hvis du kører Linux og den ikke registrere Kør værten config værktøjet igen fra /usr/local/ASR/Vx/bin/hostconfigcli. Du skal angive adgangstilladelser ved at køre chmod som rod.

    ![Bekræfte target server](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

>[AZURE.NOTE] Det kan tage op til 15 minutter, efter at registrering er fuldført master destinationsadresse-serveren skal være angivet i portalen. Klik på **Opdater** for at opdatere med det samme, på siden **Konfiguration servere** .

## <a name="step-4-deploy-the-on-premises-process-server"></a>Trin 4: Installere lokale proces server

Vi anbefaler, at du konfigurerer en statisk IP-adresse på serveren processen, så det har garanti for, at være fast på tværs af genstarter, før du går i gang.

1. Klik på Hurtig Start > **installere proces Server lokalt** > **downloade og installere proces-server**.

    ![Installere proces server](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)

2.  Kopiere hentede zip-filen på serveren, som du vil installere proces-server. Zip-filen indeholder to installationsfiler:

    - Microsoft-ASR_CX_TP_8.4.0.0_Windows*
    - Microsoft-ASR_CX_8.4.0.0_Windows*

3. Udpakke arkivet, og Kopiér installationsfilerne til en placering på serveren.
4. Køre den **Microsoft-ASR_CX_TP_8.4.0.0_Windows*** installation filen, og følg vejledningen. Dette installerer tredjeparters komponenter, der kræves for installationen.
5. Kør derefter **Microsoft-ASR_CX_8.4.0.0_Windows***.
6. Vælg **Proces Server**på siden **Server-tilstand** .
7. Gør følgende på siden **Miljø** :


    - Hvis du vil beskytte VMware virtuelle maskiner klikke på **Ja**
    - Hvis du kun vil beskytte fysiske servere og dermed behøver VMware vCLI installeret på processerveren. Klik på **Nej** , og fortsæt.

8. Bemærk følgende, når du installerer VMware vCLI:

    - **Kun VMware vSphere CLI 5.5.0 understøttes**. Processen serveren ikke fungerer sammen med andre versioner eller opdateringer af vSphere CLI.
    - Hente vSphere CLI 5.5.0 fra [her.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
    - Hvis du har installeret vSphere CLI lige før du startede installerer processerveren, og installationsprogrammet ikke registrerer det, du vente op til fem minutter, før du forsøg at installere igen. Dette sikrer, at alle miljøvariabler behov for vSphere CLI registrering har initialiseret korrekt.

9.  Vælg det netværkskort, der skal bruge proces-server i **NIC markeringen for processen Server** .

    ![Vælg kort](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)

10. I **konfigurationsoplysninger Server**:

    - Angiv den interne IP-adressen på server configuration og 443 for port for IP-adresse og port, hvis du opretter forbindelse via VPN. Ellers skal du angive den offentlige virtuelle IP-adresse og tilknyttede offentlige HTTP slutpunkt.
    - Skriv adgangskoden på konfigurationsserveren.
    - Fjern **bekræfte mobilitet service software signatur** , hvis du vil deaktivere kontrol, når du bruger automatisk opslagsnål til at installere tjenesten. Signaturen skal have forbindelse til internettet fra processerveren.
    - Klik på **Næste**.

    ![Registrere server configuration](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)


11. Vælg et drev, cachen i **Vælg Installation drev** . Processen serveren kræver et cache drev med mindst 600 GB ledig plads. Klik derefter på **Installer**. 

    ![Registrere server configuration](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)

12. Bemærk, at det være nødvendigt at genstarte serveren for at fuldføre installationen. I **Server Configuration** > **Detaljer om Server** kontrollere, at processen serveren vises og er registreret i samling af legitimationsoplysninger.

>[AZURE.NOTE]Det kan tage op til 15 minutter, efter at registrering er fuldført processen-serveren skal vises som angivet under af konfigurationsserveren. Hvis du vil opdatere med det samme, skal du opdatere server configuration ved at klikke på opdateringsknappen nederst på konfigurationssiden af server
 
![Validere process-server](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

Hvis du ikke deaktivere signaturen for tjenesten mobilitet, når du har registreret processerveren kan du gøre det senere på følgende måde:

1. Log på proces server som administrator, og Åbn filen C:\pushinstallsvc\pushinstaller.conf til redigering. I afsnittet **[PushInstaller.transport]** Indsæt følgende linje: **SignatureVerificationChecks = "0"**. Gem og Luk filen.
2. Genstart tjenesten InMage PushInstall.


## <a name="step-5-update-site-recovery-components"></a>Trin 5: Opdater gendannelse af websteder komponenter

Fra tid til anden opdateres websted gendannelse komponenter. Når der er nye opdateringer tilgængelige skal du installere dem i følgende rækkefølge:

1. Server Configuration
2. Process-server
3. Master target server
4. Failback værktøjet (vContinuum)

### <a name="obtain-and-install-the-updates"></a>Hente og installere opdateringerne


1. Du kan hente opdateringer til konfiguration, proces og master target servere fra **Dashboard**til gendannelse af websteder. Udtrække filerne fra gzippede installationsprogrammet til Linux installation og Kør kommandoen "sudo. / installere" at installere opdateringen.
2. [Hent](http://go.microsoft.com/fwlink/?LinkID=533813) den nyeste opdatering til Failback tool(vContinuum).
3. Hvis du kører virtuelle computere eller fysisk servere, der allerede har tjenesten mobilitet installeret, kan du hente opdateringer til tjenesten på følgende måde:

    - **Mulighed 1**: hente opdateringer:
        - [Windows Server (kun 64 bit)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
        - [CentOS 6.4,6.5,6.6 (kun 64 bit)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
        - [Oracle Enterprise Linux 6.4,6.5 (kun 64 bit)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
        - [SUSE Linux Enterprise Server SP3 (kun 64 bit)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
        - Når du har opdateret processerveren vil den opdaterede version af tjenesten mobilitet være tilgængelige i mappen C:\pushinstallsvc\repository på processerveren.
    - **Mulighed 2**: Hvis du har en computer med en ældre version af tjenesten mobilitet installeret, kan du automatisk opgradere tjenesten mobilitet på maskinen fra administrationsportalen.

        1. Sikre, at processen serveren er opdateret.
        2. Sørg for, at den beskyttede maskine overholder [forudsætninger](#install-the-mobility-service-automatically) for at placere automatisk tjenesten mobilitet så opdateringen fungerer som forventet.
        2. Vælg gruppen beskyttelse, fremhæve den beskyttede maskine, og klik på **Opdater mobilitet service**. Denne knap er kun tilgængelig, hvis der er en nyere version af tjenesten mobilitet. 

            ![Vælg vCenter server](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

Angiv administratorkontoen skal bruges til at opdatere tjenesten mobilitet på den server, der er beskyttet i Vælg konti. Klik på OK, og vent udløste jobbet til at fuldføre.


## <a name="step-6-add-vcenter-servers-or-vsphere-hosts"></a>Trin 6: Tilføj vCenter servere eller vSphere hosts

1. Klik på **servere** > **Konfiguration servere** > server configuration >**Tilføj vCenter Server** til at føje en vCenter server eller vSphere vært.

    ![Vælg vCenter server](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)

2. Angiv oplysninger om serveren eller host, og vælg proces-server, der bruges til at finde det.

    - Angiv det portnummer, som vCenter serveren kører, hvis vCenter serveren ikke kører på 443 standardporten.
    - Processen server skal være på det samme netværk som vCenter server/vSphere vært og bør have VMware vSphere CLI 5.5.0 installeret.

    ![vCenter serverindstillinger](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)


3. Når registrering afsluttes vCenter serveren, vises under oplysninger om server konfigurationen.

    ![vCenter serverindstillinger](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)

4. Hvis du bruger en ikke-administratorkonto tilføje server eller vært, skal du kontrollere kontoen, har følgende rettigheder:

    - vCenter konti skal have Datacenter, Datastore, mappe, Host, netværk, ressource, lagerplads visninger, virtuelt og vSphere distribueret Skift rettigheder, der er aktiveret.
    - vSphere host konti skal have Datacenter, Datastore, mappe, Host, netværk, ressource, virtuelt og vSphere distribueret Skift tilladelser aktiveret



## <a name="step-7-create-a-protection-group"></a>Trin 7: Oprette en beskyttelse gruppe

1. Åbne **Beskyttet elementer** > **Gruppen beskyttelse** > **Opret beskyttelse gruppe**.

    ![Oprette gruppen beskyttelse](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)

2. Angiv et navn til gruppen på siden **Angiv indstillinger for beskyttelse** , og vælg den konfiguration server, som du vil oprette en gruppe.

    ![Indstillinger for beskyttelse](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)

3. Konfigurere indstillingerne for gentagelse, der skal bruges til alle maskiner i gruppen på siden **Angiv indstillinger for gentagelse** .

    ![Beskyttelse gruppen gentagelse](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)

4. Indstillinger:
    - **Flere VM konsistens**: Hvis du deaktiverer denne programmet opretter delte program-ensartet gendannelse punkter på tværs af computere i gruppen beskyttelse. Denne indstilling er mest relevante, når alle maskiner i gruppen beskyttelse kører samme arbejdsbelastningen. Alle de computere, vil blive gendannet i det samme datapunkt. Kun tilgængelig for Windows-servere.
    - **Grænseværdi for frigivne Produktionsordre**: beskeder bliver genereret, når sammenhængende databeskyttelse gentagelse frigivne Produktionsordre overstiger den konfigurerede frigivne Produktionsordre tærskelværdi.
    - **Gendannelse Peg opbevaring**: Angiver vinduet opbevaring. Beskyttet computere kan gendannes til en hvilken som helst tidspunkt i dette vindue.
    - **Program-ensartet øjebliksbillede frekvens**: Angiver, hvor ofte gendannelse punkter, der indeholder programmet ensartet snapshots oprettes.

Du kan overvåge gruppen beskyttelse, som de er oprettet på siden **Beskyttede elementer** .

## <a name="step-8-set-up-machines-you-want-to-protect"></a>Trin 8: Konfigurere computere, du vil beskytte

Du skal installere tjenesten mobilitet på virtuelle maskiner og fysiske servere, du vil beskytte. Du kan gøre dette på to måder:

- Push- og installere tjenesten på hver maskine fra serveren processen automatisk.
- Installer manuelt tjenesten. 

### <a name="install-the-mobility-service-automatically"></a>Installere tjenesten mobilitet automatisk

Når du føjer computere til en gruppe af beskyttelse rykkes og installeret på hver maskine af processen serveren tjenesten mobilitet automatisk. 

**Automatisk installere opslagsnål tjenesten mobilitet på Windows-servere:** 

1. Installere de seneste opdateringer til processerveren, som beskrevet i [trin 5: installere de seneste opdateringer til](#step-5-install-latest-updates), og Sørg for, at processen serveren er tilgængelig. 
2. Sikre, at der er netværksforbindelse mellem kilde maskinen og processerveren, og at kilde maskinen er tilgængelige fra processerveren.  
3. Konfigurere Windows firewall for at tillade **fil- og printerdeling** og **Windows Management Instrumentation**. Vælg indstillingen "Tillad en app eller en funktion passerer gennem Firewall" under Windows Firewall-indstillinger, og vælg programmerne, som vist i billedet nedenfor. Du kan konfigurere firewallpolitikken med et Gruppepolitikobjekt for maskiner, der hører til et domæne.

    ![Firewall-indstillinger](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)

4. Den konto, der bruges til at udføre opslagsnål installationen skal være i gruppen Administratorer på den computer, du vil beskytte. Disse legitimationsoplysninger anvendes kun for push installation af tjenesten mobilitet, og du vil give dem, når du føjer en computer til en beskyttelse gruppe.
5. Hvis den medfølgende konto ikke er en domænekonto, skal du deaktivere Remote brugeradgang kontrolelement på den lokale computer. Hvis du vil gøre dette Tilføj posten LocalAccountTokenFilterPolicy DWORD registreringsdatabasen med værdien 1 under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Til at tilføje posten i registreringsdatabasen fra en CLI Åbn cmd eller powershell og skrive **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. 

**Automatisk installere opslagsnål tjenesten mobilitet på Linux-servere:**

1. Installere de seneste opdateringer til processerveren, som beskrevet i [trin 5: installere de seneste opdateringer til](#step-5-install-latest-updates), og Sørg for, at processen serveren er tilgængelig.
2. Sikre, at der er netværksforbindelse mellem kilde maskinen og processerveren, og at kilde maskinen er tilgængelige fra processerveren.  
3. Kontrollér, at kontoen er en rodbruger på Linux kildeserveren.
4. Sørg for, at filen fjernsystemet på Linux kildeserveren indeholder posterne, der er tilknyttet IP-adresser, der er knyttet til alle netværkskort lokale værtsnavnet.
5. Installere den nyeste openssh, openssh-server, openssl pakker på den computer, du vil beskytte.
6. Sørg for SSH er aktiveret og kører på port 22. 
7. Aktivere SFTP undersystem og din adgangskode godkendelse i filen sshd_config på følgende måde: 

    - a) log på som rod.
    - b) find den linje, der begynder med **PasswordAuthentication**i filen fil /etc/ssh/sshd_config.
    - c) Fjern linjen, og Skift værdien fra "Nej" til "Ja".

        ![Linux mobilitet](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)

    - d) find den linje, der begynder med undersystem, og fjern kommentar fra linjen.
    
        ![Linux opslagsnål mobilitet](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    

8. Sikre kilde maskine Linux variant understøttes. 
 
### <a name="install-the-mobility-service-manually"></a>Installere tjenesten mobilitet manuelt

De softwarepakker, der er brugt til at installere tjenesten mobilitet er på processerveren i C:\pushinstallsvc\repository. Logge på processerveren og kopiere den relevante installationspakken til kilde maskinen baseret på tabellen nedenfor:-

| Kilde-operativsystem                               | Mobilitet servicepakke på process-server                                                            |
|---------------------------------------------------    |------------------------------------------------------------------------------------------------------ |
| Windows Server (kun 64 bit)                          | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe`         |
| CentOS 6.4, 6.5, 6.6 (kun 64 bit)                    | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz`     |
| SUSE Linux Enterprise Server 11 SP3 (kun 64 bit)     | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5 (kun 64 bit)        | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz`       |


**Installere tjenesten mobilitet manuelt på en Windows server**, skal du gøre følgende:

1. Kopiér **Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** pakken fra mappesti proces server, er angivet i tabellen herover til kildecomputer.
2. Installere tjenesten mobilitet ved at køre den eksekverbare fil på kildecomputeren.
3. Følg vejledningen for installer.
4. Vælg **tjeneste, mobilitet** som rollen, og klik på **Næste**.
    
    ![Installere mobilitet service](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)

5. Lade installationsmappen som standard installationssti, og klik på **Installer**.
6. Angiv den IP-adresse og HTTPS port på konfigurationsserveren, i **Host Agent Config** .

    - Hvis du opretter forbindelse via internettet kan du angive den offentlige virtuelle IP-adresse og offentlige HTTPS slutpunkt som port.
    - Hvis du opretter forbindelse via VPN angive den interne IP-adresse og 443 for port. Lad **Brug HTTPS** markeret.

    ![Installere mobilitet service](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)

7. Angiv adgangskoden til den konfigurationen server, og klik på **OK** for at registrere tjenesten mobilitet med konfigurationsserveren.

**Sådan køres fra kommandolinjen:**

1. Kopiere adgangskoden fra CX til filen "C:\connection.passphrase" på serveren, og Kør denne kommando. I vores eksempel CX i 104.40.75.37 og HTTPS-port er 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Installere tjenesten mobilitet manuelt på en Linux-server**:

1. Kopiere det relevante tjære arkiv, der er baseret på tabellen herover, fra serveren processen til kildecomputer.
2. Åbn et shell-program og udtrække zip-tjære arkiv til en lokal sti ved at udføre`tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Oprette en passphrase.txt fil i den lokale mappe, hvor du har pakket indholdet af tjære arkiv ved at angive *`echo <passphrase> >passphrase.txt`* fra shell.
4. Installere tjenesten mobilitet ved at angive *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Angiv den IP-adresse og port:

    - Hvis du opretter forbindelse til server configuration via internettet angive konfiguration virtuelle offentlige IP-adresse og offentlige HTTPS slutpunkt i `<IP address>` og `<port>`.
    - Angiv den interne IP-adresse og 443, hvis du opretter forbindelse via en VPN-forbindelse.

**Til at køre fra kommandolinjen**:

1. Kopiere adgangskoden fra CX til filen "passphrase.txt" på serveren, og Kør denne kommandoer. I vores eksempel CX i 104.40.75.37 og HTTPS-port er 62519:

Sådan installeres på en fremstilling server:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
Sådan installeres på serveren:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE] Når du tilføjer computere til en beskyttelse gruppe, der allerede kører en passende version af tjenesten mobilitet derefter installationen opslagsnål ignoreret.


## <a name="step-9-enable-protection"></a>Trin 9: Aktivér beskyttelse

For at aktivere beskyttelse tilføjer du virtuelle maskiner og fysiske servere på en beskyttelse gruppe. Inden du starter, Bemærk, at:

- Virtuelle maskiner er blevet fundet hver 15 minutter, og det kan tage op til 15 minutter, før de kan vises i Azure gendannelse af websteder efter registrering.
- Miljøændringer i den virtuelle maskine (såsom VMware værktøjer installation) kan også tage op til 15 minutter i gendannelse af websteder skal opdateres.
- Du kan se opdaget sidst i feltet **Sidste kontakt på** efter værten vCenter server/ESXi på siden **Konfiguration servere** .
- Hvis du har en allerede oprettet gruppen for beskyttelse og tilføje en vCenter Server eller ESXi host bagefter, kræver det 15 minutter til portalen Azure gendannelse af websteder til at opdatere og virtuelle maskiner opført i dialogboksen **Tilføj computere til en beskyttelse gruppe** .
- Hvis du gerne vil fortsætte med det samme med at tilføje computere til gruppen for beskyttelse uden at vente planlagte registrering, fremhæve server configuration (ikke klikke på den), og klik på knappen **Opdater** .
- Når du tilføjer virtuelle maskiner eller fysisk maskiner til en gruppe med beskyttelse, proces-server automatisk flytter og installerer tjenesten mobilitet på kildeserveren, hvis it ikke allerede er installeret.
- Sørg for, at du har konfigureret din beskyttet computere, som beskrevet i forrige trin for den automatiske opslagsnål metode til at arbejde.

Tilføje computere på følgende måde:

1. Klik på **beskyttet elementer** > **Gruppen beskyttelse** > **maskiner** > **tilføje computere**. Vi anbefaler, at beskyttelse grupper skal spejle din arbejdsbelastninger, så du har tilføjet maskiner, der kører et bestemt program til den samme gruppe som en bedste fremgangsmåde.
2. Hvis du beskytte fysisk i **Vælg virtuelle maskiner** give-servere i guiden **Tilføj fysiske maskiner** IP-adresse og fulde navn. Vælg operativsystem familie.

    ![Tilføje V Center server](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)

3. Hvis du beskytte VMware virtuelle maskiner, skal du i **Vælg virtuelle maskiner** Vælg en vCenter-server, der administrerer din virtuelle maskiner (eller værten EXSi, som de kører) og derefter vælge maskiner.

    ![Tilføje V Center server](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png) 
4. Vælg master target servere og lagerplads, der skal bruge til gentagelse, og vælg, om indstillingerne, der skal bruges til alle arbejdsbelastninger i **Angive mål ressourcer** . Vælg [Premium lagerplads konto](../storage/storage-premium-storage.md) under konfigurationen af beskyttelse af arbejdsbelastninger, der kræver ensartet høj EY ydeevne og lav ventetid for at være vært for EY intensivt arbejdsmængder. Hvis du vil bruge en Premium lagerplads konto for arbejdsbelastningen disken, skal du bruge Master destinationen for DS-serien. Du kan ikke bruge Premium lagerplads diske med Master destinationen for ikke-DS-serien.

    >[AZURE.NOTE] Vi understøtter ikke flytning af lagerplads konti, der er oprettet ved hjælp af [nye Azure portal](../storage/storage-create-storage-account.md) på tværs af grupper.

    ![vCenter server](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)

5. Vælg den konto, du vil bruge til at installere tjenesten mobilitet på beskyttet maskiner i **Angive konti** . Legitimationsoplysninger konto, som du skal bruge til automatisk installation af tjenesten mobilitet. Hvis du ikke kan vælge en konto Sørg for, at konfigurere du en som beskrevet i trin 2. Bemærk, at denne konto ikke kan åbnes af Azure. Til Windows har server kontoen, der administratorrettigheder på kildeserveren. Linux være kontoen rod.

    ![Linux legitimationsoplysninger](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)

6. Klik på markering til færdig med at tilføje computere til gruppen for beskyttelse og for at starte første replikering til hver computer. Du kan overvåge status på siden **job** .

    ![Tilføje V Center server](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)

7. Desuden kan du overvåge beskyttelsesstatus ved at klikke på **Beskyttet elementer** > beskyttelse gruppenavn > **virtuelle computere** . Når første replikering er fuldført, og maskiner synkroniserer data vises de **beskyttet** status.

    ![Virtuelt job](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)


### <a name="set-protected-machine-properties"></a>Angive beskyttet maskine egenskaber

1. Du kan konfigurere egenskaberne failover, når en maskine har statussen **beskyttet** . Vælg maskinen, og Åbn fanen **Konfigurer** i gruppeoplysninger beskyttelse.
2. Du kan ændre det navn, der skal angives på computeren i Azure efter failover og Azure virtuelt størrelse. Du kan også vælge Azure netværket, der skal knyttes maskinen efter failover.

    > [AZURE.NOTE] [Overførsel af netværk](../resource-group-move-resources.md) på tværs af grupper i det samme abonnement eller på tværs af abonnementer understøttes ikke af netværk, der bruges til implementering af gendannelse af websteder.

    ![Angive virtuelt egenskaber](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

Bemærk, at:

- Navnet på den Azure maskine skal overholde krav til Azure.
- Som standard forbindelse ikke replikerede virtuelle maskiner i Azure til et netværk med Azure. Hvis du vil replikerede Sørg virtuelle maskiner til at kommunikere for at angive det samme Azure netværk for dem.
- Hvis du ændre størrelsen på en enhed på en VMware virtuel computer eller fysisk server er det i en kritisk. Hvis du har brug for at ændre størrelsen, skal du gøre følgende:

    - a) ændre størrelsesindstillingen.
    - b) Vælg den virtuelle maskine under fanen **virtuelle maskiner** og klikke på **Fjern**.
    - c) Vælg indstillingen **deaktivere beskyttelse (bruges til gendannelse analysere og tilpasse lydstyrken)**i **fjerne Virtual Machine** . Denne indstilling deaktiverer beskyttelse, men bevarer gendannelse punkter i Azure.

        ![Angive virtuelt egenskaber](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)

    - d) genaktivere beskyttelse til den virtuelle maskine. Når du genaktiverer beskyttelse overføres dataene til størrelse er tilpasset lydstyrken til Azure.

    

## <a name="step-10-run-a-failover"></a>Trin 10: Køre en failover

Du kan i øjeblikket kun køre ikke-planlagt failover til beskyttet VMware virtuelle computere og fysiske servere. Bemærk følgende:



- Før du starter en failover, sikre, at konfiguration og master target serverne, der kører og sund. Ellers mislykkes failover.
- Kilde-computere Luk ikke som en del af en ikke-planlagt failover. Udføre en ikke-planlagt failover stopper replikering af data for de beskyttede servere. Du skal slette maskiner fra gruppen beskyttelse og tilføje dem igen for at begynde at beskytte computere igen, når den ikke-planlagte sekundære er fuldført.
- Hvis du vil skifte over uden at miste data, skal du kontrollere, at de primære websted virtuelle maskiner er slået fra, før du kan starte sekundære.

1. På den **Gendannelse planer** side, og Tilføj en plan for gendannelse. Angiv oplysninger om planen, og vælg **Azure** som destination.

    ![Konfigurere en plan](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)

2. Vælg gruppen, beskyttelse i **Vælg Virtual Machine** og derefter vælge maskiner i gruppen for at tilføje til planen, gendannelse. [Læs mere](site-recovery-create-recovery-plans.md) om gendannelse planer.

    ![Tilføje virtuelle maskiner](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)

3. Hvis det er nødvendigt kan du tilpasse planlægger at oprette grupper og sekvens rækkefølgen i hvilke computere i at genoprettelsen plan er mislykkedes. Du kan også tilføje instruktionerne til manuel handlinger og scripts. Scriptene, når gendanne til Azure kan tilføjes ved hjælp af [Azure automatisering Runbooks](site-recovery-runbook-automation.md).

4. Vælg planen, og klik på **Ikke-planlagt Failover**siden **Gendannelse planer** .
5. I **Bekræfte Failover** bekræfte failover retning (Azure), og vælg det gendannelsespunkt for at skifte til.
6. Vent et failover job skal udføre, og derefter kontrollere, at sekundære fungerer som forventet, og at starte replikerede virtuelle maskiner korrekt i Azure.




## <a name="step-11-fail-back-failed-over-machines-from-azure"></a>Trin 11: Mislykkes tilbage mislykkedes over maskiner fra Azure

[Få mere at vide](site-recovery-failback-azure-to-vmware-classic-legacy.md) om, hvordan du tage dine mislykkedes over maskiner, der kører Azure tilbage til dit lokale miljø.


## <a name="manage-your-process-servers"></a>Administrere serverne proces

Processen serveren sender gentagelse data til den overordnede mål serveren i Azure og opdager nye VMware virtuelle maskiner føjet til en vCenter server. I følgende situationer kan du vil ændre processerveren i din installation:

- Hvis den aktuelle proces server går ned
- Hvis din gendannelse punkt målsætning (frigivne Produktionsordre) stiger til et kan accepteres niveau for organisationen.

Hvis det er nødvendigt, kan du flytte gentagelse af nogle eller alle dine lokale VMware virtuelle maskiner og fysiske servere til en anden proces-server. Eksempel:

- **Fejl**– Hvis en proces server mislykkes eller ikke er tilgængelig, kan du flytte beskyttet maskine gentagelse til en anden proces server. Metadataene i den kildecomputer og replika maskine flyttes til den nye proces server og data er synkroniseret. Den nye proces server opretter automatisk forbindelse til vCenter serveren for at udføre automatisk registrering. Du kan overvåge tilstanden for processen servere på dashboardet til gendannelse af websteder.
- **Justering af belastning for at justere frigivne Produktionsordre**– For bedre justering af belastning du kan vælge en anden proces server i portalen gendannelse af websteder, og Flyt gentagelse af en eller flere computere til den til manuel belastning. I dette tilfælde flyttes metadataene i de valgte kilde- og replika maskiner til den nye proces-server. Den oprindelige proces server forbliver forbindelsen til vCenter serveren. 

### <a name="monitor-the-process-server"></a>Overvåge process-server

Hvis en process-server er i tilstanden kritisk vises en advarsel om status på dashboardet til websted gendannelse. Du kan klikke på statussen for at åbne fanen begivenheder og derefter større detaljeringsgrad ned til bestemte job på fanen job. 

### <a name="modify-the-process-server-used-for-replication"></a>Ændre proces-server, der bruges til gentagelse

1. Åbn **servere** > **Konfiguration servere** > server configuration > **Serverdetaljer**.
2. Klik på **Proces Servers** > **Skift proces Server** ud for den server, du vil ændre.

    ![Skift proces Server 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)

3. I **Skift proces Server** > **Target proces Server** Vælg den nye server, du vil bruge, og vælg derefter de virtuelle maskiner, du vil gentage til den nye server. Klik på oplysningsikonet ud for navnet på serveren for oplysninger om ledig diskplads og anvendte hukommelse. Det gennemsnitlige område, der bliver bedt om at gentage hver valgte virtuelt til den nye proces server vises for at hjælpe dig med at træffe indlæse beslutninger.

    ![Skift proces Server 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)

4. Klik på markeringen i afkrydsningsfeltet for at starte replikering til den nye proces-server. Bemærk, at hvis du fjerner alle virtuelle maskiner fra en process-server, der var kritiske det skal ikke længere vise en vigtig advarsel i dashboardet.


## <a name="third-party-software-notices-and-information"></a>Softwaremeddelelser fra tredjepart og oplysninger

Send ikke oversætte eller lokalisere

Softwaren og de firmware kører i Microsoft-produkt eller tjeneste er baseret på eller inkorporerer materiale fra projekter på listen nedenfor (samlet "fra tredjepart-kode").  Microsoft er den ikke oprindelige forfatter af koden fra tredjepart.  Den oprindelige meddelelse om ophavsret og licens, under hvilket Microsoft modtaget sådanne fra tredjepart kode, er indstillet tilbage nedenfor.

Oplysningerne i sektionen A drejer sig om fra tredjepart kode komponenter i de projekter, der vises nedenfor. Disse licenser og oplysninger, der leveres til orientering.  Denne kode fra tredjepart er der relicensed til dig af Microsoft under Microsoft-softwarelicensvilkårene for Microsoft-produkt eller tjeneste.  

Oplysningerne i afsnit B drejer sig om fra tredjepart kode komponenter, der gøres tilgængelige for dig af Microsoft under oprindelige licensvilkårene.

Hele filen kan findes på [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft forbeholder sig alle rettigheder, der ikke udtrykkeligt er tildelt denne aftale, uanset om det skyldes det vil sige, estoppel eller på anden måde.
