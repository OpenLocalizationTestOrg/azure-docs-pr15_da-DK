<properties
    pageTitle="Overføre til Azure Premium lagerplads | Microsoft Azure"
    description="Overføre din eksisterende virtuelle maskiner til Azure Premium-lager. Premium lagerplads giver høj ydeevne og lav ventetid disk understøttelse af jeg/O-intensivt arbejdsbelastninger, som kører på virtuelle Azure-computere."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="yuemlu"/>


# <a name="migrating-to-azure-premium-storage"></a>Overføre til Azure Premium-lager

## <a name="overview"></a>Oversigt

Azure Premium lagerplads tilbyder høj ydeevne og lav ventetid disk understøttelse af virtuelle maskiner, der kører jeg/O-intensivt arbejdsmængder. Du kan drage fordel af hastigheden og ydeevnen af disketterne ved at overføre dit program VM diske til Azure Premium-lager.

Formålet med denne vejledning er at nye brugere af Azure Premium lager bedre forberede dig på at foretage en problemfri overgang fra deres aktuelle system til Premium-lager. Vejledningen adresser tre af de vigtigste komponenter i denne proces: 

  - [Plan for overførsel til Premium-lager](#plan-the-migration-to-premium-storage)
  - [Forberede og kopiere virtuelle harddiske (virtuelle harddiske) til Premium-lager](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
  - [Oprette Azure virtuelt ved hjælp af Premium lagerplads](#create-azure-virtual-machine-using-premium-storage)

Du kan overføre FOS fra andre platforme til Azure Premium lager eller overføre eksisterende Azure FOS fra Standard lagerplads til Premium-lager. Denne vejledning beskrives trin til begge to scenarier. Følg de trin, der er angivet i det relevante afsnit afhængigt af scenariet.

>[AZURE.NOTE] Du kan finde en oversigt over funktioner og priser Premium lager i Premium lagerplads: [High-Performance lagerplads til Azure virtuelt arbejdsmængder](storage-premium-storage.md). Vi anbefaler, at overføre et virtuelt diskplads, der kræver høj IOP'ER til Azure Premium lager til den bedste ydeevne for dit program. Hvis harddisken ikke kræver høj IOP'ER, kan du begrænse omkostninger ved at vedligeholde i Standard-lager, som lagrer virtuelt diskdata på harddiske (harddiske) i stedet for SSDs.

Fuldfør overførselsprocessen i sin helhed kan kræve ekstra handlinger, både før og efter trinnene i denne vejledning. Som eksempler kan nævnes konfiguration af virtuelle netværk eller slutpunkter eller ændrer kode selve programmet, kan kræve nogle nedetid i programmet. Disse handlinger er entydige for hvert program, og du skal fuldføre dem sammen med følge trinnene i denne vejledning for at gøre fuld overgangen til Premium-lager som problemfri som muligt.


## <a name="plan-the-migration-to-premium-storage"></a>Planlægge i forhold til overførsel til Premium-lager

Dette afsnit sikrer, at du er klar til at følge trinnene i denne artikel overførsel, og hjælper dig med at gøre det bedste beslutning på VM og Disk.

### <a name="prerequisites"></a>Forudsætninger
- Du skal et Azure-abonnement. Hvis du ikke har en, kan du oprette et abonnement til én måned [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/) eller gå til [Azure priser](https://azure.microsoft.com/pricing/) til flere indstillinger.
- Hvis du vil udføre PowerShell-cmdletter, skal du Microsoft Azure PowerShell-modulet. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) til installationsanvisningerne punkt og installation.
- Når du planlægger at bruge Azure VM'er, der kører på Premium lagerplads, skal du bruge de Premium lagerplads kan FOS. Du kan bruge både Standard og Premium lagerplads med Premium lagerplads kan FOS. Premium lagerplads diske bliver tilgængelig med flere VM typer i fremtiden. Se [størrelser for virtuelle maskiner](../virtual-machines/virtual-machines-windows-sizes.md) og [skriftstørrelser for Cloud Services](../cloud-services/cloud-services-sizes-specs.md)for flere oplysninger om alle tilgængelige Azure VM disktyper og -størrelser.

### <a name="considerations"></a>Overvejelser i forbindelse med

En Azure VM understøtter vedhæfte flere diske Premium lagerplads, så dine programmer kan have op til 64 TB storage per VM. Dine programmer kan opnå 80.000 IOP'ER (input/output-operationer sekundet) per VM og 2000 MB hver anden disk overførselshastighed per VM med meget lav latenstider for Læs handlinger med Premium lagerplads. Du har indstillinger i en række forskellige FOS og diske. Dette afsnit er at hjælpe dig med at finde en indstilling, der passer bedst til din arbejdsbyrde.

#### <a name="vm-sizes"></a>VM størrelser
Azure VM størrelse specifikationer findes i [størrelser til virtuelle computere](../virtual-machines/virtual-machines-windows-sizes.md). Gennemse karakteristika af virtuelle maskiner, der arbejder med Premium lagerplads, og vælg den bedst egnede VM størrelse, der passer bedst til din arbejdsbyrde. Sørg for, at der er tilstrækkelig båndbredde på din VM til at drive disk trafikken.


#### <a name="disk-sizes"></a>Diskstørrelser
Der findes tre typer diske, der kan bruges sammen med din VM og de har bestemt IOP'er og overførselshastighed begrænsninger. Tag, i betragtning disse begrænsninger, når du vælger disktypen til din VM baseret på behovene i dit program med hensyn til kapacitet, ydeevne og skalerbarhed og spidsbelastning indlæser.

|Premium lagerplads Disk Type|P10|R20 =|P30|
|:---:|:---:|:---:|:---:|
|Diskstørrelse|128 GB|512 GB|1024 GB (1 TB)|
|IOP'ER per disk|500|2300|5000|
|Overførselshastighed per disk|100 MB sekundet|150 MB sekundet|200 MB sekundet|

Afgøre, om yderligere data diske er nødvendige for din VM, afhængigt af din arbejdsbyrde. Du kan vedhæfte flere fast datadisce til din VM. Hvis det er nødvendigt, kan du fordeler over disk, der øger kapacitet og ydeevnen af lydstyrken. (Se hvad er disken Striping [her](storage-premium-storage-performance.md#disk-striping).) Hvis du fordeler Premium lagerplads datadisce ved hjælp af [Lagerplads mellemrum][4], skal du konfigurere det med én kolonne for hver disk, der bruges. Ellers overordnede ydeevne på Stribet lydstyrken måske mindre end forventet på grund af ulige fordelingen af trafikken på tværs af diskene. Du kan bruge værktøjet *mdadm* til at opnå den samme for Linux FOS. Se artiklen [Konfigurere softwaren RAID på Linux](../virtual-machines/virtual-machines-linux-configure-raid.md) få mere at vide.

#### <a name="storage-account-scalability-targets"></a>Lagerplads konto skalerbarhed destinationer
Premium lagerplads konti har følgende skalerbarhed mål foruden [Azure lagerplads skalerbarhed og ydeevne mål](storage-scalability-targets.md). Hvis dine krav til programmet overskrider skalerbarhed mål for en enkelt lagerplads konto, opbygge dit program tilladelse til at bruge flere lagerplads konti og dele dine data på tværs af disse lagerplads konti.

|Samlet højde for kapacitet|Samlede båndbredde til et lokalt overflødige lagerplads konto|
|:--|:---|
|Disk kapacitet: 35TB<br />Snapshot kapacitet: 10 TB|Op til 50 Gigabit sekundet for indgående + udgående|

Flere oplysninger om Premium lagerplads specifikationer, se [skalerbarhed og ydeevne destinationer, når du bruger Premium lagerplads](storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

#### <a name="disk-caching-policy"></a>Disken cachelagring politik
Disken cachelagring politik er som standard *Skrivebeskyttet tilstand* for alle dataene, Premium diske og *Læse-og skriveadgang* i forbindelse med Premium operativsystem disken, der er knyttet til VM. Denne indstilling for søgekonfiguration anbefales at opnå optimal ydeevne til dit program IOs. Om Skriv aktiveret eller kun skrive data disk (såsom SQL Server-logfilerne), kan du deaktivere disk cachelagring af så kan du opnå en bedre ydeevne af programmet. Cacheindstillinger om en eksisterende data disk kan blive opdateret via [Azure Portal](https://portal.azure.com) eller parameteren *- HostCaching* af cmdlet'en *Set-AzureDataDisk* til.

#### <a name="location"></a>Placering
Vælg en placering, hvor Azure Premium lager er tilgængelig. Se [Azure Services efter område](https://azure.microsoft.com/regions/#services) for opdaterede oplysninger om tilgængelige placeringer. FOS placeret i det samme område som kontoen lagerplads, butikker diske til VM giver meget bedre ydeevne end hvis de er i separate områder.

#### <a name="other-azure-vm-configuration-settings"></a>Andre Azure VM konfigurationsindstillinger
Når du opretter en Azure VM, vil du bedt om at konfigurere bestemte indstillinger for VM. Husk, at nogle indstillinger er rettet til levetiden for VM, mens du kan ændre eller tilføje andre senere. Gennemse disse Azure VM konfigurationsindstillinger, og Sørg for, at disse er konfigureret korrekt så de passer til dine krav til arbejdsbelastningen.

### <a name="optimization"></a>Optimering

[Azure Premium lager: Design med høj ydeevne](storage-premium-storage-performance.md) får du retningslinjer til udvikling af høj ydeevne programmer ved hjælp af Azure Premium-lager. Du kan følge retningslinjerne kombineres med ydeevnen bedste fremgangsmåder, der gælder for teknologier, der bruges af dit program.


## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Forberede og kopiere virtuelle harddiske (virtuelle harddiske) til Premium-lager

Følgende afsnit indeholder retningslinjer for at forberede virtuelle harddiske fra din VM og kopiere virtuelle harddiske til Azure-lager.

- [Scenarie 1: "jeg overføre eksisterende Azure FOS til Azure Premium-lager."](#scenario1)
- [Scenarie 2: "jeg skifte FOS fra andre platforme til Azure Premium-lager."](#scenario2)

### <a name="prerequisites"></a>Forudsætninger

For at forberede de virtuelle harddiske til overførsel skal du:

- Et Azure-abonnement, en lagerplads konto og en objektbeholder i den pågældende lagerplads-konto, som du kan kopiere din Virtuelle. Bemærk, at kontoen destination lagerplads kan være en Standard- eller Premium lagerplads konto afhængigt af dine behov.
- Et værktøj til generalize den virtuelle harddisk, hvis du planlægger at oprette flere forekomster af VM fra den. For eksempel sysprep til Windows eller virt-sysprep for Ubuntu.
- Et værktøj til at overføre filen Virtuelle til kontoen lagerplads. Se [overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md) , eller brug en [Azure lagerplads explorer](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Denne vejledning beskrives kopiere din Virtuelle ved hjælp af værktøjet AzCopy.

> [AZURE.NOTE] Hvis du vælger synkron kopiere kopi mulighed med AzCopy, for at opnå optimal ydeevne, din Virtuelle ved at køre en af disse værktøjer fra en Azure VM, der er i samme område som destination lagerplads konto. Hvis du kopierer en virtuel harddisk fra en Azure VM i et andet område, kan din ydeevnen være langsommere.
>
> Kopiere en stor mængde data over begrænset båndbredde, kan du overveje at [ved hjælp af tjenesten Azure Importér/Eksportér for at overføre data til Blob-lager](storage-import-export-service.md); Dette giver dig mulighed at overføre dine data ved forsendelse harddiskdrev til en Azure datacenter. Du kan bruge tjenesten Azure Importér/Eksportér til at kopiere data til en standard-lagerplads konto. Når dataene er i din standard lagerplads konto, kan du bruge enten [Kopiér Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) eller AzCopy til at overføre data til kontoen premium lagerplads.
>
> Bemærk, at Microsoft Azure kun understøtter fast størrelse Virtuelle filer. VHDX filer eller dynamiske virtuelle harddiske understøttes ikke. Hvis du har en dynamisk virtuel harddisk, kan du konvertere den til fast størrelse ved hjælp af [Konverter Virtuelle](http://technet.microsoft.com/library/hh848454.aspx) cmdlet.

### <a name="scenario1"></a>Scenarie 1: "jeg overføre eksisterende Azure FOS til Azure Premium-lager."

Hvis du vil overføre eksisterende Azure FOS, stop VM, forberede virtuelle harddiske per typer Virtuelle, du vil og derefter kopiere den virtuelle harddisk med AzCopy eller PowerShell.

VM skal være helt ned til at overføre en ren tilstand. Der vil være en nedetid, indtil overførslen er fuldført.

#### <a name="step-1-prepare-vhds-for-migration"></a>Trin 1. Forberede virtuelle harddiske til overførsel

Hvis du vil overføre eksisterende Azure FOS til Premium-lager, være din Virtuelle:

- Billede af en generalized operativsystem
- En entydig operativsystem disk
- En datadisk

Nedenfor gennemgår vi disse 3 scenarier til forberedelse af din Virtuelle.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Bruge en generalized operativsystem virtuel harddisk til at oprette flere VM forekomster

Hvis du sender en virtuel harddisk, der skal bruges til at oprette flere generisk Azure VM forekomster, skal du først generalisere Virtuelle ved hjælp af en sysprep-funktionen. Dette gælder for en virtuel harddisk, der er lokalt eller i skyen. Sysprep fjerner en hvilken som helst computer-specifikke oplysninger fra den virtuelle harddisk.

>[AZURE.IMPORTANT] Tage et øjebliksbillede eller sikkerhedskopiere din VM før generalisering det. Køre sysprep stopper og deallokere VM forekomst. Følg trinnene nedenfor til sysprep en Windows OS virtuel harddisk. Bemærk, at køre kommandoen Sysprep kræver, at du at lukke den virtuelle maskine. Se [Oversigt over Sysprep](http://technet.microsoft.com/library/hh825209.aspx) eller [Teknisk vejledning til Sysprep](http://technet.microsoft.com/library/cc766049.aspx)kan finde flere oplysninger om Sysprep.

1. Åbn et kommandopromptvindue som administrator.
2. Skriv følgende kommando for at åbne Sysprep:

        %windir%\system32\sysprep\sysprep.exe

3. I systemforberedelsesværktøjet til forberedelse af, vælge Angiv System Out of Box Experience (OOBE), Markér afkrydsningsfeltet generalisere, Vælg **Luk computeren**og derefter klikke på **OK**, som vist i billedet nedenfor. Sysprep skal generalize operativsystemet og lukke systemet.

    ![][1]

Brug virt sysprep til at opnå den samme for en Ubuntu VM. Se [virt sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) få mere at vide. Se også nogle af Åbn kilden [Linux serverklargøring softwaren](http://www.cyberciti.biz/tips/server-provisioning-software.html) til andre Linux-operativsystemer.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Bruge et entydigt operativsystem Virtuelle til at oprette en enkelt forekomst af VM

Hvis du har et program, der kører på den VM som kræver, at computerens bestemte data, du ikke generalize den virtuelle harddisk. En ikke-generalized Virtuelle kan bruges til at oprette en entydig Azure VM forekomst. Eksempelvis hvis du har domænenavn fra domænecontrolleren på din Virtuelle, vil udfører sysprep gøre det ineffektiv som et domænenavn fra domænecontrolleren. Gennemse de programmer, der kører på din VM og påvirkningen af kører sysprep på dem, før du generalisering den virtuelle harddisk.

##### <a name="register-data-disk-vhd"></a>Registrere datadisk Virtuelle

Hvis du har datadisce i Azure overføres, skal du sikre dig VM'er, der bruger disse datadisce er lukket.

Følg trinnene beskrevet nedenfor til at kopiere Virtuelle til Azure Premium lager og registrere den som en klargjort datadisk.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Trin 2. Oprette destinationen for din Virtuelle

Oprette en lagerplads konto til at vedligeholde din virtuelle harddiske. Overvej følgende punkter, når du planlægger lagringssted til din virtuelle harddiske:

- Destinationen Premium lagerplads konto.
- Konto lagerplacering skal være det samme som Premium lagerplads FOS for stand til Azure du opretter i den sidste fase. Du kan kopiere til et nyt lagerplads firma eller planlægger at bruge den samme lagerplads-konto, der er baseret på dine behov.
- Kopier og Gem kontonøgle lagerplads på kontoen destination lagerplads til næste trin.

Om data disk, kan du vælge at beholde nogle datadisce i en standard-lager-konto (for eksempel diske, der har afkøler lagerplads), men vi anbefaler du flytter alle data for fremstilling arbejdsbyrde bruge premium-lager.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Trin 3. Kopiere Virtuelle med AzCopy eller PowerShell

Du skal finde din objektbeholder sti og lager kontonøgle til at behandle en af disse to indstillinger. Objektbeholder sti og lager kontonøgle kan findes i **Azure Portal** > **lagerplads**. Objektbeholder URL-adressen vil være identisk med "https://myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Mulighed 1: Kopiere en virtuel harddisk med AzCopy (asynkron kopi)

Ved hjælp af AzCopy, kan du nemt overføre den virtuelle harddisk via internettet. Afhængigt af størrelsen på de virtuelle harddiske, kan det tage tid. Husk at kontrollere lagergrænser konto vandindtrængen/udgangspunkt, når du bruger denne indstilling. Se [Azure lagerplads skalerbarhed og ydeevne mål](storage-scalability-targets.md) for detaljer.

1. Downloade og installere AzCopy her: [seneste version af AzCopy](http://aka.ms/downloadazcopy)
2. Åbn Azure PowerShell, og gå til den mappe, hvor AzCopy er installeret.
3. Brug følgende kommando til at kopiere den Virtuelle fil fra "Kilde" til "Destination".

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Eksempel:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd

    Her er en beskrivelse af de parametre, bruges i kommandoen AzCopy:

 - * */Datakilde: * &lt;kilde&gt;:*** placeringen af den mappe eller lagerplads objektbeholder Webadresse, der indeholder den virtuelle harddisk.
 - * */SourceKey: * &lt;kilde kontonøgle&gt;:*** lagerplads kontonøgle kilde lagerplads konto.
 - * */Dest: * &lt;destination&gt;:*** lagerplads objektbeholder URL-adressen til at kopiere den virtuelle harddisk til.
 - * */DestKey: * &lt;dest kontonøgle&gt;:*** lagerplads kontonøgle destination lagerplads konto.
 - * */Mønster: * &lt;filnavn&gt;:*** angive filnavnet på den virtuelle harddisk til at kopiere.

Få mere at vide om brug af AzCopy værktøj, kan du se [overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Mulighed 2: Kopiere en virtuel harddisk med PowerShell (Synchronized kopi)

Du kan også kopiere filen Virtuelle ved hjælp af PowerShell-cmdlet Start AzureStorageBlobCopy. Brug følgende kommando på Azure PowerShell til at kopiere Virtuelle. Erstat værdier i <> med tilsvarende værdier fra kontoen kilde- og destinationstabellerne lagerplads. Hvis du vil bruge denne kommando, skal du have en objektbeholder, kaldet virtuelle harddiske i kontoen destination lagerplads. Hvis objektbeholderen ikke findes, skal du oprette én før du kører kommandoen.

    $sourceBlobUri = <source-vhd-uri>

    $sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

    $destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

    Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext

Eksempel:

    C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

    C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

    C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

    C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext

### <a name="scenario2"></a>Scenarie 2: "jeg skifte FOS fra andre platforme til Azure Premium-lager."

Hvis du vil overføre Virtuelle fra ikke - Azure Cloud Storage til Azure, skal du først eksportere den virtuelle harddisk til en lokal mappe. Har fuldført kildestien til den lokale mappe, hvor Virtuelle er gemt praktiske, og derefter bruge AzCopy til at overføre den til Azure-lager.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Trin 1. Eksportere Virtuelle til en lokal mappe

##### <a name="copy-a-vhd-from-aws"></a>Kopiere en virtuel harddisk fra AWS

1. Hvis du bruger AWS, kan du eksportere EC2 forekomsten til en virtuel harddisk i en Amazon S3 Malerbøtte. Følg trinnene beskrevet i Amazon dokumentationen til eksport af Amazon EC2 forekomster til at installere værktøjet Amazon EC2 kommandolinjen (CLI) og køre kommandoen Opret-forekomst-eksport-opgave for at eksportere forekomsten EC2 til en Virtuelle fil. Sørg for at bruge **Virtuelle** til disken & #95; billede & #95; FORMATÉR variabel, når du kører kommandoen **Opret-forekomst-eksport-opgave** . Den eksporterede Virtuelle fil er gemt i den Amazon S3 Malerbøtte, du angiver under denne proces.

        aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
        --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX

2. Du kan hente filen Virtuelle fra S3 Malerbøtte. Vælg fil, Virtuelle, derefter **Handlinger** > **hente**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Kopiere en virtuel harddisk fra andre ikke-Azure skyen

Hvis du vil overføre Virtuelle fra ikke - Azure Cloud Storage til Azure, skal du først eksportere den virtuelle harddisk til en lokal mappe. Kopiere fuldført kildestien til den lokale mappe, hvor Virtuelle er gemt.

##### <a name="copy-a-vhd-from-on-premise"></a>Kopiere en virtuel harddisk fra lokalt

Hvis du overfører Virtuelle fra et lokalt miljø, skal du fuldført kildestien hvor Virtuelle er gemt. Kildestien kan være en server eller et filshare.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Trin 2. Oprette destinationen for din Virtuelle

Oprette en lagerplads konto til at vedligeholde din virtuelle harddiske. Overvej følgende punkter, når du planlægger lagringssted til din virtuelle harddiske:

- Kontoen mål lagerplads kan være standard eller førsteklasses lagerplads afhængigt af dit program krav.
- Området lagerplads konto skal være det samme som Premium lagerplads FOS for stand til Azure du opretter i den sidste fase. Du kan kopiere til et nyt lagerplads firma eller planlægger at bruge den samme lagerplads-konto, der er baseret på dine behov.
- Kopier og Gem kontonøgle lagerplads på kontoen destination lagerplads til næste trin.

Vi anbefaler du flytter alle data for fremstilling arbejdsbyrde bruge premium-lager.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Trin 3. Overføre den virtuelle harddisk til Azure-lager

Nu hvor du har din Virtuelle i den lokale mappe, kan du bruge AzCopy eller AzurePowerShell til at overføre .vhd-filen til Azure-lager. Begge indstillinger findes her:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Mulighed 1: Brug af Azure PowerShell Tilføj-AzureVhd til at overføre filen .vhd

    Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>

Et eksempel <Uri> kan være **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_**. Et eksempel <FileInfo> kan være **_"C:\path\to\upload.vhd"_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Mulighed 2: Brug af AzCopy til at overføre filen .vhd

Ved hjælp af AzCopy, kan du nemt overføre den virtuelle harddisk via internettet. Afhængigt af størrelsen på de virtuelle harddiske, kan det tage tid. Husk at kontrollere lagergrænser konto vandindtrængen/udgangspunkt, når du bruger denne indstilling. Se [Azure lagerplads skalerbarhed og ydeevne mål](storage-scalability-targets.md) for detaljer.

1. Downloade og installere AzCopy her: [seneste version af AzCopy](http://aka.ms/downloadazcopy)
2. Åbn Azure PowerShell, og gå til den mappe, hvor AzCopy er installeret.
3. Brug følgende kommando til at kopiere den Virtuelle fil fra "Kilde" til "Destination".

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Eksempel:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd

    Her er en beskrivelse af de parametre, bruges i kommandoen AzCopy:

 - * */Datakilde: * &lt;kilde&gt;:*** placeringen af den mappe eller lagerplads objektbeholder Webadresse, der indeholder den virtuelle harddisk.
 - * */SourceKey: * &lt;kilde kontonøgle&gt;:*** lagerplads kontonøgle kilde lagerplads konto.
 - * */Dest: * &lt;destination&gt;:*** lagerplads objektbeholder URL-adressen til at kopiere den virtuelle harddisk til.
 - * */DestKey: * &lt;dest kontonøgle&gt;:*** lagerplads kontonøgle destination lagerplads konto.
 - **/BlobType: side:** Angiver, at destinationen er en side blob.
 - * */Mønster: * &lt;filnavn&gt;:*** angive filnavnet på den virtuelle harddisk til at kopiere.

Få mere at vide om brug af AzCopy værktøj, kan du se [overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Andre indstillinger for overførsel af en virtuel harddisk

Du kan også overføre en virtuel harddisk til kontoen lagerplads ved hjælp af en af følgende måder:

- [Azure-lager kopi Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Azure-lager Explorer overførsel af BLOB](https://azurestorageexplorer.codeplex.com/)
- [Lagerplads Importér/Eksportér Service RESTEN API Reference](https://msdn.microsoft.com/library/dn529096.aspx)

>[AZURE.NOTE] Det anbefales at bruge Importér/Eksportér Service, hvis anslået overføre tid er længere end 7 dage. Du kan bruge [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) til at anslå tid fra data størrelse og filoverførsel enhed.
>
> Importér/Eksportér kan bruges til at kopiere til en standard-lager-konto. Du skal du kopierer fra standard lagerplads til premium lagerplads konto ved hjælp af et værktøj som AzCopy.


## <a name="create-azure-virtual-machine-using-premium-storage"></a>Oprette Azure FOS ved hjælp af Premium lagerplads

Når den virtuelle harddisk er overført eller kopieret til den ønskede lagerplads konto, skal du følge vejledningen i dette afsnit for at registrere den virtuelle harddisk som et billede af OS eller OS disk afhængigt af scenariet og derefter oprette en forekomst af VM fra den. Data disken Virtuelle kan knyttes til VM, når den er oprettet. Et eksempel på overførsel script er angivet i slutningen af dette afsnit. Dette simple script stemmer ikke overens alle scenarier. Du skal muligvis opdatere scriptet til at matche med din scenarie. For at se, hvis dette script gælder for det pågældende scenarie skal du se under [A eksempel overførsel Script](#a-sample-migration-script).

### <a name="checklist"></a>Tjekliste

1.  Vent, indtil alle de Virtuelle diske kopiere er fuldført.
2.  Kontrollér, at Premium Storage findes i det område, du vil overføre til.
3.  Bestem, den nye VM serie, du vil bruge. Det skal være en stand til Premium lagerplads, og størrelsen skal være alt efter tilgængelighed i området og baseret på dine behov.
4.  Bestemme den nøjagtige VM størrelse, du vil bruge. VM størrelse skal være stort nok til at understøtte antallet datadisce, du har. F.eks. Hvis du har 4 datadisce, have VM 2 eller flere kerner. Overvej også at behandle potens, hukommelse og netværksbåndbredde skal.
5.  Oprette en Premium lagerplads konto i target område. Dette er den konto, du vil bruge til den nye VM.
6.  Har de aktuelle VM detaljer praktiske, herunder listen over diske og tilsvarende Virtuelle BLOB.

Forberede din ansøgning om nedetid. For at udføre en ren overførsel, er det nødvendigt at stoppe alle behandling i det aktuelle system. Kun derefter kan du få det til konsistent tilstand, som du kan overføre til den nye platform. Nedetid varighed, afhænger af mængden data i disk, der overføre.

>[AZURE.NOTE] Hvis du opretter en Azure ressourcestyring VM fra en særlig Virtuelle Disk, kan du se [denne skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) til implementering af Ressourcestyring VM ved hjælp af eksisterende disk.

### <a name="register-your-vhd"></a>Registrere din Virtuelle

For at oprette en VM fra OS Virtuelle eller vedhæfte en datadisk til en ny VM, skal du først registrere dem. Følg trinnene nedenfor afhængigt af din Virtuelle scenarie.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalized operativsystem Virtuelle til at oprette flere Azure VM forekomster

Når generalized OS billede Virtuelle er overført til kontoen lagerplads, skal du registrere den som et **Azure VM billede** , så du kan oprette en eller flere forekomster af VM fra den. Brug følgende PowerShell-cmdlet'er til at registrere din Virtuelle som et Azure VM OS billede. Angiv den komplette objektbeholder URL-adresse, hvor Virtuelle blev kopieret til.

    Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

Kopiere og gemme navnet på denne Azure VM billede. I det foregående eksempel er det *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Entydige operativsystem Virtuelle til at oprette en enkelt forekomst af Azure VM

Når den entydige OS virtuelle harddisk er overført til kontoen lagerplads, du registrere den som en **Azure OS Disk** , så du kan oprette en forekomst af VM fra den. Brug følgende PowerShell-cmdlet'er til at registrere din Virtuelle som en Azure OS Disk. Angiv den komplette objektbeholder URL-adresse, hvor Virtuelle blev kopieret til.

    Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

Kopier og Gem navnet på denne nye Azure OS Disk. I det foregående eksempel er det *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Data disk Virtuelle skal knyttes til nye Azure VM forekomster

Når data disken Virtuelle er overført til lagerplads konto, du registrere den som en Azure Data Disk, så det kan knyttes til din nye DS serie DSv2 serier eller GS serie Azure VM forekomst.

Brug følgende PowerShell-cmdlet'er til at registrere din Virtuelle som en Azure Data Disk. Angiv den komplette objektbeholder URL-adresse, hvor Virtuelle blev kopieret til.

    Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Kopier og Gem navnet på denne nye Azure Data Disk. I det foregående eksempel er det *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Oprette en Premium lagerplads kan VM

Når billedet OS eller OS disken er registreret, skal du oprette en ny DS-serie, DSv2 serie eller GS serie VM. Du skal bruge operativsystem billedet eller operativsystem disken navn, du har registreret. Vælg typen VM Premium lagerplads niveau. I eksemplet herunder bruger vi *Standard_DS2* VM størrelse.

>[AZURE.NOTE] Opdater størrelsen at sikre, at det svarer til din kapacitet og krav til ydeevne og størrelsen Azure ledig diskplads.

Følg den trinvise PowerShell-cmdletter nedenfor for at oprette den nye VM. Først skal du indstille fælles parametre:

    $serviceName = "yourVM"
    $location = "location-name" (e.g., West US)
    $vmSize ="Standard_DS2"
    $adminUser = "youradmin"
    $adminPassword = "yourpassword"
    $vmName ="yourVM"
    $vmSize = "Standard_DS2"

Opret først en skybaseret tjeneste, hvor din nye FOS være vært.

    New-AzureService -ServiceName $serviceName -Location $location

Opret derefter forekomsten Azure VM afhængigt af scenariet, fra OS billede eller OS Disk, du har registreret.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalized operativsystem Virtuelle til at oprette flere Azure VM forekomster

Oprette en eller flere nye DS serie Azure VM forekomster ved hjælp af **Azure OS billede** , du har registreret. Angiv denne OS billede navn i sektionen VM konfiguration, når du opretter nye VM, som vist nedenfor.

    $OSImage = Get-AzureVMImage –ImageName "OSImageName"

    $vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

    Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

    New-AzureVM -ServiceName $serviceName -VM $vm

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Entydige operativsystem Virtuelle til at oprette en enkelt forekomst af Azure VM

Oprette en ny DS serie Azure VM forekomst ved hjælp af **Azure OS disken** , du har registreret. Angiv denne OS diskplads navn i sektionen VM konfiguration, når du opretter den nye VM, som vist nedenfor.

    $OSDisk = Get-AzureDisk –DiskName "OSDisk"

    $vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

    New-AzureVM -ServiceName $serviceName –VM $vm

Angiv andre Azure VM oplysninger som en skybaseret tjeneste, region, lagerplads konto, tilgængelighed og med cachelagring politik. Bemærk, at forekomsten VM skal placeres sammen med tilhørende operativsystem eller data disk så kontoen markerede skyen service, region og lager være alle på samme placering, som de underliggende virtuelle harddiske disse diske.

### <a name="attach-data-disk"></a>Vedhæft datadisk

Endelig, hvis du har registreret datadisk virtuelle harddiske, knytte dem til den nye Premium lagerplads kan Azure VM.

Brug følgende PowerShell-cmdlet til at vedhæfte datadisk til den nye VM og angive cachelagring politikken. I eksemplet herunder er den cachelagring politik indstillet til *skrivebeskyttet*.

    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

    Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

    Update-AzureVM  -VM $vm

>[AZURE.NOTE] Der kan være yderligere trin behov til at understøtte dit program, der er ikke omfattet af denne vejledning.

### <a name="checking-and-plan-backup"></a>Kontrollere og Planlæg sikkerhedskopiering

Når den nye VM er oppe at køre, åbne den med det samme log-id og din adgangskode er som den oprindelige VM, og bekræft den alt fungerer som forventet. Alle de indstillinger, herunder de stribede enheder, der ville være findes i den nye VM.

Det sidste trin er at planlægge sikkerhedskopiering og vedligeholdelse tidsplan for den nye VM baseret på programmets behov.

### <a name="a-sample-migration-script"></a>Et eksempel på overførsel script

Hvis du har flere FOS til at overføre, kan automatisering via PowerShell-scripts være nyttig. Følgende er et eksempel på script, som kan automatisere overførslen af en VM. Bemærk, under script er kun et eksempel, og der er nogle antagelser om de aktuelle VM diske. Du skal muligvis opdatere scriptet til at matche med din scenarie.

Forudsætninger er:

- Du opretter klassisk Azure FOS.
- Din datakilde OS disketter og kilde Data er i samme lagerplads konto og samme beholder. Hvis din OS disketter og Data ikke er på samme sted, kan du bruge AzCopy eller Azure PowerShell og overskrive lagerplads konti og beholdere virtuelle harddiske. Refererer til det forrige trin: [Kopiér Virtuelle med AzCopy eller PowerShell](#copy-vhd-with-azcopy-or-powershell). Redigere dette script for at imødekomme scenariet er et andet valg, men det anbefales at bruge AzCopy eller PowerShell, da det er nemmere og hurtigere.

Automatisering scriptet er angivet nedenfor. Erstatte tekst med dine oplysninger og opdatere scriptet til at matche med din scenarie.

>[AZURE.NOTE] Ved hjælp af det eksisterende script bevarer ikke netværkskonfiguration af dine imidlertid VM. Du skal nyt config indstillingerne netværk på din overførte FOS.

    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location

#### <a name="optimization"></a>Optimering

Den aktuelle VM konfiguration kan tilpasses specifikt for at fungerer fint med Standard diske. For eksempel at øge ydeevnen ved hjælp af mange diske i et Stribet lydstyrken. Du kan eksempelvis ikke kunne optimere omkostningerne ved at få en enkelt disk i stedet for bruger 4 diske separat på Premium lagerplads. Optimeringer som denne skal håndteres på grundlag af tilfælde og kræver brugerdefinerede trin efter overførslen. Bemærk også, at denne proces ikke fungerer muligvis godt for databaser og programmer, der afhænger af layoutet defineret i konfigurationen.

##### <a name="preparation"></a>Forberedelse

1.  Udfør Simple overførslen, som er beskrevet i det tidligere afsnit. Optimeringer der skal udføres for den nye VM efter overførslen.
2.  Definere de nye diskstørrelser, der kræves for optimerede konfigurationen.
3.  Bestemme tilknytningen af de aktuelle diske/enheder til de nye disk specifikationer.

##### <a name="execution-steps"></a>Udførelse af trin

1.  Opret de nye diske med højre størrelsen på Premium lagerplads VM.
2.  Log på VM og Kopiér dataene fra den aktuelle enhed til den nye disk, der er tilknyttet lydstyrken. Gøre dette for alle de aktuelle enheder, der skal knyttes til en ny disk.
3.  Derefter Skift programindstillinger til at skifte til de nye diske, og fjerne de gamle enheder.

Se [Optimering af ydeevne i programmet](storage-premium-storage-performance.md#optimizing-application-performance)til justering af programmet for at forbedre ydeevnen for diskplads.

### <a name="application-migrations"></a>Programmet overførsler

Databaser og andre komplekse programmer kan kræve specielle trin, som defineret af provideren programmet til overførslen. Se dokumentationen til respektive programmet. F.eks. typisk databaser kan overføres til sikkerhedskopiering og gendannelse.

## <a name="next-steps"></a>Næste trin

Se følgende ressourcer for bestemte scenarier, hvor overføre virtuelle maskiner:

- [Overføre Azure virtuelle maskiner mellem lagerplads konti](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
- [Oprette og overføre en Windows Server Virtuelle til Azure.](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md)
- [Oprette og overføre en virtuel harddisk, der indeholder Linux-operativsystemet](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md)
- [Overføre virtuelle maskiner fra Amazon AWS til Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Se også i følgende ressourcer for at lære mere om Azure-lager og virtuelle Azure-computere:

- [Azure-lager](https://azure.microsoft.com/documentation/services/storage/)
- [Azure virtuelle maskiner](https://azure.microsoft.com/documentation/services/virtual-machines/)
- [Premium opbevaring: High-Performance lagerplads til Azure virtuelt arbejdsmængder](storage-premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
