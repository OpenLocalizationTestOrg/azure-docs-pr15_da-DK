<properties
    pageTitle="Oprette HBase klynger i et virtuelt netværk | Microsoft Azure"
    description="Introduktion til brug af HBase i Azure HDInsight. Lær at oprette HDInsight HBase klynger på Azure virtuelt netværk."
    keywords=""
    services="hdinsight,virtual-network"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/18/2016"
   ms.author="jgao"/>

# <a name="create-hbase-clusters-in-azure-virtual-network"></a>Oprette HBase klynger i Azure virtuelt netværk 

Lær at oprette Azure HDInsight HBase klynger i en [Azure virtuelt netværk][1].

HBase klynger kan installeres på det samme virtuelle netværk som dine programmer med virtuelt netværksintegration, så programmer kan kommunikere med HBase direkte. Fordelene omfatter:

- Direkte tilslutning af webprogrammet noderne af HBase klynge, som gør det muligt kommunikation via HBase Java remote procedure call (RPC) API'er.
- Forbedret ydeevne ved ikke at have din trafik gå over flere gateways og Indlæs balancere.
- Muligheden for at behandle følsomme oplysninger i en mere sikker måde uden at udsætte en offentlige slutpunkt.

###<a name="prerequisites"></a>Forudsætninger
Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **En arbejdsstation med Azure PowerShell**. Se [installation og brug Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). 

## <a name="create-hbase-cluster-into-virtual-network"></a>Oprette HBase klynge i virtuelt netværk

