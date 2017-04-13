<properties
    pageTitle="Hukommelse fejl (OOM) - Hive indstillinger | Microsoft Azure"
    description="Rette en forældet hukommelsesfejl (OOM) fra en Hive-forespørgsel i Hadoop i HDInsight. Kunde-scenarie er en forespørgsel på tværs af mange store tabeller."
    keywords="af hukommelse fejl, OOM, Hive indstillinger"
    services="hdinsight"
    documentationCenter=""
    authors="rashimg"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/02/2016"
    ms.author="rashimg;jgao"/>

# <a name="fix-an-out-of-memory-oom-error-with-hive-memory-settings-in-hadoop-in-azure-hdinsight"></a>Rette fejlen ud af hukommelse (OOM) med Hive hukommelsesindstillinger i Hadoop i Azure HDInsight

En af de almindelige problemer ansigt vores kunder får fejlen ud af hukommelse (OOM) når ved hjælp af Hive. I denne artikel beskrives et scenarie med kunde og indstillingerne Hive, anbefales det for at løse problemet.

## <a name="scenario-hive-query-across-large-tables"></a>Scenarie: Hive forespørgsel på tværs af store tabeller

En kunde kørte forespørgslen under brug af Hive.

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

Nogle små forskelle af denne forespørgsel:

* T1 er et alias til en stor tabel, Tabel1, som har mange streng kolonnetyper.
* Andre tabeller er ikke, store, men har et stort antal kolonner.
* Alle tabeller kommer hinanden, i nogle tilfælde med flere kolonner på Tabel1 og andre.

Når kunden har kørt den forespørgsel, der bruger Hive på MapReduce på en 24 node A3 klynge, kørte forespørgslen i ca. 26 minutter. Kunden bemærket følgende advarsler, når den kører forespørgslen ved hjælp af Hive på MapReduce:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Da forespørgslen blevet udført i ca. 26 minutter, kunden ignoreres disse advarsler og i gang i stedet for at fokusere på hvordan du kan forbedre denne forespørgslens ydeevne yderligere.

Kunden hørt [optimere Hive forespørgsler til Hadoop i HDInsight](hdinsight-hadoop-optimize-hive-query.md)og besluttet at bruge Tez udførelse af program. Når den samme forespørgsel er kørt med indstillingen Tez aktiveret forespørgslen kørte til 15 minutter, og derefter udløste følgende fejl:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

Kunden derefter besluttede dig for at bruge en større VM (det vil sige D12) tænker en større VM ville have mere plads til heap. Kunden fortsat selv derefter at se fejlen. Kunden nået til HDInsight gruppen for at få hjælp i forbindelse med fejlfinding af problemet.

## <a name="debug-the-out-of-memory-oom-error"></a>Fejlfinding af fejlen ud af hukommelse (OOM)

Vores support og tekniske teams sammen findes et af de problemer, der forårsager fejlen ud af hukommelse (OOM) er et [kendt problem, der beskrives i Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306). Fra beskrivelsen i JIRA:

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

Vi bekræftet pågældende **hive.auto.convert.join.noconditionaltask** faktisk er indstillet til **Sand** ved at se under hive-site.xml filer:

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
    </property>

Baseret på advarslen og JIRA, blev vores hypotetisk kort deltage er årsagen til Java Heap mellemrum OOM fejl. Så vi gravede dybere til dette problem.

Som beskrevet i blogindlægget [Hadoop garn hukommelsesindstillinger i HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), hører hvornår Tez udførelse af engine er anvendt på heap meget plads der bruges faktisk til objektbeholderen Tez. Se billedet nedenfor, der beskriver Tez objektbeholder hukommelse.

![Tez objektbeholder hukommelse diagram: Hive af hukommelsesfejl OOM](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)


Som blogindlægget antyder, hukommelsesindstillingerne for følgende to definere objektbeholder hukommelse til heap'en: **hive.tez.container.size** og **hive.tez.java.opts**. OOM undtagelse betyder fra vores erfaring ikke objektbeholder størrelse er for lille. Det betyder, at Heapstørrelse Java (hive.tez.java.opts) er for lille. Så når du får vist OOM, kan du prøve at øge **hive.tez.java.opts**. Hvis det er nødvendigt du muligvis nødt til at øge **hive.tez.container.size**. Indstillingen **java.opts** skal være omkring 80% af **container.size**.

> [AZURE.NOTE]  Indstillingen **hive.tez.java.opts** skal altid være mindre end **hive.tez.container.size**.

Da en D12 maskine har 28GB hukommelse, vi besluttede dig for at bruge en objektbeholder størrelse på 10GB (10240MB) og tildele 80% for java.opts. Dette skete på Hive-konsollen ved hjælp af indstillingen nedenfor:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Baseret på disse indstillinger, kørt forespørgslen blev på under ti minutter.

## <a name="conclusion-oom-errors-and-container-size"></a>Konklusion: OOM fejl og objektbeholder størrelse

Få en OOM fejlmeddelelse nødvendigvis ikke objektbeholder størrelse er for lille. I stedet skal du konfigurere hukommelsesindstillingerne for, så Heapstørrelse øges og er mindst 80% af objektbeholder hukommelsesstørrelse.
