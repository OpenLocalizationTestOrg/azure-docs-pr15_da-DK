<properties
    pageTitle="Flytte Data til eller fra Azure Blob-lager ved hjælp af SSIS forbindelser | Microsoft Azure"
    description="Du kan flytte Data til eller fra Azure Blob-lager ved hjælp af SSIS forbindelser."
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />

# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Flytte Data til eller fra Azure Blob-lager ved hjælp af SSIS forbindelser

[SQL Server Integration Services Feature Pack til Azure](https://msdn.microsoft.com/library/mt146770.aspx) indeholder komponenter for at oprette forbindelse til Azure, Overfør data mellem Azure og lokale datakilder og proces data, der er gemt i Azure.

Vejledning i teknologier, der bruges til at flytte data til eller fra Azure Blob-lager er sammenkædet her:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


Når kunder har flyttet lokale data til skyen, kan de åbne den fra en hvilken som helst Azure service til at udnytte den fulde styrke i pakke med Azure-teknologier. Det kan bruges, for eksempel i Azure Machine Learning eller på en HDInsight klynge.

Dette er typisk være det første trin til gennemgange af [SQL](machine-learning-data-science-process-sql-walkthrough.md) og [HDInsight](machine-learning-data-science-process-hive-walkthrough.md) .

Du kan finde en beskrivelse af vedtaget scenarier, der bruger SSIS til at udføre almindelige i hybride data integrationsmuligheder forretningsbehov [gøre mere med SQL Server Integration Services Feature Pack til Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) -blog.

> [AZURE.NOTE] Henvise til [Azure Blob grundlæggende](../storage/storage-dotnet-how-to-use-blobs.md) og til [Azure Blob-tjenesten](https://msdn.microsoft.com/library/azure/dd179376.aspx)for at få en komplet Introduktion til Azure blob-lager.

## <a name="prerequisites"></a>Forudsætninger

For at udføre de opgaver, der er beskrevet i denne artikel, skal du have et Azure-abonnement og konfigureret en Azure lagerplads konto. Du skal kende din Azure lagerplads navn og konto kontonøgle at overføre eller hente data.

- For at konfigurere et **Azure abonnement**skal du se [gratis prøveversion af en måned](https://azure.microsoft.com/pricing/free-trial/).

- Vejledning i at oprette en **lagerplads konto** og for at komme konto og vigtige oplysninger, skal du se [om Azure lagerplads konti](../storage/storage-create-storage-account.md).


Hvis du vil bruge **SSIS forbindelser**, skal du hente:

- **SQL Server 2014 eller 2016 Standard (eller nyere)**: Installer omfatter SQL Server Integration Services.
- **Microsoft SQL Server 2014 eller 2016 Integration Services Feature Pack til Azure**: disse kan overføres, henholdsvis fra [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) og [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) sider.

> [AZURE.NOTE] SSIS er installeret med SQL Server, men er ikke inkluderet i Express-versionen. Du kan finde oplysninger om, hvilke programmer er inkluderet i forskellige versioner af SQL Server, under [Udgaver af SQL Server](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)

Finde [Hænder på kurser til SSIS](http://www.microsoft.com/download/details.aspx?id=20766) i kursusmateriale på SSIS

Brug af SISS til at oprette simple udtrækning af, transformation og indlæsning pakker, se oplysninger om, hvordan du kommer op og køre [SSIS Selvstudium: oprette en simpel ETL pakke](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Hente NYC Taxi datasæt  
Eksemplet er beskrevet her bruge et offentligt tilgængeligt datasæt – [NYC Taxi rejser](http://www.andresmh.com/nyctaxitrips/) datasættet. Datasættet består af om 173 millioner taxi ture i NYC år 2013. Der findes to typer data: forretningsrejse detaljer data og pris data. Der er en fil for hver måned, har vi 24 filer på alle, hver især er cirka 2GB ikke-komprimeret format.


## <a name="upload-data-to-azure-blob-storage"></a>Overføre data til Azure blob-lager
Hvis du vil flytte data ved hjælp af SSIS funktionen pack fra det lokale til Azure blob-lager, skal bruge vi en forekomst af [**Azure Blob overføre opgave**](https://msdn.microsoft.com/library/mt146776.aspx)vist her:

![konfigurere-data-videnskabelige-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


De parametre, der bruger opgaven er beskrevet her:


Felt|Beskrivelse|
----------------------|----------------|
**AzureStorageConnection**|Angiver en eksisterende Azure lagerplads Forbindelsesstyring eller opretter en ny, der refererer til en Azure-lager-konto, der peger på, der er vært for blob-filer.|
**BlobContainer**|Angiver navnet på objektbeholderen blob, der indeholder de overførte filer som BLOB.|
**BlobDirectory**|Angiver den blob-mappe, hvor den reviderede fil er gemt som en blok blob. Mappen blob er en virtuel hierarkiske struktur. Hvis der allerede findes på blob it ia erstattet.|
**LocalDirectory**|Angiver den lokale mappe, der indeholder filerne, der overføres.|
**Filnavn**|Angiver et navn filter for at vælge filer med det angivne navn mønster. For eksempel MySheet\*.xls\* indeholder filer som MySheet001.xls og MySheetABC.xlsx|
**TimeRangeFrom/TimeRangeTo**|Angiver filteret tid område. Filer, der er ændret efter *TimeRangeFrom* og før *TimeRangeTo* er inkluderet.|


> [AZURE.NOTE] **AzureStorageConnection** legitimationsoplysninger skal være korrekt, og **BlobContainer** skal findes, før overførslen forsøges.

## <a name="download-data-from-azure-blob-storage"></a>Hente data fra Azure blob-lager

For at hente data fra Azure blob-lager til lokal storage med SSIS, skal du bruge en forekomst af [Azure Blob overføre opgave](https://msdn.microsoft.com/library/mt146779.aspx).

##<a name="more-advanced-ssis-azure-scenarios"></a>Mere avancerede SSIS-Azure-scenarier
Vi Bemærk her, funktionspakken til SSIS gør det muligt for mere komplekse flyder skal håndteres af emballagen opgaver sammen. Blob-data kan for eksempel feed direkte i en HDInsight-klynge, hvis output kunne hentes tilbage til en blob og derefter til lokal lagerplads. SSIS kan køre Hive og gris job på en HDInsight klynge, ved hjælp af flere SSIS-forbindelser:

- For at køre en Hive-script på en Azure HDInsight klynge med SSIS, bruge [Azure HDInsight Hive opgave](https://msdn.microsoft.com/library/mt146771.aspx).
- For at køre en gris script på en Azure HDInsight klynge med SSIS, bruge [Azure HDInsight gris opgave](https://msdn.microsoft.com/library/mt146781.aspx).
