<properties
    pageTitle="Ydeevnen bedste fremgangsmåder til SQL Server | Microsoft Azure"
    description="Viser de bedste fremgangsmåder til optimering af ydeevne for SQL Server på Microsoft Azure FOS."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/07/2016"
    ms.author="jroth" />

# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Ydeevnen bedste fremgangsmåder til SQL Server på virtuelle Azure-computere

## <a name="overview"></a>Oversigt

Dette emne indeholder bedste fremgangsmåder til optimering af ydeevne for SQL Server på Microsoft Azure Virtual Machine. Mens du kører SQL Server på virtuelle Azure-computere, anbefales det, at du fortsætte med at bruge den samme database ydeevne justering af indstillinger, der gælder for SQL Server i lokal server-miljø. En relationel database i en offentlig sky ydeevne afhænger dog af mange faktorer som størrelsen på en virtuel maskine, og konfigurationen af datadisce.

Når du opretter billeder af SQL Server, [kan du overveje klargøring af din FOS i portalen Azure](virtual-machines-windows-portal-sql-server-provision.md). SQL Server FOS klargjort i portalen med ressourcestyring implementere alle disse bedste fremgangsmåder, herunder lagerplads konfigurationen.

I denne artikel fokuserer på få den *bedste* ydeevne i SQL Server på Azure FOS. Hvis din arbejdsbyrde er mindre kræver, kan du ikke kræver hver optimering nedenfor. Overvej dine krav til ydeevne og arbejdsbelastningen mønstre, når du evaluerer disse anbefalinger.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Hurtig kontrol liste

Følgende er en hurtig kontrol liste for at opnå optimal ydeevne af SQL Server på virtuelle Azure-computere:

