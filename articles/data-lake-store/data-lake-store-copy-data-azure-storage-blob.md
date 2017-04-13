<properties
   pageTitle="Kopiere data fra Azure-lager BLOB til sø datalager | Microsoft Azure"
   description="Brug AdlCopy til at kopiere data fra Azure-lager BLOB til sø datalager"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Kopiere data fra Azure-lager BLOB til sø datalager

> [AZURE.SELECTOR]
- [Brug af DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Brug af AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

Azure datalager sø indeholder et kommandolinjeværktøj, [AdlCopy](http://aka.ms/downloadadlcopy), for at kopiere data fra følgende steder:

* Fra Azure lagerplads BLOB til sø datalager. Du kan ikke bruge AdlCopy til at kopiere data fra sø datalager til Azure-lager BLOB.

* Mellem to Azure datalager sø konti. 

Du kan også bruge værktøjet AdlCopy i to forskellige tilstande:

* **Enkeltstående**, som hvor værktøjet anvender sø datalager ressourcer til at udføre opgaven.

* **Ved hjælp af en Data sø Analytics-konto**, hvor de enheder, der er tildelt til kontoen Data sø Analytics bruges til at udføre kopieringen. Du vil bruge denne indstilling, når du søger udføre kopi opgaver på en mere forudsigelige måde.

##<a name="prerequisites"></a>Forudsætninger

Før du begynder i denne artikel, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

- **Azure lagerplads BLOB** beholder med nogle data.

- **Azure Data sø Analytics-konto (valgfrit)** – som se [Introduktion til Azure Data sø Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) for oplysninger om, hvordan du opretter en sø datalager-konto.

- **AdlCopy værktøj**. Installer værktøjet i AdlCopy fra [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntaksen for værktøjet AdlCopy

Brug følgende syntaks til at arbejde med værktøjet AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

Parametre i syntaksen er beskrevet nedenfor:

| Indstillingen    | Beskrivelse                                                                                                                                                                                                                                                                                                                                                                                                          |
|-----------|------------|
| Kilde    | Angiver placeringen af kildedataene i Azure lagerplads blob. Kilden kan være en blob objektbeholder, en blob eller en anden sø datalager konto.                                                                                                                                                                                                                                                                                                 |
| Dest      | Angiver sø datalager destinationen for at kopiere til.                                                                                                                                                                                                                                                                                                                                                                |
| SourceKey | Angiver hurtigtast lagerplads til Azure lagerplads blob kilden. Dette er påkrævet, kun, hvis kilden er en blob objektbeholder eller en blob.                                                                                                                                                                                                                                                                                                                                                 |
| Konto   | **Valgfrit**. Brug denne indstilling, hvis du vil bruge Azure Data sø Analytics-konto til at køre Kopier sag. Hvis du bruger indstillingen /Account i syntaksen, men du ikke angiver en Data sø Analytics-konto, bruger AdlCopy en standardkonto til at køre jobbet. Også, hvis du bruger denne indstilling, skal du føje kilde (Azure-lager Blob) og destination (Azure sø datalager) som datakilder for din Data sø Analytics-konto.  |
| Enheder     |  Angiver antallet Data sø Analytics-enheder, der skal bruges til Kopier sag. Denne indstilling er obligatorisk, hvis du bruger indstillingen **/Account** til at angive Data sø Analytics-kontoen.
| Mønster   | Angiver et regex mønster, der angiver, hvilke BLOB eller filer for at kopiere. AdlCopy bruger store og små bogstaver tilsvarende. Standard mønstret bruges, når der er angivet nogen mønster er at kopiere alle elementer. Angive flere filer mønstre understøttes ikke.                                                                                                                                                                                                                                                                                                                                               



## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Brug AdlCopy (som enkeltstående) for at kopiere data fra en Azure-lager blob

1. Åbn en kommandoprompt, og gå til den mappe, hvor AdlCopy er installeret, typisk `%HOMEPATH%\Documents\adlcopy`.

2. Kør følgende kommando for at kopiere en bestemt blob fra objektbeholderen kilde til et datalager sø:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Eksempel:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    
    >[AZURE.NOTE] Ovenstående syntaks angiver den fil, der skal kopieres til en mappe i kontoen sø datalager. AdlCopy værktøjet opretter en mappe, hvis det angivne mappenavn ikke findes.

    Du bliver bedt om at angive legitimationsoplysninger for Azure abonnementet under, du har kontoen sø datalager. Der vises en output, der ligner følgende:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. Du kan også kopiere alle BLOB fra én objektbeholder til kontoen sø datalager ved hjælp af følgende kommando:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Eksempel:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Brug AdlCopy (som enkeltstående) for at kopiere data fra en anden sø datalager konto

Du kan også bruge AdlCopy til at flytte data mellem to sø datalager konti.

1. Åbn en kommandoprompt, og gå til den mappe, hvor AdlCopy er installeret, typisk `%HOMEPATH%\Documents\adlcopy`.

2. Kør følgende kommando for at kopiere en bestemt fil fra den ene sø datalager konto til en anden.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    Eksempel:

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

    >[AZURE.NOTE] Ovenstående syntaks angiver den fil, der skal kopieres til en mappe i destinationen sø datalager konto. AdlCopy værktøjet opretter en mappe, hvis det angivne mappenavn ikke findes.

    Du bliver bedt om at angive legitimationsoplysninger for Azure abonnementet under, du har kontoen sø datalager. Der vises en output, der ligner følgende:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. Følgende kommando kopierer alle filer fra en bestemt mappe i kilden sø datalager konto til en mappe i destinationen sø datalager konto.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Brug AdlCopy (med Data sø Analytics-konto) til at kopiere data

Du kan også bruge din Data sø Analytics-konto til at køre AdlCopy jobbet for at kopiere data fra Azure lagerplads BLOB til sø datalager. Du vil bruge denne indstilling typisk, når data, der flyttes er i området af gigabyte og TB, og du vil bedre og mere forudsigelige ydeevne overførselshastighed.

Hvis du vil bruge din Data sø Analytics-konto med AdlCopy for at kopiere fra en Azure-lager Blob, skal kilden (Azure-lager Blob) tilføjes som en datakilde til kontoen Data sø Analytics. Flere oplysninger om at tilføje flere datakilder i din Data sø Analytics-konto, under [administrere Data sø Analytics kontoindstillinger datakilder](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

>[AZURE.NOTE] Hvis du kopierer fra en Azure sø datalager konto som kilde ved hjælp af en Data sø Analytics-konto, behøver du ikke skal tilknyttes kontoen Data sø Analytics kontoen sø datalager. Kravet om at knytte store kilde til Data sø Analytics-konto er kun, når kilden er en Azure-lager-konto.

Kør følgende kommando for at kopiere fra en Azure-lager blob til en sø datalager konto ved hjælp af Data sø Analytics konto:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Eksempel:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


På samme måde, skal du køre følgende kommando for at kopiere fra en Azure-lager blob til en sø datalager konto ved hjælp af Data sø Analytics konto:

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Brug AdlCopy til at kopiere data ved hjælp af mønstersammenligning

I dette afsnit, skal du lære, hvordan du bruger AdlCopy til at kopiere data fra en kilde (i eksemplet nedenfor vi bruger Azure lagerplads Blob) til en destination sø datalager-konto ved hjælp af mønstersammenligning. Du kan for eksempel bruge nedenstående trin til at kopiere alle filer med filtypenavnet .csv fra kilde blob til destinationen.

1. Åbn en kommandoprompt, og gå til den mappe, hvor AdlCopy er installeret, typisk `%HOMEPATH%\Documents\adlcopy`.

2. Kør følgende kommando for at kopiere alle filer med filtypenavnet *.csv fra en bestemt blob fra objektbeholderen kilde til et datalager sø:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Eksempel:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

    
## <a name="billing"></a>Fakturering

* Hvis du bruger værktøjet AdlCopy separat vil du blive faktureret for udgangspunkt omkostninger til at flytte data, hvis kilden Azure-lager konto ikke er i samme område som sø datalager.

* Hvis du bruger værktøjet AdlCopy med dine Data sø Analytics-konto, gælder standard [Data sø Analytics fakturering satser](https://azure.microsoft.com/pricing/details/data-lake-analytics/) .

## <a name="considerations-for-using-adlcopy"></a>Overvejelser ved brug af AdlCopy

* AdlCopy (for version 1.0.5), understøtter kopiering af data fra kilder, samlet har mere end tusindvis af filer og mapper. Hvis du støder på problemer med kopiering af store datasæt, kan du dog distribuere filer/mapper i forskellige undermapper og bruge stien til de underordnede mapper som kilde i stedet.

## <a name="next-steps"></a>Næste trin

- [Sikre data i sø datalager](data-lake-store-secure-data.md)
- [Bruge Azure Data sø Analytics med sø datalager](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Bruge Azure HDInsight med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)
