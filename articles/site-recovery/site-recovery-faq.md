<properties
    pageTitle="Azure gendannelse af websteder: Ofte stillede spørgsmål | Microsoft Azure"
    description="I denne artikel beskrives populære spørgsmål om gendannelse af Azure websteder."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="get-started-article"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>


# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure gendannelse af websteder: Ofte stillede spørgsmål (FAQ)

Denne artikel indeholder ofte stillede spørgsmål om gendannelse af Azure websteder. Hvis du har spørgsmål efter at have læst i denne artikel, skal du lægge dem på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Generelt

### <a name="what-does-site-recovery-do"></a>Hvad gør gendannelse af websteder?

Gendannelse af websteder bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR)-strategi ved orchestrating og automatisere gentagelse fra lokale virtuelle maskiner og fysiske servere til Azure eller til en sekundær datacenter. [Få mere at vide](site-recovery-overview.md).


### <a name="what-can-site-recovery-protect"></a>Hvad beskytter gendannelse af websteder?

- **Hyper-V virtuelle maskiner**: gendannelse af websteder kan beskytte en hvilken som helst arbejdsbyrde, der kører på en Hyper-V VM.
- **Fysiske servere**: gendannelse af websteder kan beskytte fysiske servere, der kører Windows eller Linux.
- **VMware virtuelle maskiner**: gendannelse af websteder kan beskytte en hvilken som helst arbejdsbelastningen kører i et VMware VM.

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>Understøtter gendannelse af websteder Azure ressourcestyring modellen?

Ud over gendannelse af websteder i portalen Azure klassisk findes gendannelse af websteder i portalen Azure med support til Ressourcestyring. De fleste installationer gendannelse af websteder i Azure portal giver en strømlinet installation oplevelse, og du kan gentage FOS og fysiske servere i klassisk lagerplads eller Ressourcestyring lagerplads. Her er de understøttede installationer:

- [Gentage VMware FOS eller fysiske servere til Azure i portalen Azure](site-recovery-vmware-to-azure.md)
- [Gentage Hyper-V FOS i VMM skyer til Azure i portalen Azure](site-recovery-vmm-to-azure.md)
- [Gentage Hyper-V FOS (uden VMM) til Azure i portalen Azure](site-recovery-hyper-v-site-to-azure.md)
- [Gentage Hyper-V FOS i VMM skyer til en sekundær websted på portalen Azure](site-recovery-vmm-to-vmm.md)


### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Hvad skal jeg i Hyper-V dirigerer replikering med gendannelse af websteder?

Til Hyper-V host serveren til afhænger hvad du bør af scenariet installation. Se Hyper-V forudsætningerne i:

