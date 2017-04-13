<properties
    pageTitle="Brug scripthandling til at installere Solr på Hadoop klynge | Microsoft Azure"
    description="Lær, hvordan du tilpasser HDInsight klynge med Solr ved hjælp af Script handlingen."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Installere og bruge Solr på HDInsight Hadoop klynger

Lær, hvordan du tilpasser Windows-baseret HDInsight klynge med Solr ved hjælp af Script handlingen, og hvordan du kan bruge Solr til at søge data. Se oplysninger om brug af Solr med en Linux-baserede klynge, [installation og brug Solr på HDinsight Hadoop-klynger (Linux)](hdinsight-hadoop-solr-install-linux.md).
 
Du kan installere Solr på alle typer klynge (Hadoop Storm HBase, og knallertmotor) på Azure HDInsight ved hjælp af *Scripthandling*. Et eksempel på script til at installere Solr på en HDInsight klynge er tilgængelig fra en skrivebeskyttet Azure lagerplads blob på [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). 

Eksempelscriptet fungerer kun med HDInsight klynge version 3.1. Du kan finde flere oplysninger om HDInsight klynge versioner, [HDInsight klynge versioner](hdinsight-component-versioning.md).

Eksempelscriptet bruges i dette emne opretter en Windows-baseret Solr klynge med en bestemt konfiguration. Hvis du vil konfigurere Solr klynge med forskellige samlinger, shards, skemaer, replikaer, osv., skal du redigere script og Solr binære filer i overensstemmelse hermed.

**Relaterede artikler**

