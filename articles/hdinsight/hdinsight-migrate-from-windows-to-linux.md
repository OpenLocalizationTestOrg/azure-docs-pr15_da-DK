<properties
pageTitle="Overføre fra Windows-baseret HDInsight til Linux-baserede HDInsight | Azure"
description="Lær, hvordan du overfører fra en Windows-baseret HDInsight klynge til en Linux-baserede HDInsight klynge."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/28/2016"
ms.author="larryfr"/>

#<a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Overføre fra en Windows-baseret HDInsight klynge til en Linux-baserede klynge

Windows-baseret HDInsight giver en nem måde at bruge Hadoop i skyen, kan du opleve, at du har brug for en Linux-baserede klynge for at drage fordel af værktøjer og -teknologier, der kræves til din løsning. Mange ting i Hadoop økosystemet udvikles på Linux-baserede systemer, og nogle muligvis ikke tilgængelig til brug sammen med Windows-baseret HDInsight. Desuden forudsætter mange bøger, videoer og andre kursusmateriale, at du bruger et Linux-system, når du arbejder med Hadoop.

Dette dokument indeholder oplysninger om forskellene mellem HDInsight i Windows og Linux og vejledning i at overføre eksisterende arbejdsbelastninger til en Linux-baserede klynge.

> [AZURE.NOTE] HDInsight klynger bruge Ubuntu langtidsaftale support (LTS) som operativsystemet for knuderne i klyngen. Du kan finde oplysninger på versionen af Ubuntu, der er tilgængelige med HDInsight, sammen med andre komponent versionsstyring oplysninger [HDInsight komponent versioner](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Overførselsopgaver

Generelle arbejdsprocessen for overførsel er som følger.

![Overførsel arbejdsprocesdiagram](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1.  Læs hvert afsnit af dette dokument for at forstå ændringer, kan det være nødvendigt når overføre din eksisterende arbejdsproces, job osv til en Linux-baserede klynge.

2.  Oprette en Linux-baserede klynge som en test/kvalitet assurance-miljø. Du kan finde flere oplysninger om oprettelse af en Linux-baserede klynge, [oprette Linux-baserede klynger i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3.  Kopiere eksisterende sager, datakilder og dræn til det nye miljø. Se Kopiér dataene til sektionen test-miljø kan finde flere oplysninger.

4.  Udføre tests, validering for at kontrollere, at dine sager fungerer som forventet på den nye klynge.

Når du har bekræftet, at alt fungerer som forventet, skal du planlægge nedetid for overførslen. Under denne nedetid, skal du gøre følgende handlinger.

1.  Lav sikkerhedskopi af alle midlertidige data, der er gemt lokalt på klyngenoderne. For eksempel, hvis du har gemt data direkte på en hoved node.

2.  Slet den Windows-baseret klynge.

3.  Oprette en Linux-baserede klynge ved hjælp af den samme standard datalager, der bruges på Windows-baseret klynge. Dette gør det nye klynge for at fortsætte med at arbejde med dine eksisterende fremstilling data.

4.  Importere en hvilken som helst midlertidige data, du har sikkerhedskopieret.

5.  Start job/fortsætte med at behandle ved hjælp af ny klynge.

### <a name="copy-data-to-the-test-environment"></a>Kopiere data til testmiljøet

Der er mange metoder til at kopiere data og job, direkte, men de to, der er beskrevet i dette afsnit er de nemmeste metoder til at flytte filer til en test klynge.

#### <a name="hdfs-dfs-copy"></a>HDFS DFS kopi

Du kan bruge kommandoen Hadoop HDFS for at kopiere direkte data fra lagerplads for dit eksisterende fremstilling klynge til lagerplads til en ny test klynge at benytte følgende fremgangsmåde.

1. Finde lagerplads konto og standard objektbeholder oplysninger til din eksisterende klynge. Du kan gøre dette ved hjælp af følgende Azure PowerShell-script.

        $clusterName="Your existing HDInsight cluster name"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
        write-host "Default container: $clusterInfo.DefaultStorageContainer"

2. Følg trinnene i oprette Linux-baserede klynger i HDInsight dokument for at oprette et nyt testmiljø. Stoppe før du opretter klyngen og i stedet vælge **Valgfri konfiguration**.

3. Vælg **Sammenkædede lagerplads konti**bladet valgfri konfiguration.

4. Vælg **Tilføj en lagerplads nøgle**, og når du bliver bedt om det, Vælg den konto, lagerplads, der blev returneret af PowerShell-script i trin 1. Klik på **Vælg** på hvert blade at lukke dem.. Til sidst skal oprette klyngen.

5. Når klyngen er blevet oprettet, kan du oprette forbindelse til den ved hjælp af **SSH.** Hvis du ikke er vant til at bruge SSH med HDInsight, kan du se en af følgende artikler.

    * [Bruge SSH med Linux-baserede HDInsight fra Windows-klienter](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Bruge SSH med Linux-baserede HDInsight fra Linux, Unix og Mac-klienter](hdinsight-hadoop-linux-use-ssh-unix.md)

6. Brug følgende kommando for at kopiere filer fra den sammenkædede lagerplads konto til den nye standardkonto for lagerplads fra session SSH. Erstatte OBJEKTBEHOLDER og konto med objektbeholder og kontooplysninger, der returneres af PowerShell-script i trin 1. Erstat stien til data med stien til en datafil.

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    [AZURE.NOTE] Hvis mappestrukturen, der indeholder dataene, der ikke findes på testmiljøet, kan du oprette den med følgende kommando.

        hdfs dfs -mkdir -p /new/path/to/create

    Den `-p` Skift gør det muligt for oprettelse af alle mapper i stien.

#### <a name="direct-copy-between-azure-storage-blobs"></a>Direkte kopi mellem Azure-lager BLOB

Alternativt kan du overveje at bruge den `Start-AzureStorageBlobCopy` Azure PowerShell-cmdlet til at kopiere BLOB mellem lagerplads konti uden for HDInsight. Du kan finde flere oplysninger, se hvordan til at administrere Azure BLOB sektion af bruge Azure PowerShell med Azure-lager.

##<a name="client-side-technologies"></a>Klientsiden teknologier

Generelt fortsat klientsiden teknologier som [Azure PowerShell-cmdletter](../powershell-install-configure.md), [Azure CLI](../xplat-cli-install.md) eller [.NET SDK for Hadoop](https://hadoopsdk.codeplex.com/) fungerer på samme måde med Linux-baserede klynger, som de skal have REST API'er, der er den samme på tværs af begge typer klynge OS.

##<a name="server-side-technologies"></a>Serversiden teknologier

Den følgende tabel giver vejledning i overføre serversiden komponenter, der er specifikke Windows.

| Hvis du bruger denne teknologi... | Udføre denne handling... |
| ----- | ----- |
| **PowerShell** (serverbaserede scripts, herunder scripthandlinger bruges under oprettelse af klynge) | Omskrivning som fest scripts. For scripthandlinger skal du se [tilpasse Linux-baserede HDInsight med scripthandlinger](hdinsight-hadoop-customize-cluster-linux.md) og [Script handling udvikling for Linux-baserede HDInsight](hdinsight-hadoop-script-actions-linux.md). |
| **Azure CLI** (serverbaserede scripts) | Mens Azure CLI er tilgængelig på Linux, det ikke er forudinstalleret på noderne HDInsight klynge hoved. Hvis du skal bruge den til serverbaserede scripts, du se [installere Azure CLI](../xplat-cli-install.md) oplysninger om installation af på Linux-baserede platforme. |
| **.NET komponenter** | .NET understøttes ikke fuldt ud på Linux-baserede HDInsight klynger. Linux-baserede Storm på HDInsight klynger oprettet efter 10-28-2017 support C# Storm topologier ved hjælp af SCP.NET framework. Yderligere understøttelse af .NET, føjes i fremtidige opdateringer. |
| **Win32-komponenter eller anden kun Windows-teknologi** | Vejledning afhænger af den komponent eller teknologi; Du kan muligvis finde en version, der er kompatibelt med Linux, eller du muligvis finde et alternativ løsning eller omskrive komponenten. |

##<a name="cluster-creation"></a>Oprettelse af klynge

Dette afsnit indeholder oplysninger om forskelle i oprettelse af klynge.

### <a name="ssh-user"></a>SSH bruger

Linux-baserede HDInsight klynger bruger protokollen **Secure Shell (SSH)** til at levere fjernadgang til klyngenoderne. I modsætning til Remote Desktop til Windows-baserede klynger de fleste SSH klienter indeholder ikke en grafisk brugeroplevelse, men i stedet giver en kommandolinje, hvor du kan køre kommandoer på klyngen. Nogle klienter (såsom [MobaXterm](http://mobaxterm.mobatek.net/),) giver en grafisk fil system browser ud over en remote kommandolinjen.

Under oprettelse af klynge, skal du angive en SSH bruger og enten en **adgangskode** eller **et certifikat offentlig nøgle** til godkendelse.

Vi anbefaler ved hjælp af certifikat med offentlig nøgle, som det er mere sikker end ved hjælp af en adgangskode. Certifikatgodkendelse fungerer ved oprettelse af en signeret offentlige/private nøgler par og derefter give den offentlige nøgle, når du opretter klyngen. Når du opretter forbindelse til serveren ved hjælp af SSH, giver den private nøgle på klienten godkendelse for forbindelsen.

Du kan finde flere oplysninger om brug af SSH med HDInsight, i:

- [Bruge SSH med HDInsight fra Windows-klienter](hdinsight-hadoop-linux-use-ssh-windows.md)

- [Bruge SSH med HDInsight fra Linux, Unix og OS X-klienter](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="cluster-customization"></a>Klynge tilpasning

**Scripthandlinger** bruges med Linux-baserede klynger skal skrives i fest script. Mens scripthandlinger kan bruges under oprettelse af klynge, til Linux-baserede klynger kan de også kan bruges til at udføre tilpasning, når en klynge er op at køre. Se [tilpasse Linux-baserede HDInsight med scripthandlinger](hdinsight-hadoop-customize-cluster-linux.md) og [Script handling udvikling for Linux-baserede HDInsight](hdinsight-hadoop-script-actions-linux.md)kan finde flere oplysninger.

Funktionen til tilpasning af en anden er **bootstrap**. Dette kan du angive placeringen af flere biblioteker til brug med Hive til Windows klynger. Efter oprettelse af klynge skal disse biblioteker er automatisk tilgængelige til brug med Hive forespørgsler uden at skulle bruge `ADD JAR`.

Bootstrap til Linux-baserede klynger giver ikke denne funktion. I stedet bruge scripthandling, der er beskrevet i [tilføje Hive biblioteker under oprettelse af klynge](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Virtuelt netværk

Windows-baseret HDInsight klynger kun arbejde med klassisk virtuelle netværk, mens Linux-baserede HDInsight klynger kræver ressourcestyring virtuelle netværk. Hvis du har ressourcer i et klassisk virtuelt netværk, Linux-HDInsight klynge skal oprettes forbindelse til, kan du se [oprette forbindelse til en klassisk virtuelt netværk til et ressourcestyring virtuelle netværk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Du kan finde flere oplysninger om af konfigurationskrav til brug af Azure virtuelle netværk med HDInsight, [udvide HDInsight-funktioner ved hjælp af et virtuelt netværk](hdinsight-extend-hadoop-virtual-network.md).

##<a name="management-and-monitoring"></a>Administration og overvågning

Mange af World Wide web computerens brugergrænseflade, du har brugt med Windows-baseret HDInsight, som jobs eller garn brugergrænseflade, er tilgængelige via Ambari. Desuden giver visningen Hive Ambari en metode til at køre Hive forespørgsler via din webbrowser. Ambari Webbrugergrænsefladen er tilgængelig på Linux-baserede klynger på https://CLUSTERNAME.azurehdinsight.net.

Du kan finde flere oplysninger om at arbejde med Ambari at se de dokumenter, der er følgende:

- [Ambari Web](hdinsight-hadoop-manage-ambari.md)

- [Ambari REST-API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari beskeder

Ambari har en besked om system, der kan fortælle dig om potentielle problemer med klyngen. Beskeder vises som rød eller gul elementer i Brugergrænsefladen i Web Ambari, men du kan også hente dem via REST-API.

> [AZURE.IMPORTANT] Ambari beskeder angiver, der *kan* være et problem, ikke der *er* et problem. For eksempel vises der muligvis en besked, ikke kan åbnes HiveServer2, selvom du har adgang til det normalt.
>
> Mange beskeder er implementeret som en tjeneste interval-baserede forespørgsler og forventer et svar inden for en bestemt tidsramme. Så beskeden ikke nødvendigvis, tjenesten er nede, returnerede ikke blot, at den resultater inden for den forventede tidsramme.

Generelt, skal du evaluere, om en besked er blevet virkelighed for en udvidet periode, eller spejle brugerproblemer, der tidligere er registreret med klyngen, før du udfører handlingen på den.

##<a name="file-system-locations"></a>Placering af system

Linux radialklyngebaseret filsystemet er anderledes end Windows-baseret HDInsight klynger kortformat. Bruge tabellen nedenfor til at finde ofte anvendte filer.

| Jeg har brug at finde... | Det er placeret... |
| ----- | ----- |
| Konfiguration | `/etc`. For eksempel`/etc/hadoop/conf/core-site.xml` |
| Logfiler | `/var/logs` |
| Hortonworks Data Platform (HDP) | `/usr/hdp`. Der er to mapper findes her, er den aktuelle version af HDP (for eksempel `2.2.9.1-1`,) og `current`. Den `current` directory indeholder symbolic links til filer og mapper, der findes i mappen version tal, og der leveres som en nem måde at få adgang til HDP filer siden versionen tal ændres, efterhånden som HDP versionen opdateres. |
| hadoop-streaming.jar | `/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

Generelt, hvis du kender navnet på fil, kan du bruge følgende kommando fra en SSH session til at finde stien til filen:

    find / -name FILENAME 2>/dev/null

Du kan også bruge jokertegn filnavnet. For eksempel `find / -name *streaming*.jar 2>/dev/null` returnerer stien til en hvilken som helst glas filer, der indeholder ordet streaming som en del af filnavnet.

##<a name="hive-pig-and-mapreduce"></a>Hive, gris og MapReduce

Gris og MapReduce arbejdsmængder ligner meget de på Linux-baserede klynger – den største forskel er, hvis du bruger Remote Desktop til at oprette forbindelse til en Windows-baseret klynge og køre job, kan du oftest bruger SSH med Linux-baserede klynger.

- [Brug gris med SSH](hdinsight-hadoop-use-pig-ssh.md)

- [Bruge MapReduce med SSH](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

Det følgende diagram giver vejledning i at overføre din Hive arbejdsmængder.

| På Windows-baseret, jeg bruge... | På Linux-baserede... |
| ----- | ----- |
| **Hive-Editor** | [Hive visning i Ambari](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;`aktivere Tez | Tez er udførelse af standardprogrammet til Linux-baserede klynger, så set-sætningen ikke længere er nødvendig. |
| CMD filer eller scripts på serveren gældende som en del af en Hive-job | bruge fest scripts |
| `hive`kommandoen fra Fjernskrivebord | Brug [Beeline](hdinsight-hadoop-use-hive-beeline.md) eller [Hive fra en SSH session](hdinsight-hadoop-use-hive-ssh.md) |

##<a name="storm"></a>Storm

| På Windows-baseret, jeg bruge... | På Linux-baserede... |
| ----- | ----- |
| Storm Dashboard | Dashboardet Storm er ikke tilgængelig. Se [Implementer og administrere Storm topologier på Linux-baserede HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md) for måder til at sende topologier |
| Storm brugergrænseflade | Der findes på https://CLUSTERNAME.azurehdinsight.net/stormui Storm-brugergrænseflade |
| Visual Studio til at oprette, installere og administrere C# eller hybrid topologier | Visual Studio kan bruges til at oprette, installere og administrere C# (SCP.NET) eller hybrid topologier på Linux-baserede Storm på HDInsight klynger oprettet efter 10-28-2017. |

##<a name="hbase"></a>HBase

Linux-baserede klynger, er overordnet indholdstype znode for HBase `/hbase-unsecure`. Du skal angive dette i sektionen konfiguration for en hvilken som helst Java-klienten programmer, der bruger oprindelige HBase Java API.

Se [opbygge et Java-baserede HBase program](hdinsight-hbase-build-java-maven.md) til en eksempel-klient, der angiver denne værdi.

##<a name="spark"></a>Knallertmotor

Knallertmotor klynger var tilgængelige på Windows-klynger under eksempelvisning; dog for release er knallertmotor kun tilgængelig med Linux-baserede klynger. Der er ingen migreringsstien fra en Windows-baseret knallertmotor preview klynge til en overgang Linux-baserede knallertmotor klynge.

##<a name="known-issues"></a>Kendte problemer

### <a name="azure-data-factory-custom-net-activities"></a>Azure Data Factory brugerdefinerede .NET-aktiviteter

Azure Data Factory brugerdefinerede .NET aktiviteter understøttes ikke i øjeblikket på Linux-baserede HDInsight klynger. I stedet skal du bruge en af følgende metoder til at implementere brugerdefinerede aktiviteter som en del af din ADF pipeline.

-   Udføre .NET aktiviteter på Azure batchen puljen. Se afsnittet Brug Azure batchen sammenkædet tjeneste i [Brug brugerdefinerede aktiviteter i en Azure Data Factory pipeline](../data-factory/data-factory-use-custom-activities.md#AzureBatch)

-   Implementere aktivitet som en MapReduce aktivitet. Du kan få flere oplysninger i [Kalde MapReduce programmer fra Data Factory](../data-factory/data-factory-map-reduce.md) .

### <a name="line-endings"></a>Slutningen af linjen

Linje slutningen på Windows-baserede systemer bruger generelt CRLF, mens Linux-baserede systemer bruger LF. Hvis du landbrugsprodukter eller forventer, data med CRLF linjeafslutninger, skal du muligvis ændre producenter eller forbrugere til at arbejde med LF linje slutningen.

For eksempel returnerer bruge Azure PowerShell til at forespørgsel HDInsight på en Windows-baseret klynge data med CRLF. Den samme forespørgsel med en Linux-baserede klynge returnerer LF. I mange tilfælde er dette ligegyldig til dataforbruger, men den skal undersøges, før du flytter til en Linux-baserede klynge.

Hvis du har scripts, der skal udføres direkte på noderne Linux-klynge (såsom en Python script, der bruges med Hive eller et MapReduce job), skal du altid bruge LF som linje slutningen. Hvis du bruger CRLF, kan du se fejl, når du kører scripts på en Linux-baserede klynge.

Hvis du ved, at scriptene ikke indeholder strenge med integrerede CR tegn, du kan flere ændre linjeslutpunkter ved hjælp af en af følgende fremgangsmåder:

-   **Hvis du har scripts, som du planlægger at overføre til klyngen**, Brug følgende PowerShell-sætninger til at ændre linjeslutpunkter fra CRLF til LF før du overfører scriptet til klyngen.

        $original_file ='c:\path\to\script.py'
        $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
        [IO.File]::WriteAllText($original_file, $text)

-   **Hvis du har et script, der allerede findes i den lagerplads, der anvendes af klyngen**, du kan bruge følgende kommando fra en SSH session til den Linux-baserede klynge til at ændre scriptet.

        hdfs dfs -get wasbs:///path/to/script.py oldscript.py
        tr -d '\r' < oldscript.py > script.py
        hdfs dfs -put -f script.py wasbs:///path/to/script.py

##<a name="next-steps"></a>Næste trin

-   [Lær at oprette Linux-baserede HDInsight klynger](hdinsight-hadoop-provision-linux-clusters.md)

-   [Oprette forbindelse til en Linux-baserede klynge ved hjælp af SSH fra en Windows-klient](hdinsight-hadoop-linux-use-ssh-windows.md)

-   [Oprette forbindelse til en Linux-baserede klynge ved hjælp af SSH fra en Linux, Unix eller Mac-klient](hdinsight-hadoop-linux-use-ssh-unix.md)

-   [Administrere en Linux-baserede klynge ved hjælp af Ambari](hdinsight-hadoop-manage-ambari.md)
