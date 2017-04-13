<properties
    pageTitle="Hvad er de forskellige komponenter, der er tilgængelige med en HDInsight klynge? | Microsoft Azure"
    description="HDInsight understøtter flere installeres Hadoop klyngekomponenter og versioner. Se de Hadoop og HortonWorks Data Platform (HDP) fordeling versioner understøttes."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jgao"/>


# <a name="what-are-the-different-hadoop-components-available-with-hdinsight"></a>Hvad er de forskellige Hadoop-komponenter, der er tilgængelige med HDInsight?

Lær mere om de anden tjeneste niveauer, som HDInsight samt versionerne af forskellige hadoop-komponenter, der følger med HDInsight.

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard- og HDInsight Premium

Azure HDInsight giver stor data skyen tilbud i to kategorier: **Standard** og **Premium**. Tabellen nedenfor sektion viser en liste over de funktioner, der er tilgængelige **kun som en del af Premium**. Funktioner, der ikke er eksplicit fremhævet i tabellen her er tilgængelige som en del af Standard.

>[AZURE.NOTE] Den HDInsight Premium med tilbud om er i øjeblikket i Vis udskrift og kun tilgængelig for Linux klynger.

| HDInsight Premium funktion | Beskrivelse |
|--------------|---------------|
| Medlem af et domæne HDInsight klynger       | Deltage i HDInsight klynger til Azure Active Directory (AAD) domæner til enterprise-sikkerhed på brugerniveau. Nu kan du konfigurere en liste over medarbejdere fra din virksomhed, der kan godkende via Azure Active Directory til at logge på HDInsight klynge. Enterprise-administrator kan også konfigurere rolle baseret adgangskontrol for Hive sikkerhed ved hjælp af [Apache Ranger](http://hortonworks.com/apache/ranger/), og som derfor begrænse adgang til data, der skal kun som nogenlunde samme måde som det er nødvendigt. Til sidst skal kan administratoren overvåge de data, der åbnes af medarbejdere, og de ændringer, der er udført politik for adgangskontrol, således at opnå en høj grad af styring af deres virksomhedsressourcer. Se [konfigurere medlem af et domæne HDInsight klynger](hdinsight-domain-joined-configure)kan finde flere oplysninger.

### <a name="cluster-types-supported-for-premium"></a>Klynge datatyper understøttes for Premium

I følgende tabel vises den HDInsight klynge type og Premium support matrix.

| Klynge type | Standard  | Premium |
|--------------|---------------|--------------|
| Hadoop       | Ja           | Ja (kun HDInsight 3.5)         |
| Knallertmotor        | Ja           | Nej          |
| HBase        | Ja           | Nej           |
| Storm        | Ja           | Nej           |
| Interaktive Hive (Preview) | Ja | Nej |
| R Server (Preview) | Ja | Nej |

I denne tabel opdateres, når flere klynge typer er inkluderet i HDInsight Premium.

### <a name="pricing-and-sla"></a>Priser og SERVICENIVEAUAFTALE

Finde oplysninger om priser og SERVICENIVEAUAFTALE for HDInsight Premium, se [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Hadoop-komponenter, der følger med forskellige HDInsight versioner

Azure HDInsight understøtter flere Hadoop klynge versioner, der kan være installeret, når som helst. Hver version valg opretter en bestemt version for den stokastiske variabel Hortonworks Data Platform (HDP) og et sæt komponenter, der er indeholdt i fordelingen. De komponent versioner, der er knyttet til HDInsight klynge versioner er specificeret i den følgende tabel. Bemærk, at standardversionen klynge bruges af Azure HDInsight er i øjeblikket 3.4, og, og 09-14/2016, baseret på HDP 2.4.

> [AZURE.NOTE] Standardversionen fra tjenesten kan ændres uden varsel. Vi anbefaler, at du angiver versionen, når du opretter klynger ved hjælp af .NET SDK/Azure PowerShell og Azure CLI, hvis du har en version afhængighed. 

Komponent|HDInsight version 3.5 | HDInsight version 3.4 (standard) | HDInsight Version 3.3 | HDInsight Version 3.2 |HDInsight Version 3.1 |HDInsight Version 3.0|
---|---|---|---|---|---|---
Hortonworks Data Platform|2,5|2.4|2.3|2.2|2.1.7|2.0|
Hadoop-Apache og GARN|2.7.3|2.7.1|2.7.1|2.6.0|2.4.0|2.2.0|
Apache Tez|0.7.0|0.7.0|0.7.0 | 0.5.2|0.4.0||
Apache gris|0.16.0|0.15.0|0.15.0|0.14.0|0.12.1|0.12.0|
Apache Hive & HCatalog|1.2.1.2.5|1.2.1|1.2.1|0.14.0|0.13.1|0.12.0|
Apache HBase |1.1.2|1.1.2|1.1.1|0.98.4|0.98.0||
Apache Sqoop|1.4.6|1.4.6|1.4.6|1.4.5|1.4.4|1.4.4|1.4.3
Apache Oozie|4.2.0|4.2.0|4.2.0|4.1.0|4.0.0|4.0.0|
Apache Zookeeper|3.4.6|3.4.6|3.4.6|3.4.6|3.4.5|3.4.5|
Apache Storm|1.0.1|0.10.0|0.10.0|0.9.3|0.9.1||
Apache Mahout|0.9.0+|0.9.0+|0.9.0+|0.9.0|0.9.0||
Apache København|4.7.0|4.4.0|4.4.0|4.2.0|4.0.0.2.1.7.0-2162||
Apache knallertmotor|1.6.2 + 2.0 (kun Linux)|1.6.0 (kun Linux)|1.5.2 (Linux kun/Experimental build)|1.3.1 (kun Windows)|||

**Få aktuelle komponent versionsoplysninger**

De komponent versioner, der er knyttet til HDInsight klynge versioner kan blive ændret i fremtidige opdateringer til HDInsight. En metode til at bestemme de tilgængelige komponenter og til at kontrollere, hvilke versioner bliver brugt til en klynge er at bruge Ambari REST-API. Kommandoen **GetComponentInformation** kan bruges til at hente oplysninger om en tjeneste-komponent. Yderligere oplysninger finder du i [dokumentationen til Ambari][ambari-docs]. En anden måde at få disse oplysninger er at logge på en klynge ved hjælp af Remote Desktop og undersøge indholdet af den "C:\apps\dist\" directory direkte.


**Produktbemærkninger**

Du kan se [HDInsight produktbemærkninger](hdinsight-release-notes.md) til yderligere produktbemærkninger på de nyeste versioner af HDInsight.


## <a name="supported-hdinsight-versions"></a>Understøttede HDInsight versioner
I følgende tabel vises versionerne af HDInsight tilgængelig i øjeblikket, de tilsvarende Hortonworks Data Platform versioner, de bruger og Udgivelsesdato. Når kendt, findes også deres support udløb og ændrede funktionsmåde datoer. Vær opmærksom på følgende:

* Meget tilgængelige klynger med to hoved noder er installeret som standard til HDInsight 2.1 og over. De er ikke tilgængelig for HDInsight 1,6 klynger.
* Når fra support er udløbet for en bestemt version, kan det muligvis ikke tilgængelig via Azure-portalen. Den følgende tabel angiver, hvilke versioner er tilgængelige på klassisk Azure-portalen. Klynge versioner fortsat vil være tilgængelige ved hjælp af den `Version` parameter i kommandoen [Ny AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) for Windows PowerShell og .NET SDK indtil dens ændrede funktionsmåde dato.

HDInsight Version|HDP Version|VM OS|Høj tilgængelighed|Udgivelsesdato|Tilgængelig på Azure-portalen|Udløbsdatoen for support|Ændrede funktionsmåde dato
---|---|---|---|---|---|---|---
HDI 3.5 | HDP 2,5| Ubuntu 16 | Ja | 9-30/2016 | Ja | 
HDI 3.4|HDP 2.4|Ubuntu 14.0.4 LTS|Ja|03-29/2016|Ja|12-29/2016|1/9/2018|
HDI 3.3|HDP 2.3|Ubuntu 14.0.4 LTS eller Windows Server 2012R2|Ja|12-02-2015|Ja|06-27/2016|07-31-2017
HDI 3.2|HDP 2.2|Ubuntu 12.04 LTS eller Windows Server 2012R2|Ja|2-18-2015|Ja|3-1/2016|04-01-2017
HDI 3.1|HDP 2.1|Windows Server 2012R2|Ja|6-24-2014|Nej|05-18-2015|06-30/2016
HDI 3.0|HDP 2.0|Windows Server 2012R2|Ja|02-11-2014|Nej|09-17-2014|06-30-2015
HDI 2.1|HDP 1.3|Windows Server 2012R2|Ja|2013-28/10|Nej|12-05-2014|05-31-2015
HDI 1,6|HDP 1.1||Nej|2013-28/10|Nej|04-26-2014|05-31-2015

**Installation af ikke-standard klynger**

### <a name="the-service-level-agreement-for-hdinsight-cluster-versions"></a>Serviceniveauaftale for HDInsight klynge versioner

SERVICENIVEAUAFTALEN defineres med hensyn til et "Support vindue". Et Support-vindue refererer til tidsrum, der understøttes en HDInsight klynge version af Microsoft kundeservice og Support. En HDInsight klynge er uden for vinduet Support, hvis dens version har en **Support udløbsdato** tidligere dags dato. En liste over understøttede HDInsight klynge versioner kan findes i tabellen ovenfor. Understøttelse af udløbsdatoen for en given HDInsight version X (når der findes en nyere version af X + 1) beregnes som den seneste af:  

- Formlen 1: Tilføje 180 dage til den dato HDInsight klynge version X er blevet frigivet.
- Formel 2: Føje 90 dage til datoen HDInsight klynge version X + 1 (den efterfølgende version efter X) gøres tilgængelige på portalen.

Den **Ændrede funktionsmåde dato** er den dato, hvorefter den klynge version ikke kan oprettes på HDInsight.

> [AZURE.NOTE] Windows-baseret HDInsight klynge (herunder version 2.1, 3.0, 3.1, 3.2 og 3.3) køre på Azure gæst OS familie 4, der bruger den 64-bit version af Windows Server 2012 R2 og understøtter .NET Framework 4.0, 4.5, 4.5.1 og 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Hortonworks produktbemærkninger, der er knyttet til HDInsight versioner##

* HDInsight klynge version 3.4 bruger en Hadoop-fordeling, der er baseret på [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html). Dette er **den Hadoop klynge, der er oprettet, da ved hjælp af portalen** .



* HDInsight klynge version 3.3 bruger en Hadoop-fordeling, der er baseret på [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).
    * Apache Storm produktbemærkninger findes [her](https://storm.apache.org/2015/11/05/storm0100-released.html).
    * Apache Hive produktbemærkninger findes [her](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).

* HDInsight klynge version 3.2 bruger et Hadoop-fordeling, der er baseret på [Hortonworks Data Platform 2.2][hdp-2-2].  

    * Produktbemærkninger til bestemte Apache komponenter - [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450) [gris 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954) [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [København 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [GARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [almindelige](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), og [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).


* HDInsight klynge version 3.1 bruger et Hadoop-fordeling, der er baseret på [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. HDInsight 3.1 klynger blev oprettet, inden 11-7-2014 er baseret på [Hortonworks Data Platform 2.1.1][hdp-2-1-1].

* HDInsight klynge version 3.0 bruger et Hadoop-fordeling, der er baseret på [Hortonworks Data Platform 2.0][hdp-2-0-8].

* HDInsight klynge version 2.1 bruger et Hadoop-fordeling, der er baseret på [Hortonworks Data Platform 1.3][hdp-1-3-0].

* HDInsight klynge version 1,6 bruger et Hadoop-fordeling, der er baseret på [Hortonworks Data Platform 1.1][hdp-1-1-0].


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/