- [Replikering Hyper-V FOS (uden VMM) til Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replikering Hyper-V FOS (med VMM) til Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [Replikering Hyper-V FOS til en sekundær datacenter](site-recovery-vmm-to-vmm.md#before-you-start)

- Hvis du replikering til en sekundær datacenter, kan du læse om [understøttede gæsteoperativsystemer til Hyper-V FOS](https://technet.microsoft.com/library/mt126277.aspx).
- Hvis du replikeres til Azure, understøtter alle gæst operativsystemer, der [understøttes af Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)gendannelse af websteder.

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kan jeg beskytte FOS, når Hyper-V kører på operativsystemet på klientcomputeren?

Nej, FOS skal være placeret på en Hyper-V host-server, der kører på en understøttede Windows server maskine. Hvis du har brug at beskytte en klientcomputer kan du gentage den som en fysisk maskine til [Azure](site-recovery-vmware-to-azure.md) eller en [sekundær datacenter](site-recovery-vmware-to-vmware.md).


### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Hvilke arbejdsbelastninger, som kan jeg beskytte med gendannelse af websteder?

Du kan bruge gendannelse af websteder til at beskytte de fleste arbejdsbelastninger, som kører på en understøttede VM eller fysisk server. Gendannelse af websteder indeholder understøttelse af programmet-aware gentagelse, så apps kan gendannes til en intelligent tilstand. Den integreres med Microsoft-programmer som SharePoint, Exchange, Dynamics, SQL Server og Active Directory og arbejder tæt sammen med foranstillet leverandører, herunder Oracle, SAP, IBM og Red Hat. Du kan [Få mere at vide](site-recovery-workload.md) om beskyttelse med arbejdsbyrde.


### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Skal Hyper-V hosts skal være i VMM skyer?

Hvis du vil gentage til en sekundær datacenter, og derefter Hyper-V FOS skal være på vært Hyper-V servere, der er placeret i en VMM sky. Hvis du vil kan replikeres til Azure, kan du gentage FOS på Hyper-V værtsservere med eller uden VMM skyer. [Du finder flere oplysninger](site-recovery-hyper-v-site-to-azure.md).

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Kan jeg installere gendannelse af websteder med VMM, hvis jeg kun have én VMM server?

Ja. Du kan enten gentage FOS i Hyper-V-servere i skyen VMM til Azure, eller du kan gentage mellem VMM skyer på den samme server. Lokalt på lokale replikering anbefales det, at du har en VMM server i begge de primære og sekundære websteder.  [Få mere at vide](site-recovery-single-vmm.md)

### <a name="what-physical-servers-can-i-protect"></a>Hvilke fysiske servere kan jeg beskytte?

Du kan gentage fysiske servere, der kører Windows og Linux til Azure eller til et sekundært websted. [Få mere at vide om](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) krav til operativsystem.  De samme krav gælder, uanset om du replikering fysiske servere til Azure eller til et sekundært websted.

Bemærk, at fysiske servere kører som FOS i Azure Hvis din lokale server går ned. Failback til en lokal fysisk server understøttes ikke i øjeblikket, men du kan ikke tilbage til en virtuel maskine, der kører på Hyper-V eller VMware.


### <a name="what-vmware-vms-can-i-protect"></a>Hvilke VMware FOS kan jeg beskytte?

Hvis du vil beskytte VMware FOS skal du bruge en vSphere hypervisor og virtuelle maskiner, der kører VMware værktøjer. Vi anbefaler også, at du har en VMware vCenter server til at administrere hypervisors. Du kan [Få mere at vide](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) om nøjagtige krav til replikering VMware servere og FOS til Azure eller til en sekundær websted.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Kan jeg administrere nedbrud til min lokale kontorer med gendannelse af websteder?

Ja. Når du bruger gendannelse af websteder til dirigerer gentagelse og belastningsjustering i din lokale kontorer, får du vist et samlet organisering og visning af alle dine gren office arbejdsbelastninger på en central placering. Du kan nemt køre failover og administrere nedbrud i alle grene fra din hovedkontor, uden at besøge forgreningerne.

## <a name="security"></a>Sikkerhed

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Sendes gentagelse data til tjenesten gendannelse af websteder?

Nej, gendannelse af websteder SKÆRING ikke replikerede data og har ikke nogen oplysninger om, hvad der kører på din virtuelle maskiner eller fysiske servere.
Replikeringsdata er udvekslet mellem lokale Hyper-V hosts, VMware hypervisors eller fysiske servere og Azure lagerplads eller webstedet sekundær. Gendannelse af websteder har ikke mulighed for at opfange disse data. Kun de metadata, der er behov for at dirigerer gentagelse og belastningsjustering sendes til tjenesten gendannelse af websteder.

Gendannelse af websteder er ISO 27001:2013, 27018, HIPAA, DPA certificeret og er ved at bedømmelse SOC2 og FedRAMP JAB.


### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Overholdelse af angivne standarder årsager, skal selv vores lokale metadata forblive i den samme geografiske område. Gendannelse af websteder, der kan hjælpe os?

Ja. Når du opretter en samling af legitimationsoplysninger gendannelse af websteder i et område, vi sørge for, at alle metadata, som vi har brug at aktivere og dirigerer replikering og failover forbliver i dette området er geografiske rammen.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterer gendannelse af websteder gentagelse?

Virtuelle maskiner og fysiske servere ved understøttes replikering mellem lokale websteder kryptering-i-overførsel. Virtuelle maskiner og fysiske servere replikeres til Azure ved understøttes både kryptering i overførsel og kryptering på resten (i Azure).


## <a name="replication"></a>Gentagelse


### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Er der en hvilken som helst forudsætninger for replikering virtuelle maskiner til Azure?

Virtuelle maskiner, du vil gentage til Azure skal overholde [krav til Azure](site-recovery-best-practices.md#virtual-machines).

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kan jeg gentage Hyper-V generering af 2 virtuelle maskiner til Azure?

Ja. Gendannelse af websteder konverterer fra generering af 2 til generering af 1 under failover. På failback konverteres maskinen tilbage til generering af 2. [Du finder flere oplysninger](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Hvis jeg replikeres til Azure hvordan jeg betale for Azure FOS?

Under almindelig replikering data replikeres til geografisk overflødige Azure-lager, og du behøver ikke at betale en hvilken som helst Azure IaaS virtuelt gebyrer, give en betydeligt fordel. Når du kører en failover til Azure, gendannelse af websteder opretter automatisk Azure IaaS virtuelle maskiner, og herefter du faktureres for de ressourcer, Beregn, som du forbruger i Azure.


### <a name="is-there-an-sdk-i-can-use-to-automate-the-asr-workflow"></a>Er der en SDK, som jeg kan bruge til at automatisere ASR arbejdsprocessen?

Ja. Du kan automatisere gendannelse af websteder arbejdsprocesser ved hjælp af Rest-API, PowerShell eller Azure SDK. Understøttes i øjeblikket scenarier til implementering af gendannelse af websteder ved hjælp af PowerShell:

- [Gentage Hyper-V FOS i VMMs skyer til Azure PowerShell klassisk](site-recovery-deploy-with-powershell.md)
- [Gentage Hyper-V FOS i VMMs skyer til Azure PowerShell ressourcestyring](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Gentage Hyper-V FOS uden VMM til Azure PowerShell klassisk](site-recovery-hyper-v-site-to-azure-classic.md)
- [Gentage Hyper-V FOS uden VMM til Azure PowerShell ressourcestyring](site-recovery-deploy-with-powershell-resource-manager.md)


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Hvis jeg replikeres til Azure hvilken slags lagerplads konto har jeg brug for?

- **Azure klassisk portal**: Hvis du anvender gendannelse af websteder i portalen Azure klassisk, skal du bruge en [standard geografisk overflødige lagerplads konto](../storage/storage-redundancy.md#geo-redundant-storage). Premium lagerplads understøttes ikke i øjeblikket. Kontoen skal være i samme område som samling af legitimationsoplysninger gendannelse af websteder.

- **Azure-portalen**: Hvis du anvender gendannelse af websteder i portalen Azure, skal du bruge en LRS eller GRS lagerplads-konto. Vi anbefaler GRS, så dataene er tolerant, hvis der opstår en internationale afbrydelse, eller hvis det primære område ikke kan gendannes. Kontoen skal være i samme område som gendannelse Services samling. Premium lagerplads understøttes kun, hvis du replikering VMware FOS eller fysiske servere.

### <a name="how-often-can-i-replicate-data"></a>Hvor ofte kan jeg gentage data?

- **Hyper-v** Hyper-V FOS kan replikeres hver 30 sekunder, fem minutter eller 15 minutter. Hvis du har konfigureret SAN replikering derefter er replikering synkron.
- **VMware og fysiske servere:** En gentagelse frekvens er ikke relevant her. Replikering er sammenhængende.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kan jeg udvide gentagelse fra eksisterende gendannelse websted til et andet tertiære websted?

Udvidede eller sammenkædede replikering understøttes ikke. Anmode om denne funktion i [feedback-forummet](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).


### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Kan jeg gøre en offline gentagelse første gang jeg replikeres til Azure?

Dette understøttes ikke. Anmode om denne funktion i [feedback-forummet](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### <a name="can-i-exclude-specific-disks-from-replication"></a>Er det muligt at udelade bestemte diske fra gentagelse?

Dette understøttes, når du er [replikering VMware FOS og fysiske servere](site-recovery-vmware-to-azure.md#exclude-disks-from-replication) til Azure, ved hjælp af portalen Azure.


### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Kan jeg gentage virtuelle maskiner med dynamiske diske?

Dynamiske diske understøttes ved replikering Hyper-V virtuelle computere. De er også understøttet ved replikering VMware FOS og fysiske maskiner til Azure. Operativsystem disken skal være en grundlæggende disk.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Kan jeg tilføje en ny computer til en eksisterende Replikeringsgruppe?

Tilføjelse af nye maskiner til eksisterende replikeringsgrupper understøttes. Vælg Replikeringsgruppe (fra 'Replikeret post' blade) og højreklik på/Vælg kontekstmenu på gruppen gentagelse for at gøre det, og vælg den ønskede indstilling.

![Føje til gentagelse gruppe](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Kan jeg begrænse båndbredde, der er tildelt til Hyper-V gentagelse trafik?

Ja. Du kan læse mere om begrænsning af båndbredde i artiklerne installation:

- [Kapacitet, planlægning af replikering VMware FOS og fysiske servere](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
- [Kapacitet, planlægning af replikering Hyper-V FOS i VMM skyer](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
- [Kapacitet, planlægning af replikering Hyper-V FOS uden VMM](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## <a name="failover"></a>Failover


### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Hvis jeg ikke Azure, hvordan får jeg adgang til Azure virtuelle maskiner efter failover?

Du kan få adgang til Azure FOS via en sikker Internetforbindelse, over en VPN til-websted eller over Azure ExpressRoute. Du skal forberede en række ting for at oprette forbindelse. Få mere at vide i:

- [Oprette forbindelse til Azure FOS efter failover af VMware FOS eller fysiske servere](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
- [Oprette forbindelse til Azure FOS efter failover af Hyper-V FOS i VMM skyer](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
- [Oprette forbindelse til Azure FOS efter failover af Hyper-V FOS uden VMM](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Hvis jeg ikke Azure hvordan Azure sikre, at dataene er tolerant?

Azure henvender sig til spændstighed. Gendannelse af websteder er allerede udviklet til failover til en sekundær Azure datacenter i overensstemmelse med Azure SERVICENIVEAUAFTALE, hvis behov. Hvis dette sker, vi Sørg for, metadata for din og vaults forbliver i den samme geografiske område, du har valgt til din samling af legitimationsoplysninger.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Hvis jeg replikering mellem to datacentre Hvad sker der, hvis min primære datacenter har en uventet afbrydelse?

Du kan udløse en ikke-planlagt failover fra webstedet sekundær. Gendannelse af websteder ikke har brug for forbindelse fra det primære websted til at udføre sekundære.

### <a name="is-failover-automatic"></a>Er failover automatisk?

Failover er ikke automatisk. Du starter failover med enkelt klik i portalen, eller du kan bruge [Webstedet gendannelse PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) til at udløse en failover. Ned tilbage er en enkel handling i portalen gendannelse af websteder.

For at automatisere du kan bruge lokale Orchestrator eller Operations Manager til at registrere en virtuel maskinfejl, og derefter udløse den sekundære ved hjælp af SDK.

- [Læs mere](site-recovery-create-recovery-plans.md) om gendannelse planer.
- [Læs mere](site-recovery-failover.md) om failover.
- [Læs mere](site-recovery-failback-azure-to-vmware.md) om fejlbehæftede tilbage VMware FOS og fysiske servere


## <a name="service-providers"></a>Tjenesteudbydere


### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jeg er en udbyder. Fungerer gendannelse af websteder for dedikeret og delte infrastruktur modeller?

Ja, understøtter gendannelse af websteder begge dedikeret og delte infrastruktur-modeller.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>For en tjenesteudbyder er identiteten af min lejer, der er delt med tjenesten gendannelse af websteder?

Nej. Lejer identitet forbliver anonyme. Din lejere behøver adgang til portalen gendannelse af websteder. Kun udbyder tjenesteadministratoren skal arbejde sammen med på portalen.


### <a name="will-tenant-application-data-ever-go-to-azure"></a>Lejer programmet data nogensinde går til Azure?

Ved replikering mellem service provider ejes steder, går programmet data aldrig til Azure. Data er krypteret i overførsel og replikerede direkte mellem internetudbyder steder.

Hvis du replikeres til Azure, sendes programmet data til Azure-lager, men ikke til tjenesten gendannelse af websteder. Data er krypteret i overførsel, og forbliver krypterede i Azure.


### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Min lejere, modtager en regning for en hvilken som helst Azure-tjenester?

Nej. Azures fakturering relation er direkte hos udbyderen af. Tjenesteudbydere er ansvarlig for oprettelse af bestemte fakturaer for deres lejere.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Hvis jeg replikeres til Azure, skal vi køre virtuelle maskiner i Azure til enhver tid?

Nej, replikeres Data til en Azure lagerplads konto i dit abonnement. Når du foretager en test failover (DR analysér) eller en faktisk failover, opretter gendannelse af websteder automatisk virtuelle maskiner i dit abonnement.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Du sikrer lejerniveau isolationsniveauet når jeg replikeres til Azure?

Ja.

### <a name="what-platforms-do-you-currently-support"></a>Hvilke platforme aktuelt understøtter i?

Vi understøtter Azure Pack, skyen Platform System og System Center baseret (2012 eller nyere) installationer. Du kan [Få mere at vide](https://technet.microsoft.com/library/dn850370.aspx) om integration af Azure Pack og gendannelse af websteder.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Understøtter du enkelt Azure-pakken og enkelt VMM serverinstallationer?

Ja, kan du gentage Hyper-V virtuelle maskiner til Azure, eller du kan gentage mellem internetudbyder steder.  Bemærk, at hvis du gentage mellem internetudbyder steder, Azure runbook integration er ikke tilgængelig.


## <a name="next-steps"></a>Næste trin

- Læs [Oversigt over gendannelse af websteder](site-recovery-overview.md)
- Få mere at vide om [gendannelse af websteder arkitektur](site-recovery-components.md)  
