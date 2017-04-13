<properties
   pageTitle="Optimere dine Hive forespørgsler hurtigere udførelse i HDInsight | Microsoft Azure"
   description="Lær at optimere dine Hive forespørgsler til Hadoop i HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/28/2015"
   ms.author="rashimg"/>


# <a name="optimize-hive-queries-for-hadoop-in-hdinsight"></a>Optimere Hive forespørgsler til Hadoop i HDInsight

Hadoop klynger er som standard ikke optimeret til ydeevne. I denne artikel beskrives nogle af de mest almindelige Hive ydeevne optimering-metoder, du kan anvende til vores forespørgsler.

##<a name="scale-out-worker-nodes"></a>Skalere ud arbejder noder

Øge antallet af knuder arbejder i en klynge kan udnytte flere mappers og reducers skal køres parallelt. Der er to måder, du kan øge Skaler ud i HDInsight:

- På klargøring tid, kan du angive antallet arbejder noder ved hjælp af Azure-portalen, Azure PowerShell eller på tværs af platforme kommandogrænseflade linje.  Du kan finde yderligere oplysninger finder [klargøring HDInsight klynger](hdinsight-provision-clusters.md). Følgende skærmbillede viser arbejderen node konfiguration på Azure-portalen:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]

- På kørselstidspunktet, kan du også skalere ud af en klynge uden at genoprette en. Dette er vist nedenfor.
![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Se [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/)kan finde flere oplysninger om de forskellige virtuelle maskiner, der understøttes af HDInsight.

##<a name="enable-tez"></a>Aktivere Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) er en alternativ udførelse af program til MapReduce program:

![tez_1][image-hdi-optimize-hive-tez_1]


Tez er hurtigere, fordi:

- Udføre ført acykliske Graph (DAG) som en enkelt sag i MapReduce-program, den DAG, der angives kræver, at hvert sæt af mappers der skal følges ved et sæt af reducers. Derved flere MapReduce opgaver, der skal være spundet for hver Hive-forespørgsel. Tez har ikke denne begrænsning og kan behandle komplekse DAG som ét job derved minimere job Start omkostninger.
- **Avoids unødvendige skriver** På grund af flere job der af af til den samme Hive forespørgsel i MapReduce engine, skrives output for hver sag til HDFS til mellemliggende data. Da Tez minimerer antallet af sager for hver Hive-forespørgsel, den er i stand til at undgå unødvendig skrivning.
- **Minimizes opstart forsinkelser** Tez er bedre kan du minimere opstart forsinkelse ved at reducere antallet af mappers skal starte og forbedre også optimering i hele.
- **Genbruger objektbeholdere** Når mulige Tez kan genbruge beholdere for at sikre, at ventetid på grund af opstart objektbeholdere er reduceret.
- **Kontinuert optimering teknikker** Optimering skete traditionelt i kompilering fase. Men der findes flere oplysninger om input, der tillader for bedre optimering under kørslen. Tez bruger uafbrudt optimering teknikker, der gør det muligt at optimere plan yderligere i fasen runtime.

Flere oplysninger om disse begreber, ved at klikke [her](http://hortonworks.com/hadoop/tez/)

Du kan foretage en Hive forespørgsel Tez aktiveret af præfikser forespørgslen med indstillingen nedenfor:

    set hive.execution.engine=tez;

Til Windows-baseret HDInsight klynger skal være aktiveret Tez klargøring samtidig. Følgende er et eksempel Azure PowerShell-script til klargøring af en Hadoop-klynge med Tez aktiveret:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $clusterName = "[HDInsightClusterName]"
    $location = "[AzureDataCenter]" #i.e. West US
    $dataNodes = 32 # number of worker nodes in the cluster

    $defaultStorageAccountName = "[DefaultStorageAccountName]"
    $defaultStorageContainerName = "[DefaultBlobContainerName]"
    $defaultStorageAccountKey = $defaultStorageAccountKey = Get-AzureStorageKey $defaultStorageAccountName.ToLower() | %{ $_.Primary }

    $hdiUserName = "[HTTPUserName]"
    $hdiPassword = "[HTTPUserPassword]"

    $hdiSecurePassword = ConvertTo-SecureString $hdiPassword -AsPlainText -Force
    $hdiCredential = New-Object System.Management.Automation.PSCredential($hdiUserName, $hdiSecurePassword)

    $hiveConfig = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
    $hiveConfig.Configuration = @{ "hive.execution.engine"="tez" }

    New-AzureHDInsightClusterConfig -ClusterSizeInNodes $dataNodes -HeadNodeVMSize Standard_D14 -DataNodeVMSize Standard_D14 |
    Set-AzureHDInsightDefaultStorage -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" -StorageAccountKey $defaultStorageAccountKey -StorageContainerName $defaultStorageContainerName |
    Add-AzureHDInsightConfigValues -Hive $hiveConfig |
    New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $hdiCredential

    
> [AZURE.NOTE] Linux-baserede HDInsight klynger har Tez som standard aktiveret.
    

## <a name="hive-partitioning"></a>Hive partitionering

I/o-handling er den overordnede ydeevne flaskehals kører Hive forespørgsler. Ydeevnen kan forbedres, hvis mængden data, der skal læses kan blive reduceret. Som standard scanne Hive forespørgsler hele Hive tabeller. Dette er velegnet til forespørgsler, som tabel scanner, men til forespørgsler, der kun har brug for at scanne et lille mængde data (fx forespørgsler med filtrering), dette opretter unødvendige omkostninger. Hive partitionering giver mulighed for Hive forespørgsler til at få adgang til den nødvendige mængde data i Hive tabeller.

Hive partitionering er implementeret af omorganisere den rækkedata til nye mapper med hver partition har sin egen directory - hvor partitionen er defineret af brugeren. I følgende diagram vises partitionering en Hive-tabellen efter kolonnen *Year*. Der oprettes en ny mappe for hvert år.

![partitionering][image-hdi-optimize-hive-partitioning_1]

Nogle leverandør overvejelser:

- Du **kan ikke under partition** - partitionering på kolonner med kun nogle få værdier, kan det medføre meget få partitioner. For eksempel kan partitionering på køn kun oprette to partitioner, der skal oprettes (mand og kvinde), og som derfor kun reducere ventetid med højst halvdelen.

- **Ikke uberettiget partition** - medfører på de andre særligt, oprette en partition på en kolonne med en entydig værdi (fx bruger-id) flere partitioner, der forårsager en masse belastning på klynge namenode, som den nødt til at håndtere en stor mængde kataloger.

- **Undgå data vride** - vælge din leverandør nøgle med omhu, så alle partitioner er lige størrelse. Et eksempel partitionering på *tilstand* kan medføre antallet af poster under California skal være næsten 30 x, Vermont på grund af forskellen i populationen.

Brug *Opdelt By* -delsætningen til at oprette en partitionstabel:

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE        STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Når tabellen partitioneret er oprettet, kan du enten oprette statisk partitionering eller dynamisk partitionering.

- **Statisk partitionering** betyder, at du har allerede delt data i de relevante mapper, og du kan stille Hive partitioner manuelt baseret på mappeplaceringen. Dette er vist i nedenstående kodestykket.

        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’

        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasbs://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'

- **Dynamisk partitionering** betyder, at du vil Hive til at oprette partitioner automatisk for dig. Da vi allerede har oprettet tabellen leverandør fra den midlertidige tabel, er vi skal blot indsætte data til tabellen partitioneret, som vist nedenfor:

        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
             L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as       L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as       L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as   L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Du kan finde flere detaljer, [Opdelt tabeller](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

##<a name="use-the-orcfile-format"></a>Brug formatet ORCFile

Hive understøtter forskellige filformater. Eksempel:

- **Tekst**: Dette er standardfilformatet og arbejder sammen med de fleste scenarier
- **Avro**: fungerer godt til interoperabilitet scenarier
- **ORC/Parquet**: bedst egnet til ydeevne

ORC (optimeret række kolonner)-formatet er en meget effektiv metode til at lagre Hive data. Sammenlignet med andre formater, har ORC følgende fordele:

- understøttelse af komplekse typer, herunder dato og klokkeslæt og typer af komplekse og semistrukturerede
- op til 70% komprimering
- indekserer hver 10.000 rækker, som gør det muligt at springe over rækker
- en betydeligt slip i udførelse af Runtime-

For at aktivere ORC format skal oprette du først en tabel med delsætningen *lagrede som ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT     STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Derefter skal du indsætte data til tabellen ORC fra den midlertidige tabel. Eksempel:

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
           L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

Du kan læse mere om formatet ORC [her](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

##<a name="vectorization"></a>Vectorization

Vectorization kan Hive til at behandle en mængde 1024 rækker sammen, i stedet for behandling af én række ad gangen. Det betyder, enkle foregår hurtigere fordi mindre interne kode, der kræves for at køre.

Hvis du vil aktivere præfiks vectorization forespørgslen Hive med de følgende indstillinger:

    set hive.vectorized.execution.enabled = true;

Du kan finde yderligere oplysninger finder [Vectorized udførelse af forespørgsel](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).


##<a name="other-optimization-methods"></a>Andre metoder til optimering

Der findes flere optimering metoder, du kan overveje, f.eks.:

- **Hive bucketing:** en teknik, der gør det til klynge eller segment skal tildeles store datasæt til at optimere forespørgselsydelse.
- **Deltage i optimering:** optimering af hives forespørgsel udførelse af planlægning til at forbedre effektiviteten af joinforbindelser og nedsætte behovet for bruger tip. Yderligere oplysninger finder du se [deltage i optimering](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
- **øge Reducers**

##<a id="nextsteps"></a>Næste trin
I denne artikel, har du lært flere almindelige Hive forespørgsel optimering metoder. Hvis du vil vide mere, skal du se følgende artikler:

- [Brug Apache Hive i HDInsight](hdinsight-use-hive.md)
- [Analysere flight forsinkelse data ved hjælp af Hive i HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analysere Twitter data ved hjælp af Hive i HDInsight](hdinsight-analyze-twitter-data.md)
- [Analysere føler data ved hjælp af konsollen Hive forespørgsel på Hadoop i HDInsight](hdinsight-hive-analyze-sensor-data.md)
- [Bruge Hive med HDInsight til at analysere logfiler fra websteder](hdinsight-hive-analyze-website-log.md)


[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
