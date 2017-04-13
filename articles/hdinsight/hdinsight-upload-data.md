<properties
    pageTitle="Overføre data til Hadoop-job i HDInsight | Microsoft Azure"
    description="Lær at overføre og få adgang til data for Hadoop sager i HDInsight ved hjælp af Azure CLI, Azure Storage Explorer, Azure PowerShell, Hadoop kommandolinjen eller Sqoop."
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>



#<a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Overføre data til Hadoop-job i HDInsight

Azure HDInsight giver et komplet Hadoop-distribueret filsystem (HDFS) over Azure Blob-lager. Det er udviklet som filtypenavnet HDFS at levere en problemfri oplevelse til kunderne. Det giver mulighed for det samlede sæt af komponenter i Hadoop økosystemet til at betjene direkte på det administrerer dataene. Azure Blob-lager og HDFS er entydige filsystemer, der er optimeret til lagring af data og beregninger på disse data. Finde oplysninger om fordelene ved at bruge Azure Blob-lager, i [Brug Azure Blob-lager med HDInsight][hdinsight-storage].

**Forudsætninger**

Bemærk følgende krav, inden du går i gang:

* En Azure HDInsight klynge. Flere oplysninger under [Introduktion til Azure HDInsight] [ hdinsight-get-started] eller [klargøring HDInsight grupper][hdinsight-provision].

##<a name="why-blob-storage"></a>Hvorfor blob storage?

Azure HDInsight klynger er typisk installeret for at køre MapReduce job, og klynger udelades, når disse job fuldført. Gemme dataene i HDFS klynger når beregninger er fuldført, kan det være en dyr måde at gemme disse data. Azure Blob-lager er en høj tilgængelighed, meget SVG, høj kapacitet, lave omkostninger og delbare datalager for data, der skal behandles ved hjælp af HDInsight. Lagring af data i en blob gør det muligt for de HDInsight klynger, der bruges til beregning skal frigives sikkert uden at miste data.

###<a name="directories"></a>Mapper

Azure Blob-objektbeholdere lagre data som nøgle/værdi-par, og der er ikke et directory hierarki. Men tegnet "/" kan bruges i navnet på den nøgle for at vise den som om en fil er gemt i en mappestrukturen. HDInsight ser disse, som om de er faktisk kataloger.

En blob nøgle kan for eksempel være *input/log1.txt*. Der findes ingen faktiske "input" mappe, men på grund af tilstedeværelsen af "/" tegnet i navnet på den nøgle, der er udseendet af en sti til filen.

Hvis du bruger Azure Explorer værktøjer på grund af det, kan du opleve nogle 0 byte-filer. Disse filer bruges to formål:

- Hvis der er tomme mapper, markere de af eksistensen af mappen. Azure Blob-lager er intelligente nok til, at hvis der findes en blob hedder foo/panel, der er en mappe med navnet **foo**. Men den eneste måde til at angive en tom mappe med navnet **foo** er ved at få denne specielle 0 byte-fil på plads.

- De indeholder særlige metadata, der skal bruges af filsystem Hadoop, især tilladelser og ejere for mapperne.

##<a name="command-line-utilities"></a>Funktioner fra en kommandolinje

Microsoft yder følgende værktøjer til at arbejde med Azure Blob-lager:

| Værktøjet | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Azure kommandolinjen][azurecli] | ✔ | ✔ | ✔ |
| [Azure PowerShell][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Hadoop-kommando](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] Mens Azure CLI, Azure PowerShell og AzCopy kan alle bruges fra eksterne Azure, Hadoop kommando er kun tilgængelig på HDInsight klyngen og kun tillader, at indlæse data fra det lokale filsystem i Azure Blob-lager.

###<a id="xplatcli"></a>Azure CLI

Azure CLI er et værktøj i tværs af platforme, der gør det muligt at administrere Azure tjenester. Brug følgende trin til at overføre data til Azure Blob-lager:

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Installere og konfigurere Azure CLI til Mac, Linux og Windows](../xplat-cli-install.md).

2. Åbn en kommandoprompt, Fest eller andre shell, og brug følgende fremgangsmåde til at godkende abonnementet Azure.

        azure login

    Når du bliver bedt om det, kan du angive det brugernavn og adgangskode for dit abonnement.

3. Skriv følgende kommando til at få vist kontiene lagerplads for dit abonnement:

        azure storage account list

4. Vælg den konto, lagerplads, der indeholder den blob, du vil arbejde med, og derefter bruge følgende kommando til at hente nøglen for denne konto:

        azure storage account keys list <storage-account-name>

    Dette skal returnere **primære** og **sekundære** taster. Kopiér værdien for den **primære** nøgle, fordi den bruges i de næste trin.

5. Brug følgende kommando til at hente en liste over blob objektbeholdere i kontoen lagerplads:

        azure storage container list -a <storage-account-name> -k <primary-key>

6. Brug følgende kommandoer til at uploade og downloade filer til blob:

    * Overføre en fil:

            azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

    * Hente en fil:

            azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Hvis du altid skal arbejde med den samme lagerplads-konto, kan du angive følgende miljøvariabler i stedet for kontoen, der angiver og nøgle til hver kommando:
