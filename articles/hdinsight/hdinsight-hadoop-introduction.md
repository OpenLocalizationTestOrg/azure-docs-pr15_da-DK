 <properties
    pageTitle="Introduktion til Hadoop - Hvad er Hadoop på HDInsight? | Microsoft Azure"
    description="Få en introduktion til Hadoop, fordelt stor databehandling og analyse og komponenterne i Hadoop økosystemet i skyen på HDInsight."
    keywords="stor dataanalyse, Introduktion til hadoop, hvad er hadoop, hadoop-teknologi stak, hadoop økosystemet"
    services="hdinsight"
    documentationCenter=""
    authors="cjgronlund"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="cgronlun"/>


# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>En introduktion til Hadoop økosystemet på Azure HDInsight

 Denne artikel indeholder en introduktion til Hadoop på Azure HDInsight, dens økosystemet og stor data. Få mere at vide om Hadoop-komponenter, fælles terminologi og scenarier, hvor stor dataanalyse.

## <a name="what-is-hadoop-on-hdinsight"></a>Hvad er Hadoop på HDInsight?

Hadoop refererer til et netværk af open source-software, der er en ramme for fordelt behandling, lagring og analyse af store datasæt på klynger af computere. Azure HDInsight gør Hadoop-komponenter fra **Hortonworks Data Platform (HDP)** fordelingen tilgængelig i skyen, og installerer og bestemmelser administrerede klynger med høj pålidelighed og tilgængelighed.  

