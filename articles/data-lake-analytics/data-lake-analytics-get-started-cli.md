<properties 
   pageTitle="Introduktion til Azure Data sø analyser ved hjælp af Azure kommandolinjen | Microsoft Azure" 
   description="Lær, hvordan du kan bruge kommandolinjen Azure til at oprette en sø datalager-konto, skal du oprette et Data sø Analytics-job fra U-SQL, og Send jobbet. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Selvstudium: Introduktion til Azure Data sø analyser ved hjælp af Azure kommandolinjen (CLI)

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Lær, hvordan du bruger Azure CLI til at oprette Azure Data sø Analytics-konti, definere Data sø Analytics job i [U-SQL](data-lake-analytics-u-sql-get-started.md)og sende job til Data sø Analytics-konti. Se [Oversigt over Azure Data sø Analytics](data-lake-analytics-overview.md)kan finde flere oplysninger om Data sø analyser.

I dette selvstudium, vil du udvikle et job, som læser en fane adskilt fil værdier (TSV) og konverterer det til en fil med kommaseparerede værdier (CSV). For at gå gennem samme selvstudiet ved hjælp af andre understøttede værktøjer skal du klikke på fanerne øverst på denne sektion.

##<a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
- **Azure CLI**. Se [installere og konfigurere Azure CLI](../xplat-cli-install.md).
    - Hent og Installer **foreløbige** [Azure CLI værktøjer](https://github.com/MicrosoftBigData/AzureDataLake/releases) for at fuldføre denne demo.
- **Godkendelse**ved hjælp af følgende kommando:

        azure login
    Du kan finde flere oplysninger om godkendelse af med en arbejds- eller skolekonto, [Opret forbindelse til et Azure-abonnement fra Azure CLI](../xplat-cli-connect.md).
- **Skift til Azure ressourcestyring-tilstand**, som med følgende kommando:

        azure config mode arm
        
## <a name="create-data-lake-analytics-account"></a>Oprette Data sø Analytics-konto

Du skal have en Data sø Analytics-konto, før du kan køre et job. Hvis du vil oprette en Data sø Analytics-konto, skal du angive følgende:

- **Azure ressourcegruppe**: A Data sø Analytics konto skal oprettes i en Azure ressourcegruppe. [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md) gør det muligt at arbejde med ressourcer i dit program som en gruppe. Du kan installere, opdatere eller slette alle ressourcerne for dit program i en enkelt, koordineret handling.  

    Optælles ressourcegrupperne i dit abonnement:
    
        azure group list 
    
    Sådan oprettes en ny ressourcegruppe:

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Data sø Analytics kontonavn**
- **Placering**: en af de Azure datacentre, der understøtter Data sø analyser.
- **Standard Data sø konto**: hver Data sø Analytics-konto har en standardkonto Data sø.

    Med listen over de eksisterende Data sø-konto:
    
        azure datalake store account list

    Sådan oprettes en ny Data sø-konto:

        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

    > [AZURE.NOTE] Kontonavnet Data sø skal kun indeholde små bogstaver og tal.



**Oprette en Data sø Analytics-konto**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"          

![Vise data sø Analytics konto](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] Kontonavnet Data sø Analytics skal kun indeholde små bogstaver og tal.


## <a name="upload-data-to-data-lake-store"></a>Overføre data til sø datalager

I dette selvstudium skal du behandle nogle Søg logfiler.  Søg loggen kan være gemt i enten Data sø store eller Azure Blob-lager. 

Azure-portalen indeholder en brugergrænseflade til kopiering nogle eksempeldatafilerne til Data sø standardkonto, der indeholder en søgning logfil. Se [forberede kildedataene](data-lake-analytics-get-started-portal.md#prepare-source-data) til at overføre data til sø datalager standardkontoen.

Hvis du vil overføre filer ved hjælp af cli, skal du bruge følgende kommando:

    azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
    azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Data sø Analytics kan også få adgang til Azure Blob-lager.  Overføre data til Azure Blob-lager, under [Brug af Azure CLI med Azure-lager](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Sende Data sø Analytics-job

Data sø Analytics-job skrives på sproget, U-SQL. Hvis du vil vide mere om U-SQL, skal du se [komme i gang med U-SQL sprog](data-lake-analytics-u-sql-get-started.md) og [U-SQL language reference](http://go.microsoft.com/fwlink/?LinkId=691348).

**Oprette et Data sø Analytics job script**

- Oprette en tekstfil med følgende U-SQL-script, og Gem tekstfilen til computeren:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Dette U-SQL-script læser kildefilen data ved hjælp af **Extractors.Tsv()**og opretter derefter en CSV-fil ved hjælp af **Outputters.Csv()**. 
    
    Undlad at ændre de to stier, medmindre du kopiere kildefilen til en anden placering.  Data sø Analytics vil oprette outputmappen, hvis den ikke findes.
    
    Det er nemmere at bruge relative stier efter filer, der er lagret i data sø konti. Du kan også bruge absolutte stier.  For eksempel 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Du skal bruge absolutte stier til at få adgang til filer i sammenkædede lagerplads konti.  Syntaksen for filer, der er gemt i sammenkædede Azure-lager-konto er:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Azure Blob-objektbeholder med offentlige BLOB eller offentlige beholdere adgangstilladelser understøttes ikke i øjeblikket.      

    
**Til at sende jobbet**


    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
Følgende kommandoer kan bruges til at listen job, få oplysninger om job og annullere job:

    azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
    azure datalake analytics job list "<Data Lake Analytics Account Name>"
    azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

Når jobbet er fuldført, kan du bruge følgende cmdletter for at vise fil, og du kan hente filen:
    
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>Se også

- For at se det samme selvstudium ved hjælp af andre værktøjer skal du klikke på fanen vælgere øverst på siden.
- For at se en mere kompleks forespørgsel skal du se [analysér websted logfiler, der bruger Azure Data sø analyser](data-lake-analytics-analyze-weblogs.md).
- For at komme i gang U-SQL udviklingsprogrammer skal du se [udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Få U-SQL under [Introduktion til Azure Data sø Analytics U-SQL-sprog](data-lake-analytics-u-sql-get-started.md).
- Du kan finde administrationsopgaver, [Administrere Azure Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-manage-use-portal.md).
- For at få et overblik over Data sø Analytics skal du se [Oversigt over Azure Data sø analyser](data-lake-analytics-overview.md).

