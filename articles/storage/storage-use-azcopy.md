<properties
    pageTitle="Kopiere eller flytte data til lager med AzCopy | Microsoft Azure"
    description="Bruge værktøjet AzCopy til at flytte eller kopiere data til eller fra blob, tabel og filens indhold. Kopiere data til Azure-lager fra lokale filer, eller kopiere data i eller mellem lagerplads konti. Nemt overføre dine data til Azure-lager."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="micurd"/>

# <a name="transfer-data-with-the-azcopy-command-line-utility"></a>Overføre data med AzCopy kommandolinjeparametre Utility

## <a name="overview"></a>Oversigt

AzCopy er et Windows kommandolinjen værktøj til kopiering af data til og fra Microsoft Azure Blob-fil og tabel-lager, med enkle kommandoer med optimal ydeevne. Du kan kopiere data fra et objekt til en anden inden for din lagerplads konto eller mellem lagerplads konti.

> [AZURE.NOTE] Denne vejledning forudsætter, at du allerede har kendskab til [Azure-lager](https://azure.microsoft.com/services/storage/). Hvis ikke, skal du læse i dokumentationen til [Introduktion til Azure-lager](storage-introduction.md) vil være nyttige. Det vigtigste er, skal du [oprette en lagerplads konto](storage-create-storage-account.md#create-a-storage-account) for at begynde at bruge AzCopy.

## <a name="download-and-install-azcopy"></a>Hent og Installer AzCopy

### <a name="windows"></a>Windows

Hent den [nyeste version af AzCopy](http://aka.ms/downloadazcopy).

### <a name="maclinux"></a>Mac/Linux

AzCopy er ikke tilgængelig for Mac/Linux OSs. Azure CLI er dog et passende alternativ kopierer data til og fra Azure-lager. Læs [ved hjælp af Azure CLI med Azure-lager](storage-azure-cli.md) for at få mere at vide.

## <a name="writing-your-first-azcopy-command"></a>Oprettelsen af din første AzCopy-kommando

Den grundlæggende syntaks for AzCopy kommandoer er:

    AzCopy /Source:<source> /Dest:<destination> [Options]

Åbn et kommandovindue og gå til mappen AzCopy installation på din computer - hvor den `AzCopy.exe` eksekverbare er placeret. Hvis du vil, kan du kan føje installationsplacering AzCopy til en sti. Som standard installeres AzCopy til `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` eller `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

I følgende eksempler viser en række forskellige scenarier for at kopiere data til og fra Microsoft Azure BLOB-filer og tabeller. Se afsnittet [AzCopy parametre](#azcopy-parameters) for en detaljeret beskrivelse af de parametre, bruges i hver prøve.

## <a name="blob-download"></a>BLOB: hente

### <a name="download-single-blob"></a>Hente enkelt blob

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"

Bemærk, at hvis mappen `C:\myfolder` ikke findes, oprettes den og hente AzCopy `abc.txt ` til den nye mappe.

### <a name="download-single-blob-from-secondary-region"></a>Hente enkelt blob fra sekundær område

    AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Bemærk, at du skal have læseadgang geografisk overflødige lagerplads aktiveret.

### <a name="download-all-blobs"></a>Hent alle BLOB

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Lad os antage følgende BLOB er placeret i den angivne objektbeholder:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Efter overførslen, mappen `C:\myfolder` indeholder følgende filer:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Hvis du ikke angiver indstillingen `/S`, ingen blob, der hentes.

### <a name="download-blobs-with-specified-prefix"></a>Hente BLOB med angivne præfiks

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Forudsætter følgende BLOB er placeret i den angivne beholder. Alle BLOB begynder med præfikset `a` , der hentes:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Efter overførslen, mappen `C:\myfolder` indeholder følgende filer:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Præfikset gælder for den virtuelle mappe, som den første del af navnet på blob-formularer. I eksemplet ovenfor, stemmer den virtuelle mappe ikke overens angivne præfikset, så det ikke er hentet. Desuden, hvis indstillingen `\S` ikke er angivet, AzCopy ikke henter en hvilken som helst BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Angive tidspunkt for seneste ændring af eksporterede filer til det samme som kilde BLOB

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

Du kan også udelukke BLOB fra downloade baseret på deres senest har ændret tid. Eksempelvis hvis du vil udelukke blob, hvor den seneste ændrede tidsværdi er den samme eller nyere end destinationsfilen, føje den `/XN` indstilling:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

Eller hvis du vil udelukke blob, hvor den seneste ændrede tidsværdi er den samme eller ældre end destinationsfilen, føje den `/XO` indstilling:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

## <a name="blob-upload"></a>BLOB: overføre

### <a name="upload-single-file"></a>Overføre én fil

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"

Hvis objektbeholderen angivne destination ikke findes, oprettes den AzCopy og overføre filen til den.

### <a name="upload-single-file-to-virtual-directory"></a>Overføre én fil til virtuel mappe

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Hvis den angivne virtuelle mappe ikke findes, AzCopy bliver overført fil for at medtage den virtuelle mappe i navnet (*f.eks.*, `vd/abc.txt` i eksemplet ovenfor).

### <a name="upload-all-files"></a>Overføre alle filer

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

Angive indstillingen `/S` uploader indholdet af den angivne mappe til Blob storage gælder, hvilket betyder, at alle undermapper og deres filer kan overføres samt. For eksempel antager de følgende filer, der er placeret i mappen `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Efter Overførselshandlingen omfatter objektbeholderen følgende filer:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Hvis du ikke angiver indstillingen `/S`, gælder ikke bliver overført med AzCopy. Efter Overførselshandlingen omfatter objektbeholderen følgende filer:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-files-matching-specified-pattern"></a>Overføre filer, der svarer angivet mønster

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Forudsætter, at de følgende filer, der er placeret i mappen `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Efter Overførselshandlingen omfatter objektbeholderen følgende filer:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Hvis du ikke angiver indstillingen `/S`, AzCopy kun overfører blob, der ikke findes i en virtuel mappe:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Angiv den MIME-indholdstype af en destination blob

Som standard indstiller AzCopy indholdstypen af en destination blob til `application/octet-stream`. Begynder med version 3.1.0, kan du eksplicit angive indholdstype via indstillingen `/SetContentType:[content-type]`. Følgende syntaks angiver en indholdstype for alle BLOB i Overførselshandlingen.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Hvis du angiver `/SetContentType` uden en værdi, derefter AzCopy indstiller hver blob eller filens indholdstype ifølge dens filtypenavn.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## <a name="blob-copy"></a>BLOB: Kopiér

### <a name="copy-single-blob-within-storage-account"></a>Kopiere enkelt blob Storage firma

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt

Når du kopierer en blob inden for en konto med lagerplads, udføres [serversiden](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) kopiering.

### <a name="copy-single-blob-across-storage-accounts"></a>Kopiere enkelt blob på tværs af lagerplads konti

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Når du kopierer en blob på tværs af lagerplads konti, udføres [serversiden](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) kopiering.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Kopiere enkelt blob fra sekundær område til primære område

    AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Bemærk, at du skal have læseadgang geografisk overflødige lagerplads aktiveret.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Kopiere enkelt blob og tilhørende snapshot på tværs af lagerplads konti

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Efter kopieringen eller ej inkluderer objektbeholderen target blob og tilhørende snapshot. Hvis du allerede blob i det foregående eksempel har to snapshots, omfatter objektbeholderen følgende blob og snapshots:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Kopiere synkront BLOB på tværs af lagerplads konti

AzCopy som standard kopierer data mellem to lagerplads slutpunkter asynkront. Derfor kører kopieringen baggrunden med ekstra båndbredde kapacitet, der har ingen SLA med hensyn til hvordan hurtigt en blob skal kopieres, og Kontrollér AzCopy med jævne mellemrum Kopiér status indtil kopiering er fuldført eller mislykkedes.

Den `/SyncCopy` indstilling sikrer, at kopieringen får ensartede hastighed. AzCopy udfører synkron kopien ved at downloade BLOB du kopierer fra den angivne kilde til lokale hukommelse, og derefter overføre dem til Blob storage destinationen.

    AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

`/SyncCopy`kan oprette flere udgangspunkt omkostninger sammenlignet med asynkron kopi, den anbefalede tilgang er at bruge denne indstilling i en Azure VM, der er i samme område som kontoen kilde lagerplads for at undgå udgangspunkt omkostninger.

## <a name="file-download"></a>Fil: hente

### <a name="download-single-file"></a>Hente én fil

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Hvis den angivne kilde er en Azure filshare og derefter skal du enten angive det nøjagtige filnavn (*fx* `abc.txt`) til at hente en enkelt fil, eller angive indstillingen `/S` til at hente alle filer i del gælder. Forsøger at angive både en fil mønster og indstillingen `/S` sammen, medfører en fejl.

### <a name="download-all-files"></a>Hente alle filer

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Bemærk, at de tomme mapper ikke hentes.

## <a name="file-upload"></a>Fil: overføre

### <a name="upload-single-file"></a>Overføre én fil

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt

### <a name="upload-all-files"></a>Overføre alle filer

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Bemærk, at de tomme mapper ikke overføres.

### <a name="upload-files-matching-specified-pattern"></a>Overføre filer, der svarer angivet mønster

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## <a name="file-copy"></a>Fil: Kopiér

### <a name="copy-across-file-shares"></a>Kopiere på tværs af filshares

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

### <a name="copy-from-file-share-to-blob"></a>Kopiere fra filshare til blob

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Bemærk, at asynkron kopiering fra fillagring til siden Blob ikke understøttes.

### <a name="copy-from-blob-to-file-share"></a>Kopiere fra blob til filshare

    AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

### <a name="synchronously-copy-files"></a>Kopiere synkront filer

Du kan angive den `/SyncCopy` alternativ til at kopiere data fra fillagring til fillagring, fillagring til Blob-lager og Blob-lager til fillagring synkront, AzCopy gør dette ved at downloade kildedataene til lokale hukommelse og overføre den igen til destination.

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

Når du kopierer fra fillagring til Blob-lager, blob standardtypen er Bloker blob, bruger kan angive indstillingen `/BlobType:page` ændre typen destination blob.

Bemærk, at `/SyncCopy` kan generere yderligere udgangspunkt omkostninger sammenligne til asynkron kopi, den anbefalede tilgang er at bruge denne indstilling i Azure VM som er i samme område som kontoen kilde lagerplads for at undgå udgangspunkt omkostninger.

## <a name="table-export"></a>Tabel: eksportere

### <a name="export-table"></a>Eksportér tabel

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy skriver en manifestfil til den angivne destinationsmappe. Manifestfilen bruges i importen til at finde de nødvendige datafiler og udføre datavalidering. Manifestfilen bruger følgende navngivningskonvention som standard:

    <account name>_<table name>_<timestamp>.manifest

Brugeren kan også angive indstillingen `/Manifest:<manifest file name>` til at angive Manifestets filnavn.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest

### <a name="split-export-into-multiple-files"></a>Opdele Eksportér i flere filer

    AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy bruger en *lydstyrken indeks* i filnavne de opdelte data til at skelne mellem flere filer. Lydstyrken indekset består af to dele- *partition vigtige område indeks* og en *opdele fil indeks*. Begge indeks er baseret på nul.

Partition vigtige område indekset være 0, hvis brugeren ikke angive indstillingen `/PKRS`.

Antag for eksempel AzCopy genererer to datafiler, når brugeren angiver indstillingen `/SplitSize`. De resulterende filnavne data kan være:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Bemærk, at den mindste mulige værdien af indstillingen `/SplitSize` 32 MB. Hvis den angivne destination er Blob-lager, AzCopy vil opdele datafilen, når dens størrelser når størrelsesbegrænsning blob (200GB), uanset om alternativ `/SplitSize` er blevet angivet af brugeren.

### <a name="export-table-to-json-or-csv-data-file-format"></a>Eksportér tabel til JSON- eller csv-data-filformat

AzCopy som standard eksporterer tabeller til JSON-datafiler. Du kan angive indstillingen `/PayloadFormat:JSON|CSV` at eksportere tabeller som JSON- eller csv.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

Når du angiver formatet CSV-data, AzCopy også oprette en skemafil med filtypenavnet `.schema.csv` for hver datafil.

### <a name="export-table-entities-concurrently"></a>Eksport tabel enheder samtidigt

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy begynder samtidige handlinger for at eksportere objekter, når brugeren angiver indstillingen `/PKRS`. Hver operation eksporterer ét partition vigtige område.

Bemærk, at antallet af samtidige handlinger styres også af indstillingen `/NC`. AzCopy bruger antallet processorer som standardværdien for `/NC` når du kopierer tabel enheder, også selvom `/NC` blev ikke angivet. Når brugeren angiver indstillingen `/PKRS`, AzCopy bruger den mindste af de to værdier - partition vigtige områder kontra implicit eller eksplicit er angivet samtidige handlinger - bestemme antallet af samtidige handlinger til at starte. Få mere at vide Skriv `AzCopy /?:NC` på kommandolinjen.

### <a name="export-table-to-blob"></a>Eksportér tabel til blob

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy, genererer en JSON-datafil i objektbeholderen blob med følge navnekonventionen:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Den oprettede JSON-datafil følger formatet data til minimale metadata. Få at vide om dette data format, kan du se [Data Format for tabellen servicehandlinger](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Bemærk, at når du eksporterer tabeller til BLOB, vil AzCopy hente tabel enheder til lokale midlertidige datafiler og overfør derefter disse enheder til blob. Disse midlertidige filer er placeret i mappen journal fil med standardstien "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>", kan du angive indstillingen/Z: [journal-fil-mappe] til at ændre kladden fil mappeplacering og dermed ændre placeringen af midlertidige datafilerne. Midlertidige datafiler størrelse, bestemmes af din tabel enheder størrelse og den størrelse, du har angivet med indstillingen /SplitSize, selvom den midlertidige datafil i lokal disk, slettes med det samme, når den er blevet overført til blob, Kontroller, at du har tilstrækkelig lokal diskplads til at gemme disse midlertidige filer, før de er blevet slettet.

## <a name="table-import"></a>Tabel: Importér

### <a name="import-table"></a>Importér tabel

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace

Indstillingen `/EntityOperation` angiver, hvordan du indsætter objekter i tabellen. Mulige værdier er:

- `InsertOrSkip`: Springer et eksisterende objekt eller indsætter et nyt objekt, hvis den ikke findes i tabellen.
- `InsertOrMerge`: Flettes et eksisterende objekt eller indsætter et nyt objekt, hvis den ikke findes i tabellen.
- `InsertOrReplace`: Erstatter et eksisterende objekt eller indsætter et nyt objekt, hvis den ikke findes i tabellen.

Bemærk, at du ikke kan angive indstillingen `/PKRS` i scenariet import. I modsætning til eksport scenarie, hvor du skal angive indstillingen `/PKRS` for at starte samtidige handlinger skal AzCopy som standard starter samtidige handlinger når du importerer en tabel. Standardantallet af samtidige handlinger, der er i gang er lig med antallet af processorer; Du kan dog angive et andet antal samtidige med indstillingen `/NC`. Få mere at vide Skriv `AzCopy /?:NC` på kommandolinjen.

Bemærk, at AzCopy understøtter kun importeret til JSON, ikke CSV. AzCopy ikke understøtter tabel importen fra brugeroprettede JSON, og manifestet filer. Begge disse filer skal komme fra en AzCopy tabel eksport. For at undgå fejl, kan du ikke at ændre den eksporterede JSON eller manifestet fil.

### <a name="import-entities-to-table-using-blobs"></a>Importerer objekter til tabellen med BLOB

Forudsætter en Blob objektbeholder indeholder følgende: A JSON-fil, der repræsenterer en Azure-tabel og dens tilhørende manifestfilen.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Du kan køre følgende kommando for at importere objekter i en tabel med manifestfilen i blob objektbeholderen:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"

## <a name="other-azcopy-features"></a>Andre AzCopy funktioner

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Kun kopiere data, der ikke findes i destinationen

Den `/XO` og `/XN` parametre til at udelukke ældre eller nyere datakilde ressourcer fra der kopieres henholdsvis. Hvis du kun vil kopiere kilde ressourcer, der ikke findes i destinationen, kan du angive begge parametre i kommandoen AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Bemærk: Dette understøttes ikke, når den kilde eller destination er en tabel.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Bruge en fil fra et nøglecenter til at angive kommandolinjeparametre.

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

Du kan medtage en hvilken som helst AzCopy kommandolinjeparametre i en fil fra et nøglecenter. AzCopy behandler parametre i feltet fil, som hvis de havde er angivet på kommandolinjen skal udføre en direkte erstatning med indholdet af filen.

Forudsætter svarfilen `copyoperation.txt`, der indeholder følgende linjer. Hver AzCopy parameter kan angives på en enkelt linje

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

eller på separate linjer:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy mislykkes, hvis du opdeler parameteren på tværs af to linjer, som vist her til den `/sourcekey` parameter:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Bruge flere svarfiler til at angive kommandolinjeparametre.

Forudsætter svarfilen `source.txt` , der angiver en objektbeholder, kilde:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

Og en fil med navnet `dest.txt` , der angiver en destinationsmappe i filsystemet:

    /Dest:C:\myfolder

Og en fil med navnet `options.txt` , der angiver indstillinger for AzCopy:

    /S /Y

Hvis du vil ringe AzCopy med filerne svar, som alle placeret i en mappe `C:\responsefiles`, Brug denne kommando:

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy behandler denne kommando præcis, som hvis du har brugt alle de enkelte parametre på kommandolinjen:

    AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

### <a name="specify-a-shared-access-signature-sas"></a>Angive en delt adgang signatur (SAS)

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

Du kan også angive en SAS på objektbeholderen URI:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

### <a name="journal-file-folder"></a>Journal filmappe

Hver gang du udsteder en kommando til AzCopy, kontrollerer det, om en journal-fil findes i standardmappen, eller om den findes i en mappe, du har angivet via denne indstilling. Hvis journalfilen ikke findes på et sted, AzCopy behandler handlingen som ny og genererer en ny fil i journal.

Hvis journalfilen findes, kontrollerer AzCopy om kommandolinjen, som du indtaster svarer kommandolinjen i journal-fil. Hvis svarer til de to kommando linjer, genoptages AzCopy handlingen ufuldstændig. Hvis de ikke stemmer overens, bliver du bedt om enten overskrive journal-fil til at starte en ny handling eller for at annullere den aktuelle handling.

Hvis du vil bruge standardplaceringen for journal-fil:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Hvis du udelader indstilling `/Z`, eller angive indstillingen `/Z` uden stien til mappen, som vist ovenfor, AzCopy opretter journalfilen på standardplaceringen, som er `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Hvis journalfilen allerede findes, genoptages AzCopy handlingen baseret på journal-fil.

Hvis du vil angive en brugerdefineret placering for journalfilen:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

I dette eksempel opretter journal-fil, hvis den ikke allerede findes. Hvis den findes, fortsætter AzCopy handlingen baseret på journal-fil.

Hvis du vil fortsætte med handlingen AzCopy:

    AzCopy /Z:C:\journalfolder\

I dette eksempel genoptages den seneste handling, som muligvis ikke fuldført.

### <a name="generate-a-log-file"></a>Oprette en logfil

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Hvis du angiver indstillingen `/V` uden at give en sti til filen til den detaljerede logfil, derefter AzCopy opretter logfilen på standardplaceringen, som er `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Ellers kan du oprette en logfil i en brugerdefineret placering:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Bemærk, at hvis du angiver en relativ sti efter indstillingen `/V`, såsom `/V:test/azcopy1.log`, og derefter den detaljerede logfil er oprettet i den aktuelle arbejdsmappe inden for en undermappe med navnet `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Angiv antallet af samtidige handlinger til at starte

Indstillingen `/NC` angiver antallet samtidige kopi handlinger. Som standard starter AzCopy et bestemt antal samtidige handlinger at øge filoverførsel dataoverførsel. Antallet af samtidige handlinger er lig med antallet processorer, du har for tabel handlinger. For Blob og fil handlinger, antallet af samtidige handlinger er lig med 8 gange antallet processorer, du har. Hvis du kører AzCopy på tværs af et netværk med lav båndbredde, kan du angive en lavere tal for /NC for at undgå fejl skyldes ressource konkurrence.

### <a name="run-azcopy-against-azure-storage-emulator"></a>Kør AzCopy i forhold til Azure lagerplads Emulator

Du kan køre AzCopy mod [Azure lagerplads Emulator](storage-use-emulator.md) til BLOB:

    AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

og tabeller:

    AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table

## <a name="azcopy-parameters"></a>AzCopy parametre

Parametre for AzCopy er beskrevet nedenfor. Du kan også skrive et af følgende kommandoer fra kommandolinjen for at få hjælp på ved hjælp af AzCopy:

- Til detaljeret hjælp til AzCopy:`AzCopy /?`
- Detaljeret hjælp til en hvilken som helst AzCopy parameter:`AzCopy /?:SourceKey`
- Kommandolinjen eksempler:`AzCopy /?:Samples`

### <a name="sourcesource"></a>/ Datakilde: "kilde"

Specificerer kildedataene hvorfra du vil kopiere. Kilden kan være en fil system-mappe, en blob objektbeholder, en blob virtuel mappe, et filshare lagerplads, en lagerplads filmappe eller en Azure-tabel.

**Gælder for:** BLOB, filer, tabeller

### <a name="destdestination"></a>/ Dest: "destination"

Angiver destinationen for at kopiere til. Destinationen kan være en fil system-mappe, en blob objektbeholder, en blob virtuel mappe, et filshare lagerplads, en lagerplads filmappe eller en Azure-tabel.

**Gælder for:** BLOB, filer, tabeller

### <a name="patternfile-pattern"></a>/ Mønster: "fil-mønster"

Angiver et mønster i filen, der angiver, hvilke filer, der skal kopieres. Funktionsmåden for parameteren /Pattern bestemmes af placeringen af kildedataene, og tilstedeværelsen af indstillingen rekursiv tilstand. Rekursiv tilstand er angivet via indstillingen/s.

Hvis den angivne kilde er en mappe i filsystemet, derefter standard jokertegn er aktiveret, og det angivne fil mønster er matchet med filer i mappen. Hvis indstillingen /S er angivet, derefter AzCopy svarer til det angivne mønster mod alle filer i undermapper under mappen.

Hvis den angivne kilde er et blob objektbeholder eller virtuel mappe, der jokertegn ikke anvendt. Hvis indstillingen /S er angivet, derefter AzCopy fortolker mønster den angivne fil som et præfiks, du blob. Hvis indstillingen /S ikke er angivet, derefter AzCopy genkender fil mønster mod nøjagtige blob navne.

Hvis den angivne kilde er en Azure filshare og derefter skal du enten angive det nøjagtige filnavn, (fx abc.txt) for at kopiere en enkelt fil eller angive parameteren /S til at kopiere alle filer i del gælder. Forsøger at angive både en fil mønster og indstillingen medfører /S sammen en fejl.

AzCopy bruger store og små bogstaver tilsvarende, når /Source er en blob objektbeholder eller blob virtuel mappe, og bruger case-insensitive identiske i alle andre tilfælde.

Standard fil mønstret bruges, når der er angivet nogen fil mønster er *.* for et filsystem eller en tom præfiks for et Azure lagringssted. Angive flere filer mønstre understøttes ikke.

**Gælder for:** BLOB, filer

### <a name="destkeystorage-key"></a>/ DestKey: "lagerplads-nøgle"

Angiver kontonøgle lagerplads for ressourcen destination.

**Gælder for:** BLOB, filer, tabeller

### <a name="destsassas-token"></a>/ DestSAS: "sas-token"

Angiver en delt Access signatur (SAS) med læse- og SKRIVETILLADELSER tilladelser for destination (hvis relevant). Omgive SAS med dobbelte anførselstegn, som den muligvis indeholder kommandolinjen specialtegn.

Hvis destination ressourcen er et blob objektbeholder, et filshare eller en tabel, kan du enten angive denne indstilling, efterfulgt af SAS tokenet, eller du kan angive Sikkerhedstilknytningerne som en del af objektbeholderen destination blob, filshare eller tabellens URI, uden at denne indstilling.

Hvis kilde- og er begge blob, skal ligge destination blob i den samme lagerplads konto som kilde blob.

**Gælder for:** BLOB, filer, tabeller

### <a name="sourcekeystorage-key"></a>/ SourceKey: "lagerplads-nøgle"

Angiver kontonøgle lagerplads for ressourcen kilde.

**Gælder for:** BLOB, filer, tabeller

### <a name="sourcesassas-token"></a>/ SourceSAS: "sas-token"

Angiver en delt Access signatur med læse- og listen tilladelser for kilden (hvis relevant). Omgive SAS med dobbelte anførselstegn, som den muligvis indeholder kommandolinjen specialtegn.

Hvis kilde ressource er en blob objektbeholder, og hverken en nøgle eller en SAS leveres, blive, objektbeholderen blob læst via anonym adgang.

Hvis kilden er et filshare eller en tabel, skal en nøgle eller en SAS angives.

**Gælder for:** BLOB, filer, tabeller

### <a name="s"></a>/S

Angiver rekursiv tilstand for kopi handlinger. I rekursiv tilstand kopiere AzCopy alle BLOB eller filer, der svarer til den angivne fil mønster, herunder dem i undermapper.

**Gælder for:** BLOB, filer

### <a name="blobtypeblock--page--append"></a>/ BlobType: "Bloker" | "side" | "Tilføj"

Angiver, om destination blob er en blok blob, blob en side eller en Føj blob. Denne indstilling gælder kun, når du sender en blob. Ellers genereres en fejl. Hvis destinationen er et blob og denne indstilling ikke er angivet som standard opretter AzCopy en blok blob.

**Gælder for:** BLOB

### <a name="checkmd5"></a>/ CheckMD5

Beregner en MD5-hash for hentede data og bekræfter, at du giver MD5 resultat gemt BLOB eller filens indhold MD5 egenskaben stemmer overens med giver beregnede resultat. Kontrollér MD5 er slået fra som standard, så du skal angive denne indstilling for at udføre kontrollen MD5, når du henter data.

Bemærk, at Azure-lager ikke garanterer, at MD5 hashen gemt for den blob eller fil er opdateret. Det er kundens ansvar at opdatere MD5, når den blob eller fil er ændret.

AzCopy angives altid egenskaben indhold MD5 for et Azure blob eller en fil når du har overført til tjenesten.  

**Gælder for:** BLOB, filer

### <a name="snapshot"></a>/ Øjebliksbillede

Angiver, om du vil overføre snapshots. Denne indstilling gælder kun, når kilden er en blob.

Overførte blob snapshots omdøbes i dette format: blob-navn (snapshot-tid) .extension

Snapshots kopieres ikke som standard.

**Gælder for:** BLOB

### <a name="vverbose-log-file"></a>/ V: [detaljerede--logfil]

Output detaljeret statusmeddelelser i en logfil.

Som standard hedder den detaljerede logfil AzCopyVerbose.log i `%LocalAppData%\Microsoft\Azure\AzCopy`. Hvis du angiver en eksisterende filplacering for denne indstilling, føjes den detaljerede logfil til den pågældende fil.  

**Gælder for:** BLOB, filer, tabeller

### <a name="zjournal-file-folder"></a>/ Z: [journal-fil-mappe]

Angiver en journal filmappe genoptage en handling.

AzCopy understøtter altid genoptage Hvis handlingen er blevet afbrudt.

Hvis denne indstilling ikke er angivet, eller det er angivet uden en mappesti, skal derefter AzCopy oprette journalfilen på standardplaceringen, som er % LocalAppData%\Microsoft\Azure\AzCopy.

Hver gang du udsteder en kommando til AzCopy, kontrollerer det, om en journal-fil findes i standardmappen, eller om den findes i en mappe, du har angivet via denne indstilling. Hvis journalfilen ikke findes på et sted, AzCopy behandler handlingen som ny og genererer en ny fil i journal.

Hvis journalfilen findes, kontrollerer AzCopy om kommandolinjen, som du indtaster svarer kommandolinjen i journal-fil. Hvis svarer til de to kommando linjer, genoptages AzCopy handlingen ufuldstændig. Hvis de ikke stemmer overens, bliver du bedt om enten overskrive journal-fil til at starte en ny handling eller for at annullere den aktuelle handling.

Journalfilen slettes efter vellykket gennemførelse af handlingen.

Bemærk, at genoptage en handling fra en journal-fil, der er oprettet af en tidligere version af AzCopy ikke understøttes.

**Gælder for:** BLOB, filer, tabeller

### <a name="parameter-file"></a>/@:"parameter-file"

Angiver en fil, der indeholder parametre. AzCopy behandler parametre i filen, som om de er angivet på kommandolinjen.

I en fil fra et nøglecenter, kan du enten angive flere parametre på en enkelt linje eller angive hver parameter på en separat linje. Bemærk, at en enkelt parameter kan indeholde flere linjer.

Svarfiler kan omfatte kommentarer linjer, der begynder med symbolet, #.

Du kan angive flere svarfiler. Bemærk, at AzCopy ikke understøtter indlejrede svarfiler.

**Gælder for:** BLOB, filer, tabeller

### <a name="y"></a>/ Y

Udelader alle AzCopy bekræftelse meddelelser.

**Gælder for:** BLOB, filer, tabeller

### <a name="l"></a>/ L

Angiver en handling af listen. ingen data kopieres.

AzCopy vil fortolke den ved hjælp af af denne indstilling som en simulering til kørsel af kommandolinjen uden dette alternativ/l og tælle, hvor mange objekter skal kopieres, kan du angive indstillingen /V på samme tid at kontrollere, hvilke objekter skal kopieres i den detaljerede logfil.

Funktionsmåden for denne indstilling er også afhænger af placeringen af kildedataene og tilstedeværelsen af rekursiv tilstand indstilling /S fil mønster indstillingen og /Pattern.

AzCopy kræver liste og læsning tilladelse fra denne kildeplacering, når du bruger denne indstilling.

**Gælder for:** BLOB, filer

### <a name="mt"></a>/MT

Angiver den hentede fil senest ændret tid til at være den samme som kilde blob eller filens.

**Gælder for:** BLOB, filer

### <a name="xn"></a>/XN

Udelader en nyere datakilde ressource. Vil ikke blive kopieret ressourcen, hvis det tidspunkt for seneste ændring af kilden er den samme eller nyere end destination.

**Gælder for:** BLOB, filer

### <a name="xo"></a>/XO

Omfatter ikke en ældre kilde ressource. Vil ikke blive kopieret ressourcen, hvis det tidspunkt for seneste ændring af kilden er den samme eller ældre end destination.

**Gælder for:** BLOB, filer

### <a name="a"></a>/A

Overfører kun de filer, der har attributten Arkiv indstillet.

**Gælder for:** BLOB, filer

### <a name="iarashcnetoi"></a>/ IA: [RASHCNETOI]

Overfører kun de filer, der har en af de angivne attributter er angivet.

Tilgængelige attributter omfatter:

- R = skrivebeskyttede filer
- A = filer, der er klar til at arkivere
- S = systemfiler
- H = skjulte filer
- C = komprimeret filer
- N = Normal filer
- E = krypterede filer
- T = midlertidige filer
- O = Offline-filer
- Jeg = ikke-indekserede filer

**Gælder for:** BLOB, filer

### <a name="xarashcnetoi"></a>/ XA: [RASHCNETOI]

Udelader de filer, der har en af de angivne attributter er angivet.

Tilgængelige attributter omfatter:

- R = skrivebeskyttede filer
- A = filer, der er klar til at arkivere
- S = systemfiler
- H = skjulte filer
- C = komprimeret filer
- N = Normal filer
- E = krypterede filer
- T = midlertidige filer
- O = Offline-filer
- Jeg = ikke-indekserede filer

**Gælder for:** BLOB, filer

### <a name="delimiterdelimiter"></a>/ Afgrænser: "afgrænser"

Angiver den afgrænsningstegn, der bruges til at afgrænse virtuelle mapper i et blob-navn.

Som standard AzCopy bruger / som afgrænsningstegnet. AzCopy understøtter dog ved hjælp af et almindelige tegn (såsom @, # eller %) som en afgrænser. Hvis du vil medtage en af følgende specialtegn på kommandolinjen skal du sætte filnavn med dobbelte anførselstegn.

Denne indstilling kan kun anvendes til at hente BLOB.

**Gælder for:** BLOB

### <a name="ncnumber-of-concurrent-operations"></a>/ AK: "tal-for-samtidige-handlinger"

Angiver antallet af samtidige handlinger.

AzCopy som standard starter et bestemt antal samtidige handlinger at øge filoverførsel dataoverførsel. Bemærk, at stort antal samtidige handlinger i et miljø med lav båndbredde kan overvælde netværksforbindelsen og forhindre handlinger fra fuldt gennemførelse. Begrænse samtidige handlinger, der er baseret på faktisk tilgængelige netværksbåndbredde.

Den øvre grænse for samtidige handlinger er 512.

**Gælder for:** BLOB, filer, tabeller

### <a name="sourcetypeblob--table"></a>/ SourceType: "Blob" | "Tabel"

Angiver, at den `source` ressource er en tilgængelig i den lokale udviklingsmiljø skal køre i lagerplads emulatoren blob.

**Gælder for:** BLOB, tabeller

### <a name="desttypeblob--table"></a>/ DestType: "Blob" | "Tabel"

Angiver, at den `destination` ressource er en tilgængelig i den lokale udviklingsmiljø skal køre i lagerplads emulatoren blob.

**Gælder for:** BLOB, tabeller

### <a name="pkrskey1key2key3"></a>/ PKRS: "nøgle1 # nøgle2 key3 #..."

Opdeles partition vigtige område for at aktivere eksporterer tabeldata parallelt, hvilket øger hastigheden for eksporten.

Hvis denne indstilling ikke er angivet, bruger AzCopy en enkelt tråd til at eksportere tabellen enheder. For eksempel, hvis brugeren angiver /PKRS: "aa #bb", og klik derefter AzCopy starter tre samtidige handlinger.

Hver operation eksporterer en af tre partition vigtige områder, som vist nedenfor:

  [første-partition-nøgle, å)

  [aa, bb)

  [bb, sidste-partition-nøgle]

**Gælder for:** Tabeller

### <a name="splitsizefile-size"></a>/ SplitSize: "filstørrelse"

Angiver den eksporterede fil opdele størrelse i MB, den minimale tilladte værdi er 32.

Hvis denne indstilling ikke er angivet, eksportere AzCopy tabeldata til én fil.

Hvis tabeldataene eksporteres til en blob, og den eksporterede filstørrelsen er 200 GB grænsen for blob størrelse, derefter opdele AzCopy den eksporterede fil, selvom denne indstilling ikke er angivet.

**Gælder for:** Tabeller

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation: "InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Angiver data til import af tabelfunktionsmåde.

- InsertOrSkip - springer et eksisterende objekt eller indsætter et nyt objekt, hvis den ikke findes i tabellen.

- InsertOrMerge - flettes en eksisterende enhed eller indsætter et nyt objekt, hvis den ikke findes i tabellen.

- InsertOrReplace - erstatter et eksisterende objekt eller indsætter et nyt objekt, hvis den ikke findes i tabellen.

**Gælder for:** Tabeller

### <a name="manifestmanifest-file"></a>/ Manifest: "manifest-fil"

Angiver manifestfilen til tabellen eksportere og importere handling.

Denne indstilling er valgfri under eksporten, AzCopy genererer en manifestfil med foruddefineret navn, hvis denne indstilling ikke er angivet.

Denne indstilling er påkrævet under importen til at finde datafilerne.

**Gælder for:** Tabeller

### <a name="synccopy"></a>/ SyncCopy

Angiver, om du vil kopiere synkront BLOB eller filer mellem to slutpunkter for Azure-lager.

AzCopy som standard bruger serversiden asynkron kopi. Angiv denne indstilling for at udføre en synkroniseret kopi, som henter BLOB eller filer til lokale hukommelse og overfører dem til Azure-lager.

Du kan bruge denne indstilling, når du kopierer filer i Blob-lager, inden for fillagring eller fra Blob-lager til lagring af filer eller omvendt.

**Gælder for:** BLOB, filer

### <a name="setcontenttypecontent-type"></a>/ SetContentType: "indholdstype"

Angiver den MIME-indholdstype til destination BLOB eller filer.

AzCopy angiver en indholdstype for et blob eller en fil til program-/ oktetstreamen som standard. Du kan angive en indholdstype for alle BLOB eller filer ved at angive en værdi for denne indstilling eksplicit.

Hvis du angiver denne indstilling uden en værdi, indstiller AzCopy hver blob eller filens indholdstype ifølge dens filtypenavn.

**Gælder for:** BLOB, filer

### <a name="payloadformatjson--csv"></a>/ PayloadFormat: "JSON" | "CSV"

Angiver formatet på den eksporterede datafil i tabellen.

Hvis denne indstilling ikke er angivet, eksporterer AzCopy som standard fil tabeldata i JSON-format.

**Gælder for:** Tabeller

## <a name="known-issues-and-best-practices"></a>Kendte problemer og bedste praksis

### <a name="limit-concurrent-writes-while-copying-data"></a>Begrænse samtidige skriver under kopiering af data

Når du kopierer BLOB eller filer med AzCopy, Husk på, at et andet program kan ændre dataene mens du kopierer det. Hvis det er muligt sikre, at de data, du kopierer ikke redigeres under kopieringen. For eksempel, når du kopierer en virtuel harddisk, der er knyttet til en Azure virtuelt, kontrollere, at ingen andre programmer i øjeblikket skriver til den virtuelle harddisk. Der er en god måde at gøre dette ved overdragelse ressourcen skal kopieres. Du kan også oprette et øjebliksbillede af den virtuelle harddisk først og derefter kopiere snapshot.

Hvis du ikke kan forhindre andre programmer i at skrive til BLOB eller filer, mens de kopieres, derefter Husk på, efter det tidspunkt, jobbet afsluttes, de kopierede ressourcer måske ikke længere har fuld fungerer ensartet med ressourcerne, der kilde.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Køre én forekomst af AzCopy på én maskine.
AzCopy er udviklet til at maksimere anvendelsen af din maskine at fremskynde dataoverførslen, vi anbefaler, at du kun køre én AzCopy forekomst på en computer, og angiv indstillingen `/NC` Hvis du har brug for flere samtidige handlinger. Få mere at vide Skriv `AzCopy /?:NC` på kommandolinjen.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Aktivere FIPS-kompatible MD5 algoritmer AzCopy når du "bruger FIPS kompatible algoritmer til kryptering, krypteres og signering".
AzCopy som standard bruger .NET MD5 implementering til at beregne MD5, når du kopierer objekter, men der er nogle sikkerhedskrav, der skal AzCopy aktivering af FIPS kompatibel MD5 indstilling.

Du kan oprette en app.config fil `AzCopy.exe.config` med egenskaben `AzureStorageUseV1MD5` og lægge det til side med AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

For egenskaben "AzureStorageUseV1MD5" • True - anvender standardværdien, AzCopy .NET MD5 implementering.
• FALSK – AzCopy bruger FIPS kompatibel MD5 algoritme.

Bemærk, at FIPS-kompatible algoritmer er deaktiveret som standard på din Windows-computer, du kan skrive secpol.msc i vinduet Kør og kontrollere denne parameter på sikkerhedsindstilling -> lokal politik -> Sikkerhed Indstillinger -> System kryptering: Brug FIPS-kompatible algoritmer til kryptering, krypteres og signering.

## <a name="next-steps"></a>Næste trin

Se følgende ressourcer for flere oplysninger om Azure-lager og AzCopy.

### <a name="azure-storage-documentation"></a>Azure lagerplads dokumentation:

- [Introduktion til Azure-lager](storage-introduction.md)
- [Sådan bruger du Blob-lager fra .NET](storage-dotnet-how-to-use-blobs.md)
- [Sådan bruger du fillagring fra .NET](storage-dotnet-how-to-use-files.md)
- [Sådan bruger du Table storage fra .NET](storage-dotnet-how-to-use-tables.md)
- [Hvordan du kan oprette, administrere eller slette en lagerplads konto](storage-create-storage-account.md)

### <a name="azure-storage-blog-posts"></a>Azure lagerplads blogindlæg:
- [Introduktion til Azure lagerplads bevægelse bibliotek datavisning](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy: Introduktion til synkron kopi og tilpasset indholdstype](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy: Præsentation af generelle tilgængelighed af AzCopy 3.0 plus preview-versionen af AzCopy 4.0 med understøttelse af tabel og filer](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy: Optimeret til store kopi scenarier](http://go.microsoft.com/fwlink/?LinkId=507682)
- [AzCopy: Understøttelse af læseadgang geografisk overflødige lagerplads](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: Overføre data med igen kan startes fra disken tilstand og SAS token](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: Brug af tværs konto kopi Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Overføre/downloade filer til Azure BLOB](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