Apache Hadoop blev oprindelige open source-projektet til stor databehandling. Følgende blev udvikling af relaterede programmer og funktioner, der betragtes som en del af Hadoop-teknologi stak, herunder Apache Hive, Apache HBase, Apache knallertmotor og mange andre. Du kan finde oplysninger i [Oversigt over Hadoop økosystemet i HDInsight](#overview) .

## <a name="what-is-big-data"></a>Hvad er stor data?

Stor data beskrives en stor brødteksten i digitale oplysninger fra teksten i en Twitter feed til føler oplysningerne fra industrielle udstyr til oplysninger om kunde-browsing og køb på et websted. Big data kan være historiske (dvs lagrede data) eller realtid (dvs. streames direkte fra kilden). Stor data indsamles i nogensinde Eskalering enheder på stigende højere hastighed og i en udvide forskellige formater.

Stor data til at levere effektive intelligence eller indsigt, skal du indsamle relevante data og Spørg højre. Du skal også kontrollere data, der er tilgængelige, ryddes, analysere og derefter præsenteres på en nyttig måde. Det er, hvor stor dataanalyse på Hadoop i HDInsight kan hjælpe.

## <a name="overview"></a>Oversigt over Hadoop økosystemet i HDInsight

HDInsight er en skybaseret fordeling på Microsoft Azure af hurtigt voksende Apache Hadoop teknologi stablen for stor dataanalyse. Den indeholder installationer Apache gnister, HBase, Storm, gris, Hive, Sqoop, Oozie, Ambari og så videre. HDInsight integrerer også med business intelligence (BI)-værktøjer som Power BI, Excel, SQL Server Analysis Services og SQL Server Reporting Services.

### <a name="hadoop-hbase-spark-storm-and-customized-clusters"></a>Hadoop, HBase, gnister, Storm og tilpasset klynger

HDInsight indeholder klyngekonfigurationer til Apache Hadoop, gnister, HBase eller Storm. Eller du kan [tilpasse klynger med scripthandlinger](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop**: giver pålidelig datalagring [HDFS](#hdfs)og en simpel [MapReduce](#mapreduce) programming model til at behandle og analysere data parallelt.

* **<a target="_blank" href="http://spark.apache.org/">Apache knallertmotor</a>**: en parallel behandling ramme, der understøtter i hukommelsen behandling for at øge ydeevnen for stor dataanalyse programmer, sætte gang fungerer for SQL, streaming data, og machine learning. Se [Oversigt: Hvad er Apache knallertmotor i HDInsight?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**: en NoSQL database, der er bygget på Hadoop, der indeholder RAM og stærke konsistens til store mængder ustrukturerede og semistrukturerede data - potentielt milliarder af rækker gange millioner af kolonner. Se [Oversigt over HBase på HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>**: et fordelt, realtid beregning system til at behandle store streams data hurtigt. Storm tilbydes som en administreret klynge i HDInsight. Se [analysér realtid føler data ved hjælp af Storm og Hadoop](hdinsight-storm-sensor-data-analysis.md).

### <a name="example-customization-scripts"></a>Eksempel tilpasning scripts

Scripthandlinger er scripts, der kører under klynge klargøringen og kan bruges til at installere yderligere komponenter på klyngen. Dette er fest scripts til Linux-baserede klynger.

Følgende eksempel scripts der leveres af HDInsight-gruppen:

* [Farvetone](hdinsight-hadoop-hue-linux.md): et sæt af webprogrammer, der bruges til at interagere med en klynge. Kun Linux klynger.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): Graph behandling til model relationer mellem ting eller personer.

* [R](hdinsight-hadoop-r-scripts-linux.md): et open source-sprog og miljø til statistiske computing bruges i machine learning.

* [Solr](hdinsight-hadoop-solr-install-linux.md): en enterprise-skala Søg platform, som gør det muligt for søgning i hele teksten på data.

Du kan finde oplysninger om udvikling af dit eget scripthandlinger [scripthandling udvikling med HDInsight](hdinsight-hadoop-script-actions-linux.md).


## <a name="what-are-the-hadoop-components-and-utilities"></a>Hvad er de Hadoop-komponenter og funktioner?

Følgende komponenter og funktioner er inkluderet på HDInsight klynger.

* **[Ambari](#ambari)**: klynge klargøring, administration, overvågning og værktøjer.

* **[Avro](#avro)** (Microsoft .NET bibliotek til Avro): dataserialisering for Microsoft .NET-miljø.

* **[Hive & HCatalog](#hive)**: SQL Structured Query Language ()-som forespørgsler og en tabel og lager administration af lag.

* **[Mahout](#mahout)**: For SVG maskine læ programmer.

* **[MapReduce](#mapreduce)**: ældre ramme for Hadoop-distribueret behandling-og ressourceadministration. Se [GARN](#yarn), den næste-generering af ressource, som.

* **[Oozie](#oozie)**: Administration af arbejdsproces.

* **[København](#phoenix)**: relationsdatabase lag over HBase.

* **[Gris](#pig)**: enklere scripting til MapReduce transformationer.

* **[Sqoop](#sqoop)**: Data import og eksport.

* **[Tez](#tez)**: gør det muligt for data-intensivt processer til at køre effektivt skaleres til brug.

* **[GARN](#yarn)**: en del af Hadoop core bibliotek og næste oprettelse af MapReduce software framework.

* **[ZooKeeper](#zookeeper)**: køre koordineret af processer i distribuerede systemer.

> [AZURE.NOTE] Oplysninger om bestemte komponenter og versionsoplysninger, se [Hadoop-komponenter, versionsstyring, og -servicetilbud i HDInsight][component-versioning]

### <a name="ambari"></a>Ambari

Apache Ambari gælder for klargøring, administrere og overvågning Apache Hadoop klynger. Den indeholder en intuitiv samling operator værktøjer og et solidt sæt API'er, skjuler Hadoop, forenkle driften af klynger kompleksitet. Linux-baserede HDInsight klynger yde på Ambari på internettet Brugergrænsefladen og Ambari REST-API, mens Windows-baserede klynger giver et undersæt af REST-API. Ambari visninger på HDInsight klynger tillade plug-in'en brugergrænseflade-funktioner.

Se [administrere HDInsight klynger ved hjælp af Ambari](hdinsight-hadoop-manage-ambari.md) (kun Linux), [overvåge Hadoop klynger i HDInsight ved hjælp af Ambari API](hdinsight-monitor-use-ambari-api.md)og <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API reference</a>.

### <a name="avro"></a>Avro (Microsoft .NET bibliotek til Avro)

Microsoft .NET-bibliotek til Avro implementerer Apache Avro kompakt binære data interchange format for serialisering for Microsoft .NET-miljø. <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> bruges til at definere et sprog agnostic skema, der dækker sprog interoperabilitet, betydning data serialiseres i ét sprog kan læses i en anden. Detaljerede oplysninger om formatet kan findes i den < en destination = _ "tom" linkreference = "http://avro.apache.org/docs/current/spec.html" > Apache Avro specifikation</a>.
Formatet af Avro filer understøtter fordelt MapReduce programming modellen. Filer er "kan opdeles", hvilket betyder, at du kan søge efter et vilkårligt sted i en fil og begynde at læse fra en bestemt tekstblok. For at finde ud af, hvordan skal du se [serialiseret data med Microsoft .NET-bibliotek til Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

Hadoop-distribueret fil System (HDFS) er et distribueret filsystem, som er kernen i Hadoop økosystemet med MapReduce og GARN. HDFS er standard filsystemet for Hadoop klynger på HDInsight.

### <a name="hive"></a>Hive & HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> er data warehouse software bygget på Hadoop, hvor du kan køre forespørgsler og administrere store datasæt i fordelt lagerplads ved hjælp af en SQL-lignende sprog kaldet HiveQL. Hive som gris, er en fremstilling oven på MapReduce. Når kører, oversætter Hive forespørgsler til en række MapReduce job. Hive er objekter tættere på en relationsdatabase administrationssystem end gris og bør derfor til brug sammen med mere strukturerede data. Til ustrukturerede data er gris det bedste valg. Se [bruge Hive med Hadoop i HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> er en tabel og lager administration af lag til Hadoop, der giver brugere med en relationel visning af data. I HCatalog, kan du læse og skrive filer i et format, kan der skrives en Hive SerDe (serialiseringsfunktion deserialisering).

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> er et SVG machine learning-algoritmer, der kører på Hadoop-bibliotek. Ved hjælp af principper for statistik machine learning programmer for at lære systemer, du kan få mere at vide fra data og bruge tidligere resultater til at bestemme fremtidige funktionsmåde. Se [Generer film anbefalinger ved hjælp af Mahout på Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce er en ældre software ramme for Hadoop til at skrive programmer til batchen proces store datasæt parallelt. Et MapReduce job opdeles store datasæt og organiserer data i nøgle-værdi-par for behandling.

[GARN](#yarn) er Hadoop næste-generering af ressource manager og programmet struktur, og der refereres til som MapReduce 2.0. MapReduce job køres på GARN.

Du kan finde flere oplysninger om MapReduce, <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> i Hadoop Wiki.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> er et arbejdsprocessen kører koordineret system, der administrerer Hadoop-job. Det er integreret med Hadoop stablen og understøtter Hadoop-job til MapReduce, gris, Hive og Sqoop. Det kan også bruges til at planlægge job, der er specifikke for et system, som Java-programmer eller shell-scripts. Se [Brug Oozie med Hadoop](hdinsight-use-oozie.md).

### <a name="phoenix"></a>København
<a  target="_blank" href="http://phoenix.apache.org/">Apache København</a> er et relationsdatabase lag over HBase. København omfatter en JDBC driver, så brugerne kan køre forespørgsler og administrere SQL tabeller direkte. København oversætter forespørgsler og andre sætninger til oprindelig NoSQL API-kald - i stedet for brug af MapReduce – så hurtigere programmer oven på NoSQL butikker. Se [Brug Apache København og SQuirreL med HBase klynger](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Gris
<a  target="_blank" href="http://pig.apache.org/">Apache gris</a> er en overordnet platform, som gør det muligt at udføre kompleks MapReduce transformationer på meget store datasæt ved hjælp af en simpel scriptsproget kaldet gris latinsk. Gris oversætter gris latinsk scripts, så de får kører i Hadoop. Du kan oprette brugerdefinerede funktioner til at udvide gris latinsk. Se [Brug gris med Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> er værktøj, overførsler flere data mellem Hadoop og relationsdatabaser sådanne SQL eller andre strukturerede data butikker mest effektive måde. Se [Brug Sqoop med Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> er et program struktur, der er bygget på Hadoop GARN, der udfører komplekse, acykliske grafer for generelle databehandling. Det er en mere fleksibel og effektiv efterfølgende til MapReduce framework, hvor data-intensivt processer, som Hive til at køre mere effektivt skaleres til brug. Se ["Brug Apache Tez til forbedret ydeevne" i brug Hive og HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>GARN
Apache GARN er næste oprettelse af MapReduce (MapReduce 2.0 eller MRv2) og understøtter databehandling scenarier ud over MapReduce batchbehandling med større skalerbarhed og realtid behandling. GARN indeholder ressourcestyring og en distribueret program ramme. MapReduce job køres på GARN.

Hvis du vil vide mere om GARN, se <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (GARN)</a>.


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> koordinater processer i store distribuerede systemer ved hjælp af en delt hierarkisk struktur af data registre (znodes). Znodes indeholder lidt af metakoden oplysninger bruges til at koordinere processer: status, placering, konfiguration og så videre.

## <a name="programming-languages-on-hdinsight"></a>Programmering sprog på HDInsight

HDInsight klynger – Hadoop, HBase, Storm og knallertmotor klynger – understøtter et antal programmeringssprog, men nogle installeres ikke som standard. Biblioteker, moduler eller ikke installeret som standard-pakker, skal du bruge handlingen script for at installere komponenten. Se [Script handling udvikling med HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="default-programming-language-support"></a>Standard programming understøttelse af sprog

Som standard klynger HDInsight support:

* Java

* Python

Flere sprog kan installeres ved hjælp af scripthandlinger: [Script handling udvikling med HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Java virtuelt (JVM) sprog

Mange sprog end Java kan køres ved hjælp af en Java virtual machine (JVM) kørsel af nogle af disse sprog kan dog kræve yderligere komponenter, der er installeret på klyngen.

Disse JVM-baserede sprog understøttes på HDInsight klynger:

* Clojure

* Jython (Python til Java)

* Scala

### <a name="hadoop-specific-languages"></a>Hadoop-specifikke sprog

HDInsight klynger understøtter følgende sprog, der vedrører Hadoop økosystemet:

* Gris latinsk for gris sager

* HiveQL for Hive job og SparkSQL


## <a name="advantage"></a>Fordelene ved Hadoop i skyen

Hadoop i HDInsight tilbyder en række fordele mellem dem som en del af Azure skyen økosystemet:

* Automatisk klargøring af Hadoop klynger. HDInsight klynger er meget nemmere at oprette end manuel konfiguration af Hadoop klynger. Yderligere oplysninger finder du [klargøring Hadoop klynger i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* De avancerede Hadoop-komponenter. Yderligere oplysninger finder du [Hadoop komponenter, versionsstyring, og -servicetilbud i HDInsight][component-versioning].

* Høj tilgængelighed og pålidelighed af klynger. Du kan se [tilgængelighed og pålidelighed af Hadoop klynger i HDInsight](hdinsight-high-availability-linux.md) få mere at vide.

* Effektive og økonomisk datalagring med Azure Blob-lager, en indstilling for Hadoop-kompatible. Du kan finde oplysninger i [Brug Azure Blob-lager med Hadoop i HDInsight](hdinsight-hadoop-use-blob-storage.md) .

* Integration med andre Azure tjenesterne, herunder [webapps](https://azure.microsoft.com/documentation/services/app-service/web/) og [SQL-Database](https://azure.microsoft.com/documentation/services/sql-database/).

* Yderligere VM størrelser og typer til kørsel af HDInsight klynger. Se [Hadoop-komponenter, versionsstyring, og -servicetilbud i HDInsight] [ component-versioning] få mere at vide.

* Klynge skalering. Klynge skalering gør det muligt at ændre antallet af knuder af en igangværende HDInsight klynge uden at slette eller gendanne den.

* Virtuel understøttelse af netværk. HDInsight klynger kan bruges med Azure virtuelt netværk til at understøtte isolationsniveauet skyen ressourcer eller hybrid scenarier, der linker skyen ressourcer med dem i dit datacenter.

* Lav angivelse af omkostninger. Starte en [gratis prøveversion](https://azure.microsoft.com/free/), eller kontakt [HDInsight prisoplysninger](https://azure.microsoft.com/pricing/details/hdinsight/).

Til at få mere at vide om fordelene ved på Hadoop i HDInsight, se [siden Azure funktioner til HDInsight][marketing-page].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard- og HDInsight Premium

HDInsight indeholder stor data skyen tilbud i to kategorier, Standard og Premium. HDInsight Standard indeholder en enterprise-skala klynge, som organisationer kan bruge til at køre deres arbejdsbelastninger, som stor data. HDInsight Premium bygger på, og leverer avancerede analytical og sikkerhedsfunktioner for en HDInsight klynge. Du kan finde flere oplysninger, se [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)


## <a id="resources"></a>Ressourcer til at lære mere om stor dataanalyse, Hadoop og HDInsight

Udbygges med denne introduktion til Hadoop i skyen og stor dataanalyse med nedenstående ressourcer.


### <a name="hadoop-documentation-for-hdinsight"></a>Hadoop dokumentationen til HDInsight

* [HDInsight dokumentation](https://azure.microsoft.com/documentation/services/hdinsight/): siden dokumentation for Hadoop på Azure HDInsight med links til artikler, videoer og flere ressourcer.

* [Komme i gang med Hadoop i HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): en hurtig start selvstudium klargøring HDInsight Hadoop klynger og køre eksempel Hive forespørgsler.

* [Komme i gang med knallertmotor i HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): en hurtig start selvstudium for at oprette en knallertmotor klynge og interaktive knallertmotor SQL-forespørgsler, der kører.

* [Brug R Server på HDInsight](hdinsight-hadoop-r-server-get-started.md): begynde at bruge R Server i HDInsight Premium.

* [Klargøring HDInsight klynger](hdinsight-hadoop-provision-linux-clusters.md): Lær, hvordan du klargør en HDInsight Hadoop klynge via Azure-portalen, Azure CLI eller Azure PowerShell.


### <a name="apache-hadoop"></a>Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: få flere oplysninger om Apache Hadoop software bibliotek, en ramme, som muliggør fordelt behandling af store datasæt på tværs af klynger af computere.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: få flere oplysninger om arkitektur og designet af det Hadoop-distribueret filsystem, det primære lagerplads system, der bruges af Hadoop-programmer.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce selvstudium</a>: Få mere at vide mere om programmering framework til at skrive Hadoop-programmer, der behandler hurtigt store mængder data parallelt på stor klynger af Beregn noder.


### <a name="microsoft-business-intelligence"></a>Microsoft business intelligence

Velkendte business intelligence (BI)-værktøjer - som Excel, PowerPivot, SQL Server Analysis Services og SQL Server Reporting Services - hente, analysere og rapportere data, der er integreret med HDInsight ved hjælp af enten tilføjelsesprogrammet Power-forespørgsel eller Microsoft Hive ODBC-Driver.

Disse BI-værktøjer kan hjælpe med at din stor dataanalyse:

* [Forbinder du Excel til Hadoop med Power-forespørgsel](hdinsight-connect-excel-power-query.md): Lær, hvordan du knytter Excel til kontoen Azure-lager, der indeholder de data, der er knyttet til din HDInsight klynge ved hjælp af Microsoft Power-forespørgsel til Excel. Windows-arbejdsstation påkrævet. Fungerer med Windows - eller Linux-baserede klynge.

* [Forbinder du Excel til Hadoop med Microsoft Hive ODBC-Driver](hdinsight-connect-excel-hive-odbc-driver.md): Lær, hvordan du importerer data fra HDInsight med Microsoft Hive ODBC-Driver. Windows-arbejdsstation påkrævet. Fungerer med Windows - eller Linux-baserede klynge.

* [Microsoft Cloud-platformen](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): Få mere at vide om Power BI til Office 365, hente prøveversionen af SQL Server og konfiguration af SharePoint Server 2013 og SQL Server BI.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).




[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
