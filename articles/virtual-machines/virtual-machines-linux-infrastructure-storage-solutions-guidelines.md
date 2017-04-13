<properties
    pageTitle="Lagerplads løsninger retningslinjer | Microsoft Azure"
    description="Få mere at vide om de vigtigste design og implementering retningslinjer for installation af lagerplads løsninger i Azure infrastrukturtjenester."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="storage-infrastructure-guidelines"></a>Lagerplads infrastruktur retningslinjer

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

I denne artikel fokuserer på om lagerplads behov og Designovervejelser for at opnå optimal VM (virtual machine) ydeevne.


## <a name="implementation-guidelines-for-storage"></a>Implementering retningslinjer for lager

Beslutninger:

- Har du brug at bruge Standard eller Premium lagerplads for dit arbejdsbyrde?
- Har du brug for disk striping til at oprette større end 1023 GB?
- Har du brug for disk striping at opnå optimal i/o-ydeevne for din arbejdsbyrde?
- Hvilke konfigurere af lagerplads konti skal du være vært for din IT arbejdsbelastningen eller infrastruktur?

Opgaver:

- Gennemse i/o-kravene til de programmer, du installerer og planlægge relevante antallet og typen af lagerplads konti.
- Oprette mængde lagerplads firmaer ved hjælp af din navngivningskonvention. Du kan bruge Azure CLI eller på portalen.


## <a name="storage"></a>Lagerplads

Azure-lager er en central del af implementering og administration af virtuelle maskiner (VM'er) og programmer. Azure-lager tilbyder tjenester til lagring af data fra en fil, ustrukturerede data og meddelelser, og det er også en del af den infrastruktur FOS.

Der findes to typer lagring konti til understøttende FOS:

- Standard lagerplads konti giver dig adgang til blob-lager (bruges til at gemme Azure VM diske), tabellagring, kø lager og fillagring.
- [Premium lagerplads](../storage/storage-premium-storage.md) konti levere høj ydeevne og lav ventetid disk understøttelse af i/o-intensivt arbejdsbelastninger, som MongoDB delt klynge. Premium lagerplads understøtter i øjeblikket kun Azure VM diske.

Azure opretter FOS med en operativsystem disk, en midlertidig disk og nul eller flere valgfrit datadisce. Operativsystem disken og datadisce er Azure siden blob, mens den midlertidige disk er gemt lokalt på noden hvor maskinen findes. Tager sig, når du designer programmer til brug kun denne midlertidige disk til ikke-fast data, som VM kan overføres mellem hosts under vedligeholdelse af hændelser. Data gemt på den midlertidige disk vil gå tabt.

Holdbarhed og høj tilgængelighed leveres af det underliggende Azure-lager-miljø til at sikre, at dine data forbliver beskyttet mod ikke-planlagt vedligeholdelse eller hardware fejl. Når du designer dit miljø til Azure-lager, kan du vælge at gentage VM lagerplads:

- lokalt inden for en given Azure datacenter
- på tværs af Azure datacentre inden for et bestemt område
- på tværs af Azure datacentre på tværs af forskellige områder.

Du kan få [mere at vide om Replikeringsindstillinger for høj tilgængelighed](../storage/storage-introduction.md#replication-for-durability-and-high-availability).

Operativsystem og data diske har en maksimal størrelse på 1023 gigabyte (GB). Den maksimale størrelse på en blob er 1024 GB og, der skal indeholde metadata (sidefod) på den Virtuelle fil (en GB er 1024<sup>3</sup> byte). Du kan bruge logiske lydstyrken Manager (LVM) til overskrider denne grænse ved gruppering af datadisce for at præsentere logiske drev, der er større end 1023GB for din VM sammen.

Er der nogle grænser skalerbarhed, når du designer dit Azure-lager installationer – se [grænser for Microsoft Azure-abonnement og service, kvoter, og begrænsninger](azure-subscription-service-limits.md#storage-limits) for at få flere oplysninger. Se også [Azure lagerplads skalerbarhed og ydeevne destinationer](../storage/storage-scalability-targets.md).

Programlageret, kan du gemme ustrukturerede objektdata som dokumenter, billeder, sikkerhedskopier, konfigurationsdata, logfiler osv. Brug af blob-lager. I stedet for at skrive til en virtuel disk, der er knyttet til VM programmet, kan programmet skriver direkte til Azure blob-lager. BLOB-lager også giver mulighed for [varm- og smarte lagerplads niveauer](../storage/storage-blob-storage-tiers.md) afhængigt af din tilgængelighedsbehov og omkostningsbegrænsninger.


## <a name="striped-disks"></a>Stribet diske
Ud over gør det muligt at oprette større end 1023 GB i mange tilfælde ved hjælp af striping om en data disk du forbedrer ydeevnen ved at tillade flere BLOB bagest lagerplads på en enkelt enhed. Med spredning fortsætter I/O kræves for at skrive og læse data fra en enkelt logisk disk parallelt.

Azure er sat, begrænsninger på antallet af datadisce og mængde båndbredde, der er tilgængelige, afhængigt af VM størrelsen. Yderligere oplysninger finder du [størrelser til virtuelle computere](virtual-machines-linux-sizes.md).

Hvis du bruger disk striping om en Azure data disk, skal du overveje følgende retningslinjer:

- Datadisce skal altid være den maksimale størrelse (1023 GB).
- Vedhæft de maksimale datadisce, der er tilladt for VM størrelse.
- Brug LVM.
- Undgå at bruge Azure datadisk indstillinger for cachelagring (cachelagring politik = ingen).

Se [Konfigurere LVM på en Linux VM](virtual-machines-linux-configure-lvm.md)kan finde flere oplysninger.


## <a name="multiple-storage-accounts"></a>Flere lagerplads konti

Når du designer dit Azure-lager-miljø, du kan bruge flere lagerplads konti som antallet af FOS du installerer øges. Denne fremgangsmåde hjælper med at distribuere ud I/O på tværs af den underliggende Azure-lager infrastruktur til at vedligeholde optimal ydeevne for dine FOS og programmer. Når du designer programmer, du installerer, kan du overveje at i/o-kravene hver VM har og balance ud disse FOS på tværs af Azure-lager konti. Prøv at undgå gruppering alle høj I/O kræver FOS i til et eller to lagerplads konti.

Flere oplysninger om i/o-funktionerne i de forskellige indstillinger for Azure-lager og nogle anbefaler maksimumværdier, skal du se [Azure lagerplads skalerbarhed og ydeevne destinationer](../storage/storage-scalability-targets.md).


## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 