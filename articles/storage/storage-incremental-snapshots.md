<properties
    pageTitle="Bruge stigende snapshots til sikkerhedskopiering og gendannelse af Azure virtuelle maskiner | Microsoft Azure"
    description="Oprette en brugerdefineret løsning for sikkerhedskopiering og gendannelse af Azure virtuelt disken ved hjælp af trinvise snapshots."
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
    ms.date="10/18/2016"
    ms.author="aungoo"/>


# <a name="back-up-azure-virtual-machine-disks-with-incremental-snapshots"></a>Sikkerhedskopiere Azure virtuelt diske med trinvise snapshots

## <a name="overview"></a>Oversigt

Azure-lager giver mulighed for at tage øjebliksbilleder af BLOB. Snapshots registrere tilstanden blob på dette tidspunkt. I denne artikel beskriver vi et scenario, hvordan du kan bevare sikkerhedskopier af virtuelt diske ved hjælp af snapshots. Du kan bruge denne metode, når du vælger ikke at bruge Azure sikkerhedskopiering og gendannelse-tjenesten, og vil oprette en brugerdefineret sikkerhedskopiering strategi for virtuelt disken.

Azure virtuelt diske er gemt som siden BLOB i Azure-lager. Da vi taler om strategi for sikkerhedskopiering om en virtuelt disk i denne artikel, kan vi referere til snapshots i forbindelse med siden BLOB. Hvis du vil vide mere om snapshots skal referere til [oprettelse af et øjebliksbillede af en Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## <a name="what-is-a-snapshot"></a>Hvad er et øjebliksbillede?

Et snapshot af blob er en skrivebeskyttet version af en blob, der er hentet på et sted i gang. Når et øjebliksbillede er blevet oprettet, kan det være læse, kopieret, eller slettet, men ikke ændret. Snapshots ikke en metode til at sikkerhedskopiere en blob, som den vises på et tidspunkt, hvor tidspunkt. Indtil RESTEN version 2015-04-05 har du mulighed for at kopiere hele snapshots. Med RESTEN version 2015-07-08 og ovenfor, du kan også kopiere trinvise snapshots.

## <a name="full-snapshot-copy"></a>Fuld øjebliksbilleder

Snapshots kan kopieres til en anden lagerplads konto som en blob beholde sikkerhedskopier af grundlæggende blob. Du kan også kopiere et øjebliksbillede over dens base blob, som er som at gendanne blob til en tidligere version. Når et øjebliksbillede er kopieret fra den ene lagerplads konto til en anden, det fylder det samme mellemrum som grundlæggende side blob. Derfor kopiere hele snapshots fra den ene lagerplads konto til en anden langsom og vil også forbruge masse plads i kontoen target lagerplads.

>[AZURE.NOTE] Hvis du kopierer det grundlæggende blob til en anden destination, kopieres øjebliksbilleder af blob ikke sammen med den. På samme måde, hvis du overskriver en grundlæggende blob med en kopi, snapshots, der er knyttet til den grundlæggende blob påvirkes ikke og forbliver intakte under grundlæggende blob navn.

### <a name="back-up-disks-using-snapshots"></a>Sikkerhedskopiere diske ved hjælp af snapshots

Du kan tage periodiske snapshot af disk eller siden blob og kopiere dem til en anden lagerplads konto ved hjælp af funktioner som [Kopi Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) handling eller [AzCopy](storage-use-azcopy.md)som en sikkerhedskopi strategi for virtuelt disken. Du kan kopiere et øjebliksbillede til en destination siden blob med et andet navn. Resulterende destination siden blob er en skrivbar siden blob og ikke et øjebliksbillede. Senere i denne artikel beskriver vi trin for at tage sikkerhedskopier af virtuelt diske ved hjælp af snapshots.

### <a name="restore-disks-using-snapshots"></a>Gendanne diske ved hjælp af snapshots

Når det er tid til at gendanne harddisken til en tidligere stabil version, registreres i én af de ekstra snapshots, kan du kopiere et øjebliksbillede over grundlæggende side blob. Når snapshot hæves til siden grundlæggende blob, forbliver øjebliksbillede, men kilden overskrives med en kopi, der kan være både læses og skrives. Senere i denne artikel beskriver vi trin for at gendanne en tidligere version af harddisken fra dens øjebliksbillede.

### <a name="implementing-full-snapshot-copy"></a>Implementere fuld øjebliksbilleder

Du kan implementere en fuld øjebliksbillede kopi ved at gøre følgende

-   Først skal tage et øjebliksbillede af de grundlæggende blob ved hjælp af handlingen [Øjebliksbillede Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) .
-   Kopier derefter snapshot til en destination lagerplads-konto ved hjælp af [Kopi Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).
-   Gentag denne fremgangsmåde for at bevare sikkerhedskopier af dine grundlæggende blob.

## <a name="incremental-snapshot-copy"></a>Trinvis snapshot kopi

Den nye funktion i [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) API giver en meget bedre måde at sikkerhedskopiere snapshot af din side BLOB eller diske. API returnerer en liste over ændringer mellem grundlæggende blob og snapshots. Dette reducerer mængden af anvendt på kontoen ekstra lagerplads. API understøtter siden BLOB på Premium lagerplads, såvel som Standard lagerplads. Bruger denne API, kan du nu bygge hurtigere og effektivt sikkerhedskopiering løsninger til Azure FOS. Dette vil være tilgængelige med RESTEN versionen 2015-07-08 eller nyere.

Trinvis Snapshot kopi gør det muligt at kopiere fra den ene lagerplads konto til en anden forskellen mellem,

-   Grundlæggende blob og dens øjebliksbillede eller
-   En hvilken som helst to snapshot af den grundlæggende blob

Hvis følgende betingelser er opfyldt,

- Blob blev oprettet på Jan-1-2016 eller nyere.
- Blob blev ikke overskrevet med [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) eller [Kopiér Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) mellem to øjebliksbilleder.


**Bemærk**: Denne funktion er tilgængelig til Premium og Standard Azure siden BLOB.

Når du har en brugerdefineret strategi for sikkerhedskopiering, der bruger snapshots, kopiere snapshots fra den ene lagerplads konto til en anden kan være meget længe og bruger en masse lagerplads. I stedet for at kopiere hele snapshot til en ekstra lagerplads-konto, kan du skrive forskellen mellem hinanden snapshots til en sikkerhedskopien af siden blob. Denne måde er tid til at kopiere og plads til at gemme sikkerhedskopier væsentligt reduceret.

### <a name="implementing-incremental-snapshot-copy"></a>Implementere trinvise øjebliksbilleder

Du kan implementere trinvise øjebliksbilleder ved at gøre følgende

-   Tage et øjebliksbillede af de grundlæggende blob ved hjælp af [Øjebliksbillede Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Kopiere snapshot target ekstra lagerplads konto ved hjælp af [Kopi Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx). Dette er sikkerhedskopien af siden blob. Tage et øjebliksbillede af denne sikkerhedskopien af siden blob og gemme i sikkerhedskopiering konto.
-   Tage en anden øjebliksbillede af de grundlæggende blob ved hjælp af øjebliksbillede Blob.
-   Få forskellen mellem første og anden snapshot af grundlæggende blob ved hjælp af [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx). Brug den nye parameter **prevsnapshot** til at angive værdien af dette billede, du vil have vist forskellen med dato/klokkeslæt. Når denne parameter er til stede, omfatter RESTEN svaret kun de sider, der er blevet ændret mellem target øjebliksbillede og forrige øjebliksbillede, herunder Ryd sider.
-   Brug [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) for at anvende ændringerne på sikkerhedskopien af siden blob.
-   Til sidst skal tage et øjebliksbillede af sikkerhedskopien af siden blob og gemme den i ekstra lagerplads konto.

I næste afsnit, der gennemgås mere detaljeret hvordan du kan bevare sikkerhedskopier af diske ved hjælp af trinvise øjebliksbilleder

## <a name="scenario"></a>Scenarie

I dette afsnit beskriver vi et scenarie, der omfatter en brugerdefineret strategi for sikkerhedskopiering om en virtuelt disk ved hjælp af snapshots.

Overveje en DS serie Azure VM med en premium lagerplads P30 disk vedhæftet. P30 disken kaldet *mypremiumdisk* er gemt i en premium lagerplads konto med navnet *mypremiumaccount*. En almindelig lagerplads konto med navnet *mybackupstdaccount* bruges til at gemme sikkerhedskopien af *mypremiumdisk*. Vi vil gerne holde et øjebliksbillede af *mypremiumdisk* hver 12 timer.

Se [om Azure lagerplads konti](storage-create-storage-account.md)for at få mere for at vide om oprettelse af lagerplads konto og diske.

For at få mere for at vide om at sikkerhedskopiere Azure FOS, se [planlægge Azure VM sikkerhedskopier](../backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Trin til at vedligeholde sikkerhedskopier af en disk med trinvise snapshots

De trin, der er beskrevet nedenfor vil tage øjebliksbilleder af *mypremiumdisk* og vedligeholde sikkerhedskopier i *mybackupstdaccount*. Sikkerhedskopien bliver en standardside blob kaldet *mybackupstdpageblob*. Sikkerhedskopien af siden blob afspejler altid den samme tilstand som den sidste øjebliksbillede af *mypremiumdisk*.

1.  Opret først sikkerhedskopien af siden blob for harddisken premium lagerplads. For at gøre dette, tage et øjebliksbillede af *mypremiumdisk* navnet *mypremiumdisk_ss1*.
2.  Kopiér denne snapshot til mybackupstdaccount som en side blob kaldet *mybackupstdpageblob*.
3.  Tage et øjebliksbillede af *mybackupstdpageblob* kaldet *mybackupstdpageblob_ss1*, ved hjælp af [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) og gemme den i *mybackupstdaccount*.
4.  Under sikkerhedskopieringen, oprette en anden snapshot af *mypremiumdisk*, sig *mypremiumdisk_ss2*og gemme den i *mypremiumaccount*.
5.  Få de trinvise ændringer mellem de to snapshots, *mypremiumdisk_ss2* og *mypremiumdisk_ss1*, ved hjælp af [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) på *mypremiumdisk_ss2* med **prevsnapshot** parameter angivet til tidsstemplet for *mypremiumdisk_ss1*. Skrive disse trinvise ændringer til sikkerhedskopien af siden blob *mybackupstdpageblob* i *mybackupstdaccount*. Hvis der er slettede områder i de trinvise ændringer, skal de ryddes fra sikkerhedskopien af siden blob. Brug [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) til at skrive trinvise ændringer til sikkerhedskopien af siden blob.
6.  Tage et øjebliksbillede af de sikkerhedskopien af siden blob *mybackupstdpageblob*, kaldet *mybackupstdpageblob_ss2*. Slette den tidligere øjebliksbillede *mypremiumdisk_ss1* fra premium lagerplads konto.
7.  Gentag trin 4-6 hvert sikkerhedskopiering vindue. På denne måde, kan du bevare sikkerhedskopier af *mypremiumdisk* med en konto med standard-lager.

![Sikkerhedskopiere disken ved hjælp af trinvise snapshots](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Trin til at gendanne en disk fra snapshots

De trin, der er beskrevet nedenfor gendanner premium disk, *mypremiumdisk* til en tidligere snapshot fra sikkerhedskopien lagerplads konto *mybackupstdaccount*.

1.  Identificere det punkt i gang, du vil gendanne premium disk til. Antag, at det er øjebliksbillede *mybackupstdpageblob_ss2*, som er gemt i ekstra lagerplads konto *mybackupstdaccount*.
2.  Hæve øjebliksbillede *mybackupstdpageblob_ss2* i mybackupstdaccount, som den nye base sikkerhedskopien blob *mybackupstdpageblobrestored*.
3.  Tage et øjebliksbillede af denne gendannede sikkerhedskopien blob, kaldet *mybackupstdpageblobrestored_ss1*.
4.  Kopiere gendannede siden blob *mybackupstdpageblobrestored* fra *mybackupstdaccount* til *mypremiumaccount* som ny premium disk *mypremiumdiskrestored*.
5.  Tage et øjebliksbillede af *mypremiumdiskrestored*, kaldet *mypremiumdiskrestored_ss1* til oprettelse af fremtidige trinvise sikkerhedskopier.
6.  Punkt DS serie VM til de gendannede disk *mypremiumdiskrestored* og fjerne den gamle *mypremiumdisk* fra VM.
7.  Starte sikkerhedskopiering af processen beskrevet i forrige afsnit for de gendannede disk *mypremiumdiskrestored*, ved hjælp af *mybackupstdpageblobrestored* som sikkerhedskopien af siden blob.

![Gendanne disk fra snapshots](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Næste trin

Lær mere om at oprette snapshot af en blob og planlægge infrastrukturen VM sikkerhedskopiering ved hjælp af linkene nedenfor.

- [Oprette et øjebliksbillede af en Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Planlægge infrastrukturen VM sikkerhedskopiering](../backup/backup-azure-vms-introduction.md)
