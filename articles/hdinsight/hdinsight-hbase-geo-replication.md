<properties 
   pageTitle="Konfigurere HBase replikering mellem to datacentre | Microsoft Azure" 
   description="Lær, hvordan du konfigurerer HBase gentagelse på tværs af to datacentre og om use cases for klynge gentagelse." 
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
   ms.date="07/25/2016"
   ms.author="jgao"/>

# <a name="configure-hbase-geo-replication-in-hdinsight"></a>Konfigurere HBase geografisk-gentagelse i HDInsight

> [AZURE.SELECTOR]
- [Konfigurere VPN-forbindelse](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Konfigurere DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Konfigurere HBase gentagelse](hdinsight-hbase-geo-replication.md) 
 
Lær, hvordan du konfigurerer HBase gentagelse på tværs af to datacentre. Nogle bruger sager til klynge gentagelse omfatter:

- Sikkerhedskopiering og genoprettelse efter nedbrud
- Datasammenlægning
- Geografiske datafordeling
- Online-data indtagelse kombineres med offline data analytics

Klynge gentagelse bruger en kilde-push metode. En HBase klynge kan være en kilde, en destination, eller kan udføre begge roller på én gang. Replikering er asynkron og formålet med replikering er eventuel konsistens. Når kilden modtager en redigering til en kolonne familie med gentagelse, der er aktiveret, overføres, Rediger til alle destination klynger. Når data replikeres fra én klynge til en anden, registreres kilde klynge og alle klynger, som allerede har brugt dataene for at undgå gentagelse løkker. I dette selvstudium skal du konfigurere en kilde destination replikering kan finde flere oplysninger.  Du kan finde andre klynge topologier [Apache HBase oversigtsvejledning](http://hbase.apache.org/book.html#_cluster_replication).

Dette er den tredje del af serien:

- [Konfigurere en VPN-forbindelse mellem to virtuelle netværk][hdinsight-hbase-replication-vnet]
- [Konfigurere DNS til de virtuelle netværk][hdinsight-hbase-replication-dns]
- Konfigurere HBase geografisk gentagelse (dette selvstudium)

I følgende diagram vises de to virtuelle netværk og netværksforbindelse, du oprettede i [konfigurere en VPN-forbindelse mellem to virtuelle netværk] [ hdinsight-hbase-geo-replication-vnet] og [Konfigurere DNS til de virtuelle netværk][hdinsight-hbase-replication-dns]: 

![HDInsight HBase gentagelse virtuelle netværksdiagram][img-vnet-diagram]

## <a id="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **En arbejdsstation med Azure PowerShell**.

    Hvis du vil udføre PowerShell-scripts, skal du køre Azure PowerShell som administrator og indstiller politikken udførelse af til *RemoteSigned*. Se bruge cmdlet'en Set-Udførselspolitikken til.
    
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **To Azure virtuelt netværk med VPN-forbindelse og med DNS konfigureret**.  Flere oplysninger under [konfigurere en VPN-forbindelse mellem to Azure virtuelle netværk][hdinsight-hbase-replication-vnet], og [Konfigurere DNS mellem to Azure virtuelle netværk][hdinsight-hbase-replication-dns].


    Før du kører PowerShell-scripts, du kontrollere, at du har forbindelse til abonnementet Azure ved hjælp af følgende cmdlet:

        Add-AzureAccount

    Hvis du har flere Azure abonnementer, kan du bruge følgende cmdlet til at angive det aktuelle abonnement:

        Select-AzureSubscription <AzureSubscriptionName>



## <a name="provision-hbase-clusters-in-hdinsight"></a>Klargør HBase klynger i HDInsight

I [konfigurere en VPN-forbindelse mellem to Azure virtuelle netværk][hdinsight-hbase-replication-vnet], du har oprettet et virtuelt netværk i en Europa datacenter, og et virtuelt netværk i et datacenter i USA. To virtuelle netværket er forbundet via VPN. I denne session, kan du klargøre en HBase klynge i hver af de virtuelle netværk. Du vil gøre et af HBase klynger at gentage de andre HBase klynge senere i dette selvstudium.

Portalen Azure klassisk understøtter ikke klargøring HDInsight klynger med brugerdefinerede indstillinger. For eksempel angive *hbase.replication* til *Sand*. Hvis du angiver værdien i konfigurationsfilen, når en klynge er klargjort, mister du indstillingen, når klyngen er der reimaged. Finde flere oplysninger under [klargøring Hadoop klynger i HDInsight][hdinsight-provision]. En af indstillingerne til at klargøre HDInsight klynge med brugerdefinerede indstillinger for bruger Azure PowerShell.


**Til at klargøre en HBase klynge i Contoso-VNet-EU-** 

1. Åbn Windows PowerShell ISE fra din arbejdsstation.
2. Angive variablerne i starten af scriptet, og derefter køre scriptet.

        # create hbase cluster with replication enabled
        
        $azureSubscriptionName = "[AzureSubscriptionName]"
        
        $hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
        $hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
        $hadoopUserLogin = "[HadoopUserName]"
        $hadoopUserpw = "[HadoopUserPassword]"
        
        $vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
        $subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
        
        $storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
        $storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
        $blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
        
        #connect to your Azure subscription
        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName
        
        # Create a storage account used by the HBase cluster
        $location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
        
        # Create a blob container used by the HBase cluster
        $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
        
        # Create provision configuration object
        $hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
            
        $hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
        
        # retrieve vnet id based on vnetname
        $vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
        
        $config = New-AzureHDInsightClusterConfig `
                        -ClusterSizeInNodes $hbaseClusterSize `
                        -ClusterType HBase `
                        -VirtualNetworkId $vNetID `
                        -SubnetName $subNetName `
                    | Set-AzureHDInsightDefaultStorage `
                        -StorageAccountName $storageAccountName `
                        -StorageAccountKey $storageAccountKey `
                        -StorageContainerName $blobContainerName `
                    | Add-AzureHDInsightConfigValues `
                        -HBase $hbaseConfigValues
        
        # provision HDInsight cluster
        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
        
        $config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Til at klargøre en HBase klynge i Contoso-VNet-dk** 

- Brug samme script med følgende værdier:

        $hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
        $vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
        $storageAccountName = 'ContosoStoreUS'  

    Da du allerede har tilsluttet din Azure-konto, kan du ikke behøver at køre følgende comlets længere:

        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName




## <a name="configure-dns-conditional-forwarder"></a>Konfigurere DNS-betinget domænes

I [Konfigurere DNS til de virtuelle netværk][hdinsight-hbase-replication-dns], du har konfigureret DNS-servere for de to netværk. HBase klynger har forskellige domænesuffikser. Så skal du konfigurere yderligere betinget DNS-videresendelser.

For at konfigurere betinget domænes, skal du vide domænesuffikser to HBase klynger. 

**Finde domænesuffikser to HBase klynger**

1. RDP til **Contoso-HBase-EU**.  Flere oplysninger under [administrere Hadoop klynger i HDInsight ved hjælp af portalen Azure klassisk][hdinsight-manage-portal]. Det er faktisk headnode0 af klyngen.
2. Åbn en Windows PowerShell console eller en kommandoprompt.
3. Køre **ipconfig**, og notér **forbindelse-specifikke DNS-suffiks**.
4. Kan du ikke lukke RDP-session.  Du skal bruge den senere at teste domæne navneoversættelse.
5. Gentag de samme trin til at finde ud af en **forbindelse-specifikke DNS-suffiks** **Contoso-HBase-US**.


**Konfigurere DNS-videresendelser**
 
1.  RDP til **Contoso-DNS-EU**. 
2.  Klik på Windows-tasten nederst til venstre.
2.  Klik på **Administration**.
3.  Klik på **DNS**.
4.  Udvid **DSN**, **Contoso-DNS-EU-**i venstre rude.
5.  Højreklik på **Betinget videresendelser**, og klik derefter på **Ny betinget domænes**. 
5.  Angiv følgende oplysninger:
    - **DNS-domæne**: Angiv DNS-suffikset i Contoso-HBase-US. For eksempel: Contoso-HBase-US.f5.internal.cloudapp.net.
    - **IP-adresserne på de overordnede servere**: Angiv 10.2.0.4, som er Contoso-DNS-USAS IP-adresse. Kontroller IP-adresse. Din DNS-server kan have en anden IP-adresse.
6.  Tryk på **ENTER**, og klik derefter på **OK**.  Nu vil du kunne løse Contoso-DNS-USAS IP-adresse fra Contoso-DNS-EU.
7.  Gentag trinnene for at tilføje en betinget domænes DNS til DNS-tjenesten på Contoso-DNS-US virtuel maskine med følgende værdier:
    - **DNS-domæne**: Angiv DNS-suffikset i Contoso-HBase-EU. 
    - **IP-adresserne på de overordnede servere**: Angiv 10.2.0.4, som er Contoso-DNS-EUS IP-adresse.

**Teste domæne navneoversættelse**

1. Skifte til Contoso-HBase-EU-RDP-vindue.
2. Åbn en kommandoprompt.
3. Kør kommandoen ping:

        ping headnode0.[DNS suffix of Contoso-HBase-US]

    ICM protokollen er slået til noderne arbejder HBase klynger

4. Luk ikke RDP-session. Du skal stadig bruge den senere i selvstudiet.
5. Gentag de samme trin for at pinge headnode0 af Contoso-HBase-EU fra Contoso-HBase-US.

>[AZURE.IMPORTANT] DNS skal arbejde, før du kan fortsætte til næste trin.

## <a name="enable-replication-between-hbase-tables"></a>Aktivere replikering mellem HBase tabeller

Nu kan du oprette en HBase eksempeltabel, aktivere gentagelse og test den derefter med nogle data. Du vil bruge eksempeltabellen har to kolonne familier: Personal og Office. 

I dette selvstudium får du Europe HBase klynge som kilde klynge og USA HBase klynge som destination klynge.

Oprette HBase tabeller med samme navne og kolonne findes på både kilde- og klynger, så destination klynge ved, hvor du vil gemme den modtager data. Du kan finde flere oplysninger om brug af HBase shell se [komme i gang med Apache HBase i HDInsight][hdinsight-hbase-get-started].

**Oprette en HBase tabel på Contoso-HBase-EU-**

1. Skifte til **Contoso-HBase-EU-** RDP-vindue.
2. Fra computeren, skal du klikke på **Hadoop-kommandolinjen**.
2. Ændre mappen til mappen HBase hjem:

        cd %HBASE_HOME%\bin
3. Åbn HBase shell:

        hbase shell
4. Oprette en tabel med HBase:

        create 'Contacts', 'Personal', 'Office'
5. Luk ikke RDP-session eller vinduet Hadoop-kommandolinjen. Du skal stadig bruge dem senere i selvstudiet.
    
**Oprette en HBase tabel på Contoso-HBase-dk**

- Gentag de samme trin for at oprette den samme tabel på Contoso-HBase-US.


**Tilføje Contoso-HBase-US som en gentagelse peer**

1. Skifte til **Contso HBase_EU** RDP-vinduet.
2. Fra vinduet HBase shell at tilføje destination klynge (Contoso-HBase-DK) som en peer, for eksempel:

        add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

    I eksemplet er det domænesuffiks *contoso-hbase-us.d4.internal.cloudapp.net*. Du skal opdatere den for at matche dit domænesuffiks af os HBase klynge. Kontrollér, at der er ingen mellemrum mellem værtsnavne.

**Sådan konfigureres hver kolonne familie skal replikeres på kilde klynge**

1. Konfigurere hver kolonne familie skal replikeres fra vinduet HBase shell af **Contso-HBase-EU-** RDP-session:

        disable 'Contacts'
        alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
        alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
        enable 'Contacts'

**Sådan masseredigeres Overfør data til tabellen HBase**

Et eksempel på datafil er blevet overført til en offentlig Azure Blob-objektbeholder med følgende URL-adressen:

        wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

Indholdet af filen:

        8396    Calvin Raji 230-555-0191    5415 San Gabriel Dr.
        16600   Karen Wu    646-555-0113    9265 La Paz
        4324    Karl Xie    508-555-0163    4912 La Vuelta
        16891   Jonathan Jackson    674-555-0110    40 Ellis St.
        3273    Miguel Miller   397-555-0155    6696 Anchor Drive
        3588    Osarumwense Agbonile    592-555-0152    1873 Lion Circle
        10272   Julia Lee   870-555-0110    3148 Rose Street
        4868    Jose Hayes  599-555-0171    793 Crawford Street
        4761    Caleb Alexander 670-555-0141    4775 Kentucky Dr.
        16443   Terry Chander   998-555-0171    771 Northridge Drive

Du kan overføre den samme datafil til din HBase klynge og importere dataene derfra.

1. Skifte til **Contoso-HBase-EU-** RDP-vindue.
2. Fra computeren, skal du klikke på **Hadoop-kommandolinjen**.
3. Ændre mappen til mappen HBase hjem:

        cd %HBASE_HOME%\bin

4. overføre dataene:

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## <a name="verify-that-data-replication-is-taking-place"></a>Kontrollér, at dataene replikering finder sted

Du kan kontrollere, at replikering finder sted ved at scanne tabellerne fra begge klynger med følgende HBase shell kommandoer:

        Scan 'Contacts'


## <a name="next-steps"></a>Næste trin

I dette selvstudium, har du lært at konfigurere HBase gentagelse på tværs af to datacentre. Hvis du vil vide mere om HDInsight og HBase, i:

- [HDInsight Tjenesteside](https://azure.microsoft.com/services/hdinsight/)
- [HDInsight dokumentation](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Komme i gang med Apache HBase i HDInsight][hdinsight-hbase-get-started]
- [Oversigt over HDInsight HBase][hdinsight-hbase-overview]
- [Klargør HBase klynger på Azure virtuelt netværk][hdinsight-hbase-provision-vnet]
- [Analysere realtid Twitter synspunkt med HBase][hdinsight-hbase-twitter-sentiment]
- [Analysere føler data med Storm og HBase i HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: powershell-install-configure.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