>
> * **AZURE\_LAGERPLADS\_konto**: kontonavn lager
>
> * **AZURE\_LAGERPLADS\_ACCESS\_nøgle**: kontonøgle lagerplads

###<a id="powershell"></a>Azure PowerShell

Azure PowerShell er et scripting-miljø, som du kan bruge til at styre og automatisere installation og administration af din arbejdsmængder i Azure. Finde oplysninger om at konfigurere din arbejdsstation for at køre Azure PowerShell [installere og konfigurere Azure PowerShell](../powershell-install-configure.md).

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Overføre en lokal fil til Azure Blob-lager**

1. Åbne konsollen Azure PowerShell, som beskrevet i [installere og konfigurere Azure PowerShell](../powershell-install-configure.md).
2. Angive værdier for de fem første variabler i følgende script:

        $resourceGroupName = "<AzureResourceGroupName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. Indsæt scriptet til Azure PowerShell console for at køre den for at kopiere filen.

For eksempel se PowerShell-scripts, der er oprettet, for at arbejde med HDInsight, [HDInsight værktøjer](https://github.com/blackmist/hdinsight-tools).

###<a id="azcopy"></a>AzCopy

AzCopy er et værktøj på kommandolinjen, der er udviklet til at forenkle opgaven at overføre data til og fra en Azure-lager-konto. Du kan bruge den som et enkeltstående værktøj eller inkorporere dette værktøj i et eksisterende program. [Hente AzCopy][azure-azcopy-download].

AzCopy syntaks er:

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Du kan finde flere oplysninger, se [AzCopy - overførsel/hente filer til Azure BLOB][azure-azcopy].


###<a id="commandline"></a>Hadoop-kommandolinjen

Kommandolinjen Hadoop er kun nyttigt til lagring af data i blob-lager, når dataene findes allerede på Klyngenoden hoved.

For at kunne bruge kommandoen Hadoop, skal du først oprette forbindelse til headnode ved hjælp af en af følgende fremgangsmåder:

* **Windows-baseret HDInsight**: [Opret forbindelse ved hjælp af Fjernskrivebord](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **Linux-baserede HDInsight**: oprette forbindelse ved hjælp af SSH ([kommandoen SSH](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) eller [trykfarver](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

Når forbindelse, kan du bruge følgende syntaks til at overføre en fil til lagring.

    hadoop -copyFromLocal <localFilePath> <storageFilePath>

For eksempel`hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Da standardfilsystemet for HDInsight er i Azure Blob-lager, er /example/data.txt faktisk i Azure Blob-lager. Du kan også referere til fil, som:

    wasbs:///example/data/data.txt

eller

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Se en liste over andre Hadoop-kommandoer, der arbejder med filer, [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [AZURE.WARNING] På HBase klynger blokere standard størrelse, der bruges, når det er 256KB at skrive data. Mens det fungerer fint, når du bruger HBase APIs REST API'er, ved hjælp af den `hadoop` eller `hdfs dfs` kommandoer til at skrive data, der er større end ~ 12GB medfører en fejl. I afsnittet [lagerplads undtagelse for skrivning på blob](#storageexception) nedenfor for at få flere oplysninger.

##<a name="graphical-clients"></a>Grafiske klienter

Der findes også flere programmer, som giver en grafisk grænseflade til at arbejde med Azure-lager. Følgende er en liste over nogle af disse programmer:

| Klient | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Microsoft Visual Studio-værktøjer til HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) | ✔ | ✔ | ✔ |
| [Azure-lager Explorer](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Cyberduck](https://cyberduck.io/) |  | ✔ | ✔ |

###<a name="visual-studio-tools-for-hdinsight"></a>Visual Studio-værktøjer til HDInsight

Se [navigere i sammenkædede ressourcer](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources)kan finde flere oplysninger.

###<a id="storageexplorer"></a>Azure-lager Explorer

*Azure lagerplads Explorer* er et nyttigt værktøj til undersøge og ændre dataene i BLOB. Det er et gratis, skal du åbne kilde-værktøj, der kan hentes fra [http://storageexplorer.com/](http://storageexplorer.com/). Koden er tilgængelig fra dette link samt.

Før du bruger værktøjet, skal du kende Azure lagerplads konto navn og konto nøglen. Yderligere oplysninger om at få disse oplysninger finder du den "Sådan: få vist, Kopiér og Regenerer lagerplads hurtigtasterne" sektion af [oprette, administrere, eller slette en lagerplads konto][azure-create-storage-account].  

1. Køre Azure Storage Explorer. Hvis det er første gang, du har kørte lagerplads Stifinder, du vil blive bedt om ___kontonavn lager__ og __lagerplads kontonøgle__. Hvis du har har kørt den før brug knappen __Tilføj__ for at tilføje et nyt lagerplads kontonavn og nøgle.

    Skriv navnet, og nøgle til kontoen lagerplads bruges af din HDinsight klynge og derefter vælge __Gem og Åbn__.

    ![HDI. AzureStorageExplorer][image-azure-storage-explorer]

5. Klik på navnet på objektbeholderen, der er knyttet til din HDInsight klynge på listen over beholdere til venstre for grænsefladen. Som standard dette er navnet på HDInsight klyngen, men kan være forskellige, hvis du har angivet et bestemt navn, når du opretter klyngen.

6. Vælg ikonet Overfør fra værktøjslinjen.

    ![Værktøjslinje med Overfør-ikonet er fremhævet](./media/hdinsight-upload-data/toolbar.png)

7. Angiv en fil, og klik derefter på **Åbn**. Når du bliver bedt om det, kan du vælge at __overføre__ at overføre filen i roden af objektbeholderen til lagring af. Hvis du vil overføre filen til en bestemt sti, Angiv stien i feltet __Destination__ og derefter vælge __Overfør__.

    ![Dialogboks for overførsel](./media/hdinsight-upload-data/fileupload.png)
    
    Når filen er overført, kan du bruge den fra job på HDInsight klyngen.

##<a name="mount-azure-blob-storage-as-local-drive"></a>Tilslutte Azure Blob-lager som lokale drev

Se [tilslutningen Azure Blob-lager som lokalt drev](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

##<a name="services"></a>Tjenester

###<a name="azure-data-factory"></a>Azure Data Factory

Tjenesten Azure Data Factory er en komplet administreret tjeneste til at skrive data lagerplads, databehandling og data bevægelse tjenester til strømlinet, SVG og pålidelige data fremstilling rørledninger.

Azure Data Factory kan bruges til at flytte data til Azure Blob-lager eller til at oprette data rørledninger, der bruger HDInsight funktioner som Hive og gris direkte.

Yderligere oplysninger finder du i [Azure Data Factory dokumentation](https://azure.microsoft.com/documentation/services/data-factory/).

###<a id="sqoop"></a>Apache Sqoop

Sqoop er et værktøj, der er udviklet til at overføre data mellem Hadoop og relationsdatabaser. Du kan bruge det til at importere data fra en relationsdatabase administrationssystem (RDBMS), såsom SQL Server, MySQL eller Oracle til Hadoop-distribueret filsystem (HDFS), transformere dataene i Hadoop med MapReduce eller Hive, og Eksportér derefter dataene i en RDBMS igen.

Kan finde flere oplysninger i [Brug Sqoop med HDInsight][hdinsight-use-sqoop].

##<a name="development-sdks"></a>Udvikling SDK'er

Azure Blob-lager kan også åbnes ved hjælp af en Azure SDK fra de følgende programmeringssprog:

* .NET
* Java
* Node.js
* PHP
* Python
* Fonetisk

Du kan finde flere oplysninger om installation af Azure SDK'er se [Azure-overførsler](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

### <a id="storageexception"></a>Lagerplads undtagelse for skrivning på blob

__Symptomer__: Når du bruger den `hadoop` eller `hdfs dfs` kommandoer til at skrive filer, der er ~ 12 GB eller mere på en HBase klynge, du kan støde på følgende fejl: 

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

__Årsag__: HBase på HDInsight klynger standard til en blokstørrelse af 256 KB, når du skriver til Azure-lager. Mens det fungerer for HBase APIs eller REST API'er, den, medfører en fejl, når du bruger den `hadoop` eller `hdfs dfs` funktioner fra en kommandolinje.

__Løsning__: Brug `fs.azure.write.request.size` til at angive en blok større. Du kan gøre dette på grundlag af per brug ved hjælp af den `-D` parameter. Følgende er et eksempel på brug af denne parameter med den `hadoop` kommandoen:

    hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data

Du kan også øge værdien af `fs.azure.write.request.size` globalt ved hjælp af Ambari. Følgende trin kan bruges til at ændre værdien i Ambari Webbrugergrænsefladen:

1. Gå til Ambari Webbrugergrænseflade til din klynge i din browser. Dette er https://CLUSTERNAME.azurehdinsight.net, hvor __CLUSTERNAME__ er navnet på din klynge.

    Når du bliver bedt om det, kan du angive administrator brugernavn og adgangskode for-klyngen.

2. Vælg __HDFS__i venstre side af skærmen, og vælg derefter fanen __konfigurationer__ .

3. Angiv i feltet __filtrere...__ `fs.azure.write.request.size`. Derved vises feltet og aktuelle værdien midt på siden.

4. Ændre værdien fra 262144 (256KB) til den nye værdi. For eksempel 4194304 (4MB).

![Billede af ændring af værdien gennem Ambari Webbrugergrænseflade](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Du kan finde flere oplysninger om brug af Ambari, [administrere HDInsight klynger ved hjælp af Ambari Webbrugergrænsefladen](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Næste trin

Nu, hvor du forstår, hvordan du kan hente data til HDInsight, kan du læse følgende artikler for at lære at udføre analyse:

* [Introduktion til Azure HDInsight][hdinsight-get-started]
* [Sende Hadoop-job fra et program][hdinsight-submit-jobs]
* [Bruge Hive med HDInsight][hdinsight-use-hive]
* [Brug gris med HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage/storage-create-storage-account.md
[azure-azcopy-download]: ../storage/storage-use-azcopy.md
[azure-azcopy]: ../storage/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
