<properties
    pageTitle="Lagerplads konfiguration for SQL Server FOS | Microsoft Azure"
    description="Dette emne beskrives, hvordan Azure konfigurerer lagerplads til SQL Server FOS under klargøringen (ressourcestyring implementeringsmodel). Det også beskrives det, hvordan du kan konfigurere lagerplads for dit eksisterende SQL Server FOS."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="ninarn"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/04/2016"
    ms.author="ninarn" />

# <a name="storage-configuration-for-sql-server-vms"></a>Lagerplads konfiguration for SQL Server FOS

Når du konfigurerer en SQL Server virtuelt billede i Azure, portalen hjælper med at automatisere konfigurationen af lagerplads. Dette omfatter vedhæfte lagerplads til VM, gøre lagerplads tilgængelige til SQL Server, og konfigurere det til at optimere til dine krav til særlig ydeevne.

Dette emne forklares, hvordan Azure konfigurerer lagerplads til dit SQL Server FOS, både under klargøring og for eksisterende FOS. Denne konfiguration er baseret på [ydeevne bedste fremgangsmåder](virtual-machines-windows-sql-performance.md) for Azure VM'er, der kører SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel.

## <a name="prerequisites"></a>Forudsætninger
Hvis du vil bruge konfigurationsindstillinger automatiseret lagerplads, kræver din virtuelle maskine følgende egenskaber:

