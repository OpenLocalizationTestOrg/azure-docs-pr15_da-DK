<properties
    pageTitle="Overvåge Hadoop klynger i HDInsight ved hjælp af API'EN Ambari | Microsoft Azure"
    description="Brug Apache Ambari APIs til at oprette, administrere og overvågning Hadoop klynger. Skjule Hadoop kompleksitet, intuitiv operator værktøjer og API'er."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    editor="cgronlun"
    manager="jhubbard"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Overvåge Hadoop klynger i HDInsight ved hjælp af Ambari API

Lær at overvåge HDInsight klynger ved hjælp af Ambari APIs.

> [AZURE.NOTE] Oplysninger i denne artikel er primært til Windows-baseret HDInsight klynger, som giver en skrivebeskyttet version af Ambari REST-API. Du kan finde Linux-baserede klynger [administrere Hadoop klynger ved hjælp af Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="what-is-ambari"></a>Hvad er Ambari?

[Apache Ambari] [ ambari-home] bruges til klargøring, administration og overvågning Apache Hadoop klynger. Den indeholder en intuitiv samling operator værktøjer og et solidt sæt API'er, skjuler Hadoop, forenkle driften af klynger kompleksitet. Du kan finde flere oplysninger om API'er, se [Ambari API Reference][ambari-api-reference]. 

HDInsight understøtter i øjeblikket kun Ambari overvågning funktionen. Ambari API 1.0 understøttes af HDInsight version 3.0 og 2.1 klynger. Denne artikel omhandler adgang til Ambari APIs på HDInsight version 3.1 og 2.1 klynger. Vigtige forskellen mellem to er, at nogle af komponenterne har ændret siden introduktionen af nye funktioner (såsom Job historik Server). 

**Forudsætninger**

Inden du starter selvstudiet, skal du have følgende:

- **En arbejdsstation med Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- (Valgfrit) [cURL] [curl]. For at installere det, se [cURL versioner og Downloads][curl-download].

    >[AZURE.NOTE] Når du bruger kommandoen krøllet i Windows, Brug dobbelte anførselstegn i stedet for enkelt anførselstegn til værdierne.

- **En Azure HDInsight klynge**. Flere oplysninger om klargøring af klynge, under [Introduktion til brug af HDInsight] [ hdinsight-get-started] eller [klargøring HDInsight grupper][hdinsight-provision]. Du skal gennemgå selvstudiet følgende data:

    Klynge egenskab|Azure PowerShell variabelnavn|Værdi|Beskrivelse
    ---|---|---|---
    HDInsight klyngenavn|$clusterName||Navnet på din HDInsight klynge.
    Klynge brugernavn|$clusterUsername||Klynge brugernavn angivet, når klyngen blev oprettet.
    Klynge adgangskode|$clusterPassword||Klynge brugeradgangskode.

    >[AZURE.NOTE] Fill-in værdier i tabellen. Dette er nyttigt til ved at følge dette selvstudium.

## <a name="jump-start"></a>Hurtigt i gang

Der findes flere måder at bruge Ambari til at overvåge HDInsight klynger.

**Bruge Azure PowerShell**

Følgende er en Azure PowerShell-script til at få oplysninger, MapReduce jobbet sporing af *i en HDInsight 3.1 klynge.*  Vigtige forskellen er, at vi adskille disse oplysninger fra den GARN service (i stedet for MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Følgende er en Azure PowerShell-script til at få MapReduce job sporing af oplysninger *i en HDInsight 2.1 klynge*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

Output er:

![Jobtracker Output][img-jobtracker-output]

**Brug krøllet**

Følgende er et eksempel på oplysninger ved hjælp af krøllet klynge:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

Output er:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**10-8-2014 slip**:

Når du bruger slutpunkt Ambari "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", feltet *værtsnavn* returnerer det fulde domænenavn (fulde Domænenavn) af noden i stedet for værtsnavnet. I dette eksempel returneres inden 8-10-2014-udgivelsen, blot "**headnode0**". Efter 8-10-2014-udgivelsen, du får det fulde Domænenavn "**headnode0. {} ClusterDNS} .azurehdinsight .net**", som vist i det forrige eksempel. Denne ændring er nødvendige for at lette scenarier, hvor flere klynge typer (såsom HBase og Hadoop) kan installeres i et virtuelt netværk (VNET). Dette sker, for eksempel, når du bruger HBase som en back end-platform til Hadoop.

## <a name="ambari-monitoring-apis"></a>Ambari overvågning API'er

I følgende tabel vises nogle af de mest almindelige Ambari overvågning af API opkald. Du kan finde flere oplysninger om API, se [Ambari API Reference][ambari-api-reference].

Overvåge API opkald|URI|Beskrivelse
---|---|---
Få klynger|`/api/v1/clusters`|
Få oplysninger om klynge.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net`|klynger, tjenester, hosts
Hent tjenester|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`|Tjenester der omfatter: hdfs, mapreduce
Få oplysninger om tjenester.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`|
Få service-komponenter|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`|HDFS: namenode, datanode<br/>MapReduce: jobtracker; tasktracker
Få oplysninger om komponenten.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`|ServiceComponentInfo, host-komponenter, målepunkter
Få hosts|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`|headnode0, workernode0
Få oplysninger om vært.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>`|
Få host komponenter|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`|namenode, ressourcestyring
Få host komponent oplysninger.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`|HostRoles, komponent, host, målepunkter
Få konfigurationer|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations`|Config typer: core-websted, hdfs-websted, mapred-websted, hive-websted
Få konfigurationsoplysninger.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>`|Config typer: core-websted, hdfs-websted, mapred-websted, hive-websted


##<a name="next-steps"></a>Næste trin

Du har nu lært Sådan bruger du Ambari overvågning af API opkald. Hvis du vil vide mere, skal du se:

- [Administrere HDInsight klynger ved hjælp af portalen Azure][hdinsight-admin-portal]
- [Administrere HDInsight klynger ved hjælp af Azure PowerShell][hdinsight-admin-powershell]
- [Administrere HDInsight klynger ved hjælp af kommandolinjen][hdinsight-admin-cli]
- [HDInsight dokumentation][hdinsight-documentation]
- [Komme i gang med HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