- [Installere og bruge Solr på HDinsight Hadoop klynger (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Oprette Hadoop klynger i HDInsight](hdinsight-provision-clusters.md): generelle oplysninger om oprettelse af HDInsight klynger.
- [Tilpasse HDInsight klynge ved hjælp af scripthandling][hdinsight-cluster-customize]: generelle oplysninger om tilpasning af HDInsight klynger ved hjælp af Script handlingen.
- [Udvikle scripthandling scripts til HDInsight](hdinsight-hadoop-script-actions.md).


## <a name="what-is-solr"></a>Hvad er Solr?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> er en enterprise search-platform, der gør det muligt for effektiv søgning i hele teksten på data. Mens Hadoop giver mulighed for at gemme og administrere store datamængder, indeholder Apache Solr søgefunktioner til hurtigt at hente dataene. 

## <a name="install-solr-using-portal"></a>Installere Solr ved hjælp af portalen

1. Begynde at oprette en klynge ved hjælp af indstillingen **Opret brugerdefineret** , som beskrevet på [oprette Hadoop klynger i HDInsight](hdinsight-provision-clusters.md#portal).
2. Klik på **Tilføj scripthandling** for at vise detaljer om handlingen script på siden **Scripthandlinger** i guiden, som vist nedenfor:

    ![Brug scripthandling til at tilpasse en klynge] (./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Brug scripthandling til at tilpasse en klynge")

    <table border='1'>
        <tr><th>Egenskaben</th><th>Værdi</th></tr>
        <tr><td>Navn</td>
            <td>Angiv et navn for handlingen script. For eksempel <b>Installere Solr</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Angiv URI Uniform Resource Identifier () til det script, der er aktiveret for at tilpasse klyngen. Eksempel: <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Node af Type</td>
            <td>Angiv noderne, hvor tilpasning scriptet skal køres. Du kan vælge <b>alle noder</b>, <b>hoved noder kun</b>eller <b>arbejder noder kun</b>.
        <tr><td>Parametre</td>
            <td>Angiv parametrene, hvis det er nødvendigt ved scriptet. Scriptet til at installere Solr kræver ikke eventuelle parametre, så du kan lade feltet være tomt.</td></tr>
    </table>

    Du kan tilføje mere end én scripthandling for at installere flere komponenter på klyngen. Når du har tilføjet scriptene, skal du klikke på markeringen for at starte oprettelsen af klynge.


## <a name="use-solr"></a>Brug Solr

Du skal starte med indeksering Solr med nogle datafiler. Du kan derefter bruge Solr til at køre søgeforespørgsler på indekserede dataene. Udfør følgende trin for at bruge Solr i en HDInsight klynge:

1. **Brug RDP Remote Desktop Protocol () til remote i HDInsight klynge med Solr, der er installeret**. Aktivere Fjernskrivebord for den klynge, du har oprettet med Solr installeret, og derefter remote i klyngen fra portalen Azure. Du kan finde instruktioner, [Opret forbindelse til HDInsight klynger ved hjælp af RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. **Indeks Solr ved at uploade datafiler**. Når du indekserer Solr, kan du lægge dokumenter i den, der muligvis skal søge efter. Hvis du vil indeksere Solr, bruge RDP til remote i klyngen, gå til skrivebordet, Åbn kommandolinjen Hadoop, og gå til **C:\apps\dist\solr-4.7.2\example\exampledocs**. Kør følgende kommando:

        java -jar post.jar solr.xml monitor.xml

    Du får vist i følgende eksempel på konsollen:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    Værktøjet post.jar indekserer Solr med dobbelt stikprøve dokumenter, **solr.xml** og **monitor.xml**. Værktøjet post.jar og eksempeldokumenter er tilgængelige med Solr installation.

3. **Brug Solr dashboardet bruges til at søge i de indekserede dokumenter**. Åbn Internet Explorer i RDP-sessionen til HDInsight klynge, og start dashboardet Solr på **http://headnodehost:8983/solr / #/**. Vælg **collection1**i venstre rude, fra rullelisten **Core Datavælger** , og klik på **forespørgsel**inden for den pågældende. Angiv følgende værdier for at markere og returnere alle dokumenter i Solr, som et eksempel:

    * I tekstfeltet **spørgsmål** skal du angive ** \*:**\*. Følgende returnerer alle de dokumenter, der er indekseret i Solr. Hvis du vil søge efter et bestemt tekststreng i dokumenterne, kan du angive strengen her.
    
    * Vælg outputformatet i tekstfeltet **wt** . Standard er **json**. Klik på **forespørgslen køres**.

    ![Brug scripthandling til at tilpasse en klynge] (./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Køre en forespørgsel på Solr dashboardet")
    
    Output returnerer de to dokumenter, vi bruges til indeksering Solr. Output ligner følgende:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }


4. **Anbefales: sikkerhedskopiere indekserede dataene fra Solr til Azure Blob-lager, der er knyttet til HDInsight klyngen**. Som en god ide, bør du sikkerhedskopiere indekserede dataene fra Solr klyngenoder til Azure Blob-lager. Udfør følgende trin for at gøre det:

    1. Åbn Internet Explorer fra RDP-session, og peg på følgende URL-adressen:

            http://localhost:8983/solr/replication?command=backup

        Du burde se et svar således:

            <?xml version="1.0" encoding="UTF-8"?>
            <response>
              <lst name="responseHeader">
                <int name="status">0</int>
                <int name="QTime">9</int>
              </lst>
              <str name="status">OK</str>
            </response>

    2. I fjernsession, gå til {SOLR_HOME}\{samling} \data. Dette skal være **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**for den klynge, der er oprettet, via eksempelscriptet. På denne placering, skal du se en snapshot-mappe, der er oprettet med et navn, der svarer til * *øjebliksbillede.* tidsstempel***.

    3. ZIP-mappen snapshot, og Overfør den til Azure Blob-lager. Naviger til placeringen af mappen snapshot ved hjælp af følgende kommando fra kommandolinjen Hadoop:

              hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

        Denne kommando kopierer snapshot til /example/data/under objektbeholderen i standard lagerplads konto, der er knyttet til klyngen.

## <a name="install-solr-using-aure-powershell"></a>Installere Solr ved hjælp af Aure PowerShell

Se [tilpasse HDInsight klynger ved hjælp af Script handlingen](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  Eksemplet viser, hvordan du installerer knallertmotor med Azure PowerShell. Du har brug at tilpasse scriptet for at bruge [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Installere Solr ved hjælp af .NET SDK

Se [tilpasse HDInsight klynger ved hjælp af Script handlingen](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). Stikprøvernes viser, hvordan du installerer knallertmotor ved hjælp af .NET SDK. Du har brug at tilpasse scriptet for at bruge [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).



## <a name="see-also"></a>Se også

- [Installere og bruge Solr på HDinsight Hadoop klynger (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Oprette Hadoop klynger i HDInsight](hdinsight-provision-clusters.md): generelle oplysninger om oprettelse af HDInsight klynger.
- [Tilpasse HDInsight klynge ved hjælp af scripthandling][hdinsight-cluster-customize]: generelle oplysninger om tilpasning af HDInsight klynger ved hjælp af Script handlingen.
- [Udvikle scripthandling scripts til HDInsight](hdinsight-hadoop-script-actions.md).
- [Installere og bruge knallertmotor på HDInsight klynger][hdinsight-install-spark]: scripthandling eksempel om installation af knallertmotor.
- [Installere R på HDInsight klynger][hdinsight-install-r]: scripthandling eksempel om installation af R.
- [Installere Giraph på HDInsight klynger](hdinsight-hadoop-giraph-install.md): scripthandling eksempel om installation af Giraph.


[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