- Klargjort med en [SQL Server galleriet Hurtige](virtual-machines-windows-sql-server-iaas-overview.md#option-1-deploy-a-sql-vm-per-minute-licensing).
- Bruger [ressourcestyring implementeringsmodel](../resource-manager-deployment-model.md).
- Bruger [Premium lagerplads](../storage/storage-premium-storage.md).

## <a name="new-vms"></a>Ny FOS
I nedenstående afsnit beskrives, hvordan du kan konfigurere lagerplads til nye SQL Server virtuelle maskiner.

### <a name="azure-portal"></a>Azure-portalen
Når en Azure VM ved hjælp af en SQL Server galleriet Hurtige, kan du vælge at konfigurere automatisk lagring til din nye VM. Du angiver den lagerstørrelse, ydeevne grænser og arbejdsbelastningen type. Følgende skærmbillede viser bladet lagerplads konfiguration, der bruges i SQL VM klargøring.

![SQL Server VM lagerplads konfiguration under klargøring](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Afhængigt af dine valg, udfører Azure de følgende lagerplads konfigurationsopgaver efter oprettelse af VM:

- Opretter og vedhæfter premium lagerplads datadisce til den virtuelle maskine.
- Konfigurerer datadisce for at få adgang til SQL Server.
- Konfigurerer diskene data i en lagerpulje, der er baseret på de angivne størrelse og ydeevne (IOP'ER og overførselshastighed) krav.
- Knytter puljen lagerplads til et nyt drev på den virtuelle maskine.
- Optimerer denne nye drev, der er baseret på din angivne arbejdsbelastningen type (Data Warehouse, transaktion behandling, eller Generelt).

Du kan finde yderligere oplysninger om hvordan Azure konfigurerer indstillinger for lagring af [lager i konfigurationsafsnittet](#storage-configuration). Du kan finde en fuld gennemgang af, hvordan du opretter en SQL Server VM i portalen Azure [klargøring selvstudiet](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Ressource Administrer skabeloner
Hvis du bruger følgende ressourcestyring skabeloner, knyttes to premium data diske som standard, med ingen lagerplads puljen konfiguration. Du kan dog tilpasse disse skabeloner for at ændre antallet af premium datadisce, der er knyttet til den virtuelle maskine.

- [Oprette VM med automatiseret sikkerhedskopiering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
- [Oprette VM med automatiske rettelser](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
- [Oprette VM med AKV-Integration](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Eksisterende FOS
For eksisterende SQL Server FOS, kan du redigere visse lagerplads indstillinger på portalen Azure. Vælg din VM skal du gå til området Indstillinger, og derefter vælge SQL Server Configuration. SQL Server Configuration blade viser den aktuelle lagerforbrug af din VM. Alle drev, der findes på din VM vises i dette diagram. For hvert drev vises lagerpladsen i fire sektioner:

- SQL-data
- SQL-logfilen
- Andre (ikke-SQL storage)
- Tilgængelige

![Konfigurere lagerplads til eksisterende SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Hvis du vil konfigurere opbevaring for at tilføje et nyt drev eller udvide et eksisterende drev, skal du klikke på linket Rediger over diagrammet.

De konfigurationsindstillinger, du får vist varierer afhængigt af om du har brugt denne funktion, før. Når du bruger for første gang, kan du angive dine krav til lagerplads til et nyt drev. Hvis du tidligere har brugt denne funktion til at oprette et drev, kan du vælge at udvide det drev lagerplads.

### <a name="use-for-the-first-time"></a>Brug for første gang
Hvis det er første gang, du bruger denne funktion, kan du angive de størrelse og ydeevne lagergrænser for et nyt drev. Denne oplevelse minder om det, du ser på klargøring tid. Den primære forskel er, at du ikke har tilladelse til at angive den arbejdsbyrde. Denne begrænsning forhindrer forstyrrer alle eksisterende konfigurationer SQL Server på virtuelle maskinen.

![Konfigurere SQL Server-lager skyderne](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure opretter et nyt drev, der er baseret på dine specifikationer. I dette scenarie skal udføres Azure følgende lagerplads konfigurationsopgaver:

- Opretter og vedhæfter premium lagerplads datadisce til den virtuelle maskine.
- Konfigurerer datadisce for at få adgang til SQL Server.
- Konfigurerer diskene data i en lagerpulje, der er baseret på de angivne størrelse og ydeevne (IOP'ER og overførselshastighed) krav.
- Knytter puljen lagerplads til et nyt drev på den virtuelle maskine.

Du kan finde yderligere oplysninger om hvordan Azure konfigurerer indstillinger for lagring af [lager i konfigurationsafsnittet](#storage-configuration).

### <a name="add-a-new-drive"></a>Tilføje et nyt drev
Hvis du allerede har konfigureret lagerplads på din SQL Server VM, viser udvide lagerplads to nye indstillinger. Den første indstilling er at tilføje et nyt drev, der kan øge din VM præstationsniveau.

![Tilføje et nyt drev til en SQL-VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

Når du har tilføjet drevet, skal du udføre nogle ekstra manuel konfiguration for at opnå forbedret ydeevne.

### <a name="extend-the-drive"></a>Udvide drevet
Anden mulighed for at udvide lager er at udvide det eksisterende drev. Denne funktion øger den tilgængelige lagerplads for dit drev, men det øger ikke ydeevnen. Du kan ikke ændre antallet af kolonner med lagerplads grupper, når lagerpulje er blevet oprettet. Antallet af kolonner bestemmer antallet af parallelle skriver, som kan være spredt diskene data. Derfor øge ikke en hvilken som helst tilføjede datadisce ydeevnen. De kan kun give mere lagerplads for de data, der skrives. Denne begrænsning betyder også, når du udvider drevet, bestemmer antallet af kolonner det mindste antal datadisce, som du kan tilføje. Hvis du opretter en lagerpulje med fire datadisce, er antallet af kolonner så også fire. Hver gang du udvide lagerplads, skal du tilføje mindst fire datadisce.

![Udvide et drev til en SQL-VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Konfigurationsproblemer med datalagring
Dette afsnit indeholder en reference til lagerplads konfigurationsændringer, der automatisk udfører Azure under SQL VM klargøring eller konfiguration af i portalen Azure.

- Hvis du har valgt færre end to TBs lager til din VM, oprette Azure ikke en lagerpulje.
- Hvis du har valgt mindst to TBs lager til din VM, konfigurerer Azure en lagerpulje. I næste afsnit i dette emne indeholder oplysninger om lagerplads puljen konfiguration.
- Automatisk lagring konfiguration altid anvendes [premium lagerplads](../storage/storage-premium-storage.md) P30 data diske. Der er derfor en 1:1 tilknytning mellem dine valgte antal TB og antallet af datadisce, der er knyttet til din VM.

Finde prisoplysninger, på siden [lagerplads priser](https://azure.microsoft.com/pricing/details/storage) under fanen **Lagerplads på disken** .

### <a name="creation-of-the-storage-pool"></a>Oprettelse af lagerpulje
Azure bruger følgende indstillinger til at oprette lagerpulje på SQL Server FOS.

| Indstilling | Værdi |
|-----|-----|
| Stripestørrelse  | 256 KB (datalagring); 64 KB (transaktioner) |
| Diskstørrelser | 1 TB hver |
| Cache | Læs |
| Allokeringsstørrelse | 64 KB NTFS allokering enhedsstørrelse |
| Chat fil initialisering | Aktiveret |
| Lås sider i hukommelsen | Aktiveret |
| Gendannelse | Enkel gendannelse (ingen fleksibilitet) |
| Antallet af kolonner | Antallet af diske<sup>1</sup> |
| TempDB placering | Gemt på data diske<sup>2</sup> |

<sup>1</sup> efter lagerpulje er oprettet, kan du ændre antallet af kolonner i lagerpulje.

<sup>2</sup> denne indstilling gælder kun for det første drev, du opretter ved hjælp af funktionen lagerplads konfiguration.

## <a name="workload-optimization-settings"></a>Arbejdsbelastningen optimeringsindstillinger
I følgende tabel beskrives de tre arbejdsbelastning type tilgængelige indstillinger og deres tilsvarende optimeringer:

| Arbejdsbelastningen type | Beskrivelse | Optimeringer |
|-----|-----|-----|
| **Generelt** | Standardindstillingen, der understøtter arbejdsbelastninger, som de fleste | Ingen |
| **Transaktions behandling** | Optimerer lagerpladsen for traditionelle database OLTP arbejdsmængder | Spore Flag 1117<br/>Spore Flag 1118 |
| **Data opbevaring** | Optimerer lagerpladsen for arbejdsbelastninger, som analytisk og rapportering | Spore Flag 610<br/>Spore Flag 1117 |

>[AZURE.NOTE] Du kan kun angive typen arbejdsbelastningen, når du klargør en SQL virtuel maskine ved at markere den i trinnet lagerplads konfiguration.

## <a name="next-steps"></a>Næste trin
Du kan finde andre emner, der er relateret til kører SQL Server i Azure FOS, [SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md).
