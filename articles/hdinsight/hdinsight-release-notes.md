<properties
    pageTitle="Produktbemærkninger til Hadoop-komponenter på Azure HDInsight | Microsoft Azure"
    description="Seneste produktbemærkninger og versioner af Hadoop-komponenter til Azure HDInsight. Få tip til udvikling og detaljer for Hadoop, Apache Storm og HBase."
    services="hdinsight"
    documentationCenter=""
    editor="cgronlun"
    manager="jhubbard"
    authors="nitinme"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="nitinme"/>


# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Produktbemærkninger til Hadoop-komponenter på Azure HDInsight

## <a name="notes-for-10262016-release-of-r-server-on-hdinsight"></a>Noter til 10-26/2016 udgave af R Server på HDInsight

- R Server på HDInsight klynge klargøring er blevet strømlinet.
- R Server på HDInsight er nu tilgængelig som normalt HDInsight "R Server" klynge type og ikke længere installeret som en separat HDInsight-program. Kantnode og R Server binære filer er nu klargjort som en del af den R Server klynge installation. Dette forbedrer hastighed og pålideligheden af klargøring. Priser model for R Server er opdateret i overensstemmelse hermed.
- R Server klynge type pris er nu baseret på Standard niveau pris plus R Server tillæg pris. Premium niveau vil nu være reserveret til Premium-funktioner på tværs af forskellige klynge typer og vil ikke blive brugt for klynge R servertype. Denne ændring påvirker ikke effektive priser på Server, R, den ændrer kun hvordan tallene vises på faktura. Alle eksisterende R serverklynger fortsat fungerer, og ARM skabeloner fungerer fortsat indtil ændrede funktionsmåde. **Det anbefales selvom til at opdatere din script installationer for at bruge nye ARM skabelon.**


## <a name="notes-for-08302016-release-of-r-server-on-hdinsight"></a>Noter til 08-30/2016 udgave af R Server på HDInsight

Den fulde version tal til Linux-baserede HDInsight klynger implementeret i denne version:

|HDI |HDI klynge version   |HDP |HDP Build   |Ambari Build |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.8268980    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8268980    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8269383    |2.4 |2.4.2.4-5   |2.2.1.12-4   |

Den fulde version tal til Windows-baseret HDInsight klynger implementeret i denne version:

|HDI |HDI klynge version   |HDP |HDP Build     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1033.2559206   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1033.2559206    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1033.2559206    |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.1033.2559206    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1033.2559206    |2.3 |2.3.3.1-25    |

## <a name="notes-for-08172016-release-of-r-server-on-hdinsight"></a>Noter til 08-17/2016 udgave af R Server på HDInsight