|Område|Optimeringer|
|---|---|
|[VM størrelse](#vm-size-guidance)|[DS3](virtual-machines-windows-sizes.md#standard-tier-ds-series) eller nyere til SQL Enterprise edition.<br/><br/>[DS2](virtual-machines-windows-sizes.md#standard-tier-ds-series) eller højere SQL Standard og Web udgaver.|
|[Lagerplads](#storage-guidance)|Brug [Premium-lager](../storage/storage-premium-storage.md). Standard lagerplads anbefales kun til Udviklingscenter/test.<br/><br/>Hold [lagerplads konto](../storage/storage-create-storage-account.md) og SQL Server VM i samme område.<br/><br/>Deaktivere Azure [geografisk overflødige lagerplads](../storage/storage-redundancy.md) (geografisk replikering) på kontoen lagerplads.|
|[Diske](#disks-guidance)|Brug et minimum af 2 [P30 diske](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) (1 til logfiler; 1 til datafiler og TempDB).<br/><br/>Undgå brug af operativsystem eller midlertidige diske til databasen lagerplads eller logføring.<br/><br/>Aktivér Læs cachelagring på disken(e) vært datafiler og TempDB.<br/><br/>Aktivér ikke cachelagring på disken(e) vært logfilen.<br/><br/>Vigtigt: Stop tjenesten SQL Server, når du ændrer cacheindstillingerne for en Azure VM disk.<br/><br/>Fordeler flere Azure datadisce for at få forøget EY overførselshastighed.<br/><br/>Formatere med dokumenterede allokering størrelser.|
|[I/O](#io-guidance)|Aktivere database siden komprimering.<br/><br/>Aktivér Hurtigsøgning fil initialisering for datafiler.<br/><br/>Begrænse eller deaktivere dobbelte størrelse på databasen.<br/><br/>Deaktivere autoshrink i databasen.<br/><br/>Flytte alle databaser til data disk herunder systemdatabaser.<br/><br/>Flytte SQL Server fejl log og sporing fil kataloger til datadisce.<br/><br/>Konfigurere standard sikkerhedskopierings- og database filplaceringer.<br/><br/>Aktivere låste sider.<br/><br/>Anvende SQL Server ydeevne løsninger.|
|[Funktion specifik](#feature-specific-guidance)|Sikkerhedskopiere direkte til blob-lager.|

Du kan finde flere oplysninger om, *hvordan* og *Hvorfor* at gøre disse optimeringer, skal du gennemse detaljer og vejledningen i følgende afsnit.

## <a name="vm-size-guidance"></a>VM størrelse vejledning

Det anbefales, at du bruger følgende [virtuelle maskiner størrelser](virtual-machines-windows-sizes.md)for ydeevnen følsomme programmer:

- **SQL Server Enterprise Edition**: DS3 eller nyere

- **SQL Server Standard- og Web udgaver**: DS2 eller nyere


## <a name="storage-guidance"></a>Lagerplads vejledning

DS serie (sammen med DSv2-serien og GS serie) FOS support [Premium lagerplads](../storage/storage-premium-storage.md). Premium lagerplads anbefales til alle arbejdsbelastninger, som fremstilling.

> [AZURE.WARNING] Standard lagerplads har forskellige latenstider og båndbredde og er kun anbefales til Udviklingscenter/afprøve arbejdsmængder. Fremstilling arbejdsbelastninger skal bruge Premium-lager.

Vi anbefaler desuden, du opretter kontoen Azure-lager i den samme datacenter som din SQL Server virtuelle computere at reducere filoverførsel forsinkelser. Når du opretter en lagerplads-konto, du Deaktiver geografisk gentagelse, som ingen er garanti for ensartet Skriv rækkefølge på tværs af flere diske. I stedet skal du overveje at konfigurere en SQL Server nedbrud gendannelse teknologi mellem to Azure datacentre. Få mere at vide under [høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Diske vejledning

Der er tre primære disktyper på en Azure VM:

- **OS disk**: Når du opretter en Azure Virtual Machine, platformen vedhæftes mindst en disk (mærket **C** -drevet) til VM for harddisken operativsystem. Disken er en virtuel harddisk, der er gemt som en side blob i lagerplads.
- **Midlertidige disk**: virtuelle Azure-computere indeholder en anden disk kaldet midlertidige disken (mærket **D**: drev). Dette er en disk på den node, der kan bruges til arbejdsområ mellemrum.
- **Datadisce**: Du kan også vedhæfte flere diske til din virtuelle maskine som datadisce, og disse skal lagres på lager, som siden BLOB.

I følgende afsnit beskrives anbefalinger til brug af disse forskellige diske.

### <a name="operating-system-disk"></a>Operativsystem disk

En operativsystem disken er en virtuel harddisk, du kan starte og tilslutte som en igangværende version af et operativsystem er navngivet som **C** -drevet.

Standard cachelagring politik på disken operativsystem er **Læse-og skriveadgang**. Til følsomme programmer i ydeevne anbefales det, at du bruger datadisce i stedet for operativsystem disken. I afsnittet på datadisce nedenfor.

### <a name="temporary-disk"></a>Midlertidige disk

Midlertidige drev, mærket **D**: drive, bevares ikke til Azure blob-lager. Gem ikke din bruger databasefilerne eller bruger transaktionslogfiler på **D**: drev.

Til D-serien, Dv2-serien og G-serien FOS er det midlertidige drev på disse FOS SSD-baserede. Hvis din arbejdsbyrde gør omfattende brug af TempDB (fx for midlertidige objekter eller komplekse joinforbindelser), kan gemme TempDB på drev **D** medføre højere TempDB overførselshastighed og nederste TempDB ventetid.

VM'er, der understøtter Premium lagerplads (DS serie, DSv2-serien og GS serie), anbefaler vi at gemme TempDB og/eller bufferen puljen filtypenavne på en disk, der understøtter Premium lagerplads med læse-cache aktiveret. Der er én undtagelse til denne anbefaling Hvis din TempDB brugen skrives meget, kan du opnå højere ydeevne ved at gemme TempDB på det lokale drev **D** , som er også SSD baseret på disse maskine størrelser.

### <a name="data-disks"></a>Datadisce

- **Brug datadisce for data og logfiler**: minimum Brug 2 Premium lagerplads [P30 diske](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) hvor én disk indeholder logfilen eller-filerne, og den anden indeholder data fil(er) og TempDB.

- **Disken Striping**: For flere overførselshastighed, kan du tilføje yderligere data diske og bruge Disk Striping. Hvis du vil finde ud af antallet datadisce, skal du analysere antallet af IOP'ER tilgængelig for dine data og logfiler disken(e). Disse oplysninger finder du i tabellerne på IOP'ER per [VM størrelse](virtual-machines-windows-sizes.md) og disk størrelse i følgende artikel: [Ved hjælp af Premium lagerplads om en disk](../storage/storage-premium-storage.md). Brug følgende disse retningslinjer:

    - Til Windows 8/Windows Server 2012 eller nyere, kan bruge [Lagerplads mellemrum](https://technet.microsoft.com/library/hh831739.aspx). Angiv stripestørrelse til 64 KB for OLTP arbejdsmængder og 256 KB for lager arbejdsbelastninger, som data for at undgå indflydelse på ydeevnen på grund af partition justeret. Desuden angive AntalKolonner = antallet af fysiske diske. Hvis du vil konfigurere en lagerplads med mere end 8 diske skal du bruge PowerShell (ikke Server Manager UI) til at angive antallet kolonner, så det svarer til antallet af diske eksplicit. Du kan finde flere oplysninger om, hvordan du konfigurerer [Lagerplads mellemrum](https://technet.microsoft.com/library/hh831739.aspx), cmdlets til [lagerplads mellemrum i Windows PowerShell](https://technet.microsoft.com/library/jj851254.aspx)

    - Til Windows 2008 R2 eller tidligere, kan du bruge dynamiske diske (OS spredt enheder) og stripestørrelse er altid 64 KB. Bemærk, at denne indstilling frarådes og Windows 8/Windows Server 2012. Du kan finde oplysninger sætningen support på [tjenesten virtuel Disk skifte til Windows Storage Management API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

    - Hvis din arbejdsbyrde er ikke logget intensivt og behøver ikke dedikeret IOP'er, kan du konfigurere kun én lagerpulje. Ellers skal du oprette to lagerplads grupper, én til logfilen eller-filerne og en anden lagerpulje for data fil(er) og TempDB. Bestemme antallet af diske, der er knyttet til hver lagerpulje, der er baseret på dine Indlæs forventninger. Husk på, at forskellige VM størrelser tillader forskellige antal vedhæftede datadisce. Du kan finde yderligere oplysninger finder [størrelser til virtuelle computere](virtual-machines-windows-sizes.md).

    - Hvis du ikke bruger Premium lagerplads (Udviklingscenter/afprøve scenarie), anbefales det at tilføje det maksimale antal datadisce, der understøttes af din [VM størrelse](virtual-machines-windows-sizes.md) og bruge disken Striping.

- **Cachelagring politik**: til Premium lagerplads data disk aktivere læse-cache på den data disk, der er vært for dine datafiler og TempDB kun. Hvis du ikke bruger Premium lagerplads, skal du ikke aktivere en hvilken som helst cachelagring på en hvilken som helst datadisce. Finder instruktioner til konfiguration af disk cachelagring, i følgende emner: [Angive AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) og [Angive AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx).

    >[AZURE.WARNING] Stoppe tjenesten SQL Server, når du ændrer indstillingen cache Azure VM diske for at undgå risikoen for en hvilken som helst beskadigelse af databasen.

- **NTFS allokering enhedsstørrelse**: Når du formaterer data disken, anbefales det, at du bruger en 64 KB allokering enhedsstørrelse for data og logfiler samt TempDB.

- **Bedste fremgangsmåder til disk management**: Når fjerne en datadisk eller ændre dens cachetype stop tjenesten SQL Server under ændringen. Når indstillingerne for cachelagring ændres på OS disken, Azure stopper VM, ændres typen cache og genstarter VM. Når cacheindstillinger af en datadisk ændres, hvor VM er stoppet ikke, men data disken er fjernet fra VM under ændringen og derefter vedhæftet igen.

    >[AZURE.WARNING] Fejl ved at stoppe tjenesten SQL Server under disse handlinger kan medføre beskadigelse af databasen.

## <a name="io-guidance"></a>I/o-vejledning

- De bedste resultater med Premium lagerplads nås, når du parallelize programmet og anmodninger. Premium lagerplads henvender sig til scenarier, hvor EY kø dybde er større end 1, så du får vist meget lidt eller ingen ydeevne single-threaded seriel anmodninger om (også selvom de er lagerplads intensivt). For eksempel påvirke dette single-threaded testresultaterne af ydeevnen analyseværktøjerne, som SQLIO.

- Overvej at bruge [databasen siden komprimering](https://msdn.microsoft.com/library/cc280449.aspx) , som det kan hjælpe med at forbedre ydeevnen af i/o-intensivt arbejdsmængder. Data komprimeringen kan dog øge CPU-forbrug på databaseserveren.

- Overvej at aktivere Hurtigsøgning fil initialisering at reducere den tid, der kræves til første fil allokering. For at kunne udnytte initialisering Chat-fil, du give SQL Server (MSSQLSERVER) servicekonto med SE_MANAGE_VOLUME_NAME og tildele den til sikkerhedspolitik **Udføre lydstyrken vedligeholdelsesopgaver** . Hvis du bruger en SQL Server-platform billede til Azure, er ikke service standardkontoen (NT Service\MSSQLSERVER) føjet til sikkerhedspolitik **Udføre lydstyrken vedligeholdelsesopgaver** . Med andre ord er Chat fil initialisering ikke aktiveret i en SQL Server Azure platform billede. Når du har tilføjet SQL Server service-kontoen til at **Udføre opgaver til lydstyrke vedligeholdelse** sikkerhedspolitik, genstart tjenesten SQL Server. Der kan være sikkerhedsovervejelser i forbindelse med denne funktion. Du kan finde yderligere oplysninger finder [Database fil initialisering](https://msdn.microsoft.com/library/ms175935.aspx).

- **dobbelte størrelse** anses for blot en begivenhed til uventede vækst. Administrer ikke dine data og logfiler vækst i det daglige med Auto-Udvid. Hvis Auto-Udvid bruges, før vokse filen ved hjælp af parameteren størrelse.

- Sørg for, at **autoshrink** er deaktiveret for at undgå unødvendig omkostninger, der kan påvirke ydeevnen negativt.

- Flytte alle databaser til data disk herunder systemdatabaser. Se [Flytte systemdatabaser](https://msdn.microsoft.com/library/ms345408.aspx)kan finde flere oplysninger.

- Flytte SQL Server fejl log og sporing fil kataloger til datadisce. Dette kan gøres i SQL Server Configuration Manager ved at højreklikke på din SQL Server-forekomst og vælge Egenskaber. Indstillingerne fejl log og sporing fil kan ændres i fanen **Startparametre** . Mappe til lagring er angivet i under fanen **Avanceret** . Følgende skærmbillede viser hvor du vil søge efter parameteren error log Start.

    ![SQL ErrorLog skærmbillede](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

- Konfigurere standard sikkerhedskopierings- og database filplaceringer. Brug anbefalingerne i dette emne, og foretag ændringerne i vinduet Server egenskaber. Flere oplysninger under [få vist eller rediger de standard placeringer for Data og logfiler (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Følgende skærmbillede viser, hvor du vil foretage disse ændringer.

    ![SQL Data Log og sikkerhedskopiering af filer](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)

- Aktivere låste sider for at reducere EY og sideinddeling aktiviteter. Yderligere oplysninger finder du i [aktivere Lås sider i hukommelse (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Hvis du kører SQL Server 2012, skal du installere Service Pack 1 Akkumuleret opdatering 10. Denne opdatering indeholder fix dårlig ydeevne på I/O, når du udfører Vælg til midlertidig tabel-sætning i SQL Server 2012. Du kan finde oplysninger i denne [artikel i knowledge base](http://support.microsoft.com/kb/2958012).

- Overvej at komprimere eventuelle datafiler, når du overfører ind/ud af Azure.

## <a name="feature-specific-guidance"></a>Funktionen specifikke vejledninger

Visse installationer kan opnå forbedret ydeevnefordele ved hjælp af mere avancerede teknikker til konfiguration. Følgende liste over fremhæver nogle SQL Server-funktioner, der kan hjælpe dig med at opnå en bedre ydeevne:

- **Sikkerhedskopi til Azure-lager**: Når du udfører sikkerhedskopier til SQL Server, der kører på Azure virtuelle computere, du kan bruge [SQL Server Backup til URL-adresse](https://msdn.microsoft.com/library/dn435916.aspx). Denne funktion er tilgængelige fra og med SQL Server 2012 SP1 CU2 og anbefales til sikkerhedskopiering op til de tilknyttede datadisce. Når du Sikkerhedskopiering/gendannelse til/fra Azure-lager, følge de anbefalinger, der er angivet i [SQL Server sikkerhedskopiering til URL-adressen bedste praksis og fejlfinding og Restoring fra sikkerhedskopier gemmes i Azure-lager](https://msdn.microsoft.com/library/jj919149.aspx). Du kan også automatisere disse sikkerhedskopier af ved hjælp af [Automatisk sikkerhedskopiering til SQL Server på virtuelle Azure-computere](virtual-machines-windows-classic-sql-automated-backup.md).

    Du kan bruge [SQL Server Backup til Azure-værktøj](https://www.microsoft.com/download/details.aspx?id=40740)før SQL Server 2012. Dette værktøj kan hjælpe med at øge sikkerhedskopiering kapaciteten ved hjælp af flere sikkerhedskopiering stribe destinationer.

- **SQL Server-datafilerne i Azure**: denne nye funktion, [SQL Server-datafilerne i Azure](https://msdn.microsoft.com/library/dn385720.aspx), er tilgængelige fra og med SQL Server-2014. Kører SQL Server med datafiler i Azure demonstrerer tilsvarende ydeevne egenskaber som at bruge Azure datadisce.

## <a name="next-steps"></a>Næste trin

Hvis du er interesseret i at udforske SQL Server og Premium lager mere detaljeret, skal du se artiklen [Brug Azure Premium lager med SQL Server på virtuelle maskiner](virtual-machines-windows-classic-sql-server-premium-storage.md).

Du kan finde bedste fremgangsmåder for sikkerhed, [Sikkerhedsovervejelser for SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-security.md).

Gennemgå andre emner i SQL Server Virtual Machine på [SQL Server på Azure virtuelle maskiner oversigt](virtual-machines-windows-sql-server-iaas-overview.md).
