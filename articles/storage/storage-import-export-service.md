<properties
    pageTitle="Ved hjælp af Importér/Eksportér til at overføre data til Blob-lager | Microsoft Azure"
    description="Lær at oprette importere og eksportere sager i portalen Azure klassisk til at overføre data for at blob storage."
    authors="renashahmsft"
    manager="aungoo"
    editor="tysonn"
    services="storage"
    documentationCenter=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="renash"/>


# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-blob-storage"></a>Bruge tjenesten Microsoft Azure Importér/Eksportér til at overføre data til Blob-lager

## <a name="overview"></a>Oversigt

Azure Importér/Eksportér Service kan du overføre sikkert store mængder data til Azure blob-lager ved forsendelses harddiskdrev til en Azure datacenter. Du kan også bruge denne tjeneste til at overføre data fra Azure blob-lager til harddiskdrev og afsende til dit lokale websted. Denne tjeneste er egnet i situationer, hvor du vil overføre flere TBs af data til eller fra Azure, men overfører eller henter via netværket er ikke muligt på grund af begrænset båndbredde eller høj netværksomkostninger.

Tjenesten kræver, at harddiskdrev skal være bit Skab krypteret for sikkerheden for dine data. Tjenesten understøtter klassisk lagerplads konti findes i alle områder af offentlige Azure. Du skal levere harddiskdrev til en af de understøttede placeringer, der er angivet senere i denne artikel.

I denne artikel kan du lære mere om tjenesten Azure Importér/Eksportér, og hvordan du leverer drev for kopiering af dine data til og fra Azure Blob-lager.

