<properties
    pageTitle="Hvad du skal gøre i tilfælde af en Azure-lager afbrydelse | Microsoft Azure"
    description="Hvad du skal gøre i tilfælde af en Azure-lager afbrydelse"
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Hvad du skal gøre, hvis der opstår en Azure-lager afbrydelse

Hos Microsoft store vi anstrengelser for at sikre, at vores tjenester der er altid tilgængelige. Nogle gange tvinger ud over vores har betydning os på måder, der kan medføre ikke-planlagt tjeneste afbrydelser i en eller flere områder. For at hjælpe dig med at håndtere disse sjældne forekomster, giver vi følgende overordnet vejledning til Azure-lager tjenester.

## <a name="how-to-prepare"></a>Sådan forberedes 

Det er vigtigt for hver kunde til at udarbejde en plan for deres egne nedbrud. Den indsats til at gendanne fra en lagerplads afbrydelse typisk omfatter både handlinger personale og automatiseret procedurer for at genaktivere programmerne i tilstanden fungerer. Se dokumentationen til Azure nedenfor for at oprette din egen nedbrud gendannelse plan:

-   [Nedbrud og høj tilgængelighed til Azure-programmer](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

-   [Azure fleksibilitet teknisk vejledning](../resiliency/resiliency-technical-guidance.md)

-   [Azure service for gendannelse af websteder](https://azure.microsoft.com/services/site-recovery/)

-   [Azure lagerplads gentagelse](storage-redundancy.md)

-   [Azure sikkerhedskopi-tjenesten](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Sådan registrerer 

Den anbefalede måde at finde ud af Azure Tjenestestatus er at abonnere på [Azure Dashboard over tjenestetilstand](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Hvad du skal gøre, hvis der opstår en lagerplads afbrydelse

Hvis en eller flere lagerplads tjenester er ikke tilgængelig i øjeblikket på en eller flere områder, er der to muligheder for du kan overveje. Hvis du ønsker øjeblikkelig adgang til dine data, skal du overveje at indstillingen 2.

### <a name="option-1-wait-for-recovery"></a>Mulighed 1: Vent gendannelse

I dette tilfælde er ingen handling fra din side påkrævet. Vi arbejder fremføre for at gendanne Azure tjenesten kører. Du kan overvåge Tjenestestatus på [Azure Dashboard over tjenestetilstand](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Mulighed 2: Kopiér data fra sekundær

Hvis du vælger [læseadgang geografisk overflødige lagerplads (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (anbefales) for kontiene lagerplads, vil du have læseadgang til dine data fra den sekundære område. Du kan bruge værktøjer, som [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)og [flytning af Data i Azure bibliotek](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) til at kopiere data fra den sekundære område til en anden lagerplads konto på et unimpacted område, og peg derefter dine programmer med det pågældende lagerplads firma i begge læse og skrive tilgængelighed.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Hvad du kan forvente, hvis der opstår en lagerplads failover

Hvis du vælger [geografisk overflødige lagerplads (GRS)](storage-redundancy.md#geo-redundant-storage) eller [læseadgang geografisk overflødige lagerplads (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (anbefales), holder Azure-lager dine data robust i to områder (primære og sekundære). I begge områder fører Azure-lager konstant flere kopier af dine data.

Når internationale nedbrud påvirker din primære region, forsøger vi først at gendanne tjenesten i det pågældende område. Afhænger af, hvilket art på nedbrud og dens indvirkning på nogle sjældne tilfælde vi kan ikke muligvis gendanne den primære område. På det pågældende tidspunkt, foretager vi en geografisk-failover. I tværs område datareplikering er en asynkron proces, kan det omfatte en forsinkelse, så det er muligt, ændringer, der endnu ikke er blevet replikeres til den sekundære område kan gå tabt. Du kan forespørge ["tidspunkt for seneste synkronisering" af din lagerplads konto](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) for at få oplysninger om status for gentagelse.

Et par punkter i forbindelse med lagerplads geografisk failover oplevelse:

-   Lagerplads geografisk-failover kun udløses af Azure-lager-teamet – der findes ingen kunde handling påkrævet.

-   Eksisterende lagerplads service slutpunkterne for blob, tabeller, køer og filer forbliver de samme efter sekundære på DNS-post, skal opdateres for at skifte fra det primære område til den sekundære område.

-   Før og under geografisk-sekundære, du har ikke skrive-adgang til kontoen lagerplads på grund af virkningerne af nedbrud, men du stadig kan læse fra sekundært, hvis din lagerplads konto er konfigureret som RA-GRS.

-   Når geografisk-sekundære er afsluttet, og DNS-ændringer overføres, genoptages din læse / skrive-adgang til kontoen lagerplads. Du kan forespørge ["geografisk Failover sidst" af din lagerplads konto](https://msdn.microsoft.com/library/azure/ee460802.aspx) for at få flere oplysninger.

-   Når sekundære kontoen lagerplads vil være fuldt fungerende, men i en "nedgraderede" status, som det er faktisk hostet i et separat område med ingen geografisk gentagelse muligt. Hvis du vil reducere denne risiko, skal vi gendanne den oprindelige primære område og derefter gøre et geografisk failback for at gendanne den oprindelige tilstand. Hvis det oprindelige primære område er uoprettelig, tildeler vi en anden sekundær område.
Se artiklen på lagerplads-teamets blog om [redundans indstillinger og RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)kan finde flere oplysninger om infrastrukturen i Azure-lager geografisk gentagelse.

##<a name="best-practices-for-protecting-your-data"></a>Bedste fremgangsmåder til at beskytte dine data

Der er nogle anbefalede fremgangsmåder til at sikkerhedskopiere din lagerplads data med jævne mellemrum.

-   VM diske – bruge [Sikkerhedskopiering af Azure service](https://azure.microsoft.com/services/backup/) til at sikkerhedskopiere VM diskene bruges af din Azure virtuelle computere.

-   Bloker BLOB-Opret et [øjebliksbillede](https://msdn.microsoft.com/library/azure/hh488361.aspx) af hver blok blob eller Kopiér BLOB til en anden lager konto i et andet område, der bruger [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)eller [flytning af Data i Azure bibliotek](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).

-   Tabeller – Brug [AzCopy](storage-use-azcopy.md) til at eksportere tabeldataene til en anden lagerplads konto i et andet område.

-   Filer – Brug [AzCopy](storage-use-azcopy.md) eller [Azure PowerShell](storage-powershell-guide-full.md) til at kopiere dine filer til en anden lagerplads konto i et andet område.
