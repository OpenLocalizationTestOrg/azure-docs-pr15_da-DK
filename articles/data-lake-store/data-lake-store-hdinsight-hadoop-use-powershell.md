<properties
   pageTitle="Oprette HDInsight klynger med Azure Data sø Store ved hjælp af PowerShell | Azure"
   description="Bruge Azure PowerShell til at oprette og bruge HDInsight klynger med Azure Data sø"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="nitinme"/>

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-powershell"></a>Oprette en HDInsight klynge med datalager sø ved hjælp af Azure PowerShell

> [AZURE.SELECTOR]
- [Ved hjælp af portalen](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Ved hjælp af PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
- [Brug af Ressourcestyring](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Lær at bruge Azure PowerShell til at konfigurere en HDInsight klynge (Hadoop, HBase eller Storm) med adgang til Azure sø datalager. Nogle vigtige overvejelser i forbindelse med denne version:

* **For gnister klynger (Linux), og Hadoop/Storm klynger (Windows og Linux)**, sø datalager kan kun bruges som en ekstra lagerplads konto. Lagerplads standardkontoen for sådanne klynger kan stadig Azure lagerplads BLOB (WASB).

* **For HBase klynger (Windows og Linux)**, sø datalager kan bruges som standard lagerplads eller ekstra lagerplads.

> [AZURE.NOTE] Nogle vigtige punkter du Bemærk. 
> 
> * Muligheden for at oprette HDInsight klynger med adgang til sø datalager er kun tilgængelig for HDInsight version 3.2 og 3.4 (for Hadoop, HBase og Storm klynger på Windows samt Linux). Denne indstilling er kun tilgængelig på HDInsight 3.4 klynger for gnister klynger på Linux.
>
> * Som nævnt ovenfor, er sø datalager tilgængeligt som standard lager til visse typer af klynge (HBase) og yderligere lagerplads til andre klynge typer (Hadoop, gnister, Storm). Brug af sø datalager som en ekstra lagerplads konto påvirker ikke ydeevnen eller muligheden for at læse-og skriveadgang til opbevaring fra klyngen. I et scenarie, hvor sø datalager bruges som ekstra lagerplads, skrives klynge-relaterede filer (såsom logfiler, osv.) til standard-lager (Azure BLOB), mens de data, du vil behandle kan gemmes i en sø datalager-konto.


I denne artikel klargøre vi en Hadoop klynge med sø datalager som ekstra lagerplads.

Konfiguration af HDInsight til at arbejde med sø datalager omfatter ved hjælp af PowerShell følgende trin:

* Oprette et Azure datalager sø
* Konfigurere godkendelse for rollebaseret adgang til sø datalager
* Oprette HDInsight klynge med godkendelse til sø datalager
* Køre et testjob på klyngen

## <a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

- **Azure PowerShell 1.0 eller større**. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

- **Windows SDK**. Du kan installere det fra [her](https://dev.windows.com/en-us/downloads). Du kan bruge dette til at oprette et sikkerhedscertifikat.

- **Azure Active Directory Service hovedstolen**. Trinnene i dette selvstudium viser instruktioner til, hvordan du opretter en tjeneste hovedstolen i Azure AD. Skal du være administrator Azure AD lov til at oprette en tjeneste sikkerhedskonto. Hvis du er Azure AD-administrator, kan du springe over disse krav og fortsætte med selvstudiet.
    
    **Hvis du er ikke Azure AD-administrator**, kan du ikke kan udføre de trin, der er nødvendige for at oprette en tjeneste sikkerhedskonto. I så fald skal administratoren Azure AD først oprette en tjeneste sikkerhedskonto før du kan oprette en HDInsight klynge med sø datalager. Desuden skal service hovedstolen oprettes ved hjælp af et certifikat, som det er beskrevet i [oprette en tjeneste vigtigste med certifikat](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate). 


## <a name="create-an-azure-data-lake-store"></a>Oprette et Azure datalager sø

Følg disse trin for at oprette et datalager sø.

1. Åbn et nyt vindue Azure PowerShell fra din computer, og Angiv følgende kodestykke. Når du bliver bedt om at logge på, skal du kontrollere du logger på som et af admininistrators/ejeren af abonnementet:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    >[AZURE.NOTE] Hvis du modtager en fejlmeddelelse, der ligner `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` når der registreres provideren sø datalager ressource, det er muligt, at din subsrcription ikke er whitelisted til Azure sø datalager. Kontrollér, at du aktiverer abonnementet Azure til sø datalager offentlige preview ved at følge disse [instruktioner](data-lake-store-get-started-portal.md#signup).

3. En Azure datalager sø konto er knyttet til en Azure ressourcegruppe. Starte med at oprette en Azure ressourcegruppe.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Oprette en Azure ressourcegruppe] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Oprette en Azure ressourcegruppe")

2. Opret en Azure datalager sø-konto. Det kontonavn, du angiver skal kun indeholde små bogstaver og tal.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Opret en Azure Data sø-konto] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Opret en Azure Data sø-konto")

3. Kontrollér, at kontoen blev oprettet.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    Output til dette skal være **sande**.

4. Overføre nogle eksempeldata til Azure Data sø. Vi bruger dette senere i denne artikel for at bekræfte, at dataene er tilgængelige fra en HDInsight klynge. Hvis du leder efter nogle eksempeldata for at overføre, kan du få mappen **Ambulance Data** fra [Azure Data sø ciffer lager](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).


        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Konfigurere godkendelse for rollebaseret adgang til sø datalager

Hver Azure abonnement er knyttet til en Azure Active Directory. Brugere og -tjenester, få adgang til ressourcer ved hjælp af Azure klassisk Portal eller Azure ressourcestyring API abonnementets skal først godkende med pågældende Azure Active Directory. Har adgang til Azure-abonnementer og -tjenester ved at tildele dem den relevante rolle på en Azure ressource.  For tjenester identificerer en tjeneste sikkerhedskonto tjenesten i Azure Active Directory (AAD). Dette afsnit viser, hvordan til at give et programtjeneste, såsom HDInsight, adgang til en Azure ressource (den Azure datalager sø konto du har oprettet tidligere) ved at oprette en tjeneste hovedstolen for programmet og tildele roller til, via Azure PowerShell.

Hvis du vil konfigurere Active Directory-godkendelse til Azure Data sø, skal du udføre følgende opgaver.

* Oprette et selvsigneret certifikat
* Oprette et program i Azure Active Directory og en tjeneste sikkerhedskonto

### <a name="create-a-self-signed-certificate"></a>Oprette et selvsigneret certifikat

Sørg for, at du har [Windows SDK](https://dev.windows.com/en-us/downloads) installeret, inden du fortsætter med trinene i dette afsnit. Du skal også have oprettet en mappe, som **C:\mycertdir**, hvor certifikatet, der skal oprettes.

1. Fra vinduet PowerShell gå til det sted, hvor du har installeret Windows SDK (typisk `C:\Program Files (x86)\Windows Kits\10\bin\x86` og bruge [MakeCert] [ makecert] giver mulighed for at oprette et selvsigneret certifikat og en privat nøgle. Brug følgende kommandoer.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        $startDate = (Get-Date).ToString('MM/dd/yyyy')
        $endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

    Du bliver bedt om at angive adgangskoden til privat nøgle. Når kommandoen er blevet udført, skal du se en **CertFile.cer** og **mykey.pvk** i mappen certifikat, du har angivet.

4. Brug [Pvk2Pfx] [ pvk2pfx] giver mulighed for at konvertere de .pvk og .cer-filer, MakeCert oprettes til en .pfx-fil. Kør følgende kommando.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Når du bliver bedt om angive den private nøgle adgangskode, du tidligere har angivet. Den værdi, du angiver for parameteren **-indkøbsordre** er den adgangskode, der er knyttet til .pfx-fil. Når kommandoen er fuldført, skal du også se et CertFile.pfx i mappen certifikat, du har angivet.

###  <a name="create-an-azure-active-directory-and-a-service-principal"></a>Oprette en Azure Active Directory og en tjeneste sikkerhedskonto

I dette afsnit, skal udføre du trinnene for at oprette en tjeneste vigtigste for et Azure Active Directory-program, Tildel en rolle til tjenesten vigtigste og godkende som service hovedstolen ved at give et certifikat. Kør følgende kommandoer til at oprette et program i Azure Active Directory.

1. Indsæt følgende cmdletter i PowerShell console-vinduet. Kontrollér, at den værdi, du angiver for egenskaben **- Vist navn** er entydige. Også værdierne for **- Hjemmesiden** og **-IdentiferUris** er pladsholder værdier, og der ikke er godkendt.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
                    -DisplayName "HDIADL" `
                    -HomePage "https://contoso.com" `
                    -IdentifierUris "https://mycontoso.com" `
                    -KeyValue $credential  `
                    -KeyType "AsymmetricX509Cert"  `
                    -KeyUsage "Verify"  `
                    -StartDate $startDate  `
                    -EndDate $endDate

        $applicationId = $application.ApplicationId

2. Oprette en tjeneste sikkerhedskonto ved hjælp af program-ID.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id

3. Giv service vigtigste adgang til den sø datalager fil/mappe, du får adgang fra HDInsight klynge. Den nedenstående kodestykke giver adgang til i roden af kontoen sø datalager.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

    Når du bliver spurgt, Angiv **Y** for at bekræfte.

## <a name="create-an-hdinsight-cluster-with-authentication-to-data-lake-store"></a>Oprette en HDInsight klynge med godkendelse til sø datalager

I dette afsnit skal oprette vi en HDInsight Hadoop-klynge. I denne version skal være HDInsight klynge og sø datalager på samme placering (af USA 2).

1. Start med at hente abonnement lejer-ID. Du skal bruge, der senere.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. I denne udgave, kan til et Hadoop-klynge, sø datalager kun bruges som en ekstra lagerplads for-klyngen. Standard lagerplads kan stadig Azure lagerplads BLOB (WASB). Så vil vi først oprette lagerplads konto og objektbeholdere, der kræves til klyngen.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext

3. Oprette HDInsight klynge. Brug følgende cmdletter.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $rdpCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Når cmdlet er fuldført, skal du se afsnittet output således:

        Name                      : hdiadlcluster
        Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
                                    crosoft.HDInsight/clusters/hdiadlcluster
        Location                  : East US 2
        ClusterVersion            : 3.2.7.707
        OperatingSystemType       : Windows
        ClusterState              : Running
        ClusterType               : Hadoop
        CoresUsed                 : 16
        HttpEndpoint              : hdiadlcluster.azurehdinsight.net
        Error                     :
        DefaultStorageAccount     :
        DefaultStorageContainer   :
        ResourceGroup             : hdiadlgroup
        AdditionalStorageAccounts :

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Køre testjob på HDInsight-klyngen for at bruge sø datalager

Når du har konfigureret en HDInsight klynge, kan du køre testjob på klynge for at teste, HDInsight klynge kan få adgang til sø datalager. Hvis du vil gøre det, vil vi gennemgå et eksempel Hive-job, der opretter en tabel med eksempeldata, som du tidligere har overført til dit sø datalager.

### <a name="for-a-linux-cluster"></a>For en Linux klynge

I dette afsnit kan du SSH i klynge og Kør på en stikprøve Hive forespørgsel. Windows indeholder ikke en indbygget SSH-klient. Det anbefales at bruge **trykfarver**, som kan hentes fra [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Finde flere oplysninger om brug af trykfarver, [Brug SSH med Linux-baserede Hadoop på HDInsight fra Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Når forbindelse, kan du begynde på Hive CLI ved hjælp af følgende kommando:

        hive

2. Angiv følgende udtalelser for at oprette en ny tabel med navnet **køretøjer** ved hjælp af eksempeldataene i lageret med sø Data ved at bruge CLI:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Du burde se output ligner følgende:

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


### <a name="for-a-windows-cluster"></a>Til en Windows-klynge

Brug følgende cmdletter til at køre forespørgslen Hive. I denne forespørgsel vi oprette en tabel ud fra data i lageret med sø Data og derefter køre en udvælgelsesforespørgsel på den oprettede tabel.

    $queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

    $hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

    Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

Dette får følgende output. **ExitValue** på 0 i outputtet foreslår, at jobbet blev fuldført.

    Cluster         : hdiadlcluster.
    HttpEndpoint    : hdiadlcluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1445386885331_0012
    ParentId        :
    PercentComplete :
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done

Hente output fra jobbet ved hjælp af følgende cmdlet:

    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

Jobbet output ligner følgende:

    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


## <a name="access-data-lake-store-using-hdfs-commands"></a>Access Data sø Store ved hjælp af HDFS kommandoer

Når du har konfigureret HDInsight klynge for at bruge sø datalager, kan du bruge kommandoerne HDFS shell til at få adgang til store.

### <a name="for-a-linux-cluster"></a>For en Linux klynge

I dette afsnit, du vil SSH i klyngen og køre kommandoerne HDFS. Windows indeholder ikke en indbygget SSH-klient. Det anbefales at bruge **trykfarver**, som kan hentes fra [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Finde flere oplysninger om brug af trykfarver, [Brug SSH med Linux-baserede Hadoop på HDInsight fra Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Når forbindelse, kan du bruge følgende HDFS filsystemet kommando til at få vist filerne i sø datalager.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Dette skal indeholde den fil, du tidligere har overført til sø datalager.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Du kan også bruge den `hdfs dfs -put` kommandoen til at overføre nogle filer til sø datalager, og brug derefter `hdfs dfs -ls` til at kontrollere, om filerne er blevet overført.


### <a name="for-a-windows-cluster"></a>Til en Windows-klynge

1. Log den nye [Azure-portalen](https://portal.azure.com).

2. Klik på **Gennemse**, klik på **HDInsight klynger**, og klik derefter på den HDInsight-klynge, du har oprettet.

3. Klik på **Fjernskrivebord**i bladet klynge, og klik derefter på **Tilslut**i bladet **Fjernskrivebord** .

    ![Remote i HDI klynge] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Oprette en Azure ressourcegruppe")

    Når du bliver bedt om det, kan du angive de legitimationsoplysninger, du har angivet for remote desktop brugeren.

4. Start Windows PowerShell i fjernsession, og bruge kommandoerne HDFS filsystemet for at vise filerne i Azure sø datalager.

        hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

    Dette skal indeholde den fil, du tidligere har overført til sø datalager.

        15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
        Found 1 items
        -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

    Du kan også bruge den `hdfs dfs -put` kommandoen til at overføre nogle filer til sø datalager, og brug derefter `hdfs dfs -ls` til at kontrollere, om filerne er blevet overført.

## <a name="see-also"></a>Se også

* [Portalen: Oprette en HDInsight klynge for at bruge sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
