<properties
   pageTitle="Brug Beeline til at arbejde med Hive på HDInsight (Hadoop) | Microsoft Azure"
   description="Lær, hvordan du bruger SSH til at oprette forbindelse til et Hadoop-klynge i HDInsight, og Send derefter interaktivt Hive forespørgsler ved hjælp af Beeline. Beeline er et værktøj til at arbejde med HiveServer2 over JDBC."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/10/2016"
   ms.author="larryfr"/>

#<a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Bruge Hive med Hadoop i HDInsight med Beeline

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

I denne artikel lærer du at bruge Secure Shell (SSH) til at oprette forbindelse til en Linux-baserede HDInsight klynge, og Send derefter interaktivt Hive forespørgsler ved hjælp af værktøjet [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) kommandolinjen.

> [AZURE.NOTE] Beeline bruger JDBC til at oprette forbindelse til Hive. Du kan finde flere oplysninger om brug af JDBC med Hive, under [oprette forbindelse til Hive på Azure HDInsight bruger Hive JDBC driveren](hdinsight-connect-hive-jdbc-driver.md).

##<a id="prereq"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende:

* Et Linux-baserede Hadoop på HDInsight klynge.

* En SSH-klient. Linux, Unix og Mac OS skal leveres med en SSH-klient. Windows-brugere skal hente en klient, som [trykfarver](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Oprette forbindelse til SSH

Oprette forbindelse til det fulde domænenavn (fulde Domænenavn) af din HDInsight klynge ved hjælp af kommandoen SSH. Det fulde Domænenavn er det navn, du har givet klynge, derefter **. azurehdinsight.net**. For eksempel følgende vil oprette forbindelse til en klynge med navnet **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Hvis du har angivet en certifikatnøgle til SSH godkendelse** , da du oprettede HDInsight klynge, du muligvis angive placeringen af den private nøgle på dit klientsystem:

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Hvis du har angivet en adgangskode til godkendelse af SSH** , da du oprettede HDInsight klynge, skal du angive adgangskoden, når du bliver bedt om.

Finde flere oplysninger om brug af SSH med HDInsight, [Brug SSH med Linux-baserede Hadoop på HDInsight fra Linux, OS X, og Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>Trykfarver (Windows-baserede klienter)

Windows indeholder ikke en indbygget SSH-klient. Det anbefales at bruge **trykfarver**, som kan hentes fra [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Finde flere oplysninger om brug af trykfarver, [Brug SSH med Linux-baserede Hadoop på HDInsight fra Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="beeline"></a>Bruge kommandoen Beeline

1. Når forbindelse, kan du bruge følgende til at starte Beeline:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Dette starter Beeline klienten og oprette forbindelse til JDBC URL-adressen. Her `localhost` bruges, da HiveServer2 kører på begge hoved noder i klyngen, og vi kører Beeline direkte på det primære headnode.
    
    Når kommandoen er fuldført, kan du kommer til en `jdbc:hive2://localhost:10001/>` prompt.

3. Beeline kommandoer normalt starter med en `!` tegn, for eksempel `!help` viser hjælp. Men det `!` kan ofte udelades. For eksempel `help` fungerer også.

    Hvis du får vist Hjælp, vil du bemærke `!sql`, der bruges til at udføre HiveQL sætninger. Men HiveQL er så ofte anvendte, kan du udelade den foregående `!sql`. Følgende to sætninger har nøjagtigt de samme resultater; Viser de tabeller, der er tilgængelige via Hive:
    
        !sql show tables;
        show tables;
    
    På en ny klynge kun én tabel skal være angivet: __hivesampletable__.

4. Brug følgende fremgangsmåde til at vise skemaet for hivesampletable:

        describe hivesampletable;
        
    Dette vil returnere følgende oplysninger:
    
        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Dette viser kolonnerne i tabellen. Mens vi kunne udfører nogle forespørgsler disse data, oprette i stedet for et helt nyt tabel for at vise, hvordan du indlæser data i Hive og anvende et skema.
    
5. Angiv følgende udtalelser for at oprette en ny tabel med navnet **log4jLogs** ved hjælp af eksempeldata, der følger med HDInsight klynge:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Disse sætninger udføre følgende handlinger:

    * **DROP TABLE** - sletter tabellen og datafilen, i tilfælde af, at tabellen allerede findes.
    * **Opret eksterne tabel** - opretter en ny 'ekstern' tabel i Hive. Eksterne tabeller indeholder kun tabeldefinitionen i Hive. Data, der er tilbage i den oprindelige placering.
    * **Række FORMAT** – som fortæller Hive hvordan dataene skal formateres. I dette tilfælde er felterne i hver log adskilt af et mellemrum.
    * **GEMT AS TEXTFILE placering** - fortæller Hive hvor dataene er gemt (mappen eksempeldataene /), og at det er gemt som tekst.
    * **Vælg** - markerer en optælling af alle rækker, hvor kolonne **t4** indeholder værdien **[ERROR]**. Dette skal returnere en værdi på **3** , når der er tre rækker, der indeholder denne værdi.
    * **INPUT__FILE__NAME som '%.log'** - fortæller Hive, som vi bør kun returnerer data fra filer, der slutter i. log. Normalt, vil du kun har data med det samme skema i samme mappe når forespørgsler med hive, men denne eksempel logfil gemmes sammen med andre dataformater.

    > [AZURE.NOTE] Eksterne tabeller skal bruges, når du forventer, at de underliggende data opdateres ved en ekstern kilde, som et automatiseret data overførslen eller af en anden MapReduce handling, men vil altid Hive forespørgsler til at bruge de nyeste data.
    >
    > Slippe en ekstern tabel betyder **ikke** slette dataene, kun tabeldefinition.
    
    Output fra denne kommando skal ligne følgende:
    
        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :
        
        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)
        
        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

4. Hvis du vil afslutte Beeline, skal du bruge `!quit`.

##<a id="file"></a>Køre en HiveQL-fil

Beeline kan også bruges til at køre en fil, der indeholder HiveQL sætninger. Brug følgende trin til at oprette en fil og derefter køre den ved hjælp af Beeline.

1. Brug følgende kommando til at oprette en ny fil med navnet __query.hql__:

        nano query.hql
        
2. Når editoren åbnes, skal du bruge følgende som indholdet af filerne. Denne forespørgsel opretter en ny 'interne' tabel med navnet **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Disse sætninger udføre følgende handlinger:

    * **Oprette tabel Hvis ikke findes** - opretter en tabel, hvis den ikke allerede findes. Da nøgleordet **eksterne** ikke bruges, er dette en intern tabel, som er gemt i et Hive datalager og administreres helt af Hive.
    * **GEMT AS ORC** - gemmer dataene i optimeret række kolonne (ORC)-format. Dette er et meget optimerede og effektivt format til lagring af Hive data.
    * **Indsæt OVERSKRIV... Vælg** - markerer rækker fra tabellen **log4jLogs** , der indeholder **[ERROR]**og derefter indsætter dataene i tabellen **errorLogs** .
    
    > [AZURE.NOTE] I modsætning til eksterne tabeller slippe en intern tabel, slettes de underliggende data.
    
3. Brug __Ctrl__+ ___X__for at gemme filen, og derefter angive __j__og til sidst på __Enter__.

4. Du kan bruge følgende til at køre den fil, ved hjælp af Beeline. Erstatte __HOSTNAME__ med det navn, der fås tidligere for hoved node og __din adgangskode__ med adgangskode til administratorkonto:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql

    > [AZURE.NOTE] Den `-i` parameter starter Beeline, kører sætningerne i filen query.hql og forbliver i Beeline på den `jdbc:hive2://localhost:10001/>` prompt. Du kan også køre en fil ved hjælp af den `-f` parameter, der returnerer til fest, når filen er blevet behandlet.

5. For at bekræfte, at tabellen **errorLogs** blev oprettet, skal du bruge følgende sætning til at returnere alle rækkerne fra **errorLogs**:

        SELECT * from errorLogs;

    Tre rækker af data skal returneres, alle med **[ERROR]** i kolonnen t4:
    
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>Flere oplysninger om Beeline forbindelse

Trinnene i dette dokument bruge `localhost` at oprette forbindelse til HiveServer2 kører på klynge headnode. Mens du kan også bruge værtsnavnet eller det fulde domænenavn for headnode dem, der kræver yderligere trin for at processen (trin for at finde den hostname eller det fulde Domænenavn). Ved hjælp af `localhost` er tilstrækkeligt, når du bruger Beeline fra headnode.

Hvis du har en kantnode i din klynge med Beeline installeret, skal du bruge hostname eller fulde for headnode til at oprette forbindelse.

Hvis du har installeret på en klient uden for din klynge Beeline, kan du oprette forbindelse ved hjælp af følgende kommando. Erstat __CLUSTERNAME__ med navnet på din HDInsight klynge. Erstat __adgangskode__ med adgangskoden for kontoen Administrator (HTTP login).

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

Bemærk, at parametrene/URI er anderledes end når du kører direkte på et headnode eller fra en kantnode inden for klyngen. Dette skyldes, at oprette forbindelse til klyngen fra internettet bruger en offentlig gateway, der dirigerer trafik via port 443. Desuden er flere andre tjenester fremvises offentlige gatewayen på port 443, så URI er anderledes end når direkte forbindelse. Når du opretter forbindelse fra internettet skal du også godkendelse af session ved at angive adgangskoden.

##<a id="summary"></a><a id="nextsteps"></a>Næste trin

Som du kan se, indeholder kommandoen Beeline en nem måde at køre interaktivt Hive forespørgsler på en HDInsight klynge.

Generelle oplysninger om Hive i HDInsight:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)

* [Bruge MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md)

Hvis du bruger Tez med Hive, skal du se følgende dokumenter til fejlfindingsoplysninger:

* [Brug Tez Brugergrænsefladen på Windows-baseret HDInsight](hdinsight-debug-tez-ui.md)

* [Brug af Ambari Tez visningen på Linux-baserede HDInsight](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