I dette afsnit, skal oprette du en Linux-baserede HBase klynge med afhængige Azure-lager kontoen i en Azure virtuelt netværk ved hjælp af en [Azure ressourcestyring skabelon](../resource-group-template-deploy.md). Andre metoder til oprettelse af klynge og forstå indstillingerne, under [oprette HDInsight klynger](hdinsight-hadoop-provision-linux-clusters.md). Finde flere oplysninger om brug af en skabelon til at oprette Hadoop klynger i HDInsight, under [oprette Hadoop klynger i HDInsight ved hjælp af Azure ressourcestyring skabeloner](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

> [AZURE.NOTE] Nogle egenskaber er blevet kopieret direkte ind skabelonen. Eksempel:
>
> * __Placering__: af US 2
> * __Cluster version: 3.4
> * __Klynge arbejder node Tæl__: 4
> * __Standardkonto lagerplads__: &lt;klyngenavn > gemme
> * __Virtuel netværksnavn__: &lt;klyngenavn >-vnet
> * __Virtuel netværk adresseområde__: 10.0.0.0/16
> * __Undernet navn__: standard
> * __Undernet adresseområde__: 10.0.0.0/24
>
> &lt;Klynge navn > erstattes med den klyngenavn, du angiver, når du bruger skabelonen.

1. Klik på følgende billede for at åbne skabelonen i portalen Azure. Skabelonen er placeret i en offentlig blob objektbeholder. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Angiv følgende oplysninger fra bladet **brugerdefineret installation** :

    - **Abonnement**: Vælg en Azure abonnement, der bruges til at oprette HDInsight klyngen, afhængige lagerplads kontoen og det Azure virtuelle netværk.
    - **Ressourcegruppe**: Vælg **Opret ny**, og Angiv en ny ressource gruppenavn.
    - **Placering**: Vælg en placering til ressourcegruppen.
    - **ClusterName**: Angiv et navn til den Hadoop-klynge, du vil oprette.
    - **Klynge login-navn og din adgangskode**: login standardnavnet er **administrator**.
    - **SSH brugernavn og din adgangskode**: standard brugernavn er **sshuser**.  Du kan omdøbe den. 
    - **Jeg accepterer vilkårene og de betingelser, der er anført ovenfor**: (Vælg)
    

3. Klik på **Køb**. Det tager om omkring 20 minutter om at oprette en klynge. Når klyngen er oprettet, kan du klikke på bladet klynge i portalen for at åbne den.

Når du har fuldført selvstudiet, kan du vil slette klyngen. Med HDInsight gemmes dine data i Azure-lager, så du kan slette en klynge sikkert, når den ikke er i brug. Du betaler også for en HDInsight klynge, selvom den ikke er i brug. Da gebyrerne for klyngen mange gange mere end gebyrer for lagerplads, giver økonomisk mening at slette klynger, når de ikke er i brug. Du kan finde instruktioner for at slette en klynge, [administrere Hadoop klynger i HDInsight ved hjælp af portalen Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Hvis du vil begynde at arbejde med din nye HBase klynge, kan du bruge procedurerne i [gang med at bruge HBase med Hadoop i HDInsight](hdinsight-hbase-tutorial-get-started.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Oprette forbindelse til den HBase klynge, ved hjælp af HBase Java RPC-API'er

1.  Oprette en infrastruktur som en tjeneste (IaaS) virtuel maskine i det samme Azure virtuelle netværk, og det samme undernet. Du kan finde vejledning i at oprette en ny IaaS virtuel maskine, [Opret virtuelt kører Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). Når du følger trinnene i dette dokument, skal du bruge følgende til netværkskonfigurationen:

    - __Virtuel netværk__: &lt;klyngenavn >-vnet
    - __Undernet__: standard

    > [AZURE.IMPORTANT] Erstatte &lt;klyngenavn > med det navn, du brugte, da oprettelse af HDInsight klynge i forrige trin.

    Bruge disse værdier, vil du konfigurere den virtuelle maskine for at anvende den samme virtuelt netværk og undernet som HDInsight klynge. Dette gør dem til at kommunikere direkte med hinanden.

2.  Når du bruger en Java-program til at oprette forbindelse til HBase fra en fjernplacering, skal du bruge det fulde domænenavn (fulde Domænenavn). For at afgøre, skal du få forbindelse-specifikke DNS-suffikset i HBase klynge. For at gøre det, kan du bruge en af følgende metoder:

    * Bruge en webbrowser til at foretage en Ambari opkald:
    
        Gå til https://&lt;ClusterName >.azurehdinsight.net/api/v1/clusters/&lt;ClusterName > / hosts?minimal_response = true. Den omdannes en JSON-fil med DNS-suffikser.

    * Brug Ambari webstedet:

        1. Gå til https://&lt;ClusterName >. azurehdinsight.net.
        2. Klik på **Hosts** fra den øverste menu.

    * Brug krøllet til at foretage RESTEN opkald:

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        I den JavaScript Object Notation (JSON) data, der returneres, finde posten "værtsnavn". Dette vil indeholde det fulde Domænenavn for knuderne i klyngen. Eksempel:

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        Del af domæne navn begynder med klyngenavnet er DNS-suffiks. For eksempel mycluster.b1.cloudapp.net.

    * Bruge Azure PowerShell
    
        Bruge følgende Azure PowerShell-script til at registrere funktionen **Get-ClusterDetail** , som kan bruges til at returnere DNS-suffiks:

            function Get-ClusterDetail(
                [String]
                [Parameter( Position=0, Mandatory=$true )]
                $ClusterDnsName,
                [String]
                [Parameter( Position=1, Mandatory=$true )]
                $Username,
                [String]
                [Parameter( Position=2, Mandatory=$true )]
                $Password,
                [String]
                [Parameter( Position=3, Mandatory=$true )]
                $PropertyName
                )
            {
            <#
                .SYNOPSIS
                 Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
                .Description
                 This command shows the following 4 properties of an HDInsight cluster:
                 1. ZookeeperQuorum (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (supports only HBase type cluster)
                    Shows a list of host FQDNs that run the HBase REST server.
                 4. FQDNSuffix (supports all cluster types)
                    Shows the FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of HBase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs that run the HBase REST server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows the FQDN suffix of hosts in the cluster.
            #>

                $DnsSuffix = ".azurehdinsight.net"

                $ClusterFQDN = $ClusterDnsName + $DnsSuffix
                $webclient = new-object System.Net.WebClient
                $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

                if($PropertyName -eq "ZookeeperQuorum")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
                }
                if($PropertyName -eq "ZookeeperClientPort")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
                }
                if($PropertyName -eq "HBaseRestServers")
                {
                    $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                    $Response1 = $webclient.DownloadString($Url1)
                    $JsonObject1 = $Response1 | ConvertFrom-Json
                    $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                    $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                    $Response2 = $webclient.DownloadString($Url2)
                    $JsonObject2 = $Response2 | ConvertFrom-Json
                    foreach ($host_component in $JsonObject2.host_components)
                    {
                        $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                        Write-host $ConnectionString
                    }
                }
                if($PropertyName -eq "FQDNSuffix")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        Når du har kørt Azure PowerShell-script, kan du bruge følgende kommando til at returnere DNS-suffiks ved hjælp af funktionen **Get-ClusterDetail** . Angiv din HDInsight HBase klyngenavn, administratornavn og administratoradgangskode, når du bruger denne kommando.

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        Dette vil returnere DNS-suffiks. For eksempel, **yourclustername.b4.internal.cloudapp.net**.

    * Brug RDP
    
        Du kan også bruge Fjernskrivebord til at oprette forbindelse til den HBase klynge (du skal have forbindelse til noden hoved) og køre **ipconfig** fra en kommandoprompt til at hente suffikset DNS. Finder instruktioner til at aktivere RDP Remote Desktop Protocol () og oprette forbindelse til klyngen ved hjælp af RDP, [administrere Hadoop klynger i portalen til Azure HDInsight][hdinsight-admin-portal].
        
        ![hdinsight.hbase.DNS.surffix][img-dns-surffix]


<!--
3.  Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

For at bekræfte, at den virtuelle maskine kan kommunikere med HBase klynge, skal du bruge kommandoen `ping headnode0.<dns suffix>` fra den virtuelle maskine. Ping headnode0.mycluster.b1.cloudapp.net.

Hvis du vil bruge disse oplysninger i et Java til computeren, kan du følge trinnene i [Bruge Maven til at oprette Java-programmer, der bruger HBase med HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) til at oprette et program. For at oprette forbindelse til en fjernserver HBase programmet, skal du redigere filen **hbase site.xml** i dette eksempel bruge det fulde Domænenavn for Zookeeper. Eksempel:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [AZURE.NOTE] Yderligere oplysninger om navneoversættelse i Azure virtuelle netværk, herunder hvordan du bruger dine egne DNS-server, skal du se [Navnet opløsning (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##<a name="next-steps"></a>Næste trin

Du har lært hvordan du opretter en HBase klynge i dette selvstudium. Hvis du vil vide mere, skal du se:

- [Komme i gang med HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Konfigurere HBase gentagelse i HDInsight](hdinsight-hbase-geo-replication.md)
- [Oprette Hadoop klynger i HDInsight](hdinsight-provision-clusters.md)
- [Introduktion til brug af HBase med Hadoop i HDInsight](hdinsight-hbase-tutorial-get-started.md)
- [Analysere Twitter synspunkt med HBase i HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
- [Oversigt over virtuelt netværk][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Klargøring oplysninger om den nye HBase klynge"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Brug scripthandling til at tilpasse en HBase klynge"

[azure-preview-portal]: https://portal.azure.com