- R Server 8.0.5 – især en rettelse version. Se [R Server produktbemærkninger](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) for at få flere oplysninger. 
- AzureML emballagen på noden kant – [denne R pakke](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) aktiverer R modeller offentliggøres og consumed som en Azure ML webtjeneste.  Se afsnittet ["giver muligheder for et Model"](hdinsight-hadoop-r-server-overview.md#operationalize-a-model) i vores ["Oversigt over R Server på HDInsight"](hdinsight-hadoop-r-server-overview.md) artikel for at få flere oplysninger.
- Linux afhængigheder af de [øverste 100 mest populære R-pakker](https://github.com/metacran/cranlogs) – disse pakkeafhængigheder er nu forudinstalleret Linux.  
- Indstilling for at bruge CRAN repo, når tilføje R-pakker for at noderne data. Se afsnittet ["Installer R pakker"](hdinsight-hadoop-r-server-get-started.md#install-r-packages) i vores ["Introduktion til brug af R Server på HDInsight"](hdinsight-hadoop-r-server-get-started.md) artikel for at få flere oplysninger.
- Øget pålidelighed af R Server klargøring oprettelsen af klynger.


## <a name="notes-for-08012016-release-of-hdinsight"></a>Noter til 08-01/2016 version af HDInsight

Den fulde version tal til Linux-baserede HDInsight klynger implementeret i denne version:

|HDI |HDI klynge version   |HDP |HDP Build   |Ambari Build |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.8028416    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8028416    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8053402    |2.4 |2.4.2.4-5   |2.2.1.12-4   |

Den fulde version tal til Windows-baseret HDInsight klynger implementeret i denne version:

|HDI |HDI klynge version   |HDP |HDP Build     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1005.2488842   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1005.2488842    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1005.2488842    |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.1005.2488842    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1005.2488842    |2.3 |2.3.3.1-25    |

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel gnister, Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Ændringer i HDInsight 3.4 klynger | Standardværdien for følgende hive konfigurationer ændres for at forbedre ydeevnen <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul>| Tjenesten    | Alle| I/T.|
| Følgende rettelser er inkluderet i denne version | HIVE-13632, HIVE-12897,HIVE-12907,HIVE-12908,HIVE-12988,HIVE-13510,HIVE-13572,HIVE-13716,HIVE-13726,HIVE-12505,HIVE-13632,HIVE-13661,HIVE-13705,HIVE-13743,HIVE-13810,HIVE-13857,HIVE-13902,HIVE-13911,HIVE-13933| Tjenesten    | Alle| I/T.

## <a name="notes-for-07142016-release-of-hdinsight"></a>Noter til 14-07/2016 version af HDInsight

Den fulde version tal til Linux-baserede HDInsight klynger implementeret i denne version:

|HDI |HDI klynge version   |HDP |HDP Build   |Ambari Build |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.7932505    |2.2 |2.2.9.1-11  |2.2.1.12-2   |
|3.3 |3.3.1000.0.7932505    |2.3 |2.3.3.1-18  |2.2.1.12-2   |
|3.4 |3.4.1000.0.7933003    |2.4 |2.4.2.0     |2.2.1.12-2   |

Den fulde version tal til Windows-baseret HDInsight klynger implementeret i denne version:

|HDI |HDI klynge version   |HDP |HDP Build     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.989.2441725    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.989.2441725     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.989.2441725     |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.989.2441725     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.989.2441725     |2.3 |2.3.3.1-21    |

## <a name="notes-for-07072016-release-of-hdinsight"></a>Noter til 07-07/2016 version af HDInsight

Den fulde version tal til Linux-baserede HDInsight klynger implementeret i denne version:

|HDI |HDI klynge version   |HDP |HDP Build   |
|----|----------------------|----|------------|
|3.2 |3.2.1000.0.7864996    |2.2 |2.2.9.1-11  |
|3.3 |3.3.1000.0.7864996    |2.3 |2.3.3.1-18  |
|3.4 |3.4.1000.0.7861906    |2.4 |2.4.2.0     |

Den fulde version tal til Windows-baseret HDInsight klynger implementeret i denne version:

|HDI |HDI klynge version   |HDP |HDP Build     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.977.2413853    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.977.2413853     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.977.2413853     |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.977.2413853     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.977.2413853     |2.3 |2.3.3.1-21    |

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel gnister, Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| [HDInsight værktøjer til IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md) | IntelliJ ide-plug-in til HDInsight Spark klynger er nu integreret med Azure-værktøjskassen til IntelliJ. Det understøtter Azure SDK v2.9.1, seneste Java SDK'er, og omfatter alle funktionerne fra enkeltstående HDInsight Plugin for IntelliJ.| Værktøjer    | Knallertmotor| I/T.|
| [HDInsight værktøjer til Eklipse](hdinsight-apache-spark-eclipse-tool-plugin.md) | Azure-værktøjskassen til Eklipse understøtter nu HDInsight Spark klynger. Det giver mulighed for følgende funktioner. <ul><li>Oprette, og skriv et styret program nemt i Scala og Java med første klasse redigering understøttelse af IntelliSense, automatisk format, fejlkontrol osv.</li><li>Test programmet knallertmotor lokalt.</li><li>Sende job til HDInsight Spark klynge og hente resultaterne.</li><li>Log på Azure og få adgang til alle klynger for gnister, der er knyttet til din Azure abonnementer.</li><li>Gå alle de tilknyttede lagerplads ressourcer af din HDInsight Spark klynge.</li></ul>| Værktøjer    | Knallertmotor| I/T.

I denne udgave, har vi ændret gæst OS rettelse politikken for Linux-baserede HDInsight klynger. Formålet med den nye politik er at reducere antallet af genstarter på grund af rettelse. Den nye politik fortsat programrettelse virtuelle maskiner (VM'er) på Linux klynger hver mandag eller torsdag fra og med 12 AM UTC i en forskudt måde på tværs af knuder på en given klynge. En given VM genstartes dog kun højst én gang hver 30 dage på grund af gæst OS rettelse. Desuden opstår den første genstart til en nyligt oprettet klynge der ikke tidligere end 30 dage fra en klynge oprettelsesdato.

>[AZURE.NOTE] Kun anvendes disse ændringer til nyoprettede klynger lig med eller større end denne release-versionen.

## <a name="notes-for-06062016-release-of-hdinsight"></a>Noter efter 06-06/2016 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

|HDP    |HDI Version    |Knallertmotor Version  |Ambari Build-nummer    |HDP Build-nummer|
|-------|---------------|---------------|-----------------------|----------------|
|2.3    |3.3.1000.0.7702215|    1.5.2|  2.2.1.8-2|  2.3.3.1-18|
|2.4    |3.4.1000.0.7702224|    1.6.1|  2.2.1.8-2|  2.4.2.0|


Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel gnister, Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Knallertmotor på HDInsight er alment tilgængelig | Denne version viser forbedringer i tilgængelighed, skalerbarhed og produktivitet til at åbne kilde Apache knallertmotor på HDInsight. <ul><li>Branche foranstillede tilgængelighed SLA 99,9%, hvilket gør det passer til kræver enterprise arbejdsmængder.</li><li>SVG lagerplads lag ved hjælp af Azure sø datalager.</li><li>Produktivitetsværktøjer til hver fase af undersøgelse af data og udvikling. Jupyter notesbøger med tilpasset knallertmotor kernen aktivere interaktive data udforske, integration med BI-dashboards som Power BI, Tableau og Qlik er velegnet til deling af hurtig data og fortløbende rapportering, IntelliJ-plug-in'en er pålidelig indstilling for langtidsaftale kode genstand udvikling og fejlfinding.</li></ul>| Tjenesten    | Knallertmotor| I/T.|
| HDInsight værktøjer til IntelliJ | Dette er en IntelliJ ide-plug-in til HDInsight Spark klynger. Det giver mulighed for følgende funktioner.<ul><li>Oprette, og skriv et styret program nemt i Scala og Java med første klasse redigering understøttelse af IntelliSense, automatisk format, fejlkontrol osv.</li><li>Test programmet knallertmotor lokalt.</li><li>Sende job til HDInsight Spark klynge og hente resultaterne.</li><li>Log på Azure og få adgang til alle klynger for gnister, der er knyttet til din Azure abonnementer.</li><li>Gå alle de tilknyttede lagerplads ressourcer af din HDInsight Spark klynge.</li><li>Gå alle job historik og joboplysninger til din HDInsight Spark klynge.</li><li>Foretage fejlfinding af knallertmotor job fra en fjernplacering fra din stationære computer.</li></ul>| Værktøjer    | Knallertmotor| I/T.

## <a name="notes-for-05132016-release-of-hdinsight"></a>Noter til 13-05/2016 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - uændret)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - uændret)
* HDInsight (Windows) 3.1.4.922.2266903 (HDP 2.1.15.0-2374 - uændret)
* HDInsight (Windows) 3.2.7.922.2266903 (HDP 2.2.9.1-11)
* HDInsight (Windows) 3.3.0.922.2266903 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.2.1000.0.7565644 (HDP 2.2.9.1-11)
* HDInsight (Linux) 3.3.1000.0.7565644 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.4.1000.0.7548380 (HDP 2.4.2.0)

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel gnister, Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Vækker Versionsopdatering og andre fejlrettelser | Denne version opdaterer knallertmotor version i HDInsight klynge til 1.6.1 og løser andre fejl| Tjenesten    | Knallertmotor| I/T.

## <a name="notes-for-04112016-release-of-hdinsight"></a>Noter til 04-11/2016 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight (Windows) 2.1.10.889.2191206 (HDP 1.3.12.0-01795 - uændret)
* HDInsight (Windows) 3.0.6.889.2191206 (HDP 2.0.13.0-2117 - uændret)
* HDInsight (Windows) 3.1.4.889.2191206 (HDP 2.1.15.0-2374 - uændret)
* HDInsight (Windows) 3.2.7.889.2191206 (HDP 2.2.9.1-10)
* HDInsight (Windows) 3.3.0.889.2191206 (HDP 2.3.3.1-16-uændret)
* HDInsight (Linux) 3.2.1000.0.7339916 (HDP 2.2.9.1-10)
* HDInsight (Linux) 3.3.1000.0.7339916 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.4.1000.0.7338911 (HDP 2.4.1.1-3)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Problemer med opgradering af brugerdefinerede metastore for HDI 3.4 | Vil ikke klynge oprettes, hvis du har brugt en brugerdefineret metastore, der tidligere blev brugt på en lavere version af en anden HDInsight klynge. Det var på grund af en opgradering scriptfejl, der nu er blevet løst| Oprettelse af klynge    | Alle | I/T.
| Livius nedbrud gendannelse | Indeholder job status fleksibilitet for et sendt via Livius job | Pålidelighed | Knallertmotor på Linux| I/T.
| Jupyter indhold HA | Giver mulighed for at gemme og indlæse Jupyter notesbog indholdet til og fra kontoen lagerplads, der er knyttet til klyngen. Du kan finde yderligere oplysninger finder [kerner, der er tilgængelige for Jupyter notesbøger](hdinsight-apache-spark-jupyter-notebook-kernels.md).| Notesbøger | Knallertmotor på Linux| I/T.
| Fjernelse af hiveContext i Jupter notesbøger | Brug `%%sql` magiske i stedet for `%%hive` magisk. SqlContext svarer til hiveContext. Du kan finde flere oplysninger, se [kerner, der er tilgængelige for Jupyter notesbøger](hdinsight-apache-spark-jupyter-notebook-kernels.md)| Notesbøger    | Knallertmotor klynger på Linux| I/T.
| Ændrede funktionsmåde for ældre knallertmotor versioner | Ældre version knallertmotor 1.3.1 fjernes fra tjenesten på 5/31 | Tjenesten | Knallertmotor klynger i Windows | I/T.

## <a name="notes-for-03292016-release-of-hdinsight"></a>Noter til 03-29/2016 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - uændret)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - uændret)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374 - uændret)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7 - uændret)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8 - uændret)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7 - uændret)
* HDInsight (Linux) 3.4.1000.0.7195842 (HDP 2.4.1.0-327)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Tilføjede HDInsight 3.4 version og opdaterede versioner af HDP for alle HDInsight klynger | Med denne version, vi har tilføjet HDInsight v3.4 (baseret på HDP 2.4) og andre HDP versioner har også opdateret. HDP 2.4 produktbemærkninger er tilgængelig [her](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html) og flere oplysninger om HDInsight versioner kan være fundet [her](hdinsight-component-versioning.md).| Tjenesten    | Alle Linux klynger| I/T.
| HDInsight Premium | HDInsight er nu tilgængelig i to kategorier - Standard- og Premium. HDInsight Premium er i øjeblikket i Vis udskrift og kun tilgængelig for Hadoop og knallertmotor klynger på Linux. Finde flere oplysninger under [her](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).| Tjenesten    | Hadoop og knallertmotor på Linux| I/T.
| Microsoft R Server | HDInsight Premium indeholder Microsoft R Server, der kan indgå i Hadoop og knallertmotor klynger på Linux. Flere oplysninger under [Oversigt over R Server på HDInsight](hdinsight-hadoop-r-server-overview.md).| Tjenesten    | Hadoop og knallertmotor på Linux| I/T.
| Knallertmotor 1.6.0 | HDInsight 3.4 klynger omfatter nu knallertmotor 1.6.0| Tjenesten    | Knallertmotor klynger på Linux| I/T.
| Forbedringer af Jupyter notesbog | Jupyter notesbøger, der er tilgængelige med knallertmotor klynger giver nu yderligere knallertmotor kerner. De indeholder også forbedringer som brug af %% magisk, automatisk visualisering og integration med Python visualisering biblioteker (såsom matplotlib). Du kan finde yderligere oplysninger finder [kerner, der er tilgængelige for Jupyter notesbøger](hdinsight-apache-spark-jupyter-notebook-kernels.md). | Tjenesten | Knallertmotor klynger på Linux | I/T.

## <a name="notes-for-03222016-release-of-hdinsight"></a>Noter til 03-22/2016 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - uændret)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - uændret)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374 - uændret)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7 - uændret)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8 - uændret)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7 - uændret)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Opdaterede HDInsight versioner til alle HDInsight klynger | Med denne version, har vi opdateret HDInsight versioner til alle HDInsight klynger| Tjenesten    | Alle| I/T.


## <a name="notes-for-03102016-release-of-hdinsight"></a>Noter til 10-03/2016 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight (Windows) 2.1.10.859.2123216 (HDP 1.3.12.0-01795 - uændret)
* HDInsight (Windows) 3.0.6.859.2123216 (HDP 2.0.13.0-2117 - uændret)
* HDInsight (Windows) 3.1.4.859.2123216 (HDP 2.1.15.0-2374 - uændret)
* HDInsight (Windows) 3.2.7.859.2123216 (HDP 2.2.9.1-7)
* HDInsight (Windows) 3.3.0.859.2123216 (HDP 2.3.3.1-5 - uændret)
* HDInsight (Linux) 3.2.1000.7076817 (HDP 2.2.9.1-8)
* HDInsight (Linux) 3.3.1000.7076817 (HDP 2.3.3.1-7)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Opdaterede HDInsight versioner til alle HDInsight klynger | Med denne version, har vi opdateret HDInsight versioner til alle HDInsight klynger| Tjenesten    | Alle| I/T.

## <a name="notes-for-01272016-release-of-hdinsight"></a>Noter til 01-27/2016 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight (Windows) 2.1.10.817.2028315 (HDP 1.3.12.0-01795 - uændret)
* HDInsight (Windows) 3.0.6.817.2028315 (HDP 2.0.13.0-2117 - uændret)
* HDInsight (Windows) 3.1.4.817.2028315 (HDP 2.1.15.0-2374 - uændret)
* HDInsight (Windows) 3.2.7.817.2028315 (HDP 2.2.9.1-1)
* HDInsight (Windows) 3.3.0.817.2028315 (HDP 2.3.3.1-5 - uændret)
* HDInsight (Linux) 3.2.1000.4072335 (HDP 2.2.9.1-1)
* HDInsight (Linux) 3.3.1000.4072335 (HDP 2.3.3.1-1)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Opdaterede HDInsight versioner til alle HDInsight klynger | Med denne version, har vi opdateret HDInsight versioner til alle HDInsight klynger| Tjenesten    | Alle| I/T.

## <a name="notes-for-12022015-release-of-hdinsight"></a>Noter til 12-02-2015 version af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight (Windows) 2.1.10.763.1931434 (HDP 1.3.12.0-01795 - uændret)
* HDInsight (Windows) 3.0.6.763.1931434 (HDP 2.0.13.0-2117 - uændret)
* HDInsight (Windows) 3.1.4.763.1931434 (HDP 2.1.15.0-2374 - uændret)
* HDInsight (Windows) 3.2.7.763.1931434 (HDP 2.2.7.1-34 - uændret)
* HDInsight (Windows) 3.3.1000.0 (HDP 2.3.3.1-5)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34 - uændret)
* HDInsight (Linux) 3.3.1000.0 (HDP 2.3.3.0-3039)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Tilføjede HDInsight 3.3 version og opdaterede versioner af HDP for alle HDInsight klynger | Med denne version, vi har tilføjet HDInsight v3.3 (baseret på HDP 2.3) og andre HDP versioner har også opdateret. HDP 2.3 produktbemærkninger er tilgængelig [her](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) og flere oplysninger om HDInsight versioner kan være fundet [her](hdinsight-component-versioning.md).| Tjenesten    | Alle| I/T.

## <a name="notes-for-11302015-release-of-hdinsight"></a>Noter i 11-30-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight (Windows) 2.1.10.757.1923908 (HDP 1.3.12.0-01795 - uændret)
* HDInsight (Windows) 3.0.6.757.1923908 (HDP 2.0.13.0-2117 - uændret)
* HDInsight (Windows) 3.1.4.757.1923908 (HDP 2.1.15.0-2374 - uændret)
* HDInsight (Windows) 3.2.7.757.1923908 (HDP 2.2.7.1-34)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Opdaterede HDInsight versioner for alle HDInsight klynger og HDP versioner til HDInsight 3.2 klynger (Windows og Linux) | Med denne version er HDInsight og HDP versioner blevet opdateret | Tjenesten    | Alle| I/T.


## <a name="notes-for-10272015-release-of-hdinsight"></a>Noter til 10-27-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight (Windows) 2.1.10.726.1866228 (HDP 1.3.12.0-01795 - uændret)
* HDInsight (Windows) 3.0.6.726.1866228 (HDP 2.0.13.0-2117 - uændret)
* HDInsight (Windows) 3.1.4.726.1866228 (HDP 2.1.15.0-2374 - uændret)
* HDInsight (Windows) 3.2.7.726.1866228 (HDP 2.2.7.1-33)
* HDInsight (Linux) 3.2.1000.0.6035701 (HDP 2.2.7.1-33)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Opdaterede HDInsight versioner til alle HDInsight klynger (Windows og Linux) | Med denne version er HDInsight og HDP versioner blevet opdateret | Tjenesten    | Alle| I/T.
| Fast Jupyter for Windows knallertmotor klynger med store bogstaver klynger | Klynger, der havde DNS-navne, der er angivet med store bogstaver i havde problemer med Jupyter notesbøger på grund af en anmodning om kontrol af origin. Fix kunne ændre DNS-navnet for Jupyters konfiguration til små bogstaver. | Tjenesten    | HDInsight knallertmotor (Windows)| I/T.


## <a name="notes-for-10202015-release-of-hdinsight"></a>Noter til 10-20-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.716.1846990 (Windows) (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.716.1846990 (Windows) (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.4.716.1846990 (Windows) (HDP 2.1.16.0-2374)
* HDInsight 3.2.7.716.1846990 (Windows) (HDP 2.2.7.1-0004)
* HDInsight 3.2.1000.0.5930166 (Linux) (HDP 2.2.7.1-0004)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Standardversionen HDP ændres til HDP 2.2 | Standardversionen for HDInsight Windows klynger ændres til HDP 2.2. HDInsight version 3.2 (HDP 2.2) har været alment tilgængelig i siden februar 2015. Denne ændring spejlvendes kun klynge standardversionen, når der ikke er foretaget en eksplicit markering under klargøring af den klynge ved hjælp af portalen Azure, PowerShell-cmdlet'er eller SDK. | Tjenesten    | Alle| I/T.                  |
|Ændringer i VM navneformat til implementering af flere HDInsight på Linux klynger i et enkelt virtuelt netværk | Support til implementering af flere HDInsight Linux klynger i et enkelt virtuelt netværk tilføjes i denne udgave. Som en del af dette formatet for virtuelt navne i klyngen er ændret fra headnode\*, workernode\* og zookeepernode\* til hn\*, wn\*, og zk\* henholdsvis. Det er ikke en anbefalede fremgangsmåde at tage en direkte afhængighed på formatet for virtuelt navne, da dette kan ændres. Brug "hostname -f" på den lokale computer eller Ambari APIs til at finde ud af, på listen over værter og tilknytning af komponenter til værter. Du kan finde flere oplysninger i [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md) og [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md). | Tjenesten | HDInsight klynger på Linux | I/T. |
| Ændringer i konfigurationen | Følgende konfigurationer er nu aktiveret til HDInsight 3.1 klynger: <ul><li>tez.yarn.ATS.Enabled og yarn.log.server.url. Dette gør det muligt for tidslinje programserver og Log serveren for at kunne fungere ud af logfiler.</li></ul>Til HDInsight 3.2 klynger er blevet ændret følgende konfigurationer: <ul><li>mapreduce.fileoutputcommitter.Algorithm.version er blevet indstillet til 2. Dette gør det muligt for brug af V2-versionen af FileOutputCommitter.</li></ul> | Tjenesten | Alle | I/T. |


## <a name="notes-for-09092015-release-of-hdinsight"></a>Noter til 09-09-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.675.1768697 (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.675.1768697 (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.4.675.1768697 (HDP 2.1.15.0-2334 - uændret)
* HDInsight 3.2.6.675.1768697 (HDP 2.2.6.1-0012 - uændret)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Opdaterede HDInsight versioner til alle HDInsight klynger | Med denne version er blevet opdateret HDInsight versioner | Tjenesten    | Alle| I/T.                  |

## <a name="notes-for-07312015-release-of-hdinsight"></a>Noter i 07-31-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.640.1695824 (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.640.1695824 (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.4.640.1695824 (HDP 2.1.15.0-2334 - uændret)
* HDInsight 3.2.6.640.1695824 (HDP 2.2.6.1-0012 - uændret)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Løse knallertmotor klynge node igen afbildning arbejdsproces | Fast en fejl, der var årsag knallertmotor klyngenoder ikke genoprettes efter igen billede | Tjenesten    | Knallertmotor| I/T.                  |


## <a name="notes-for-07312015-release-of-hdinsight"></a>Noter i 07-31-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.635.1684502 (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.635.1684502 (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.4.635.1684502 (HDP 2.1.15.0-2334 - uændret)
* HDInsight 3.2.6.635.1684502 (HDP 2.2.6.1-0012 - uændret)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Opdaterede HDInsight versioner til alle HDInsight klynger | Med denne version er blevet opdateret HDInsight versioner | Tjenesten    | Alle| I/T.                  |


## <a name="notes-for-07072015-release-of-hdinsight"></a>Noter til 07-07-2015 version af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.610.1630216 (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.610.1630216 (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.4.610.1630216 (HDP 2.1.15.0-2334 - uændret)
* HDInsight 3.2.4.610.1630216 (HDP 2.2.6.1-0012)
* SDK 1.5.8


Denne version indeholder følgende opdateringer.

| Titel                                           | Beskrivelse                                          | Påvirkede område (for eksempel tjenesten, komponent eller SDK) | Klynge Type (for eksempel Hadoop, HBase eller Storm) | JIRA (hvis relevant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Opdaterede HDP versioner til HDInsight 3.2 klynger | Med denne version installerer HDInsight 3.2 HDP 2.2.6.1-0012 | Tjenesten    | Alle                                                 | I/T.                  |


## <a name="notes-for-06262015-release-of-hdinsight"></a>Noter efter 06-26-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.601.1610731 (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.601.1610731 (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.4.601.1610731 (HDP 2.1.15.0-2334 - uændret)
* HDInsight 3.2.4.601.1610731 (HDP 2.2.6.1-0011)
* SDK 1.5.8


Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>Opdaterede HDP versioner til HDInsight 3.2 klynger</td>
<td>Med denne version installerer HDInsight 3.2 HDP 2.2.6.1</td>
<td>Tjenesten</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

</table>

## <a name="notes-for-06182015-release-of-hdinsight"></a>Noter efter 06-18-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.596.1601657 (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.596.1601657 (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.4.596.1601657 (HDP 2.1.15.0-2334)
* HDInsight 3.2.4.596.1601657 (HDP 2.2.6.1-0002)
* SDK 1.5.8


Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>Yderligere HTTPS-porte åbne</td>
<td>Skytjenesten nu åbner 5 porte 8001 til 8005 på klynge f.eks. på https://<clustername>.azurehdinsight.net:8001/. Anmodninger om til disse URL-adresser er godkendt ved hjælp af den samme basisgodkendelse adgangskode ordning som port 443. Portene binde til den samme port på den aktive headnode. Scripthandlinger kan bruges til at foretage kundeservice lytter på portene på headnode og ruten til uden for klyngen.</td>
<td>Skybaseret tjeneste</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>Vekslende MapReduce Bland problemet for HDInsight 3.2</td>
<td>Løsning for en sjældne, forbigående Kør betingelse i kort reducere Bland på stor klynger, hvilket resulterer i gang opgavefejl. Du kan finde flere oplysninger i <a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a> .</td>
<td>Hadoop Core</td>
<td>Alle</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a></td>
</tr>

<tr>
<td>Flytte til seneste Azure Java SDK 2.2 for HDInsight 3.2</td>
<td>Flyttet til nyeste version af Azure SDK til Java, der anvendes af WASB driveren. Seneste SDK indeholder nogle rettelser og Produktbemærkninger til den samme findes på https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt.</td>
<td>Hadoop Core</td>
<td>Alle</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP-11959</a></td>
</tr>

<tr>
<td>Flytte til HDP 2.1.15 til HDInsight 3.1 klynger</td>
<td>Hortonworks produktbemærkninger til udgivelsen findes <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">her</a>.</td>
<td>HDP</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

</table>

## <a name="notes-for-06042015-release-of-hdinsight"></a>Noter til 06-04-2015 version af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.583.1575584 (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.583.1575584 (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.3.583.1575584 (HDP 2.1.12.1-0003 - uændret)
* HDInsight 3.2.4.583.1575584 (HDP 2.2.6.1-1)
* SDK 1.5.8


Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>Løse for 502 ugyldig gateway fejl for Storm klynger</td>
<td>Denne version løser en fejl, som påvirker jobbet API, der forårsagede webstedet kunne ned efter en genstart.</td>
<td>Tjenesten</td>
<td>Storm</td>
<td>I/T.</td>
</tr>

</table>

## <a name="notes-for-06012015-release-of-hdinsight"></a>Noter til 06-01-2015 version af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.577.1563827 (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.577.1563827 (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.3.577.1563827 (HDP 2.1.12.1-0003 - uændret))
* HDInsight 3.2.4.577.1563827 (HDP 2.2.6.0-2800 - uændret)
* SDK 1.5.8


Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>Forskellige fejlrettelser</td>
<td>Denne version løser fejl, der er relateret til klargøring af klynge.</td>
<td>Tjenesten</td>
<td>Alle klynge typer</td>
<td>I/T.</td>
</tr>

</table>

## <a name="notes-for-05272015-release-of-hdinsight"></a>Noter til 05-27-2015 version af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 3.2.4.570.1554102 (HDP 2.2.6.0-2800)
* Andre klynge versioner og SDK installeres ikke som en del af denne version.


Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>HDP 2.2 opdatering</td>
<td>Denne version af HDInsight 3.2 indeholder HDP 2.2.6 og kombinerer flere vigtige fejlrettelser til HDInsight. Fuld produktbemærkninger er aktiveret i <a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">HDP 2.2.6 produktbemærkninger</a>.</td>
<td>HDP</td>
<td>Alle klynge typer</td>
<td>I/T.</td>
</tr>

<tr>
<td>Ændre til konfiguration af standard garn objektbeholder hukommelse</td>
<td>I denne opdatering, er den standard tilgængelig hukommelse til GARN beholdere (yarn.nodemanager.resource.memory mb og yarn.scheduler.maximum allokering mb), startet af Node Manager øges 5632MB. Tidligere dette blev reduceret til 4608MB, men baseret på forskellige job kører, den nye værdi, skal give bedre stabilitet og ydeevne til de fleste sager, derfor er en bedre standardværdi. Som normalt, hvis du en kritiske afhængighed på denne hukommelseskonfiguration, skal du angive den eksplicit mens du opretter klyngen.</td>
<td>HDP</td>
<td>Alle klynge typer</td>
<td>I/T.</td>
</tr>

<tr>
<td>Standard Config fungerer ensartet til HBase og Storm klynger</td>
<td>Denne opdatering gendanner Hbase og Storm klynger for at bruge de samme værdier af GARN konfigurationer som Hadoop klynger. Dette er gjort for fungerer ensartet på tværs af alle klynge typer.</td>
<td>HDP</td>
<td>HBase, Storm</td>
<td>I/T.</td>
</tr>

</table>

## <a name="notes-for-05202015-release-of-hdinsight"></a>Noter til 20-05-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.564.1542093 (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.564.1542093 (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.3.564.1542093 (HDP 2.1.12.1-0003)
* HDInsight 3.2.4.564.1542093 (HDP 2.2.4.6-2)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>SCP.NET EventHub Support</td>
<td>De opdaterede klynge pakker til HDInsight Storm sætter nye funktioner til SCP.NET. Du har nu adgang til nye API'er i topologi builder, som gør det nemmere at bruge EventHubSpout eller Java Spouts. Du skal opdatere kunden SCP.NET SDK, til at arbejde med nye klynger som kontrakterne er blevet opdateret. Se vigtige oplysninger om, der er inkluderet i pakken SCP.NET nuget få at vide om de nye API'er, brug og release noter (herunder fejlrettelser).</td>
<td>VS værktøjer</td>
<td>Storm HDInsight 3.2 klynger</td>
<td>I/T.</td>
</tr>

<tr>
<td>JDBC driver update</td>
<td>Opdateret driveren til versionen af SQL Server, der understøttes i sqljdbc_4.1.5605.100.</td>
<td>Metastore</td>
<td>Alle</td>
<td>I/T.</td>
</tr>
</table>

## <a name="notes-for-04272015-release-of-hdinsight"></a>Noter til 04-27-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.537.1486660 (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.537.1486660 (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.3.537.1486660 (HDP 2.1.12.0-2329 - uændret)
* HDInsight 3.2.3.537.1486660 (HDP 2.2.2.2-4)
* SDK 1.5.8

Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>Løse DLL-afhængighed</td>
<td>Fjerner HDInsight afhængighed af enhed Test Framework.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>I/T.</td>
</tr>

<tr>
<td>Rettelse til konkurrencetilstand</td>
<td>Oprette en klynge anmodning nu venter på læg anmodning accepteres før afstemning på status</td>
<td>SDK</td>
<td>Hadoop</td>
<td>I/T.</td>
</tr>
</table>

## <a name="notes-for-04142015-release-of-hdinsight"></a>Noter til 04-14-2015 version af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329 - uændret)
* HDInsight 3.2.3.525.1459730 (HDP 2.2.2.2-2)
* SDK 1.5.6

Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>Tez fejlrettelser</td>
<td>Løsninger til Apache TEZ. 2214 og TEZ 1923 er inkluderet i denne udgave af HDI 3,2. Disse er specielt behov for visse Hive forespørgsler på Tez som kræver, for at blande en betydeligt mængde data.
</td>
<td>HDP</td>
<td>Hadoop</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ. 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## <a name="notes-for-04062015-release-of-hdinsight"></a>Noter til 04-06-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795 - uændret)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117 - uændret)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329 - uændret)
* HDInsight 3.2.3.521.1453250 (HDP 2.2.2.2-1)
* SDK 1.5.6

Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>HDInsight .NET SDK 1.5.6</td>
<td>Opdateringer til at fjerne nogle interne klasser til HDInsight på Linux.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>I/T.</td>
</tr>

<tr>
<td>Avro bibliotek 1.5.6</td>
<td>Tilføjede <b>KnownTypeAttribute</b> for metode <b>GetAllKnownTypes</b>. Fast undtagelsesfejl, når en type er null-værdi for GetAllKnownTypes metode.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>I/T.</td>
</tr>

<tr>
<td>Fejlrettelser</td>
<td>Forskellige fejlrettelser til tjenesten</td>
<td>Tjenesten</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

</table>
<br>

## <a name="notes-for-04012015-release-of-hdinsight"></a>Noter til 04-01-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.513.1431705 (HDP 1.3.12.0-01795)
* HDInsight 3.0.6.513.1431705 (HDP 2.0.13.0-2117)
* HDInsight 3.1.3.513.1431705 (HDP 2.1.12.0-2329)
* HDInsight 3.2.3.513.1431705 (HDP 2.2.2.1-2600)
* SDK 1.5.5

Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>Muligheden for at aktivere/deaktivere remote desktop legitimationsoplysninger på Windows-klynger via .NET SDK</td>
<td>Programmeringsmæssig support for at aktivere eller deaktivere RDP legitimationsoplysninger på Windows-klynger.</td>
<td>SDK</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>Muligheden for at aktivere remote desktop legitimationsoplysninger på klynger, mens de er ved at blive klargjort</td>
<td>Programmeringsmæssig understøttelse af aktiverer remote desktop legitimationsoplysninger, da klyngen oprettes. Dette fjerner to trin for først klargøring af klynge og derefter aktivere Fjernskrivebord.</td>
<td>SDK</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>Opgraderede Python til 2.7.8</td>
<td>Opgraderede Python på HDInsight klynger til Python 2.7.8, som indeholder nogle vigtige sikkerhed løsninger på HDInsight version 2.1, 3.0, 3.1 og 3.2</td>
<td>Tjenesten</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>GARN konfigurationsændring</td>
<td>Ændrede GARN konfiguration yarn.resourcemanager.max fuldført-programmer til 1000 for alle typer af klynge til HDInsight version 3.1 og 3.2. Denne værdi kontrollerer kun på listen over fuldførte programmer GARN brugergrænsefladen. Hvis du vil have oplysninger om programmer, der blev sendt før på listen over programmer, der vises på Brugergrænsefladen, kan du direkte gå til oversigten serveren.</td>
<td>GARN</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>Ændre størrelsen på af knuder på en HBase klynge</td>
<td>HBase klynger tillade nu at størrelsen af knuder (op og ned) til HDInsight version 3.1 og 3.2</td>
<td>Tjenesten</td>
<td>HBase</td>
<td>I/T.</td>
</tr>

<tr>
<td>JDBC opgradering</td>
<td>SQL JDBC driver er opgraderet til version sqljdbc_4.0.2206.100 for HDInsight version 3.2. Denne version indeholder forbedringer af vigtige sikkerheden.</td>
<td>HDP</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>JVM konfigurationsopdatering</td>
<td>Opdaterede JVM konfiguration networkaddress.cache.ttl til 300 sekunder fra standardværdien for 1 for HDInsight version 3.1 og 3.2. Denne konfigurationsværdi kontrollerer politikken cachelagring for vellykket navneopslag fra navnetjenesten. Dette løser en fejl, der er relateret til vækst og formindskelse HBase klynger.</td>
<td>Tjenesten</td>
<td>HBase</td>
<td>I/T.</td>
</tr>

<tr>
<td>Opgradere til Azure lagerplads SDK til Java 2.0</td>
<td>HDInsight version 3.2 opgraderes for at bruge den nyeste version af Azure lagerplads SDK til Java. Dette indeholder flere vigtige fejlrettelser over den aktuelle 0.6.0 version.</td>
<td>HDP</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>Opgraderet til Apache WASB kildekode</td>
<td>HDInsight version 3.2 nu bruger nyeste kode for WASB filsystemet driveren fra Apache Hadoop. Med denne ændring pakket WASB driveren nu som en separat glas. Dette er kun en ændring af emballagen og indeholder ikke ændringerne i WASB driverens funktionsmåde. Navnet på filen glas er hadoop-azure-2.6.0.jar.</td>
<td>HDP</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>JAR filnavn opdateringer i HDInsight 3.2</td>
<td>Denne opdatering til HDInsight version 3.2 indeholder flere fejlrettelser, og nogle interne krukker pakket som en del af HDP er blevet opgraderet. Bemærk, at filerne glas interne til pakken HDP og ikke til direkte anvendelse af kundeprogrammer. Programmer skal Pak deres egen version af krukker, så en opgradering til de interne krukker HDP ikke bryder kundeprogrammer.</td>
<td>HDP</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

</table>
<br>

## <a name="notes-for-03032015-release-of-hdinsight"></a>Noter til 03-03-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.488.1375841 (HDP 1.3.9.0-01351 - uændret)
* HDInsight 3.0.6.488.1375841 (HDP 2.0.9.0-2097 - uændret)
* HDInsight 3.1.3.488.1375841 (HDP 2.1.10.0-2290 - uændret)
* HDInsight 3.2.3.488.1375841 (HDP-2.2.10.0-2340 - uændret)
* SDK 1.5.0 (uændret)

Denne version indeholder følgende opdatering.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA</th>
</tr>


<tr>
<td>Forbedret pålidelighed</td>
<td>Vi har foretaget løsninger, der tillader tjenesten skalere bedre med den øgede belastning i forhold til oprettelse af klynge.</td>
<td>Tjenesten</td>
<td>Alle</td>
<td>I/T.</td>
</tr>



</table>
<br>

## <a name="notes-for-02182015-release-of-hdinsight"></a>Noter til 02-18-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.471.1342507 (HDP 1.3.9.0-01351 - uændret)
* HDInsight 3.0.6.471.1342507 (HDP 2.0.9.0-2097 - uændret)
* HDInsight 3.1.3.471.1342507 (HDP 2.1.10.0-2290 - uændret)
* HDInsight 3.2.3.471.1342507 (HDP-2.2.10.0-2340)
* SDK 1.5.0

Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>HDInsight 3.2 klynger</td>
<td>Hadoop 2.6/HDP2.2 leveres med HDInsight 3.2 klynger. Den indeholder vigtigste opdateringer til alle open source-komponenter. Få mere at vide ved at se Nyheder i HDInsight og <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">HDP 2.2.0.0 produktbemærkninger</a>.</td>
<td>Open source-software</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>HDinsight på Linux (Preview)</td>
<td>Klynger kan installeres kører på Ubuntu Linux. Yderligere oplysninger finder du se komme i gang med HDInsight på Linux.</td>
<td>Tjenesten</td>
<td>Hadoop</td>
<td>I/T.</td>
</tr>

<tr>
<td>Storm generelt tilgængelighed</td>
<td>Apache Storm klynger er alment tilgængelig. Få mere at vide se Introduktion til brug af Storm i HDInsight.</td>
<td>Tjenesten</td>
<td>Storm</td>
<td>I/T.</td>
</tr>

<tr>
<td>Virtuelt størrelser</td>
<td>Azure HDInsight findes på flere virtuelt typer og -størrelser. HDInsight kan udnytte A2 til A7 størrelser, der er bygget til generelle formål. D-serien noder, der indeholder de Solid State SSD'er () og 60 procent hurtigere processorer; og A8 og A9 størrelser, der har InfiniBand understøttelse af hurtigt netværk.</td>
<td>Tjenesten</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>Klynge skalering</td>
<td>Du kan ændre antallet af dataknuder til en igangværende HDInsight klynge uden at slette eller gendanne den. På nuværende tidspunkt kun har Hadoop-forespørgsel og Apache Storm klynge denne mulighed, men understøttelse af Apache HBase klynge type er snart at følge. Se administrere HDInsight klynger kan finde flere oplysninger.</td>
<td>Tjenesten</td>
<td>Hadoop, Storm</td>
<td>I/T.</td>
</tr>

<tr>
<td>Visual Studio-værktøj</td>
<td>Ud over fuldført værktøj til Apache Storm er blevet opdateret værktøj til Apache Hive i Visual Studio for at medtage fuldførelse af sætning, lokale validering og forbedret understøttelse af fejlfinding. Yderligere oplysninger finder du se komme i gang med HDInsight Hadoop-værktøjer til Visual Studio.</td>
<td>Værktøj</td>
<td>Hadoop</td>
<td>I/T.</td>
</tr>

<tr>
<td>Hadoop-forbindelse til DocumentDB</td>
<td>Med Hadoop Connector til DocumentDB, kan du udføre komplekse sammenlægninger, analyse og udgør en vejledende over dine skema mindre JSON dokumenter, der er gemt på tværs af DocumentDB af websteder eller på tværs af database-konti. Flere oplysninger og et selvstudium, under køre Hadoop-job ved hjælp af DocumentDB og HDInsight.</td>
<td>Tjenesten</td>
<td>Hadoop</td>
<td>I/T.</td>
</tr>

<tr>
<td>Fejlrettelser</td>
<td>Vi har foretaget forskellige underordnede fejlrettelser for HDInsight tjenester. Ingen med kundekontakt funktionsmåden for funktioner ændres forventes.</td>
<td>Tjenesten</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

</table>
<br>

## <a name="notes-for-02062015-release-of-hdinsight"></a>Noter til 02-06-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.463.1325367 (HDP 1.3.9.0-01351 - uændret)
* HDInsight 3.0.6.463.1325367 (HDP 2.0.9.0-2097 - uændret)
* HDInsight 3.1.2.463.1325367 (HDP 2.1.10.0-2290)
* SDK I/T.

Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>Fejlrettelser</td>
<td>Vi har foretaget forskellige underordnede fejlrettelser for HDInsight tjenester. Ingen med kundekontakt funktionsmåden for funktioner ændres forventes.</td>
<td>Tjenesten</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>HDP 2.1 vedligeholdelse opdatering</td>
<td>HDInsight 3.1 opdateres for at installere HDP 2.1.10.0. Du kan finde flere oplysninger <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">Produktbemærkninger HDP-2.1.10</a>. </td>
<td>Open source-software</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>HDP binære opdateringer</td>
<td>Der er et par glas filer i HBase, for hvilken fil er blevet opdateret navne. Filerne glas bruges internt af HBase, så det ikke forventes, at kunder har en afhængighed på navnene på filerne glas. Dette omfatter:
<ul>
<li>./lib/jetty-6.1.26.hwx.JAR</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.JAR</li>
<li>./lib/jetty-Util-6.1.26.hwx.JAR</li>
</ul>
</td>
<td>Open source-software</td>
<td>HBase</td>
<td>I/T.</td>
</tr>

</table>
<br>

## <a name="notes-for-1292015-release-of-hdinsight"></a>Noter i 1-29-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.455.1309616 (HDP 1.3.9.0-01351 - uændret)
* HDInsight 3.0.6.455.1309616 (HDP 2.0.9.0-2097 - uændret)
* HDInsight 3.1.2.455.1309616 (HDP 2.1.9.0-2196 - uændret)
* SDK I/T.

Denne version indeholder følgende opdatering.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Påvirkede område (for eksempel tjenesten, komponent eller SDK)</p></th>
<th>Klynge Type (for eksempel Hadoop, HBase eller Storm)</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>

<td>Fejlrettelser</td>
<td>Vi har foretaget nogle vigtige fejlrettelser, forbedrer pålideligheden HDInsight klynger under Azure opgraderinger.</td>
<td>Tjenesten</td>
<td>Alle</td>
<td>I/T.</td>
</tr>



</table>
<br>

## <a name="notes-for-152015-release-of-hdinsight"></a>Noter i 1-5-2015 udgave af HDInsight

Den fulde version tal til HDInsight klynger implementeret i denne version:

* HDInsight 2.1.10.420.1246118 (HDP 1.3.9.0-01351 - uændret)
* HDInsight 3.0.6.420.1246118 (HDP 2.0.9.0-2097 - uændret)
* HDInsight 3.1.2.420.1246118 (HDP 2.1.9.0-2196 - uændret)


Denne version indeholder følgende opdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Komponent</th>
<th>Klynge Type</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>Prøver til Twitter tendensanalyse og Mahout-baserede film anbefalinger</td>
<td><p>I denne udgave har konsollen HDInsight forespørgsel to yderligere eksempler:</p>

<p><b>Twitter tendensanalyse</b><br>
Offentlige API'er, der leveres af Twitter-websteder er en praktisk datakilde til at analysere og forstå populære tendenser. Lær, hvordan du bruger Hive til at få en liste over Twitter-brugere, der sendes på de fleste tweets, der indeholder et bestemt ord i dette selvstudium. </p>

<p><b>Mahout film anbefaling</b><br>
Apache Mahout er en Apache Hadoop maskine læ bibliotek. Mahout indeholder algoritmer til at behandle data (såsom filtrering, klassificering og klynge). I dette selvstudium, du brug en anbefaling program til at generere film anbefalinger, der er baseret på film, der har set dine venner.</p></td>
<td>Forespørgsel console</td>
<td>Hadoop</td>
<td>I/T.</td>
</tr>

<tr>
<td>Ændre til standardværdien for Hive konfiguration: hive.auto.convert.join.noconditionaltask.size</td>
<td><p>Denne størrelse konfiguration gælder for automatisk konverterede kort joinforbindelser. Værdien, der repræsenterer summen af størrelsen på tabeller, der kan konverteres til hash-kort, der passer til i hukommelsen. Denne værdi i en tidligere version øges fra standardværdien på 10 MB til 128 MB. Den nye værdi for 128 MB blev dog forårsager job mislykkes forfaldsdato til af manglende hukommelse. Denne version igen standardværdien tilbage til 10 MB. Kunder kan stadig vælge at tilsidesætte denne værdi under klynge oprettelsen givet deres forespørgsler og tabel størrelse. Se <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">Optimere automatisk deltage i konverteringen</a> i Hortonworks dokumentation kan finde flere oplysninger om denne indstilling, og hvordan du kan tilsidesætte den. </p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>I/T.</td>
</tr>

</table>
<br>

## <a name="notes-for-12232014-release-of-hdinsight"></a>Noter til 23-12-2014-udgivelsen af HDInsight

Komplet version tallene for HDInsight klynger implementeret i denne version er:

* HDInsight 2.1.10.420.1246783 (HDP version uændret)
* HDInsight 3.0.6.420.1246783 (HDP version uændret)
* HDInsight 3.1.1.420.1246783 (HDP version uændret)

Denne version indeholder følgende opdatering.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Komponent</th>
<th>Klynge Type</th>
<th>JIRA (hvis relevant)</th>
</tr>


<tr>
<td>Fejl under oprettelse af forbigående klynge på grund af stor belastning</td>
<td><p>Forbedret algoritme til at hente HDP pakker under oprettelse af klynge aktiverer mere robust håndtering af mislykkede forsøg på grund af stor belastning.</p></td>
<td>Tjenesten</td>
<td>Hadoop, Hbase, Storm</td>
<td>I/T.</td>
</tr>



</table>
<br>

## <a name="notes-for-12182014-release-of-hdinsight"></a>Noter efter 18-12-2014 udgave af HDInsight

Denne version indeholder følgende komponent opdatering.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Komponent</th>
<th>Klynge Type</th>
<th>JIRA (hvis relevant)</th>
</tr>

<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">Klynge tilpasning generelt Avalability</a></td>
<td><p>Tilpasning af giver mulighed at tilpasse din Azure HDInsight klynger med projekter, der er tilgængelige fra Apache Hadoop økosystemet. Med denne nye funktion, kan du eksperimentere og installere Hadoop projekter til Azure HDInsight. Dette er aktiveret via funktionen **Scripthandling** , som kan redigere Hadoop klynger på vilkårlig måder ved hjælp af brugerdefinerede scripts. Denne tilpasning er tilgængelig på alle typer HDInsight klynger herunder Hadoop, HBase og Storm. Hvis du vil vise en potens af denne egenskab, har vi dokumenteret processen for at installere de populære <a href = "hdinsight-hadoop-spark-install.md" target="_blank">gnister</a>, <a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>, <a href = "hdinsight-hadoop-solr-install.md" target="_blank">Solr</a>og <a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a> moduler. Denne version tilføjer også muligheden for kunder til at angive deres brugerdefineret scripthandling via Azure-portalen, indeholder retningslinjer og bedste praksis om, hvordan du opretter brugerdefineret scripthandlinger ved hjælp af hjælper metoder og giver retningslinjer om, hvordan du teste handlingen script. </p></td>
<td>Funktionen generelt tilgængelighed</td>
<td>Alle</td>
<td>I/T.</td>
</tr>


</table>
<br>

## <a name="notes-for-12052014-release-of-hdinsight"></a>Noter til 05-12-2014-udgivelsen af HDInsight

Komplet version tallene for HDInsight klynger implementeret i denne version er:

* HDInsight 2.1.9.406.1221105 (HDP 1.3.9.0-01351)
* HDInsight 3.0.5.406.1221105 (HDP 2.0.9.0-2097)
* HDInsight 3.1.1.406.1221105 (HDP 2.1.9.0-2196)
* HDInsight SDK i/t.

Denne version indeholder følgende komponentopdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Komponent</th>
<th>Klynge Type</th>
<th>JIRA (hvis relevant)</th>
</tr>

<tr>
<td>Rettelse: forbigående ved at tilføje et stort antal partitioner til en tabel i en Hive DDL </td>
<td><p>Hvis der er en vekslende forbindelsesfejl med Hive metastore databasen, når du føjer en masse partitioner til en Hive-tabel, kan den Hive DDL mislykkedes. Følgende sætning vil kunne ses i fejlloggen Hive, hvis fejlen opstår: </p><p>"Fejl [main]: ql. Driver (SessionState.java:printError(547)) - mislykkedes: udførelse af fejl, kode 1 blev returneret fra org.apache.hadoop.hive.ql.exec.DDLTask. MetaException (message:java.lang.RuntimeException: commitTransaction blev kaldt men openTransactionCalls = 0. Sandsynligvis angiver, at der er opgjorte opkald til openTransaction/commitTransaction)"</p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>HIVE-482 (dette er en intern JIRA, så det kan være i anførselstegn eksternt. Angivet her til reference.)</td>
</tr>

<tr>
<td>Rettelse: lejlighedsvise hænger i konsollen HDInsight forespørgsel</td>
<td>Når dette sker, kan følgende sætning ses WebHCat log for WebHCat startprogram sagen: <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): Oversigt over filen {wasb URL-adressen til oversigtsfil,} kunne ikke indlæses "</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>HIVE-482 (dette er en intern JIRA, så det kan være i anførselstegn eksternt. Angivet her til reference.)</td>
</tr>

<tr>
<td>Rettelse: lejlighedsvise samling i ventetid for Hbase forespørgsler</td>
<td>Hvis dette sker, Bemærk brugere en lejlighedsvise samling af 3 sekunder i ventetid for Hbase forespørgsler. </td>
<td>HDInsight klynge Gateway</td>
<td>HBase</td>
<td>I/T.</td>
</tr>

<tr>
<td>HDP JAR fil navneændringer</td>
<td>Klynge version 3.0, der et par ændringerne i de interne glas-filer, der er installeret af HDP for HDI. jetty 6.1.26.jar er blevet erstattet med jetty 6.1.26.hwx.jar. jetty-util-6.1.26.jar er blevet erstattet med jetty-util-6.1.26.hwx.jar. Disse ændringer vedrører Hadoop, Mahout, WebHCat og Oozie projekter.</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>I/T.</td>
</tr>

</table>
<br>


## <a name="notes-for-11212014-release-of-hdinsight"></a>Noter til 11-21-2014-udgivelsen af HDInsight

Komplet version tallene for HDInsight klynger implementeret i denne version er:

* HDInsight 2.1.9.382.1169709 (ikke ændret fra 14-11-2014)
* HDInsight 3.0.5.382.1169709 (ikke ændret fra 14-11-2014)
* HDInsight 3.1.1.382.1169709 (ikke ændret fra 14-11-2014)
* HDINsight SDK 1.4.0

Denne version indeholder følgende komponentopdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Komponent</th>
<th>Klynge Type</th>
<th>JIRA (hvis relevant)</th>
</tr>

<tr>
<td>Adgang til programmet logfiler</td>
<td>Mulighed for at fra et program optælle programmer, der er udført på din klynger og hente relevante program-specifikke eller objektbeholder-specifikke logfiler for at fejlfinde problematisk programmer.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>I/T.</td>
</tr>

<tr>
<td>Muligheden for at angive områdenavn i IHdInsightClient.DeleteCluster </td>
<td>Azure HDInsight SDK giver mulighed for at angive et områdenavn, når du bruger **DeleteCluster**. Dette hjælper med at fjerne blokeringen af kunder, der havde to ressourcer med samme navn i forskellige områder og har været ikke slette en af dem.</td>
<td>SDK</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>Objektet **ClusterDetails** returnerer et **DeploymentID** felt, der repræsenterer et entydigt id for-klyngen. Det er sikkert at være entydige i hele klynge oprettelse af forsøg med de samme navne.</td>
<td>SDK</td>
<td>Alle</td>
<td>I/T.</td>
</tr>
</table>
<br>

## <a name="notes-for-11142014-release-of-hdinsight"></a>Noter til 14-11-2014-udgivelsen af HDInsight

Komplet version tallene for HDInsight klynger implementeret i denne version er:

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Denne version indeholder følgende nye funktioner, komponentopdateringer og fejlrettelser.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Komponent</th>
<th>Klynge Type</th>
<th>JIRA (hvis relevant)</th>
</tr>

<tr>
<td>Scripthandling (Preview)</td>
<td>Eksempel på funktionen klynge tilpasning, der gør det muligt for ændring af Hadoop klynger på vilkårlig måder ved hjælp af brugerdefinerede scripts. Brugere kan eksperimentere med og installere projekter, der er tilgængelige fra Apache Hadoop økosystemet til Azure HDInsight klynger med denne funktion. Tilpasning af funktionen er tilgængelig på alle typer HDInsight klynger, herunder Hadoop, HBase og Storm.</td>
<td>Ny funktion</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>Færdige job til Azure websteder og lagerplads analyse af logfiler</td>
<td>HDInsight forespørgsel konsol har et Introduktion galleri, der understøtter løsninger, der arbejder på dine data eller eksempeldata.
<p>**Løsninger, der virker på dine data**:<br>
Vi har oprettet job for nogle af de mest almindelige scenarier for analyse af data til at levere et udgangspunkt til at oprette dine egne løsninger. Du kan bruge dine data med jobbet til at se, hvordan det fungerer. Når du er klar, bruges hvad du har lært at oprette en løsning, der er udviklet efter færdige jobbet.</p>
<p>**Løsninger, der arbejder på eksempeldata**:<br>
Få mere at vide om at arbejde med HDInsight ved gennemgang af nogle grundlæggende scenarier (såsom analysere weblogfiler og føler data). Du skal lære, hvordan du kan bruge HDInsight til at analysere sådanne data og hvordan du kan oprette forbindelse andre programmer og tjenester til disse data. Visualisere data ved at oprette forbindelse til Microsoft Excel indeholder et eksempel på en potens af denne metode.</p></td>
<td>Forespørgsel console</td>
<td>Hadoop</td>
<td>I/T.</td>
</tr>

<tr>
<td>Hukommelsesfejl fix i Templeton</td>
<td>Et hul i hukommelsen i Templeton, påvirkes kunder, der havde længerevarende klynger eller sende 100s jobs anmoder om et sekund er blevet løst. Der blev problemet kommer til udtryk som Templeton 5xx fejl og problemet at genstarte tjenesten. Problemet ikke længere er nødvendig.</td>
<td>Templeton</td>
<td>Alle</td>
<td>https://Issues.Apache.org/jira/Browse/HADOOP-11248</td>
</tr>
</table>
<br>


**Bemærk**: Hvis du vil vise de nye funktioner, der er gjort tilgængelige ved klynge tilpasning, har er blevet dokumenteret procedurerne ved hjælp af scripthandling til at installere tænding og R moduler på en klynge. Yderligere oplysninger finder du:

* [Installere og bruge knallertmotor 1.0 på HDInsight klynger](hdinsight-hadoop-spark-install.md)
* [Installere og bruge R på HDInsight Hadoop klynger](hdinsight-hadoop-r-scripts.md)




## <a name="notes-for-11072014-release-of-hdinsight"></a>Noter i 11-07-2014 udgave af HDInsight

Komplet version tallene for HDInsight klynger, der installeres med denne version er:

* HDInsight 2.1 2.1.9.374.1153876
* HDInsight 3.0 3.0.5.374.1153876
* HDInsight 3.1 3.1.1.374.1153876

Denne version indeholder følgende komponentopdateringer.

<table border="1">
<tr>
<th>Titel</th>
<th>Beskrivelse</th>
<th>Komponent</th>
<th>Klynge Type</th>
<th>JIRA (hvis relevant)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>Denne version er baseret på Hortonworks Data Platform (HDP) 2.1.7. Du kan finde flere oplysninger <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">Produktbemærkninger til HDP 2.1.7</a>.</td>
<td>HDP</td>
<td>Alle</td>
<td>I/T.</td>
</tr>

<tr>
<td>GARN tidslinje Server</td>
<td>GARN tidslinje Server (også kaldet generisk Application historik Server) er blevet aktiveret som standard. Tidslinje serveren indeholder generelle oplysninger om færdige programmer (som program-ID, programnavn, programmet status, programmet bidrag tid og sluttidspunkt for programmet på computeren).

Dette kan du hente programoplysninger fra noden hoved ved at få adgang til URI http://headnodehost:8188 eller ved at køre kommandoen GARN: garn program – listen – appStates alle.

Disse oplysninger kan også hentes fra en fjernplacering selvom en REST-API på https://{ClusterDnsName}. azurehdinsight.NET/ws/V1/applicationhistory/.

Du kan finde mere detaljerede oplysninger <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">GARN tidslinje Server</a>.</td>
<td>GARN-tjeneste</td>
<td>Hadoop, HBase</td>
<td>I/T.</td>
</tr>

<tr>
<td>Klynge installation-ID</td>
<td>Start med SDK version 1.3.3.1.5426.29232, og brugerne kan få adgang til et entydigt ID for hver klynge, som er udstedt HDInsight. Dette gør det muligt for kunder til at finde ud af entydige forekomster af klynger, når et DNS-navn er der genbruges på tværs oprette eller slip scenarier.</td>
<td>SDK</td>
<td>Alle</td>
<td>I/T.</td>
</tr>
</table>
<br>

**Bemærk**: den fejl, der forhindret fuld versionsnummeret fra dukke op i portalen eller returneres ved SDK eller ved at Windows PowerShell er blevet løst i denne udgave.

## <a name="notes-for-10152014-release"></a>Noter til 10-15-2014-udgivelsen

Denne programrettelse udgave løst et hul i hukommelsen i Templeton, påvirkes tunge brugerne af Templeton. I nogle tilfælde vil brugere, der kræves Templeton stærkt se fejl kommer til udtryk som 500 fejlkoder fordi anmodningerne ikke vil har nok hukommelse til at køre. Løsning til dette problem er at genstarte tjenesten Templeton. Problemet er løst.


## <a name="notes-for-1072014-release"></a>Noter til 7-10-2014-udgivelsen

* Når du bruger Ambari slutpunkt, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", feltet *værtsnavn* returnerer det fulde domænenavn (fulde Domænenavn) af noden i stedet for kun værtsnavn. For eksempel i stedet for at returnere "**headnode0**", du får vist det fulde Domænenavn "**headnode0. {} ClusterDNS} .azurehdinsight .net**". Denne ændring er nødvendige for at lette scenarier, hvor flere klynge typer (såsom HBase og Hadoop) kan installeres i et virtuelt netværk. Dette sker, for eksempel, når du bruger HBase som en back end-platform til Hadoop.

* Vi har fået nyt hukommelsesindstillingerne for standard installationen af HDInsight klynge. Forrige standardindstillingerne for hukommelse ikke en dækkende måde dækker retningslinjer for antallet af CPU-kerner installeres. Hukommelsesindstillingerne kan give bedre standarder (ud fra Hortonworks anbefalinger). Se dokumentationen til SDK reference om at ændre klynge konfiguration for at ændre disse indstillinger. De nye hukommelsesindstillinger, der bruges af standard 4 CPU core (8 objektbeholder) HDInsight klynge er specificeret i den følgende tabel. (Værdierne, der bruges før denne version er også angivet parenthetically.)

<table border="1">
<tr><th>Komponent</th><th>Hukommelsesallokering</th></tr>
<tr><td> yarn.Scheduler.minimum – fordelingen</td><td>768 MB (tidligere 512 MB)</td></tr>
<tr><td> yarn.Scheduler.Maximum – fordelingen</td><td>6144 MB (uændret)</td></tr>
<tr><td>yarn.nodemanager.Resource.Memory</td><td>6144 MB (uændret)</td></tr>
<tr><td>mapreduce.Map.Memory</td><td>768 MB (tidligere 512 MB)</td></tr>
<tr><td>mapreduce.Map.Java.opts</td><td>vælger =-Xmx512m (tidligere - Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.Memory</td><td>1536 MB (tidligere 1024 MB)</td></tr>
<tr><td>mapreduce.reduce.Java.opts</td><td>vælger =-Xmx1024m (tidligere - Xmx819m)</td></tr>
<tr><td>yarn.App.mapreduce.AM.Resource</td><td>768 MB (tidligere 1024 MB)</td></tr>
<tr><td>yarn.App.mapreduce.AM.Command</td><td>vælger =-Xmx512m (tidligere - Xmx819m)</td></tr>
<tr><td>mapreduce.Task.IO.sort</td><td>256 MB (tidligere 200 MB)</td></tr>
<tr><td>tez.AM.Resource.Memory</td><td>1536 MB (uændret)</td></tr>

</table><br>

Se [Finde ud af HDP hukommelse-konfigurationsindstillinger](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html)kan finde flere oplysninger om de hukommelse konfigurationsindstillinger, der bruges af GARN og MapReduce på Hortonworks Data-platformen, der bruges af HDInsight. Hortonworks angivet også et værktøj til at beregne stort hukommelsesindstillinger.

Om Azure PowerShell og HDInsight SDK fejlmeddelelsen: "*klynge er ikke konfigureret til HTTP-tjenester adgang*":

* Denne fejl er et kendt [problem, der er kompatibilitetsproblemer](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) , der kan opstå på grund af en forskel i versionen af HDInsight SDK eller Azure PowerShell og versionen af klyngen. Klynger, der er oprettet på 8/15 eller nyere har understøttelse af nye klargøring funktion i virtuelle netværk. Men denne funktion er ikke korrekt fortolket af ældre versioner af HDInsight SDK eller Azure PowerShell. Resultatet er en fejl i nogle job bidrag handlinger. Hvis du bruger HDInsight SDK API'er eller Azure PowerShell-cmdlet'er (**Brug AzureRmHDInsightCluster** eller **Aktiver AzureRmHDInsightHiveJob**) til at sende job, disse handlinger mislykkes muligvis en fejlmeddelelse om "*klynge <clustername> er ikke konfigureret til HTTP-tjenester adgang*." Eller (afhængigt af handlingen), kan du få vist andre fejlmeddelelser, som "*kan ikke oprette forbindelse til klynge*".

* Disse kompatibilitetsproblemer er løst i de seneste versioner af HDInsight SDK og Azure PowerShell. Vi anbefaler, at opdatere HDInsight SDK til version 1.3.1.6 eller nyere og Azure PowerShell-værktøjer til version 0.8.8 eller nyere. Du kan få adgang til de seneste HDInsight SDK fra [](http://nuget.codeplex.com/wikipage?title=Getting%20Started) og Azure PowerShell værktøjerne på, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).



## <a name="notes-for-9122014-release-of-hdinsight-31"></a>Noter til 9-12-2014-udgivelsen af HDInsight 3.1

* Denne version er baseret på Hortonworks Data Platform (HDP) 2.1.5. Finde en liste over de fejl, der er rettet i denne udgave, på siden [fast i denne udgave](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) på webstedet Hortonworks.
* I mappen gris biblioteker er blevet ændret filen "avro-mapred-1.7.4.jar" til "avro-mapred-1.7.4-hadoop2.jar." Indholdet af denne fil indeholder en mindre rettelse, som er hårde. Det anbefales, at kunder ikke kan gøre en direkte afhængighed på navnet på filen glas for at undgå sideskift, når filer omdøbes.


## <a name="notes-for-8212014-release"></a>Noter til 8-21-2014-udgivelsen

* Vi tilføjer følgende WebHCat konfigurationen (HIVE-7155), som angiver den standard hukommelse grænse for et Templeton controller job til 1 GB. (Tidligere standardværdien var 512 MB).

     templeton.mapper.Memory.MB (= 1024)

    * Denne ændring adresser følgende fejl, som visse Hive forespørgsler havde køres til på grund af nederste hukommelse begrænsninger: "Objektbeholder kører ud over fysisk hukommelsesgrænser".
    * For at vende tilbage til gamle standardindstillingerne, kan du angive denne konfigurationsværdi til 512 via Azure PowerShell på klokkeslættet for oprettelsen af klynge ved hjælp af følgende kommando:

        Tilføje AzureRmHDInsightConfigValues-Core@{"templeton.mapper.memory.mb"="512";}


* Værtsnavnet for rollen zookeeper blev ændret til *zookeeper*. Dette påvirker navneoversættelse inden for klyngen, men den påvirker ikke eksterne REST API'er. Hvis du har komponenter, der bruger værtsnavnet *zookeepernode* , skal du opdatere dem for at bruge nye navn. De nye navne for de tre zookeeper noder er:
    * zookeeper0
    * zookeeper1
    * zookeeper2
* HBase version support matrix opdateres. Kun HDInsight version 3.1 (HBase version 0,98) understøttes for fremstilling for HBase arbejdsmængder. Version 3.0 (der var tilgængelig til preview) understøttes ikke flytte fremad.

## <a name="notes-about-clusters-created-prior-to-8152014"></a>Noter om klynger oprettet før 8-15-2014

Fejlmeddelelsen Azure PowerShell eller HDInsight SDK "klynge <clustername> er ikke konfigureret til HTTP-tjenester adgang" (eller afhængigt af handlingen, andre fejlmeddelelser f.eks.: "Kan ikke oprette forbindelse til klynge") kan opstå på grund af en version forskellen mellem Azure PowerShell eller HDInsight SDK og en klynge. Klynger, der er oprettet på 8/15 eller nyere har understøttelse af nye klargøring funktion i virtuelle netværk. Denne funktion ikke er korrekt fortolket af ældre versioner af Azure PowerShell eller HDInsight SDK, der medfører fejl i jobbet bidrag handlinger. Hvis du bruger HDInsight SDK API'er eller Azure PowerShell-cmdletter (såsom Brug AzureRmHDInsightCluster eller aktiver AzureRmHDInsightHiveJob) til at sende job, mislykkes disse handlinger med en af de fejlmeddelelser, der er beskrevet.

Disse kompatibilitetsproblemer er løst i de seneste versioner af HDInsight SDK og Azure PowerShell. Vi anbefaler, at opdatere HDInsight SDK til version 1.3.1.6 eller nyere og Azure PowerShell-værktøjer til version 0.8.8 eller nyere. Du kan få adgang til de seneste HDInsight SDK fra [NuGet][nuget-link]. Du kan få adgang til Azure PowerShell værktøjer ved hjælp af [Microsoft Web Platform Installer][webpi-link].


## <a name="notes-for-7282014-release"></a>Noter til 7/28/2014-udgivelsen

* **HDInsight, der er tilgængelige i nye områder**: Vi udvidet HDInsight geografiske tilstedeværelse til tre områder. HDInsight kunder kan oprette klynger i disse områder:
    * Sydøstasien
    * Nord centrale USA
    * Syd centrale USA
* HDInsight version 1,6 (HDP 1.1 og Hadoop 1.0.3) og HDInsight version 2.1 (HDP1.3 og Hadoop 1.2) der fjernes fra Azure-portalen. Du kan fortsætte med at oprette Hadoop klynger for disse versioner ved hjælp af Azure PowerShell-cmdlet, [Ny AzureRmHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx) eller ved hjælp af [HDInsight SDK](http://msdn.microsoft.com/library/azure/dn469975.aspx). Se siden [HDInsight komponent versionsstyring](hdinsight-component-versioning.md) kan finde flere oplysninger.
* Hortonworks Data Platform (HDP) ændringer i denne version:

<table border="1">
<tr><th>HDP</th><th>Ændringer</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Ingen ændringer</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Ingen ændringer</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: [3.4.5.2.1.3.0-1948] -> [3.4.5.2.1.3.2-0002]</td></tr>


</table><br>

## <a name="notes-for-6242014-release"></a>Noter til 24-6/2014-udgivelsen

Denne version indeholder forbedringer til HDInsight-tjenesten:

* **HDP 2.1 tilgængelighed**: HDInsight 3.1 (som indeholder HDP 2.1) er alment tilgængelig og er standardversionen for nye klynger.
* **HBase – Azure portalen forbedringer**: Vi gør HBase klynger tilgængelige i Preview. Du kan oprette HBase klynger fra portalen med blot nogle få klik. 

Du kan oprette en række forskellige realtid arbejdsbelastninger på HDInsight, med HBase, fra interaktive websteder, der arbejder med store datasæt til lagring af føler og telemetri data fra millioner af slutpunkter-tjenester. Næste trin er at analysere dataene i disse arbejdsmængder med Hadoop-job, og dette er muligt i HDInsight via Azure PowerShell og Hive klynge dashboard.

### <a name="apache-mahout-preinstalled-on-hdinsight-31"></a>Apache Mahout forudinstalleret på HDInsight 3.1

 [Mahout](http://hortonworks.com/hadoop/mahout/) er forudinstalleret på HDInsight 3.1 Hadoop klynger, så du kan køre Mahout job uden brug af flere klynge konfiguration. For eksempel kan du remote til et Hadoop-klynge ved hjælp af Remote Desktop Protocol (RDP), og uden yderligere trin, kan du køre følgende kommando for Hej verden Mahout:

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Se i dokumentationen til [Breiman eksempel](https://mahout.apache.org/users/classification/breiman-example.html) på webstedet Apache Mahout for en mere fyldestgørende beskrivelse af denne procedure.


### <a name="hive-queries-can-use-tez-in-hdinsight-31"></a>Hive forespørgsler kan bruge Tez i HDInsight 3.1

Hive 0,13 er tilgængelig i HDInsight 3.1, og det er i stand til at køre forespørgsler med Tez, som kan bruges til forbedring af væsentlig ydeevnen.
Tez er ikke Aktivér som standard for Hive forespørgsler. Hvis du vil bruge den, skal du vælge i. Du kan aktivere Tez ved at køre følgende kodestykke:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks har udgivet en detaljeret opdeling af Hive forbedringer af ydeevnen for forespørgsel med Tez, som leveres i standardtest. Yderligere oplysninger finder du [udviklingskurver Apache Hive 13 for Enterprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/).

Få mere at vide om at bruge Hive med Tez se [Hive på Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###<a name="global-availability"></a>Global tilgængelighed
Med versionen af HDInsight på Hadoop 2.2 har Microsoft gjort HDInsight tilgængelig i alle overordnede lande, hvor Azure er tilgængelig. Vest Europa og Sydøstasien datacentre er især blevet ført online. Dette gør det muligt for kunder til at finde klynger i et datacenter, der er tæt og potentielt i en zone med lignende overholdelseskrav.


###<a name="dos--donts-between-cluster-versions"></a>Råd og 's mellem klynge versioner

**Oozie metastores bruges sammen med en HDInsight 3.1 klynge er ikke bagudkompatible med HDInsight 2.1 klynger, og de kan ikke bruges med denne tidligere version**.

En brugerdefineret Oozie metastore database, der er installeret med en HDInsight 3.1 klynge kan genbruges med en HDInsight 2.1 klynge. Det er tilfældet, selvom metastore stammer med en HDInsight 2.1 klynge. Dette scenarie understøttes ikke, fordi metastore skemaet bliver opgraderet, når den bruges med en HDInsight 3.1 klynge, så det er ikke længere er kompatible med metastore kræves, før HDInsight 2.1 klynger. Ethvert forsøg på at genbruge en Oozie metastore, der er blevet brugt med en HDInsight 3.1 klynge gengives muligvis HDInsight 2.1 klynge ubrugelig.

**Oozie metastores kan ikke deles på tværs af klynger.**

Oozie metastores er knyttet til bestemte klynger, og de kan deles på tværs af klynger.

###<a name="breaking-changes"></a>Bryd ændringer

**Præfiks syntaks**: kun den "wasbs: / /" syntaks understøttes i HDInsight 3.1 og 3.0 klynger. De ældre "asv: / /" syntaks understøttes i HDInsight 2.1 og 1,6 klynger, men det understøttes ikke i HDInsight 3.1 eller 3.0 grupper. Det betyder, at et job, der er sendt til en HDInsight 3.1 eller 3.0 klynge, der eksplicit bruger den "asv: / /" syntaksen mislykkes. Den "wasbs: / /" syntaksen skal bruges i stedet. Desuden job, der er sendt til en hvilken som helst HDInsight 3.1 eller 3.0 grupper, der er oprettet med en eksisterende metastore, der indeholder eksplicitte referencer til ressourcer ved hjælp af den "asv: / /" syntaksen mislykkes. Disse metastores skal oprettes igen med den "wasbs: / /" syntaksen til adresse ressourcer.


**Porte**: porte, der bruges af tjenesten HDInsight er blevet ændret. De portnumre, der blev brugt blev inden for portområdet kortvarige til Windows-operativsystemet. Porte tildeles automatisk fra et foruddefineret kortvarige område for forbigående Internet protocol-baseret kommunikation. Det nye sæt af tilladte Hortonworks Data Platform (HDP) service port tal er uden for dette interval til undgår konflikter, der kan opstå med porte, der bruges af services kører på den hovedsæde node. De nye portnumre skal ikke få de seneste ændringer. De tal, der bruges er som følger:

 **HDInsight 1,6 (HDP 1.1)**
<table border="1">
<tr><th>Navn</th><th>Værdi</th></tr>
<tr><td>DFS.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>DFS.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.IPC.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>DFS.Secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.Tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.Task.Tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.1 og 3.0 (HDP 2.1 og 2.0)**
<table border="1">
<tr><th>Navn</th><th>Værdi</th></tr>
<tr><td>DFS.namenode.HTTP-adresse</td><td>namenodehost:30070</td></tr>
<tr><td>DFS.namenode.HTTPS-adresse</td><td>headnodehost:30470</td></tr>
<tr><td>DFS.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.IPC.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>DFS.namenode.Secondary.HTTP-adresse</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.WebApp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###<a name="dependencies"></a>Afhængigheder

Følgende afhængigheder blev tilføjet i HDInsight 3.x (HDP2.x):

* guice servlet
* optiq core
* javax.inject
* Aktivering
* jsr305
* geronimo-jaspic_1.0_spec
* jul-slf4j
* Java-xmlbuilder
* vant
* Commons-compileren
* jdo-api
* Commons-math3
* paranamer
* jaxb impl
* stringtemplate
* eigenbase xom
* Jersey servlet
* Commons-eksekverbar
* jaxb-api
* jetty-alle-server
* janino
* xercesImpl
* optiq avatica
* jta
* eigenbase-egenskaber
* groovy-all
* hamcrest core
* mail
* linq4j
* jpam
* Jersey-klient
* aopalliance
* geronimo-annotation_1.0_spec
* vant-starteren
* Jersey guice
* XML-API'er
* stax-api
* Asm commons
* Asm-træet
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* overførselshastighed
* udtømme
* snappy java
* jetty-all
* Commons-dbcp

Følgende afhængigheder ikke længere findes i HDInsight 3.x (HDP2.x):

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* JSON
* Core
* jdo2-api
* avro mapred
* datanucleus dyrets
* JSP
* Commons-logføring-api
* Commons-matematik
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

###<a name="version-changes"></a>Versionsændringer

Følgende versionsændringer foretaget mellem HDInsight 2.x (HDP1.x) og HDInsight 3.x (HDP2.x):

* målepunkter core-: [2.1.2] -> [3.0.0]
* derbynet: [10.4.2.0] -> [10.10.1.1]
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper compileren: [5.5.12] -> [5.5.23]
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: [10.4.2.0] -> [10.10.1.1]
* httpcore: [4.2.4] -> [4.2.5]
* hsqldb: [1.8.0.10] -> [2.0.0]
* jets3t: [0.6.1] -> [0.9.0]
* protobuf java: [2.4.1] -> [2.5.0]
* Derby: [10.4.2.0] -> [10.10.1.1]
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* Commons-daemon: [1.0.1] -> [1.0.13]
* datanucleus core-: [3.0.9] -> [3.2.10]
* datanucleus-api-jdo: [3.0.7] -> [3.2.6]
* zookeeper: [3.4.5.1.3.9.0-01320] -> [3.4.5.2.1.3.0-1948]
* bonecp: [0.7.1.RELEASE] -> ['
* 0.8.0.RELEASE']


### <a name="drivers"></a>Drivere
Java Database Connnectivity (JDBC)-driver til SQL Server bruges internt af HDInsight og bruges ikke til eksterne operationer. Hvis du vil oprette forbindelse til HDInsight ved hjælp af Open Database Connectivity (ODBC), skal du bruge Microsoft Hive ODBC-driver. Du kan finde flere oplysninger [Forbinder du Excel til HDInsight med Microsoft Hive ODBC-Driver](hdinsight-connect-excel-hive-odbc-driver.md).


### <a name="bug-fixes"></a>Fejlrettelser

Vi har opdateret følgende HDInsight versioner med flere fejlrettelser med denne version:

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## <a name="hortonworks-release-notes"></a>Hortonworks produktbemærkninger

Produktbemærkninger til de Hortonworks Data-platforme (HDPs), der bruges af HDInsight version klynger findes på følgende placeringer:

* HDInsight version 3.1 bruger et Hadoop-fordeling, der er baseret på [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Dette er den Hadoop klynge, der er oprettet, da ved hjælp af portalen Azure efter 11-7-2014. HDInsight 3.1 klynger blev oprettet, inden 11-7-2014 er baseret på [Hortonworks Data Platform 2.1.1][hdp-2-1-1]

* HDInsight version 3.0 bruger et Hadoop-fordeling, der er baseret på [Hortonworks Data Platform 2.0][hdp-2-0-8].

* HDInsight version 2.1 bruger et Hadoop-fordeling, der er baseret på [Hortonworks Data Platform 1.3][hdp-1-3-0].

* HDInsight version 1,6 bruger et Hadoop-fordeling, der er baseret på [Hortonworks Data Platform 1.1][hdp-1-1-0].

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
 
