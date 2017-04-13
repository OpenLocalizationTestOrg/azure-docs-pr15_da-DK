<properties 
    pageTitle="Produktbemærkninger til Datastyringsgateway | Azure Data Factory" 
    description="Produktbemærkninger til data Management Gateway tory" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="spelluru"/>

# <a name="release-notes-for-data-management-gateway"></a>Produktbemærkninger til Datastyringsgateway
En af udfordringerne for moderne dataintegration er problemfrit flytte data til og fra det lokale til skyen. Data Factory gør denne integration problemfri med Datastyringsgateway, som er en agent, kan du installere lokalt for at aktivere hybrid flytning af data.

Se følgende artikler for at finde detaljerede oplysninger om Datastyringsgateway og hvornår det skal bruges: 

- [Datastyringsgateway](data-factory-data-management-gateway.md)
- [Flytte data mellem lokale og ved hjælp af Azure Data Factory i skyen](data-factory-move-data-between-onprem-and-cloud.md) 

## <a name="current-version-2260721"></a>Aktuelle version (2.2.6072.1)

- Understøtter indstilling HTTP-proxy for gateway ved hjælp af Konfigurationsstyring til Datastyringsgateway. Hvis konfigureret, åbnes Azure Blob, Azure-tabel, Azure Data sø og dokument DB, via HTTP-proxy.
- Understøtter sidehoved håndtering af til tekstformat, når du kopierer data fra/til Azure Blob, Azure-sø datalager, lokale filsystem, og lokale HDFS.
- Understøtter kopiere data fra Føj Blob og siden Blob sammen med de allerede understøttede Bloker Blob.
- Introducerer en ny gatewaystatus **Online (begrænset)**, som angiver, at de vigtigste funktioner for gateway fungerer undtagen interaktive handlinger understøttelse af kopi guiden.
- Forbedrer stabilitet for gateway registrering ved hjælp af registreringsnøgle.

## <a name="earlier-versions"></a>Tidligere versioner

## <a name="2160401"></a>2.1.6040.1

- DB2 driver er inkluderet i gateway-installationspakke nu. Du behøver ikke at installere den separat. 
- DB2 driver understøtter nu z/OS og DB2 for jeg (AS / 400) sammen med de platforme, der allerede understøttes (Linux, Unix og Windows). 
- Understøtter brug af DocumentDB som kilde eller destination for lokale data butikker
- Understøtter kopiering af data fra/til kolde/hot blob storage sammen med kontoen, der allerede understøttede generelle lagerplads. 
- Gør det muligt at oprette forbindelse til lokal SQL Server via gateway med remote login rettigheder.  

## <a name="2060131"></a>2.0.6013.1

- Du kan vælge sprog/kultur der skal bruges i en gateway under manuel installation.
- Når gateway ikke fungerer som forventet, kan du vælge at sende gateway logge af seneste syv dage til Microsoft for at lette fejlfinding af problemet. Hvis gateway ikke er forbundet med skytjenesten, kan du vælge at gemme og arkivere gateway logfiler.  
- Forbedringer af brugergrænsefladen for Konfigurationsstyring til datastyringsgateway:
    - Gøre gatewaystatus mere synlige under fanen Startside.
    - Reorganiserede og forenklet kontrolelementer.
