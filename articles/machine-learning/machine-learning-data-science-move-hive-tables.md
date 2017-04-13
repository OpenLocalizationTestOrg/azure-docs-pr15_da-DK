<properties
    pageTitle="Oprette og indlæse data i Hive tabeller fra Blob-lager | Microsoft Azure"
    description="Oprette Hive tabeller og indlæse data i blob til hive tabeller"
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


#<a name="create-and-load-data-into-hive-tables-from-azure-blob-storage"></a>Oprette og indlæse data i Hive tabeller fra Azure blob-lager

Dette emne indeholder generisk Hive-forespørgsler, oprette Hive tabeller og indlæser data fra Azure blob-lager. Nogle retningslinjer findes også på partitionering Hive tabeller og ved hjælp af den optimeret række kolonne (ORC) formatering for at forbedre ydeevnen i forespørgslen.

I denne **menu** links til emner, der beskriver, hvordan indtager data i target miljøer, hvor dataene kan være gemt og behandles under Team Data videnskabelige proces (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## <a name="prerequisites"></a>Forudsætninger
I denne artikel antager, at du har:

* Oprettet en Azure-lager-konto. Hvis du har brug for instruktioner, kan du se [om Azure lagerplads konti](../storage/storage-create-storage-account.md). 
* Klargjort en tilpasset Hadoop klynge med tjenesten HDInsight.  Hvis du har brug for instruktioner, kan du se [tilpasse Azure HDInsight Hadoop klynger for avancerede analyser](machine-learning-data-science-customize-hadoop-cluster.md).
* Aktiveret fjernadgang til klyngen logget på, og åbnes konsollen Hadoop-kommandolinjen. Hvis du har brug for instruktioner, kan du se [få adgang til den i afsnit af Hadoop klynge noder](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

## <a name="upload-data-to-azure-blob-storage"></a>Overføre data til Azure blob-lager
Hvis du har oprettet en Azure virtuelt ved at følge vejledningen i at [konfigurere en Azure virtuel maskine til avanceret analyse](machine-learning-data-science-setup-virtual-machine.md), denne scriptfil skal er blevet overført til den *C:\\brugere\\\<brugernavn\>\\dokumenter\\Data videnskabelige Scripts* mappe på en virtuel maskine. Disse Hive forespørgsler kun kræver, at du tilslutter dine egne dataskemaet og konfiguration af Azure blob-lager i de relevante felter er klar til afsendelse.

Vi antager, at dataene for Hive tabeller er i en **dekomprimerede** tabelformat, og, at dataene er blevet overført til standard (eller til en ekstra) beholder den lagerplads-konto, der bruges af Hadoop-klynge.

Hvis du vil øvelse **NYC Taxi forretningsrejse Data**, vil du:

- **hente** 24 [NYC Taxi forretningsrejse](http://www.andresmh.com/nyctaxitrips) datafiler (12 forretningsrejse filer og 12 pris filer)
- **udpakke** alle filer i .csv-filer, og derefter
- **overføre** dem til standard (eller relevante objektbeholder) af kontoen Azure-lager, der blev oprettet af fremgangsmåden beskrevet i emnet [tilpasse Azure HDInsight Hadoop klynger til avanceret Analytics proces og teknologi](machine-learning-data-science-customize-hadoop-cluster.md) . Processen til at overføre .csv-filerne til objektbeholderen standard på kontoen lagerplads kan findes på denne [side](machine-learning-data-science-process-hive-walkthrough.md#upload).


## <a name="submit"></a>Hvordan du sender Hive forespørgsler

Hive forespørgsler kan sendes ved hjælp af:

1. [Sende Hive forespørgsler via Hadoop kommandolinjen i headnode af Hadoop klynge](#headnode)
2. [Sende Hive forespørgsler med Hive-Editor](#hive-editor)
3. [Sende Hive forespørgsler med Azure PowerShell-kommandoer](#ps)

Hive forespørgsler er SQL-lignende. Hvis du er bekendt med SQL, kan du finde den [Hive for SQL brugere Cheat ark](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) nyttige.

Når du sender en Hive-forespørgsel, kan du også kontrollere destinationen for output fra Hive forespørgsler, uanset om det er på skærmen eller til en lokal fil på noden hoved eller til en Azure blob.


###<a name="headnode"></a>1. sende Hive forespørgsler via Hadoop kommandolinjen i headnode af Hadoop klynge

Hvis forespørgslen Hive er kompleks, fører sende den direkte i noden hoved i Hadoop klynge typisk hurtigere slå end sende den med en Hive redaktør eller Azure PowerShell-scripts.

Log på noden hoved af Hadoop-klynge, Åbn kommandolinjen Hadoop på skrivebordet for noden hoved, og Angiv kommandoen `cd %hive_home%\bin`.

Har du tre metoder til at sende Hive forespørgsler i Hadoop kommandolinjen:

* direkte
* Brug af .hql filer
* med konsollen Hive kommandoen

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Sende Hive forespørgsler direkte i Hadoop-kommandolinjen.

Du kan køre kommandoen som `hive -e "<your hive query>;` til at sende simple Hive forespørgsler direkte i Hadoop-kommandolinjen. Her er et eksempel, hvor det røde felt beskriver den kommando, som sender Hive forespørgslen, og den grønne boks skitseres output fra forespørgslen Hive.

![Opret arbejdsområde](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Sende Hive forespørgsler i .hql filer

Når forespørgslen Hive er mere kompliceret og har flere linjer, er redigerer forespørgsler i kommandolinjen eller Hive kommandoen console ikke praktiske. Et alternativ er at bruge et tekstredigeringsprogram i noden hoved af Hadoop-klynge gemme Hive-forespørgsler i en .hql fil i en lokal mappe for noden hoved. Derefter forespørgslen Hive i filen .hql kan sendes ved hjælp af den `-f` argument på følgende måde:

    hive -f "<path to the .hql file>"

![Opret arbejdsområde](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-3.png)


**Skjule fremdrift status skærmen Udskriv af Hive forespørgsler**

Som standard, når Hive forespørgsel er blevet sendt i Hadoop kommandolinjen skal udskrives status for kort/reducere jobbet på skærmen. Hvis du vil skjule skærmen Udskriv over kort/reducere job fremgang, kan du bruge en argument `-S` ("S" med store bogstaver) i kommandoen linje på følgende måde:

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Sende Hive forespørgsler i Hive konsol.

Du kan også vælge først angive konsollen Hive kommandoen ved at køre kommandoen `hive` i Hadoop kommandolinjen, og derefter sende Hive forespørgsler i Hive konsol. Her er et eksempel. I dette eksempel fremhæve felterne to røde de kommandoer, der bruges til at angive Hive kommandokonsollen, og forespørgslen Hive blev sendt i Hive kommandoen console henholdsvis. Den grønne boks fremhæver output fra forespørgslen Hive.

![Opret arbejdsområde](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-2.png)

De tidligere eksempler output direkte Hive forespørgselsresultaterne på skærmen. Du kan også skrive output til en lokal fil på noden hoved eller en Azure blob. Du kan derefter bruge andre værktøjer til at analysere output fra Hive forespørgsler.

**Medtag Hive forespørgselsresultater til en lokal fil.**

For at sende Hive forespørgselsresultater til en lokal mappe på noden hoved, skal du sende Hive forespørgslen i Hadoop kommandolinjen på følgende måde:

    hive -e "<hive query>" > <local path in the head node>

I eksemplet nedenfor er skrevet output fra Hive forespørgsel til en fil `hivequeryoutput.txt` i directory `C:\apps\temp`.

![Opret arbejdsområde](./media/machine-learning-data-science-move-hive-tables/output-hive-results-1.png)

**Output Hive forespørgselsresultater til en Azure blob**

Du kan også udskrive forespørgselsresultaterne Hive til en Azure blob i objektbeholderen standard af Hadoop-klynge. Hive-forespørgsel til dette er som følger:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

I eksemplet nedenfor er skrevet output fra Hive forespørgsel til en mappe, blob `queryoutputdir` i objektbeholderen standard af Hadoop-klynge. Her skal du kun angive navnet på den mappe, uden blob-navn. En fejl, hvis du angiver både directory og blob navne, såsom `wasb:///queryoutputdir/queryoutput.txt`.

![Opret arbejdsområde](./media/machine-learning-data-science-move-hive-tables/output-hive-results-2.png)

Hvis du åbner objektbeholderen standard af den Hadoop klynge med Azure lagerplads Stifinder, kan du se output fra forespørgslen Hive, som vist i følgende figur. Du kan anvende filteret (fremhævet med rød boks) for at hente kun blob med angivne bogstaver i navne.

![Opret arbejdsområde](./media/machine-learning-data-science-move-hive-tables/output-hive-results-3.png)

###<a name="hive-editor"></a>2. sende Hive forespørgsler med Hive-Editor

Du kan også bruge konsollen forespørgsel (Hive Editor) ved at angive en URL-adresse i formularen *https://&#60; Hadoop klyngenavn >.azurehdinsight.net/Home/HiveEditor* i en webbrowser. Du skal være logget på se denne konsol og så du har brug for dine Hadoop klynge legitimationsoplysninger her.

###<a name="ps"></a>3. sende Hive forespørgsler med Azure PowerShell-kommandoer

Du kan også bruge PowerShell til at sende Hive forespørgsler. Flere oplysninger under [sende Hive job ved hjælp af PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


## <a name="create-tables"></a>Oprette Hive database og tabeller

Hive forespørgsler er delt i [Github lager](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) og kan hentes derfra.

Her er den Hive-forespørgsel, der opretter en Hive-tabel.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Her er en beskrivelse af de felter, du har brug for at tilslutte og andre konfigurationer:

- **& #60; databasenavn >**: navnet på den database, du vil oprette. Hvis du blot ønsker at bruge standarddatabasen, kan den forespørgsel, *oprette database...* udelades.
- **& #60; tabelnavn >**: navnet på den tabel, du vil oprette inden for den angivne database. Hvis du vil bruge standarddatabasen, tabellen kan være henvises direkte i *& #60; tabelnavn >* uden & #60; databasenavnet >.
- **& #60; feltseparator >**: den separator, der afgrænser felter i datafilen skal overføres til Hive-tabellen.
- **& #60; linjeseparator >**: den separator, der afgrænser linjer i datafilen.
- **& #60; lagerplacering >**: Azure lagerplaceringen til at gemme dataene Hive tabeller. Hvis du ikke angiver *placering & #60; lagerplacering >*, tabellerne og databasen er gemt i *hive/warehouse/* mappe i beholderen standard af Hive klynge som standard. Hvis du vil angive placeringen, skal skal lagerplaceringen være i objektbeholderen standard for databasen og tabeller. Denne placering skal være kaldes placering i forhold til objektbeholderen standard af klynge i formatet af *' wasb: / / / & #60; mappen 1 > /'* eller *' wasb: / / / & #60; mappen 1 > / & #60; mappen 2 > /'*osv. Når forespørgslen køres, oprettes de relative kataloger i objektbeholderen standard.
- **TBLPROPERTIES("SKIP.header.Line.Count"="1")**: Hvis datafilen indeholder en overskriftslinje, du skal tilføje denne egenskab **i slutningen** af forespørgslen *Opret tabel* . Ellers er linjen sidehoved indlæst som en post til tabellen. Hvis datafilen ikke har en overskriftslinje, kan denne konfiguration udelades i forespørgslen.

## <a name="load-data"></a>Indlæse data til Hive tabeller
Her er den Hive-forespørgsel, der indlæser data til en Hive-tabel.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **& #60; stien til blob-data >**: Hvis filen blob overføres til tabellen Hive i beholderen standard af klynge HDInsight Hadoop på *& #60; stien til blob-data >* skal være i formatet *' wasb: / / / & #60; directory i denne beholder > / & #60; blob filnavn >'*. Blob-filen kan også være i yderligere en objektbeholder af HDInsight Hadoop-klynge. I dette tilfælde *& #60; stien til blob-data >* skal være i formatet *' wasb: / / & #60; objektbeholder name>@&#60;storage kontonavn >.blob.core.windows.net/ & #60; blob filnavn >'*.

    >[AZURE.NOTE] Blob dataene, der skal overføres til Hive-tabellen skal være i standard- eller ekstra objektbeholder for lagerplads kontoen for Hadoop-klyngen. Ellers kan forespørgslen *INDLÆS DATA* ikke vist meddelelsen, det ikke kan få adgang til dataene.


## <a name="partition-orc"></a>Avancerede emner: opdelt tabel og store Hive-data i ORC format

Hvis dataene er stort, er partitionering tabellen nyttige til forespørgsler, der kun har brug for at scanne et par partitioner i tabellen. Det er for eksempel berettiget at partition dataene i logfilen for et websted ved datoer.

Ud over partitionering Hive tabeller, er det også være en fordel at gemme dataene Hive i formatet optimeret række kolonne (ORC). Finde flere oplysninger om formatering af ORC, <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">ved hjælp af ORC filer forbedrer ydeevnen, når Hive læse, skrive, og behandling af data</a>.

### <a name="partitioned-table"></a>Partitioneret tabel
Her er den Hive-forespørgsel, der opretter en partitioneret tabel og indlæser data i den.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Når forespørgsler partitioneret tabeller, det anbefales at tilføje betingelsen partition i **begyndelsen** af den `where` delsætningen som dette forbedrer effektiviteten af søgning betydeligt.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Store Hive data i ORC format

Du kan ikke direkte indlæser data fra blob-lager til Hive-tabeller, der er gemt i formatet ORC. Her er de trin, som du skal gennemføre for at indlæse data fra Azure BLOB Hive-tabeller, der er gemt i ORC format.

Oprette en ekstern tabel **GEMT AS TEXTFILE** og Indlæs data fra blob-lager til tabellen.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Oprette en intern tabel med det samme skema som eksterne tabellen i trin 1, med det samme feltafgrænser, og Gem Hive dataene i formatet ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Vælge data fra eksterne tabellen i trin 1 og indsætte i tabellen ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

>[AZURE.NOTE] Hvis tabellen TEXTFILE *& #60; databasenavn >. & #60; eksterne textfile tabelnavn >* har partitioner, i trin 3 skal den `SELECT * FROM <database name>.<external textfile table name>` kommandoen markerer variablen partition som et felt i den returnerede datasæt. Indsætter det i den *& #60; databasenavnet >. & #60; ORC tabelnavn >* mislykkes siden *& #60; databasenavnet >. & #60; ORC tabelnavn >* har ikke variablen partition som et felt i tabellen skemaet. I dette tilfælde skal du vælge de felter, der skal indsættes til specifikt *& #60; databasenavnet >. & #60; ORC tabelnavn >* på følgende måde:

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Det er sikkert at slippe den *& #60; eksterne textfile tabelnavn >* når med at bruge følgende forespørgsel efter alle data er blevet indsat i *& #60; databasenavn >. & #60; ORC tabelnavn >*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Når du har fulgt denne procedure, bør du have en tabel med data i formatet ORC klar til brug.  
