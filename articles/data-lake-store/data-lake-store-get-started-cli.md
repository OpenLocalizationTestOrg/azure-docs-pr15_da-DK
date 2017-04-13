<properties
   pageTitle="Komme i gang med datalager sø i tværs af platforme kommandoen linje brugergrænseflade | Microsoft Azure"
   description="Brug Azure på tværs af platforme kommandolinjen til at oprette en sø datalager konto og udføre grundlæggende handlinger"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Introduktion til Azure Data sø Store Azure kommandolinje

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST-API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Lær at bruge Azure kommandogrænseflade linje til at oprette en Azure datalager sø konto og udføre grundlæggende handlinger, sådan som oprette mapper, upload og download datafiler og slette din konto, osv. Du kan finde flere oplysninger om sø datalager, [Oversigt over sø datalager](data-lake-store-overview.md).

Azure CLI er implementeret i Node.js. Det kan bruges på alle platforme, der understøtter Node.js, herunder Windows, Mac og Linux. Azure CLI er Åbn kilde. Koden administreres i GitHub på <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Denne artikel omhandler kun ved hjælp af Azure CLI med sø datalager. Efter en generel vejledning om, hvordan du bruger Azure CLI, se, [hvordan du bruger Azure CLI] [azure-command-line-tools].


##<a name="prerequisites"></a>Forudsætninger

Før du begynder i denne artikel, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

- **Azure CLI** - se [installere og konfigurere Azure CLI](../xplat-cli-install.md) oplysninger om installation og konfiguration. Kontrollér, at du genstarter din computer, når du har installeret CLI.

## <a name="authentication"></a>Godkendelse

I denne artikel bruges en enklere godkendelse fremgangsmåde med sø datalager, der hvor du kan logge på som bruger en slutbrugerlicensaftale. Adgangsniveau til datalager sø konto og filsystem derefter er underlagt adgangsniveauet for den bruger logget på. Der er andre fremgangsmåder samt til at godkende sø datalager, som er **Slutbrugerlicensaftale godkendelse** eller **tjeneste til godkendelse**. Instruktioner og flere oplysninger om, hvordan du godkender, under [Godkend med sø datalager brug af Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

##<a name="login-to-your-azure-subscription"></a>Logge på abonnementet Azure

1. Følg trinnene beskrevet i [oprette forbindelse til et Azure-abonnement fra Azure kommandolinjen (Azure CLI)](../xplat-cli-connect.md) og oprette forbindelse til dit abonnement ved hjælp af den `azure login` metode.

2. Liste over de abonnementer, der er knyttet til din konto ved hjælp af den `azure account list` kommandoen.

        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false

    **Azure-sub-1** er aktiveret fra outputtet ovenfor, og andre abonnementet er **Azure-sub-2**. 

3. Vælg det abonnement, du vil arbejde. Hvis du vil arbejde under Azure-sub-2-abonnement, skal du bruge den `azure account set` kommandoen.

        azure account set Azure-sub-2


## <a name="create-an-azure-data-lake-store-account"></a>Oprette en Azure datalager sø-konto

Åbn en kommandoprompt, shell eller en terminal session og Kør følgende kommandoer.

2. Skifte til Azure ressourcestyring tilstand ved hjælp af følgende kommando:

        azure config mode arm


5. Oprette en ny ressourcegruppe. I følgende kommando, du give de parameterværdier, du vil bruge.

        azure group create <resourceGroup> <location>

    Hvis placeringens navn indeholder mellemrum, skal du placere det i anførselstegn. For eksempel "af US 2".

5. Oprette kontoen sø datalager.

        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Oprette mapper i dit sø datalager

Du kan oprette mapper under kontoen Azure sø datalager til at administrere og gemme data. Brug følgende kommando til at oprette en mappe med navnet "mynewfolder" i roden af sø datalager.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Eksempel:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Overføre data til din sø datalager

Til sø datalager direkte på rodniveau eller til en mappe, du har oprettet i kontoen, kan du overføre dine data. De kodestykker nedenfor viser, hvordan du overfører nogle eksempeldata til den mappe (**mynewfolder**), du oprettede i forrige afsnit.

Hvis du leder efter nogle eksempeldata for at overføre, kan du få mappen **Ambulance Data** fra [Azure Data sø ciffer lager](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Hente filen og gemme den i en lokal mappe på din computer, som C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Eksempel:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Liste over filer i sø datalager

Brug følgende kommando til at få vist filer på en sø datalager-konto.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Eksempel:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

Output fra dette skal ligne følgende:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Omdøbe, hente og slette data fra dit datalager sø

* **Omdøbe en fil**, skal du bruge følgende kommando:

        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

    Eksempel:

        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Hente en fil**, skal du bruge følgende kommando. Kontrollér, at stien til destinationsmappen startposition allerede findes.

        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

    Eksempel:

        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Slette en fil**, skal du bruge følgende kommando:

        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

    Eksempel:

        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

    Når du bliver bedt om det, kan du angive **Y** for at slette elementet.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Få vist listen access kontrolelement for en mappe i sø datalager

Brug følgende kommando til at få vist adgangskontrollisterne en sø datalager mappe. I den aktuelle udgave, kan der oprettes ACLs kun i roden af sø datalager. Så være Stiparameteren altid rod (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Eksempel:

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Slette kontoen sø datalager

Brug følgende kommando til at slette en sø datalager-konto.

    azure datalake store account delete <dataLakeStoreAccountName>

Eksempel:

    azure datalake store account delete mynewdatalakestore

Når du bliver bedt om det, kan du angive **Y** for at slette kontoen.


## <a name="next-steps"></a>Næste trin

- [Sikre data i sø datalager](data-lake-store-secure-data.md)
- [Bruge Azure Data sø Analytics med sø datalager](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Bruge Azure HDInsight med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md
