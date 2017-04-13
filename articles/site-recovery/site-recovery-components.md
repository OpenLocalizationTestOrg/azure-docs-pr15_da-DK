<properties
    pageTitle="Hvordan fungerer gendannelse af websteder? | Microsoft Azure"
    description="I denne artikel indeholder en oversigt over arkitektur gendannelse af websteder"
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
    ms.topic="get-started-article"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="how-does-azure-site-recovery-work"></a>Hvordan fungerer Azure gendannelse af websteder?

Læs denne artikel for at forstå de underliggende arkitekturen i tjenesten Azure gendannelse af websteder, og de komponenter, som gør det arbejde. 

Sende en hvilken som helst kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Oversigt

Virksomheder har brug for en strategi for BCDR, der bestemmer, hvordan apps, arbejdsmængder og data holde køre og være tilgængelige under planlagte og ikke-planlagt nedetid, og gendanne til normal arbejdsbetingelser så tidligt som muligt. Din strategi for BCDR skal beholde forretningsdata sikre og kan gendannes, og sikre, at arbejdsbelastninger forbliver konstant er tilgængelig, når der udsendes nedbrud. 

Gendannelse af websteder er en Azure-tjeneste, der bidrager til din strategi for BCDR ved orchestrating gentagelse af lokale fysiske servere og virtuelle maskiner til skyen (Azure) eller til en sekundær datacenter. Hvis der opstår afbrydelser i din primære placering, du ikke sekundær placeringen for at holde apps og arbejdsbelastninger, som er tilgængelige. Du ikke tilbage til din primære placering, når det vender tilbage til normal handlinger. Se mere i [Hvad er gendannelse af websteder?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Gendannelse af websteder i portalen Azure

Azure har to forskellige [installation modeller](../resource-manager-deployment-model.md) til oprettelse og arbejde med ressourcer: Azure ressourcestyring modellen og klassisk services management modellen. Azure har også to portaler – [Azure klassisk portal](https://manage.windowsazure.com/) , der understøtter den klassiske implementeringsmodel og [Azure-portalen](https://portal.azure.com) med understøttelse af begge installation-modeller.

Gendannelse af websteder er tilgængelig i både klassisk portalen og Azure portalen. Du kan understøtte gendannelse af websteder med klassisk services management modellen i portalen Azure klassisk. Du kan understøtte klassisk model eller ressource Model installationer på portalen Azure. [Du finder flere oplysninger](site-recovery-overview.md#site-recovery-in-the-azure-portal) om installation af ved hjælp af Azure portal.

Oplysninger i denne artikel gælder for både klassisk og Azure portalen installationer. Forskelle er angivet, hvis det er relevant.

## <a name="deployment-scenarios"></a>Installationsscenarier

Gendannelse af websteder kan installeres for at dirigerer gentagelse i en række scenarier:

- **Gentage VMware virtuelle maskiner**: Du kan gentage lokale VMware virtuelle maskiner til Azure eller til en sekundær datacenter.
- - **Gentage fysiske computere**: Du kan gentage fysiske maskiner, der kører Windows eller Linux til Azure eller til en sekundær datacenter. Processen til replikering fysiske computere er næsten på samme måde som processen til replikering VMware FOS
- **Gentage Hyper-V FOS (uden VMM)**: Du kan gentage Hyper-V VM'er, der ikke administreres af VMM til Azure.
- **Gentage Hyper-V FOS administreres i System Center VMM skyer**: Du kan gentage lokale Hyper-V virtuelle maskiner, der kører på Hyper-V værtsservere i VMM skyer til Azure eller til en sekundær datacenter. Du kan gentage bruger standard Hyper-V replika eller bruge SAN gentagelse.
- **Overføre FOS**: Du kan bruge gendannelse af websteder til at [overføre Azure IaaS FOS](site-recovery-migrate-azure-to-azure.md) mellem områder eller til at [overføre AWS Windows forekomster](site-recovery-migrate-aws-to-azure.md) til Azure IaaS FOS. I øjeblikket kun overførsel er understøttet, hvilket betyder, at du ikke kan over disse FOS, men du ikke kan dem igen.

Gendannelse af websteder kan gentage de fleste apps, der kører på disse FOS og fysiske servere. Du kan få en komplet oversigt over de understøttede apps i [hvilke arbejdsbelastninger, som kan Azure gendannelse af websteder beskytte?](site-recovery-workload.md)


## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>Kontraprøverne til Azure: VMware virtuelle maskiner eller fysiske Windows/Linux-servere

Der findes flere måder at gentage VMware FOS med gendannelse af websteder.

- **Ved hjælp af portalen Azure**-når du installerer gendannelse af websteder i portalen Azure du kan ikke over FOS til klassisk service manager lager eller til Ressourcestyring. Replikering VMware FOS i portalen Azure viser en række fordele, herunder også muligheden for kan replikeres til classic eller Ressourcestyring lageret i Azure. [Få mere at vide](site-recovery-vmware-to-azure.md).
- **Ved hjælp af portalen klassisk**-du kan installere gendannelse af websteder i portalen klassisk ved hjælp af en forbedret oplevelse. Det skal bruges til alle nye installationer på portalen klassisk. I denne installation kan du kun mislykkes over FOS, til klassisk lageret i Azure og ikke til Ressourcestyring lagerplads. [Få mere at vide](site-recovery-vmware-to-azure-classic.md). Der er også en [ældre oplevelse](site-recovery-vmware-to-azure-classic-legacy.md) til konfiguration af VMware gentagelse i portalen klassisk. Dette ikke bør bruges til nye installationer.  Hvis du har allerede installeret ved hjælp af de ældre oplevelse [Få mere at vide om at overføre](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) den forbedrede installation.



Arkitektonisk kravene for implementering af gendannelse af websteder for at gentage VMware FOS/fysiske servere i portalen Azure eller Azure klassisk portalen (udvidet) er ens, med et par forskelle:

- Hvis du installerer på portalen Azure, du kan replikeres til Ressourcestyring-baseret lager og bruge ressourcestyring netværk til at oprette forbindelse Azure FOS efter failover.
- Når du installerer i portalen Azure begge LRS og GRS lagerplads understøttes. På portalen klassisk GRS er påkrævet.
- Installationsprocessen er forenklet og mere brugervenlige i portalen Azure.


Her er, hvad du skal bruge:

- **Azure-konto**: Du skal bruge en Microsoft Azure-konto.
- **Azure-lager**: Du skal bruge en Azure-lager-konto til at gemme replikerede data. Du kan bruge en klassisk konto eller en ressourcestyring lagerplads konto. Kontoen, der kan være LRS eller GRS, når du installerer i portalen Azure. Replikerede data er gemt i Azure-lager og Azure FOS er spundet op, når der opstår failover. 
- **Azure netværk**: Du skal bruge et Azure virtuelle netværk, der Azure FOS opretter forbindelse til, når de er oprettet på failover. De kan være netværk, der er oprettet i klassisk service manager modellen eller i modellen, ressourcestyring i portalen Azure.
- **Lokal konfiguration server**: Du skal bruge en lokal Windows Server 2012 R2 computer, der kører server configuration og andre komponenter gendannelse af websteder. Hvis du replikering VMware FOS skal det være en meget tilgængelige VMware VM. Hvis du vil gentage fysiske servere kan maskinen være fysisk. Komponenterne gendannelse af websteder skal være installeret på computeren:
    - **Konfiguration af server**: koordinaterne kommunikationen mellem lokale miljø og Azure og administrerer replikering af data og gendannelse.
    - **Processen server**: fungerer som en gentagelse gateway. Den modtager gentagelse data fra beskyttet kilde maskiner, optimerer med cachelagring, komprimering og kryptering og sender dataene til Azure-lager. Det også håndterer opslagsnål installation af tjenesten mobilitet til beskyttet maskiner, og udfører automatisk registrering af VMware FOS. Du kan tilføje yderligere separat dedikeret proces servere til at håndtere stigende mængder replikeringstrafik, som din installation vokser.
    - **Mastersider target server**: håndterer gentagelse data under failback fra Azure. 
- **VMware FOS eller fysisk-servere at gentage**: hver computer, du vil gentage til Azure skal komponenten til mobilitet installeret. Denne tjeneste registrerer data skriver på computeren og sender dem til processerveren. Denne komponent kan installeres manuelt, eller kan overføres og installeres automatisk af processen serveren, når du aktiverer gentagelse til en maskine.
- **vSPhere hosts/vCenter server**: Du skal bruge en eller flere vSphere hostservere, der kører VMware FOS. Vi anbefaler, at du installerer en vCenter server til at administrere disse værter.
- **Failback**: Her er, hvad du skal:
    - **Fysisk-til-fysiske failback understøttes ikke**: Det betyder, at hvis du mislykkes over fysiske servere til Azure og derefter vil mislykkes tilbage, skal du ikke tilbage til en VMware VM. Du kan ikke tilbage til en fysisk server. Du skal bruge en Azure VM mislykkes tilbage til, og hvis du ikke installerer konfiguration server som en VMware VM skal du konfigurere en separat master target server som en VMware VM. Det er nødvendigt, fordi master target serveren skal arbejde sammen og vedhæfter VMware lagerplads til at gendanne diskene til en VMware VM.
    - - **Midlertidige proces server i Azure**: Hvis du vil mislykkes tilbage fra Azure efter failover, skal du konfigurere en Azure VM konfigureret som en proces server til at håndtere gentagelse fra Azure. Du kan slette denne VM, når failback afsluttes.
    - **VPN-forbindelse**: For failback, skal du bruge en VPN-forbindelse (eller Azure ExpressRoute) konfigurere fra Azure netværket til webstedet, i det lokale miljø.
    - **Separat lokalt mastersider target server**: lokal master target server håndterer failback. Master target server er installeret som standard på management-serveren, men hvis du ned tilbage store mængder trafik skal du konfigurere en separat lokalt master target server hertil.

**Generelle Arkitektur**

![Udvidet](./media/site-recovery-components/arch-enhanced.png)

**Installation komponenter**

![Udvidet](./media/site-recovery-components/arch-enhanced2.png)

**Failback**

![Forbedret failback](./media/site-recovery-components/enhanced-failback.png)


- Du kan [Få mere at vide](site-recovery-vmware-to-azure.md#azure-prerequisites) om kravene til Azure portalen installation.
- Du kan [Få mere at vide](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) om kravene til forbedret installation i portalen klassisk.
- Du kan [Få mere at vide](site-recovery-failback-azure-to-vmware.md) om failback i portalen Auzre.
- Du kan [Få mere at vide](site-recovery-failback-azure-to-vmware-clas- [Learn more](site-recovery-failback-azure-to-vmware-classic.md) about failback in the Auzre portal.sic.md) om failback i portalen klassisk.

## <a name="replicate-to-azure-hyper-v-vms-not-managed-by-vmm"></a>Kontraprøverne til Azure: Hyper-V FOS ikke administreres af VMM

Du kan gentage Hyper-V VM'er, der ikke administreres af System Center VMM til Azure med gendannelse af websteder på følgende måde:

- **Ved hjælp af portalen Azure**-når du installerer gendannelse af websteder i portalen Azure du kan ikke over FOS til klassisk lager eller til Ressourcestyring. [Få mere at vide](site-recovery-hyper-v-site-to-azure.md).
- **Ved hjælp af portalen klassisk**-du kan installere gendannelse af websteder i portalen klassisk. I denne installation kan du kun mislykkes over FOS, til klassisk lageret i Azure og ikke til Ressourcestyring lagerplads. [Få mere at vide](site-recovery-hyper-v-site-to-azure-classic.md).

Arkitekturen for begge installationer minder, bortset fra at:

- Hvis du installerer på portalen Azure, du kan replikeres til Ressourcestyring lager og bruge ressourcestyring netværk til at oprette forbindelse Azure FOS efter failover.
- Installationsprocessen er forenklet og mere brugervenlige i portalen Azure.

Her er, hvad du skal bruge:

- **Azure-konto**: Du skal bruge en Microsoft Azure-konto.
- **Azure-lager**: Du skal bruge en Azure-lager-konto til at gemme replikerede data. Du kan bruge en klassisk konto eller en ressourcestyring lagerplads konto i portalen Azure. Du kan bruge en klassisk konto i portalen klassisk. Replikerede data er gemt i Azure-lager og Azure FOS oprettes, når der opstår failover.
- **Azure netværk**: Du skal bruge et Azure netværk, der Azure FOS opretter forbindelse til, når de er oprettet efter failover. 
- **Hyper-v host**: Du skal bruge en eller flere Windows Server 2012 R2 Hyper-V host server. Under installationen af gendannelse af websteder skal du installere Azure websted gendannelse udbyder og tjenester til Microsoft Azure genoprettelse agent på værten.
- **Hyper-V FOS**: Du skal bruge en eller flere FOS på Hyper-V host serveren. Azure websted gendannelse udbyder og Azure gendannelse Services agent på værten Hyper-V under installation af gendannelse af websteder. Udbyderen koordinater og orchestrates replikering med tjenesten gendannelse af websteder via internettet. Agenten håndterer data gentagelse data over HTTPS 443. Kommunikation fra både provideren, og agenten er sikre og krypteret. Replikerede data på Azure-lager, krypteres også.

**Generelle Arkitektur**

![Hyper-V websted til Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


- Du kan [Få mere at vide](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) om kravene til Azure portalen installation.
- Du kan [Få mere at vide](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) om kravene til klassisk portalen installation.



## <a name="replicate-to-azure-hyper-v-vms-managed-by-vmm"></a>Kontraprøverne til Azure: Hyper-V FOS administreres af VMM

Du kan gentage Hyper-V FOS i VMM skyer til Azure med gendannelse af websteder på følgende måde:

- **Ved hjælp af portalen Azure**-når du installerer gendannelse af websteder i portalen Azure du kan ikke over FOS til klassisk lager eller til Ressourcestyring. [Få mere at vide](site-recovery-vmm-to-azure.md).
- **Ved hjælp af portalen klassisk**-du kan installere gendannelse af websteder i portalen klassisk. I denne installation kan du kun mislykkes over FOS, til klassisk lageret i Azure og ikke til Ressourcestyring lagerplads. [Få mere at vide](site-recovery-vmm-to-azure-classic.md).

Arkitekturen for begge installationer minder, bortset fra at:

- Hvis du installerer på portalen Azure, du kan replikeres til Ressourcestyring-baseret lager og bruge ressourcestyring netværk til at oprette forbindelse Azure FOS efter failover.
- Installationsprocessen er forenklet og mere brugervenlige i portalen Azure.


Her er, hvad du skal bruge:

- **Azure-konto**: Du skal bruge en Microsoft Azure-konto.
- **Azure-lager**: Du skal bruge en Azure-lager-konto til at gemme replikerede data. Du kan bruge en klassisk konto eller en ressourcestyring lagerplads konto i portalen Azure. Du kan bruge en klassisk konto i portalen klassisk. Replikerede data er gemt i Azure-lager og Azure FOS oprettes, når der opstår failover.
- **Azure netværk**: skal du konfigurere netværk tilknytning så Azure FOS er forbundet til relevante netværk, når de er oprettet efter failover. 
- **VMM server**: du har brug for en eller flere lokale VMM servere, der kører på System Center 2012 R2 og konfigureret med en eller flere private skyer. Hvis du implementerer i Azure portalen skal du bruge logiske og VM netværk konfigurere, så du kan konfigurere kort over netværk. På portalen klassisk er dette valgfrit.  Et VM netværk skal være knyttet til en logisk netværk, der er knyttet til skyen.
- **Hyper-v host**: Du skal bruge en eller flere Windows Server 2012 R2 Hyper-V værtsservere i VMM skyen.
- **Hyper-V FOS**: Du skal bruge en eller flere FOS på Hyper-V host serveren.

**Generelle Arkitektur**

![VMM til Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

- Du kan [Få mere at vide](site-recovery-vmm-to-azure.md#azure-requirements) om kravene til Azure portalen installation.
- Du kan [Få mere at vide](site-recovery-vmm-to-azure-classic.md#before-you-start) om kravene til klassisk portalen installation.




## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>Kontraprøverne til en sekundær websted: VMware virtuelle maskiner eller fysiske servere 

Kan replikeres VMware FOS eller fysiske servere til et sekundært websted som download InMage spejder, der er inkluderet i abonnementets Azure gendannelse af websteder. Det kan hentes fra Azure-portalen eller fra Azure klassisk portal. 

Du konfigurere komponent servere i hvert websted (konfiguration, proces, mastersider mål) og installere den Unified Agent på computere, du vil gentage. Efter første replikering sender agent på hver maskine delta gentagelse ændringer til processerveren. Processen serveren optimerer dataene og overfører det til master target server på webstedet sekundær. Server configuration administrerer gentagelse processen.

Her er, hvad du skal:

**Azure-konto**: du installerer dette scenario, ved hjælp af InMage spejder. Du skal have et Azure-abonnement for at hente den. Når du har oprettet en samling af legitimationsoplysninger gendannelse af websteder kan du hente InMage spejder og installere de seneste opdateringer til at konfigurere installationen.
**Processen server (primære websted)**: konfigurere komponenten proces server i din primære websted til at håndtere cachelagring, komprimering og optimering af data. Den håndterer også push installation af Unified Agent for computere, du vil beskytte. 
**VMware ESX/ESXi og vCenter server (primære websted)**: Hvis du beskytte VMware FOS skal du bruge en VMware EXS/ESXi hypervisor og eventuelt en VMware vCenter server til at administrere hypervisors.
- **FOS/fysiske servere (primære websted)**: VMware FOS eller Windows/Linux fysiske servere, du vil beskytte er brug for den Unified Agent er installeret. Agent for Unified også er installeret på de maskiner, der fungerer som master destinationsadresse-server. Agenten fungerer som en udbyder af kommunikation mellem alle komponenter. 
- - **Konfiguration af server (sekundær websted)**: af konfigurationsserveren er den første komponent, du har installeret, og den er installeret på sekundær webstedet for at administrere, konfigurere og overvåge din installation, enten ved hjælp af webstedet management eller konsollen vContinuum. Der er kun en enkelt konfiguration server i en installation, og det skal være installeret på en computer, der kører Windows Server 2012 R2.
- **vContinuum server (sekundær websted)**: den er installeret på samme placering (sekundær websted) som server configuration. Den indeholder en konsol til administration og overvågning af dit beskyttet miljø. I en standardinstallation vContinuum serveren er den første master target server og har installeret den Unified Agent.
- **Mastersider target server (sekundær websted)**: master target serveren indeholder replikerede data. Den modtager data fra processerveren, opretter en replika maskine i det sekundære websted og indeholder opbevaring datapunkter. Antallet af overordnede mål servere, du har brug for, afhænger af antallet computere, du beskytte. Hvis du vil mislykkes tilbage til det primære websted skal du en master target server der for. 

**Generelle Arkitektur**

![VMware til VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="replicate-to-a-secondary-site-hyper-v-vms-managed-by-vmm"></a>Kontraprøverne til en sekundær websted: Hyper-V FOS administreres af VMM


Du kan gentage Hyper-V VM'er, der administreres af System Center VMM til en sekundær datacenter med gendannelse af websteder på følgende måde:

- **Ved hjælp af portalen Azure**-når du installerer gendannelse af websteder i portalen Azure. [Få mere at vide](site-recovery-hyper-v-site-to-azure.md).
- **Ved hjælp af portalen klassisk**-du kan installere gendannelse af websteder i portalen klassisk. [Få mere at vide](site-recovery-hyper-v-site-to-azure-classic.md).

Arkitekturen for begge installationer minder, bortset fra at:

- Hvis du installerer på portalen Azure skal du konfigurere kort over netværk. Dette er valgfrit i portalen klassisk.
- Installationsprocessen er forenklet og mere brugervenlige i portalen Azure.
- - Hvis du installerer i Azure er klassisk portalen [lagerplads tilknytning](site-recovery-storage-mapping.md) tilgængelig.

Her er, hvad du skal bruge:

- **Azure-konto**: Du skal bruge en Microsoft Azure-konto.
- **VMM server**: Vi anbefaler en VMM server i det primære websted og i webstedet sekundær, hver med mindst én VMM privat skyen. Serveren, der skal køres mindst System centrere 2012 SP1 med seneste opdateringer og forbindelse til internettet. Skyer bør have Hyper-V egenskab profil, der er angivet. Du skal installere Azure websted gendannelse udbyder på VMM-serveren. Udbyderen koordinater og orchestrates replikering med tjenesten gendannelse af websteder via internettet. Kommunikation mellem udbyder og Azure er sikre og krypteret.
- **Hyper-V server**: Hyper-V værtsservere skal være placeret i den primære og sekundære VMM sky. Værten for servere, der skal køres mindst Windows Server 2012 med de seneste opdateringer installeret, og forbindelse til internettet. Data replikeres mellem de primære og sekundære Hyper-V værtsservere via LAN eller VPN ved hjælp af Kerberos eller certifikat godkendelse.  
- **Beskyttet maskiner**: Hyper-V host kildeserveren skal have mindst én VM, du vil beskytte.

**Generelle Arkitektur**

![Lokalt til en lokal installation](./media/site-recovery-components/arch-onprem-onprem.png)


- Du kan [Få mere at vide](site-recovery-vmm-to-vmm.md#azure-prerequisites) om kravene til installation på portalen Azure.
- - Du kan [Få mere at vide](site-recovery-vmm-to-vmm-classic.md#before-you-start) om kravene til installation på portalen Azure klassisk.




## <a name="replicate-to-a-secondary-site-with-san-replication-hyper-v-vms-managed-by-vmm"></a>Kontraprøverne til en sekundær websted med SAN gentagelse: Hyper-V FOS administreres af VMM

Du kan gentage Hyper-V FOS administreres i VMM skyer en sekundær websted ved hjælp af SAN gentagelse ved hjælp af portalen Azure klassisk. Dette scenarie understøttes ikke i øjeblikket i den nye Azure portal. 

I dette scenarie under gendannelse af websteder installation skal du installere Azure websted gendannelse udbyder på VMM servere. Udbyderen koordinater og orchestrates replikering med tjenesten gendannelse af websteder via internettet. Data replikeres mellem de primære og sekundære lagerplads matrixer ved hjælp af synkron SAN gentagelse.

Her er, hvad du skal bruge:

**Azure-konto**: Du skal bruge et Azure-abonnement
- **SAN-system**: en [understøttes SAN matrix](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) administreres af den primære VMM-server. SAN deler en netværksinfrastruktur med en anden SAN-system i det sekundære websted.
- **VMM server**: Vi anbefaler en VMM server i det primære websted og i webstedet sekundær, hver med mindst én VMM privat skyen. Serveren, der skal køres mindst System centrere 2012 SP1 med seneste opdateringer og forbindelse til internettet. Skyer bør have Hyper-V egenskab profil, der er angivet.
- **Hyper-V server**: Hyper-V værtsservere, der er placeret i den primære og sekundære VMM sky. Værten for servere, der skal køres mindst Windows Server 2012 med de seneste opdateringer installeret, og forbindelse til internettet.
- **Beskyttet maskiner**: Hyper-V host kildeserveren skal have mindst én VM, du vil beskytte.

**SAN gentagelse arkitektur**

![SAN gentagelse](./media/site-recovery-components/arch-onprem-onprem-san.png)

Du kan [Få mere at vide](site-recovery-vmm-san.md#before-you-start) om kravene til installation.
### <a name="on-premises"></a>Lokalt



## <a name="hyper-v-protection-lifecycle"></a>Hyper-V beskyttelse livscyklus

Denne arbejdsproces viser processen for at beskytte, replikering og ned over Hyper-V virtuelle computere. 

1. **Aktivér beskyttelse**: du konfigurere samling af legitimationsoplysninger gendannelse af websteder, Konfigurer replikeringsindstillinger for en VMM skyen eller Hyper-V websted, og Aktivér beskyttelse til FOS. Et job, kaldet **Aktivér beskyttelse** startes og kan overvåges i fanen **job** . Jobbet kontrollerer, at computeren opfylder forudsætninger og derefter aktiverer metoden [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) som konfigurerer gentagelse til Azure med de indstillinger, du har konfigureret. **Aktivér beskyttelse** jobbet aktiverer også metoden [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) for at initialiseret en fuld VM gentagelse.
2. **Indledende gentagelse**: et virtuelt øjebliksbillede hentes og virtuelle harddiske er replikerede én ad gangen, indtil de er alle kopieret, til Azure eller til det sekundære datacenter. Den tid, der er behov for at udføre dette afhænger af VM størrelse, netværksbåndbredde og den første replikering metode. Selvom der sker disk ændringer, mens første replikering er i gang sporer Hyper-V replika gentagelse sporingen disse ændringer som Hyper-V gentagelse logfiler (.hrl), der er placeret i samme mappe som diskene. Hver disk har en tilknyttet .hrl-fil, der skal sendes til sekundær-lager. Bemærk, at filerne øjebliksbillede, og log forbruge diskressourcer, mens første replikering er i gang. Når den første replikering afsluttes VM snapshot slettes og delta disk ændringer log der skal synkroniseres og flettede.
3. **Færdiggør beskyttelse**: Når første replikering afsluttes **Færdiggør beskyttelse** jobbet konfigurerer Netværks- og andre indstillinger, efter gentagelse, så den virtuelle maskine er beskyttet. Hvis du replikeres til Azure skal du muligvis tilpasse indstillingerne for den virtuelle maskine, så det er parat til failover. På dette tidspunkt kan du køre en test failover for at kontrollere, at alt fungerer som forventet.
4. **Gentagelse**: efter den første replikering delta synkronisering begynder, i overensstemmelse med indstillinger for gentagelse. 
    - **Fejl ved replikering**: Hvis delta replikering mislykkes, og en fuld replikering ville være dyrt med hensyn til båndbredde eller et klokkeslæt, opstår ny synkronisering. For eksempel hvis .hrl filer når 50% af diskstørrelse, derefter VM markeres på ny synkronisering. Ny synkronisering minimeres mængden data, der sendes ved computing kontrolsummer af kilde- og destinationswebsteder virtuelle maskiner og sende kun delta. Når ny synkronisering afsluttes fortsætter delta gentagelse. Som standard ny synkronisering er planlagt til at køre automatisk uden for office timer, men du kan synkronisere en virtuel maskine manuelt.
    - **Replikeringsfejl**: Hvis der opstår en gentagelse, der er indbygget sendt igen. Hvis det er en uoprettelig fejl som en godkendelse eller godkendelse fejl eller en replika maskinen befinder sig i en ugyldig tilstand, forsøges ingen nye forsøg. Hvis det er en genoprettelig fejl som et netværksfejl eller lidt ledig plads/hukommelse, opstår et nyt forsøg med øget mellemrum mellem nye forsøg (1, 2, 4, 8, 10, og klik derefter hver 30 minutter).
4. **Planlagt/ikke-planlagt failover**: Du kan køre planlagt eller uventet failover efter behov. Hvis du kører en planlagt failover derefter kilde FOS er lukket ned for at sikre, at undgå tab af data. Når replika FOS er oprettet, er de placeret i en bekræftelse afventer tilstand. Du har brug at acceptere dem for at fuldføre sekundære, medmindre du replikering med SAN i så fald Anvend automatisk. Failback kan opstå, når det primære websted er oppe at køre. Hvis du har replikeres til Azure modsat er replikering automatisk. Ellers starte du modsat gentagelse manuelt.
 

![arbejdsproces](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Næste trin

[Forberede til installation](site-recovery-best-practices.md)