- Du kan kopiere data fra et lager, med den [gratis kopi preview værktøjet kode](data-factory-copy-data-wizard-tutorial.md). Få vist [Midlertidigt kopi](data-factory-copy-activity-performance.md#staged-copy) for at få oplysninger om denne funktion Generelt. 
- Du kan bruge Datastyringsgateway til vandindtrængen data direkte fra en lokal SQL Server-database til Azure Machine Learning.
- Forbedring af ydeevnen
    - Forbedre ydeevnen på få vist skema/Preview mod SQL Server i kode-fri kopi preview værktøj.



## <a name="11259531"></a>1.12.5953.1
- Fejlrettelser

## <a name="11159181"></a>1.11.5918.1

- Maksimumstørrelsen for hændelseslog gatewayen er blevet forbedret fra 1 MB til 40 MB.
- Dialogboks med en advarsel vises i tilfælde af, at computeren skal genstartes under automatisk opdatering af gatewayen. Du kan vælge at genstarte højre derefter eller nyere. 
- I tilfælde af automatisk opdatering mislykkes, forsøg gateway installer automatisk opdatering tre gange med maksimum.
- Forbedring af ydeevnen
    - Forbedre ydeevnen for indlæsning af store tabeller fra lokal server i scenarie med kode-fri kopi.
- Fejlrettelser

## <a name="11058921"></a>1.10.5892.1

- Forbedring af ydeevnen
- Fejlrettelser

## <a name="1958652"></a>1.9.5865.2

- Funktion nul touch automatisk opdatering
- Ny proceslinjeikonet med gateway statusindikatorer
- Muligheden for at "Opdater nu" fra klienten på computeren
- Mulighed for at angive Opdater tidsplan tid
- PowerShell-script til aktivering/deaktivering af automatisk opdatering til/fra
- Understøttelse af JSON-formatet  
- Forbedring af ydeevnen
- Fejlrettelser

## <a name="1858221"></a>1.8.5822.1

- Forbedre fejlfinding oplevelse
- Forbedring af ydeevnen
- Fejlrettelser

### <a name="1757951"></a>1.7.5795.1

- Forbedring af ydeevnen
- Fejlrettelser

### <a name="1757641"></a>1.7.5764.1

- Forbedring af ydeevnen
- Fejlrettelser

### <a name="1657351"></a>1.6.5735.1

- Understøtter lokale HDFS kilde/Sink
- Forbedring af ydeevnen
- Fejlrettelser

### <a name="1656961"></a>1.6.5696.1

- Forbedring af ydeevnen
- Fejlrettelser

### <a name="1656761"></a>1.6.5676.1

- Understøtte værktøjer til diagnosticering på Konfigurationsstyring
- Understøtte tabelkolonner til datakilder i tabelformat til Azure Data Factory
- Understøttelse af DW SQL Azure Data Factory
- Understøttelse af Reclusive i BlobSource og FileSource Azure Data Factory
- Understøtte CopyBehavior – MergeFiles, PreserveHierarchy og FlattenHierarchy i BlobSink og FileSink til binær kopi til Azure Data Factory
- Understøtte Kopiér aktivitet rapportere fremdrift for Azure Data Factory
- Understøttelse af kilde Connectivity datavalidering til Azure Data Factory
- Fejlrettelser


### <a name="1656721"></a>1.6.5672.1

- Understøttelse Azure Data Factory tabelnavn for ODBC-datakilde
- Forbedring af ydeevnen
- Fejlrettelser

### <a name="1656581"></a>1.6.5658.1

- Understøttelse af fil synkronisere til Azure Data Factory
- Understøttelse af bevarelse hierarki i binær kopi til Azure Data Factory
- Kopiér aktivitet redundanskontrol understøttelse Azure Data Factory
- Fejlrettelser

### <a name="1656401"></a>1.6.5640.1

- Understøtte 3 flere datakilder til Azure Data Factory (ODBC, OData, HDFS)
- Understøttelse af dette anførselstegn i CSV-parseren Azure Data Factory
- Komprimering support (BZip2)
- Fejlrettelser

### <a name="1556121"></a>1.5.5612.1

- Understøtte fem relationsdatabaser til Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata og Sybase)
- Komprimering support (Gzip og Deflate)
- Forbedring af ydeevnen
- Fejlrettelser


### <a name="1455491"></a>1.4.5549.1

- Tilføje Oracle datakilde dataunderstøttelse til Azure Data Factory
- Forbedring af ydeevnen
- Fejlrettelser

### <a name="1454921"></a>1.4.5492.1

- Samlet binært tal, der understøtter både Microsoft Azure Data Factory-og Office 365 Power BI
- Afgræns processen Brugergrænsefladen til konfiguration og registrering
- Azure Data Factory-Azure indgangs- og udgangspunkt support til SQL Server-datakilde

### <a name="1253031"></a>1.2.5303.1

-   Løse timeout problem for at understøtte flere tidskrævende forbindelser til datakilder. 
    
### <a name="1155268"></a>1.1.5526.8

- Kræver .NET Framework 4.5.1 et under installationen.

### <a name="1051442"></a>1.0.5144.2

- Ingen ændringer, der påvirker Azure Data Factory scenarier. 