> [AZURE.IMPORTANT] Du kan oprette og administrere importere og eksportere job for klassisk lagerplads ved hjælp af portalen klassisk eller [Importér/Eksportér service REST API'er](http://go.microsoft.com/fwlink/?LinkID=329099). Ressourcestyring lagerplads konti understøttes ikke på nuværende tidspunkt.

## <a name="when-should-i-use-the-azure-importexport-service"></a>Hvornår skal jeg bruge tjenesten Azure Importér/Eksportér?

Du kan overveje at bruge Azure Importér/Eksportér service, når du overfører eller henter data via netværket er for langsomt eller få ekstra netværksbåndbredde vejer omkostningerne forbud.

Du kan bruge denne tjeneste i scenarier f.eks.:

- Overføre data til skyen: Flyt store mængder data til Azure hurtigt og effektivt omkostninger.
- Distribution af indhold: hurtigt sende data til din kunde-websteder.
- Sikkerhedskopiering: Tage sikkerhedskopier af dine lokale data til at gemme i Azure blob-lager.
- Gendannelse af data: gendanne stor mængde data, der er gemt i blob-lager og har den leveret til din lokale placering.

## <a name="pre-requisites"></a>Forudsætninger

I dette afsnit, har vi angivet forudsætninger, der er påkrævet for at bruge denne tjeneste. Gennemgå dem omhyggeligt før forsendelses dine drev.

### <a name="storage-account"></a>Lagerplads konto

Du skal have et eksisterende Azure-abonnement og en eller flere **Klassisk** lagerplads konti, for at bruge tjenesten Importér/Eksportér. Hver sag kan bruges til at overføre data til eller fra kun én klassisk lagerplads konto. Et enkelt Importér/Eksportér job kan ikke med andre ord dække på tværs af flere lagerplads konti. Oplysninger om oprettelse af en ny konto lagerplads, se, [hvordan du opretter en lagerplads konto](storage-create-storage-account.md#create-a-storage-account).

### <a name="blob-types"></a>Typer af BLOB

Du kan bruge Azure Importér/Eksportér tjenesten for at kopiere data til **Bloker** BLOB eller **siden** BLOB. Omvendt, kan du kun eksportere **Bloker** blob, **siden** BLOB eller **Tilføj** BLOB fra Azure-lager, med denne tjeneste.

### <a name="job"></a>Job

Hvis du vil begynde processen med import og eksport af fra Blob-lager, skal oprette du først en sag. Et job kan være en import eller en eksportjob:

- Oprette et job til import, når du vil overføre data, du har lokalt til BLOB i kontoen Azure-lager.
- Oprette et eksportjob, når du vil overføre data, der i øjeblikket er gemt som BLOB i kontoen lagerplads til harddiske, der er sendt til dig.

Når du opretter en sag, du give besked om tjenesten Importér/Eksportér, du leverer en eller flere harddiske til et Azure datacenter.

- For en import-sag, vil du forsendelses harddiske, der indeholder dine data.
- For en Eksportér sag forsendelses du tomme harddiske.
- Du kan levere op til 10 harddiskdrev per job.

Du kan oprette en import eller eksport job fra [Klassisk portal](https://manage.windowsazure.com/) eller [Azure lagerplads Importér/Eksportér REST-API](http://go.microsoft.com/fwlink/?LinkID=329099).

### <a name="client-tool"></a>Client-værktøj

Det første trin i at oprette et **importere** job er at forberede dit drev, som sendes til import. For at forberede dit drev, skal du forbinde den til en lokal server og køre værktøjet Azure Importér/Eksportér klienten på den lokale server. Dette klientværktøj gør det nemmere at kopiere dataene til drevet, kryptering af data på drevet med BitLocker og generere drev journal-filer.

Journalfiler gemmer grundlæggende oplysninger om dit arbejde og drev som drev serienummer og kontonavn-lager. Denne journal-fil er ikke gemt på drevet. Det bruges under importen job oprettelsen. Oplysninger om oprettelse af jobbet leveres Step by step senere i denne artikel.

Værktøjet klient er kun kompatible med 64-bit Windows-operativsystemet. Se afsnittet [operativsystem](#operating-system) for bestemte versioner af Operativsystemet understøttes.

Hent den nyeste version af [Azure Importér/Eksportér client værktøj](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Du kan finde flere oplysninger om brug af værktøjet Azure Importér/Eksportér [Azure Importér/Eksportér værktøjet Reference](http://go.microsoft.com/fwlink/?LinkId=329032).

### <a name="hard-disk-drives"></a>Harddiskdrev

Kun 3,5 SATA II/III interne harddiske understøttes til brug sammen med tjenesten Importér/Eksportér. Du kan bruge harddiske op til 10TB.
Importjob behandles kun de første data lydstyrken på drevet. Lydstyrken data skal være formateret med NTFS.
Når du kopierer data på din harddisk, du kan vedhæfte den direkte ved hjælp af en SATA forbindelse, eller du kan vedhæfte den eksternt ved hjælp af en ekstern SATA II/III USB-adapter. Det anbefales at bruge en af følgende eksterne SATA II/III USB-kort:

- Anker 68UPSATAA - 02BU
- Anker 68UPSHHDS-BU
- Startech SATADOCK22UE
- Sharkoon QuickPort XT CH

Hvis du har et konverteringsprogram, som ikke er anført ovenfor, kan du prøve at køre værktøjet Azure Importér/Eksportér ved hjælp af din konverteringsprogram til at forberede drevet, og se, om den fungerer før du køber et understøttede konverteringsprogram.

> [AZURE.IMPORTANT] Eksterne harddiske, der følger med en indbygget USB-adapter, der ikke understøttes af denne tjeneste. Desuden kan ikke bruges disken i hus af en ekstern harddisk; send ikke eksterne harddiske.

### <a name="encryption"></a>Kryptering

Dataene på drevet, skal krypteres med BitLocker drevkryptering. Dette beskytter dine data, mens det er undervejs.

Importér sager er der to måder til at udføre kryptering. Den første metode er at bruge den / kryptere parameter, når der køres værktøjet klienten under drev forberedelse. Den anden måde er at aktivere BitLocker-kryptering manuelt på drevet, og Angiv krypteringsnøglen i klienten værktøjet kommandolinjen under drev forberedelse.

Når dataene er blevet kopieret til drevene for Eksportér sager kryptere tjenesten drevet med BitLocker inden levering tilbage til dig. Krypteringsnøglen får du via portalen klassisk.  

### <a name="operating-system"></a>Operativsystem

Du kan bruge en af følgende 64-bit operativsystemer til at forberede harddisken ved hjælp af værktøjet Azure Importér/Eksportér før forsendelses drev til Azure:

Windows 7 virksomheds-, Windows 7 Ultimate Windows 8 Pro, virksomheds-Windows 8, Windows 8.1 Pro, virksomheds-Windows 8.1, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Alle disse operativsystemer understøtter sikkerhedshardware.

> [AZURE.IMPORTANT] <sup>1</sup> Hvis du bruger en computer med Windows 10 til at forberede din harddisk, kan du hente den nyeste version af værktøjet Azure Importér/Eksportér.

### <a name="locations"></a>Placeringer

Tjenesten Azure Importér/Eksportér understøtter kopiering af data til og fra alle offentlige Azure lagerplads konti. Du kan levere harddiskdrev til en af følgende steder. Hvis kontoen lagerplads er i en offentlig Azure placering, der ikke angives her, vil blive leveret en alternativ levering placering, når du opretter ved hjælp af portalen klassisk eller Importér/Eksportér REST-API jobbet.

Understøttede forsendelses placeringer:

- Indtastning af østasiatiske USA

- Vest USA

- Indtastning af østasiatiske USA 2

- Centrale USA

- Nord centrale USA

- Syd centrale USA

- Nord Europe

- Vest Europe

- Sydøstasien

- Sydøstasien

- Australien øst

- Australien Sydøst

- Japan vest

- Japan øst

- Central Indien


### <a name="shipping"></a>Levering

**Levering indfører datacenteret:**

Når du opretter en import eller eksport job, får du en leveringsadresse på et af de understøttede placeringer til at levere dine drev. Leveringsadressen, afhænger af placeringen af kontoen lagerplads, men det muligvis ikke den samme som din konto lagringssted.

Du kan bruge transportører som FedEx, DHL, sig eller OS post tjenesten til at levere dine drev til leveringsadresse.

**Levering drev fra datacenteret:**

Når du opretter en import eller eksport job, skal du angive en afsenderadresse for Microsoft skal benyttes ved levering drevene, tilbage, når din opgave er fuldført. Kontrollér, at du angiver en gyldig afsenderadresse for at undgå forsinkelser i behandling.

Skal du også angive en gyldig FedEx eller DHL carrier konto nummer skal bruges af Microsoft til forsendelses drev tilbage. Et FedEx konto tal er påkrævet for levering drev tilbage fra USA og Europa steder. Et DHL konto tal er påkrævet for levering drev tilbage fra Asien og Australien placeringer. Du kan oprette en [FedEx](http://www.fedex.com/us/oadr/) (for USA og Europa) eller [DHL](http://www.dhl.com/) (Asien og Australien) carrier konto, hvis du ikke har en. Hvis du allerede har en konto telefonnummer, skal du kontrollere, at det er gyldig.

I levering dine pakker, skal du følge betingelserne på [Microsoft Azure servicebetingelser](https://azure.microsoft.com/support/legal/services-terms/).

> [AZURE.IMPORTANT] Bemærk, at de fysiske medier, du leverer kan være nødvendigt at cross internationale kanter. Du er ansvarlig for at sikre, at din fysiske medier og dine data er importeret og/eller udføres i henhold til gældende lovgivning. Spørg din rådgivere at bekræfte, at dine medier og data kan lovligt leveres til identificerede datacentret før levering fysiske medier. Dette hjælper med at sikre, at det har Microsoft i tide. For eksempel en pakke, som bliver cross internationale kanter skal have en handelsfaktura til følger pakken (undtagen hvis krydser kanter i EU). Du kan udskrive en udfyldt kopi af den handelsfaktura fra carrier websted. Eksempel på kommerciel fakturere er [DHL handelsfaktura] (http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) eller [FedEx handelsfaktura](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Sørg for, at Microsoft ikke er angivet som eksportoeren.

## <a name="how-does-the-azure-importexport-service-work"></a>Hvordan fungerer tjenesten Azure Importér/Eksportér?

Du kan overføre data mellem dit lokale websted og Azure blob-lager ved hjælp af tjenesten Azure Importér/Eksportér ved at oprette sager og forsendelses harddiskdrev til et Azure datacenter. Hver harddisk, du leverer er knyttet til et enkelt job. Hver sag er knyttet til en enkelt lagerplads konto. Gennemse den [forudsætninger sektion](#pre-requisites) omhyggeligt for at få mere at vide om de specifikke oplysninger i denne tjeneste, såsom understøttes blob typer, disktyper, placeringer og levering.

I dette afsnit beskriver vi på et højt niveau trinnene til at importere og eksportere job. Senere i [Hurtig Start afsnit](#quick-start), kan vi viser trinvise instruktioner til at oprette en import og eksport job.

### <a name="inside-an-import-job"></a>I et job til Import

På et højt niveau omfatter et job til import følgende trin:

- Se dataene, der skal importeres, og antallet af drev, du skal bruge.
- Identificere destination BLOB til dine data i Blob-lager.
- Brug værktøjet Azure Importér/Eksportér til at kopiere dataene til en eller flere harddiskdrev og kryptere dem med BitLocker.  
- Oprette et job til import i kontoen mål klassisk lagerplads ved hjælp af portalen klassisk eller Importér/Eksportér REST-API. Hvis ved hjælp af portalen klassisk, overføre drev journal-filer.
- Angive afsenderadresse og telefonnummer konto skal bruges til forsendelses drevene tilbage til dig.
- Levere harddiskdrev til den leveringsadresse, der er angivet under oprettelse af et job.
- Opdater leveringen sporingsnummer Importér job detaljerne og sende importjobbet.
- Drev er modtaget og behandles på Azure datacenter.
- Drev leveres ved hjælp af kontoen carrier af afsenderadresse, der er angivet på importjobbet.

    ![Figur 1:Import job flow](./media/storage-import-export-service/importjob.png)


### <a name="inside-an-export-job"></a>I et eksportjob

På et højt niveau omfatter et eksportjob følgende trin:

- Bestemme dataene, der eksporteres og antallet af drev, du skal bruge.
- Identificere kilde BLOB eller objektbeholder stier af dine data i Blob-lager.
- Oprette et eksportjob i kontoen kilde lagerplads ved hjælp af portalen klassisk eller Importér/Eksportér REST-API.
- Angiv det kilde BLOB eller objektbeholder stier af dine data i eksportjobbet.
- Angive afsenderadresse og carrier konto tallet for skal bruges til forsendelses drevene tilbage til dig.
- Levere harddiskdrev til den leveringsadresse, der er angivet under oprettelse af et job.
- Opdater leveringen sporingsnummer Eksportér job detaljerne og sende eksportjobbet.
- Drevene er modtaget og behandles på Azure datacenter.
- Drevene er krypteret med BitLocker. tasterne er tilgængelige via portalen klassisk.  
- Drevene leveres ved hjælp af kontoen carrier afsenderadresse, der er angivet på importjobbet.

    ![Figur 2:Export job flow](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-status"></a>Få vist din jobstatus

Du kan spore status for din import eller eksport job fra portalen klassisk. Gå til kontoen lagerplads på portalen klassisk, og klik på fanen **Importér/Eksportér** . En liste over dine sager vises på siden. Du kan filtrere listen i jobstatus, jobbet navn, jobbet type eller sporingsnummer.

Du får vist en af følgende job statusser afhængigt af hvor drevet er i processen.

| Jobstatus   | Beskrivelse                                                                                                                                                                                                                                                                                                                                                                        |
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Oprette     | Tingene er blevet oprettet, men du endnu har angivet ikke leveringsoplysninger.                                                                                                                                                                                                                                                                                                |
| Levering     | Tingene er blevet oprettet, og du har angivet leveringsoplysninger. **Bemærk**: Når drevet leveres til Azure datacenter, status stadig få vist "Levering" i et stykke tid. Når tjenesten starter kopiering af dine data, ændrer status til "Transferring". Hvis du vil se mere specifikke status for et drev, kan du bruge Importér/Eksportér REST-API. |
| Overførsel af | Dine data, der overføres fra harddisken (for et job til import) eller på harddisken (for en Eksportér opgaver).                                                                                                                                                                                                                                                                 |
| Emballagen    | Overførsel af dine data er fuldført, og harddisken klargøres til levering tilbage til dig.                                                                                                                                                                                                                                                                             |
| Fuldføre     | Harddisken er blevet leveret tilbage til dig.                                                                                                                                                                                                                                                                                                                                      |

### <a name="time-to-process-job"></a>Tid til processen job

Hvor lang tid det tager at behandle en Importér/Eksportér job varierer afhængigt af forskellige faktorer som levering tid job type, typen og størrelsen på de data, der kopieres og størrelsen på diskene angivet. Tjenesten Importér/Eksportér har ikke en SERVICENIVEAUAFTALE. Du kan bruge REST-API til at spore tidsplanens status job nærmere. Der findes en procent fuldført parameter i handlingen listen job som giver en angivelse af Kopiér status. Tag kontakt med os, hvis du har brug for en estimering at fuldføre en tid kritiske Importér/Eksportér sag.

### <a name="pricing"></a>Priser

**Drev håndtering af gebyr**

Der er et drev håndtering af gebyr for hver drev, behandles som en del af din import eller eksport job. Du kan se oplysningerne på [Azure Importér/Eksportér priser](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Forsendelses omkostninger**

Når du leverer drev til Azure, betaler du levering omkostninger levering carrier. Når Microsoft returnerer drevene for dig, hæves levering omkostninger til kontoen carrier, som du angav på tidspunktet for oprettelse af jobbet.

**Transaktionsomkostninger**

Der er ingen omkostninger, når du importerer data i blob-lager. Standard udgangspunkt gebyrer gælder, når dataene er eksporteret fra blob-lager. Finde flere oplysninger om omkostninger, [dataoverførsel priser.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="quick-start"></a>Hurtig Start

I dette afsnit, kan vi viser trinvise instruktioner til oprettelse af en import og et eksportjob. Kontrollér, at du opfylder alle [forudsætninger](#pre-requisites) før fremover.

## <a name="how-to-create-an-import-job"></a>Sådan oprettes et Job til Import?

Oprette en importjob for at kopiere data til kontoen Azure lagerplads fra harddiske ved afsendelse af et eller flere drev, der indeholder data til den angivne datacenter. Importjobbet giver oplysninger om harddiske, data, der skal kopieres, målrette lagerplads konto og leveringsoplysninger til tjenesten Azure Importér/Eksportér. Oprette et job til import er en proces i tre trin. Først skal forberede dine drev ved hjælp af værktøjet Azure Importér/Eksportér klient. Andet, sende et job til import af ved hjælp af portalen klassisk. Tredje leveres drev til den leveringsadresse, der er angivet under oprettelse af et job og opdatere levering oplysninger i dine oplysninger om job.   

> [AZURE.IMPORTANT] Du kan sende kun ét job per lagerplads konto. Hvert drev, du leverer kan importeres til én lagerplads konto. For eksempel Antag, at du vil importere data til to lagerplads konti. Du skal bruge separat harddiskdrev for hver lagerplads konto og oprette separate job per lagerplads konto.

### <a name="prepare-your-drives"></a>Forberede dine drev

Det første trin, når du importerer data ved hjælp af tjenesten Azure Importér/Eksportér er at forberede dine drev ved hjælp af værktøjet Azure Importér/Eksportér klient. Følg nedenstående trin for at forberede dine drev.

1.  Identificere dataene, der skal importeres. Det kan skyldes mapper og enkeltstående filer på den lokale server eller et netværksshare.  

2.  Finde antallet drev, skal du afhængigt af samlede størrelse af dataene. Levere det påkrævede antal 3,5 SATA II/III harddiskdrev.

3.  Identificere target lagerplads firmaet, objektbeholder, virtuelle mapper og BLOB.

4.  Finde ud af, kataloger og/eller enkeltstående filer, der skal kopieres til hver harddiskdrevs rodmappe.

5.  Brug [Azure Importér/Eksportér værktøj](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) til at kopiere dataene til en eller flere harddiske.

    - Værktøjet Azure Importér/Eksportér opretter kopi sessioner for at kopiere dataene fra kilde til harddiskdrevene. Værktøjet kan kopiere en enkelt mappe sammen med dens undermapper eller en enkelt fil i en enkelt kopi session.

    - Du skal muligvis flere kopi sessioner Hvis kildedataene strækker sig over mange mapper.

    - Hver harddisk, du har forberedt kræver mindst én kopi session.

6.  Du kan angive den / kryptere parameter for at aktivere BitLocker-kryptering på harddisken. Du kan også aktivere BitLocker-kryptering manuelt på harddisken og leverer tasten, mens du kører værktøjet.

7.  Værktøjet Azure Importér/Eksportér genererer en drev journal-fil for hvert drev, som den er forberedt. Drev journal-fil er gemt på din lokale computer, ikke på selve drevet. Journalfilen bliver overført, når du opretter importjobbet. En drev journal-fil indeholder drev-ID og tasten BitLocker samt andre oplysninger om drevet.
**Vigtigt**: hver harddisk, du har forberedt medfører en journal-fil. Når du opretter importjobbet ved hjælp af portalen klassisk, skal du overføre alle journal-filer på de drev, som er en del af importjobbet. Drev uden journal filer ikke bliver behandlet.

8.  Rediger ikke dataene på harddiskdrevene eller journalfilen når du er færdig disk forberedelse.

Nedenfor vises de kommandoer og eksempler til forberedelse af den harddisk, ved hjælp af Azure Importér/Eksportér client værktøj.

Azure Importér/Eksportér klient værktøjet PrepImport kommandoen for den første kopi session for at kopiere en mappe:

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Eksempel:**

I eksemplet nedenfor vi kopierer alle filerne og mapper fra H:\Video til den harddisk, der er tilsluttet på X: Underside. Dataene, der skal importeres til destination lagerplads konto, der er angivet efter kontonøgle lager og i objektbeholderen til lagring af kaldet video. Hvis objektbeholderen til lagring af ikke findes, oprettes den. Denne kommando kan også formatere og kryptere den destinationsadresse harddisk.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:storageaccountkey /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Azure Importér/Eksportér klient tool PrepImport-kommando for efterfølgende kopi sessioner for at kopiere en mappe:

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

For efterfølgende kopi sessioner til den samme harddisk, angive det samme journal filnavn og angive en ny session-ID; der er ingen grund til at levere lagerplads konto tasten- og destinationswebsteder drev igen, eller til at formatere eller kryptere drevet. I dette eksempel vi kopiere mappen H:\Photo og dets underordnede mapper til samme destination drev, til objektbeholderen til lagring af kaldet foto.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Azure Importér/Eksportér client værktøj PrepImport kommandoen for den første kopi session for at kopiere en fil:

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Azure Importér/Eksportér client værktøj PrepImport kommandoen for efterfølgende kopi sessioner for at kopiere en fil:

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Husk**: som standard, der skal importeres data som Bloker BLOB. Du kan bruge parameteren /BlobType til at importere dataene som en side BLOB. Eksempelvis hvis du importerer Virtuelle filer, der bliver tilsluttet som diske på en Azure VM, skal du importere dem som siden BLOB. Hvis du er usikker på, hvilken blob tast for at bruge, kan du angive /blobType:auto og vi hjælper med at afgøre den rigtige type. I dette tilfælde skal alle virtuelle og vhdx filer, der skal importeres som siden blob, og resten bliver importeret som Bloker BLOB.

Se flere oplysninger om brug af værktøjet Azure Importér/Eksportér klient i [Forberede harddiske til Import](https://msdn.microsoft.com/library/dn529089.aspx).

Se også [Eksempel på arbejdsgang til at forberede harddiske til et Job til Import](https://msdn.microsoft.com/library/dn529097.aspx) for at få mere detaljerede trinvise instruktioner.  

### <a name="create-the-import-job"></a>Oprette importjobbet

1.  Når du har forberedt et drev, gå til kontoen lagerplads på [Klassisk portal](https://manage.windowsazure.com) og få vist Dashboard. Klik på **Opret et Job til Import**under **Hurtig Glance**. Gennemse trinnene, og Markér afkrydsningsfeltet for at angive, at du har forberedt harddisken og at du har den drev journal-fil, der er tilgængelige.

2.  Angiv kontaktoplysningerne for den person, der er ansvarlig for at importere jobbet og en gyldig afsenderadresse i trin 1. Hvis du vil gemme detaljeret logdata til importjobbet, skal du se mulighed for at **gemme den detaljerede logfil i min 'waimportexport' blob beholder**.

3.  I trin 2 skal du overføre drev journal-filer, som du hentede under trinnet drev forberedelse. Du skal overføre en fil for hver drev, du har forberedt.

    ![Oprette job til import - trin 3](./media/storage-import-export-service/import-job-03.png)

4.  Angiv et beskrivende navn til importjobbet i trin 3. Bemærk, at det navn, du angiver kan indeholde kun små bogstaver, tal, bindestreger og understregningstegn, skal starte med et bogstav og må ikke indeholde mellemrum. Du skal bruge det navn, du vælger at spore dine sager, mens de er i gang, og når de er gennemført.

    Vælg derefter dit data center område på listen. Center dataområde angiver det datacenter og den adresse, skal du leverer din pakke. Se ofte stillede spørgsmål nedenfor for at få flere oplysninger.

5.  Vælg din returnerede carrier på listen i trin 4, og Angiv dine carrier konto tal. Microsoft bruger denne konto til at levere drev tilbage til dig, når import tingene er fuldført.

    Hvis du har din sporingsnummer, Vælg din levering carrier på listen, og Angiv dine sporingsnummer.

    Hvis du ikke et sporingsnummer endnu, Vælg **jeg angiver min leveringsoplysninger for denne import sag, når jeg har leveret min pakke**, derefter udføre importen.

6. Vælg tingene på listen for at angive din sporingsnummer, når du har leveret pakken, vende tilbage til siden **Importér/Eksportér** for kontoen lagerplads på klassisk-portalen, og vælg **Forsendelses oplysninger**. Navigere gennem guiden, og skriv din sporingsnummer i trin 2.

    Hvis sporingsnummeret ikke opdateres i 2 uger for at oprette jobbet, udløber jobbet.

    Hvis opgaven er i tilstanden Oprettelse, levering eller Transferring, kan du også opdatere dit carrier kontonummer i trin 2 i guiden. Når jobbet er i tilstanden emballagen, kan du ikke opdatere din konto telefonnummer for det pågældende job.

7. Du kan spore job status på dashboardet til portalen. Se, hvad hver enkelt jobtilstand i det forrige afsnit betyder ved at [få vist din jobstatus](#viewing-your-job-status).

## <a name="how-to-create-an-export-job"></a>Sådan oprettes et eksportere Job?

Oprette en eksportjob for at give besked tjenesten Importér/Eksportér, du skal forsendelses et eller flere tomme drev til datacenteret så data kan eksporteres fra kontoen lagerplads til drevene og drevene derefter sendt til dig.

### <a name="prepare-your-drives"></a>Forberede dine drev

Følgende foreløbig kontrol er anbefalet til forberedelse af dine drev for en Eksportér sag:

1. Se antallet af diske påkrævet ved hjælp af værktøjet Azure Importér/Eksportér PreviewExport kommandoen. Du kan finde yderligere oplysninger finder [Aflytning Drive forbrug for en eksportere sag](https://msdn.microsoft.com/library/azure/dn722414.aspx). Det hjælper dig med at få vist brugen af drevet til BLOB du har valgt, baseret på størrelsen på de drev, du vil bruge.

2. Kontrollér, at du kan læse-og skriveadgang på harddisken, som sendes til eksportjobbet.

### <a name="create-the-export-job"></a>Oprette eksportjobbet

1.  Gå til kontoen lagerplads på [Klassisk portal](https://manage.windowsazure.com)for at oprette en eksportjob, og få vist Dashboard. Klik på **Opret et eksportere Job** under **Hurtig Glance**, og Fortsæt gennem guiden.

2.  Angiv kontaktoplysningerne for den person, der er ansvarlig for at eksportere jobbet i trin 2. Hvis du vil gemme detaljeret logdata for Eksportér sagen, skal du se mulighed for at **gemme den detaljerede logfil i min 'waimportexport' blob beholder**.

3.  I trin 3 skal du angive hvilke blob-data, du vil eksportere fra kontoen lagerplads til dit tom drev eller på drev. Du kan vælge at eksportere alle blob-data i kontoen lagerplads, eller du kan angive som BLOB eller sæt BLOB eksportere.

    Brug vælgeren **Lig med** for at angive en blob eksportere, og Angiv den relative sti til blob, begynder med objektbeholdernavnet. Brug *$root* til at angive rodobjektbeholderen.

    For at angive alle BLOB starter med et præfiks, du skal bruge vælgeren **Starter med** , og angiv præfikset, begynder med en skråstreg '/'. Præfikset kan være præfikset for objektbeholder navn, fuldført objektbeholder navn eller objektbeholdernavnet på fuldført efterfulgt af præfikset for navnet på blob.

    I følgende tabel vises eksempler på gyldige blob stier:

    Datavælger|BLOB sti|Beskrivelse
    ---|---|---
    Starter med|/|Eksporterer alle BLOB i kontoen lagerplads
    Starter med|/$root /|Eksporterer alle BLOB i rodobjektbeholderen
    Starter med|/Book|Eksporterer alle BLOB i en objektbeholder, der begynder med præfiks **Adressekartotek**
    Starter med|/Music/|Eksporterer alle BLOB i beholderen **musik**
    Starter med|/ musik/Kærlighed|Eksporterer alle BLOB i beholderen **musik** , der starter med præfiks **elsker**
    Lig med|$root/logo.bmp|Eksporter blob **logo.bmp** i rodobjektbeholderen
    Lig med|videos/Story.mp4|Eksporter blob **story.mp4** i beholderen **videoer**

    Du skal angive blob stier i gyldige formater for at undgå fejl under behandling, som vist i dette skærmbillede.

    ![Oprette Eksportér sag - trin 3](./media/storage-import-export-service/export-job-03.png)


4.  Angiv et beskrivende navn til eksportjobbet i trin 4. Det navn, du angiver kan indeholde kun små bogstaver, tal, bindestreger, og understregningstegn, skal starte med et bogstav og må ikke indeholde mellemrum.

    Center dataområde vil angive et datacenter, skal du leverer din pakke. Se ofte stillede spørgsmål nedenfor for at få flere oplysninger.

5.  Vælg din returnerede carrier på listen i trin 5, og Angiv dine carrier konto tal. Microsoft bruger denne konto til at levere dine drev tilbage til dig, når eksporten tingene er fuldført.

    Hvis du har din sporingsnummer, Vælg din levering carrier på listen, og Angiv dine sporingsnummer.

    Hvis du ikke et sporingsnummer endnu, Vælg **jeg angiver min leveringsoplysninger for dette eksportjob, når jeg har leveret min pakke**, derefter udføre eksporten.

6. Vælg tingene på listen for at angive din sporingsnummer, når du har leveret pakken, vende tilbage til siden **Importér/Eksportér** for kontoen lagerplads på klassisk-portalen, og vælg **Forsendelses oplysninger**. Navigere gennem guiden, og skriv din sporingsnummer i trin 2.

    Hvis sporingsnummeret ikke opdateres i 2 uger for at oprette jobbet, udløber jobbet.

    Hvis opgaven er i tilstanden Oprettelse, levering eller Transferring, kan du også opdatere dit carrier kontonummer i trin 2 i guiden. Når jobbet er i tilstanden emballagen, kan du ikke opdatere din konto telefonnummer for det pågældende job.

    > [AZURE.NOTE] Hvis blob der skal eksporteres er i brug samtidig med at kopiere til harddisk, vil Azure Importér/Eksportér service tage et øjebliksbillede af blob og kopiere snapshot.

7.  Du kan spore job status på dashboardet i portalen klassisk. Se, hvad hver enkelt jobtilstand betyder i forrige afsnit på "få vist din jobstatus".

8.  Når du modtager drevene med de eksporterede data, kan du få vist og kopiere tasterne BitLocker genereres af tjenesten for din drev. Gå til kontoen lagerplads på portalen klassisk, og klik på fanen Importér/Eksportér. Vælg Eksportér tingene på listen, og klik på knappen Vis taster. BitLocker-nøgler vises som vist nedenfor:

    ![Få vist BitLocker taster til eksportjob](.\media\storage-import-export-service\export-job-bitlocker-keys.png)

Skal du gå til afsnittet ofte stillede spørgsmål nedenfor, så det dækker de mest almindelige spørgsmål kunder støder på, når du bruger denne tjeneste.

## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål ##


**Hvor lang tid tager det for at kopiere dataene, når min drev når datacenteret?**

Tid til at kopiere varierer afhængigt af forskellige faktorer som jobtype typen og størrelsen af data, der kopieres størrelsen på diske leveres, og eksisterende arbejdsbyrde. Det kan variere fra et par dage til et par uger, afhængigt af disse faktorer. Det er svært at give et generelt estimat. Tjenesten forsøger at optimere dit arbejde ved at kopiere flere drev parallelt, når det er muligt. Hvis du har en gang kritiske Importér/Eksportér sag, Henvend dig til os for anslået.

**Hvornår skal jeg bruge Azure Importér/Eksportér Service?**
En overveje med Azure Importer eksporter Hvis overfører eller henter via netværk tager omkring anslå mere end syv dage. Du kan beregne, hvor lang tid tager det ved hjælp af en hvilken som helst online Lommeregner eller ved at [hente](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/archive/master.zip) den, der er placeret i vores Azure Importér eksportere RESTEN API eksempel i Azure eksempler lager på [Data overføre hastighed Lommeregner](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html). Dette er ikke en bestemt beregning, men kun en grov angivelse.

**Kan jeg bruge tjenesten Azure Importér/Eksportér med en ressourcestyring lagerplads konto?**

Nej, du kan ikke kopiere data til eller fra en ressourcestyring lagerplads konto, der er direkte ved hjælp af tjenesten Azure Importér/Eksportér. Tjenesten understøtter kun klassisk lagerplads konti. Understøttelse af Ressourcestyring lagerplads konti kommer snart. Du kan importere data til en klassisk lagerplads konto og overføre den til kontoen ressourcestyring lagerplads ved hjælp af [AzCopy](storage-use-azcopy.md) eller CopyBlob som et alternativ.

**Kan jeg kopiere Azure filer ved hjælp af tjenesten Azure Importér/Eksportér?**

Nej, understøtter tjenesten Azure Importér/Eksportér kun Bloker BLOB og siden BLOB. Alle andre herunder Azure-filer, tabeller og køer lagerplads-datatyper understøttes ikke.

**Fås tjenesten Azure Importér/Eksportér til CSP abonnementer?**

Nej, tjenesten Azure Importér/Eksportér understøtter ikke CSP abonnementer. Fra support, føjes i fremtiden.

**Kan jeg springe trinnet drev forberedelse til et job til import eller kan jeg forberede et drev uden at kopiere?**

Et drev, du vil sende til at importere data skal være forberedt ved hjælp af værktøjet Azure Importér/Eksportér klient. For at kopiere data til drevet, skal du bruge værktøjet klient.

**Har jeg brug for til at udføre en hvilken som helst disk forberedelse, når du opretter et eksportjob?**

Ingen, men nogle foreløbig Kontroller anbefales. Se antallet af diske påkrævet ved hjælp af værktøjet Azure Importér/Eksportér PreviewExport kommandoen. Du kan finde yderligere oplysninger finder [Aflytning Drive forbrug for en eksportere sag](https://msdn.microsoft.com/library/azure/dn722414.aspx). Det hjælper dig med at få vist brugen af drevet til BLOB du har valgt, baseret på størrelsen på de drev, du vil bruge. Også kontrollere, at du kan læse fra og skrive på harddisken, som sendes til eksportjobbet.

**Hvad sker der, hvis jeg ved et uheld sender en harddisk, som ikke stemmer overens med de understøttede krav?**

Azure datacenteret vil returnere det drev, der ikke overholder understøttede kravene til dig. Hvis der kun nogle af drevene i pakken opfylder kravene support, behandles disse drev, og de drev, der ikke opfylder kravene returneres til dig.

**Kan jeg annullere Mit job?**

Du kan annullere et job, når status er oprettelse eller forsendelses.

**Hvor længe kan jeg se status for afsluttede sager i klassisk portal?**

Du kan få vist status for fuldførte job i op til 90 dage. Fuldførte job slettes efter 90 dage.

**Hvad skal jeg gøre, hvis jeg vil importere eller eksportere mere end 10 drev?**

En import eller eksport tingene kan referere til kun 10 drev i et enkelt job for tjenesten Importér/Eksportér. Hvis du vil afsende mere end 10 drev, kan du oprette flere job. Drev, der er knyttet til samme job skal leveres sammen i den samme pakke.

**Kan jeg bruge en USB-SATA-adapter, der ikke er på listen anbefalede?**

Hvis du har et konverteringsprogram, som ikke er anført ovenfor, kan du prøve at køre værktøjet Azure Importér/Eksportér ved hjælp af din konverteringsprogram til at forberede drevet, og se, om den fungerer før du køber et understøttede konverteringsprogram.

**Tjenesten formatere drev før der returneres dem?**

Nej. Alle drev er krypteret med BitLocker.

**Kan jeg købe drev for Importér/Eksportér job fra Microsoft?**

Nej. Du skal levere din egen drev for både import og eksport job.

**Når importjobbet er fuldført, hvad mine data ser ud i kontoen lagerplads? Bliver mine mappehierarkiet bevaret?**

Når du forbereder en harddisk til et job til import, destinationen er angivet af /dstdir: parameter. Dette er objektbeholderen destination i kontoen lagerplads, kopieres data fra harddisken. I denne destination objektbeholder virtuelle mapper er oprettet for mapper fra harddisken og BLOB er oprettet for filer.

**Hvis drevet har filer, der allerede findes i min lagerplads konto, tjenesten overskrive eksisterende BLOB i min lagerplads konto?**

Når du forbereder drevet, kan du angive om at destinationsfilen skal overskrives eller ignorerede ved hjælp af parameteren kaldet /Disposition: < omdøbe | ingen overskrive | overskrive >. Som standard tjenesten vil omdøbe de nye filer i stedet for overskrive eksisterende BLOB.

**Er værktøjet Azure Importér/Eksportér klient kompatibelt med 32-bit operativsystemer?**
Nej. Værktøjet klient er kun kompatible med 64-bit Windows-operativsystemer. Se afsnittet operativsystemer i [forudsætninger](#pre-requisites) for en komplet liste over understøttede OS versioner.

**Skal jeg have noget andet end harddisken i min pakke?**

Skal du levere kun dine harddiske. Du skal ikke medtage elementer som power levering kabler eller USB-kabler.

**Skal jeg anvende til at levere min drev ved hjælp af FedEx eller DHL?**

Du kan levere drev til datacenteret ved hjælp af en hvilken som helst kendte carrier som FedEx, DHL, sig eller OS post tjeneste. Dog for levering drev tilbage til dig fra datacenter, skal du angive et FedEx konto tal i USA og EU- eller et DHL konto tal i Asien og Australien områder.

**Er der nogen begrænsninger med forsendelses min drev internationalt?**

Bemærk, at de fysiske medier, du leverer kan være nødvendigt at cross internationale kanter. Du er ansvarlig for at sikre, at din fysiske medier og dine data er importeret og/eller udføres i henhold til gældende lovgivning. Spørg din advisors at bekræfte, at dine medier og data kan lovligt leveres til identificerede datacentret før levering fysiske medier. Dette hjælper med at sikre, at det har Microsoft i tide.

**Når du opretter en sag, er leveringsadressen en placering, der adskiller sig fra min konto lagringssted. Hvad skal jeg gøre?**

Nogle lagerplads konto placeringer er tilknyttet alternative levering placeringer. Tidligere kan tilgængelige forsendelses placeringer også midlertidigt knyttes til alternative placeringer. Altid se leveringsadresse angivet under oprettelse af job før forsendelses dine drev.

**Hvorfor vises mine jobstatus i den klassisk portalen sig "forsendelses" Når webstedet Carrier viser min pakke leveres?**

Status på portalen klassisk ændres fra levering til overførsel hvornår drevet behandling starter. Hvis dit drev er nået frem til funktionen, men er ikke startet behandling af, vises din jobstatus som levering.

**Når levering min drev, carrier beder dig om data center kontaktpersonens navn og telefonnummer. Hvad kan jeg give?**

Telefonnummeret er angivet til dig under oprettelse af et job. Hvis du har brug for navnet på en kontaktperson, skal du kontakte os på waimportexport@microsoft.com og vi giver dig disse oplysninger.

**Kan jeg bruge tjenesten Azure Importér/Eksportér til at kopiere PST-postkasser og SharePoint-data til O365?**

Se [importere PST-filer eller SharePoint-data til Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Kan jeg bruge tjenesten Azure Importér/Eksportér til at kopiere min sikkerhedskopier offline til Azure sikkerhedskopi-tjenesten?**

Se [Offline sikkerhedskopi arbejdsproces i Azure sikkerhedskopi](../backup/backup-azure-backup-import-export.md).

## <a name="see-also"></a>Se også:

- [Konfiguration af værktøjet Azure Importér/Eksportér klient](https://msdn.microsoft.com/library/dn529112.aspx)

- [Overføre data sammen med værktøjet AzCopy kommandolinjen](storage-use-azcopy.md)

- [Azure Importér Eksportér RESTEN API eksempel](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
