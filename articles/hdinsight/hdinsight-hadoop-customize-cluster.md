<properties
    pageTitle="Tilpasse HDInsight klynger ved hjælp af scripthandlinger | Microsoft Azure"
    description="Lær, hvordan du tilpasser HDInsight klynger ved hjælp af Script handlingen."
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
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Tilpasse Windows-baseret HDInsight klynger ved hjælp af scripthandling

**Scripthandling** kan bruges til at kalde [brugerdefinerede scripts](hdinsight-hadoop-script-actions.md) under oprettelsen af klynge til installation af yderligere software på en klynge.

Oplysninger i denne artikel gælder kun for Windows-baseret HDInsight klynger. Du kan finde Linux-baserede klynger [tilpasse Linux-baserede HDInsight klynger ved hjælp af Script handlingen](hdinsight-hadoop-customize-cluster-linux.md). 

HDInsight klynger kan tilpasses i en række andre måder, som herunder flere konti i Azure-lager, ændre Hadoop konfiguration filer (core site.xml, hive-site.xml osv.), eller tilføje delt biblioteker (f.eks., Hive, Oozie) til almindelige steder i klyngen. Disse tilpasninger kan udføres via Azure PowerShell, Azure HDInsight .NET SDK eller Azure-portalen. Kan finde flere oplysninger i [oprette Hadoop klynger i HDInsight][hdinsight-provision-cluster].

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Scripthandling i oprettelsen af klynge

Script handlingen bruges kun mens en klynger er ved at blive oprettet. I følgende diagram vises, når Script handlingen er udført under oprettelsen af:

![HDInsight klynge tilpasning og stadier under oprettelse af klynge][img-hdi-cluster-states]

Når scriptet kører, indsætter klyngen fasen **ClusterCustomization** . På dette tidspunkt scriptet kører under kontoen system administrator parallelt på alle de angivne noder i klynge, og giver fuld administratortilladelser til noderne.

> [AZURE.NOTE] Fordi du har administratorrettigheder på klyngenoderne under **ClusterCustomization** fase, kan du bruge scriptet til at udføre handlinger som stopper og starter tjenesterne, herunder tjenester, der vedrører Hadoop. Så som en del af scriptet, skal du kontrollere, at Ambari tjenesterne og andre tjenester, der vedrører Hadoop oppe at køre før scriptet er afsluttet. Disse tjenester kræves for at fastslå tilstand og tilstanden for klyngen korrekt, mens det oprettes. Hvis du ændrer en hvilken som helst konfiguration på den-klynge, påvirker disse tjenester, skal du bruge funktionerne hjælpeprogram, der kan bruges. Du kan finde flere oplysninger om hjælpefunktioner se [udvikle scripthandling scripts til HDInsight][hdinsight-write-script].

Output og fejllogger for scriptet er gemt i standardkontoen lagerplads du har angivet for-klyngen. Logfiler er gemt i en tabel med navnet **u < \cluster-name-fragment >< \time-stamp > setuplog**. Dette er Sammenlæg logfiler fra scriptet køre på alle noderne (hoved node og arbejder noder) i klyngen.

Hver klynge kan acceptere flere scripthandlinger, der aktiveres i den rækkefølge, hvori de er angivet. Et script kan kørte på noden hoved, arbejder noderne eller begge dele.

HDInsight indeholder flere scripts for at installere følgende komponenter på HDInsight klynger:

Navn | Script
----- | -----
**Installere knallertmotor** | https://hdiconfigactions.BLOB.Core.Windows.NET/sparkconfigactionv03/Spark-Installer-v03.ps1. Se [installation og brug sætte gang på HDInsight klynger][hdinsight-install-spark].
**Installere R** | https://hdiconfigactions.BLOB.Core.Windows.NET/rconfigactionv02/r-Installer-v02.ps1. Se [installation og brug R på HDInsight klynger][hdinsight-install-r].
**Installere Solr** | https://hdiconfigactions.BLOB.Core.Windows.NET/solrconfigactionv01/solr-Installer-v01.ps1. Se [installation og brug Solr på HDInsight klynger](hdinsight-hadoop-solr-install.md).
- **Installere Giraph** | https://hdiconfigactions.BLOB.Core.Windows.NET/giraphconfigactionv01/giraph-Installer-v01.ps1. Se [installation og brug Giraph på HDInsight klynger](hdinsight-hadoop-giraph-install.md).
| **Foreløbig Indlæs Hive biblioteker** | https://hdiconfigactions.BLOB.Core.Windows.NET/setupcustomhivelibsv01/Setup-customhivelibs-v01.ps1. Se [tilføje Hive biblioteker på HDInsight klynger](hdinsight-hadoop-add-hive-libraries.md) |


## <a name="call-scripts-using-the-azure-portal"></a>Ringe til scripts ved hjælp af portalen Azure

**Fra Azure-portalen**

1. Begynde at oprette en klynge, som beskrevet på [oprette Hadoop klynger i HDInsight](hdinsight-provision-clusters.md#portal).
2. Under konfiguration af valgfrit for bladet **Scripthandlinger** skal du klikke på **Tilføj scripthandling** for at vise detaljer om handlingen script, som vist nedenfor:

    ![Brug scripthandling til at tilpasse en klynge] (./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Brug scripthandling til at tilpasse en klynge")

    <table border='1'>
        <tr><th>Egenskaben</th><th>Værdi</th></tr>
        <tr><td>Navn</td>
            <td>Angiv et navn for handlingen script.</td></tr>
        <tr><td>Script URI</td>
            <td>Angiv URI til det script, der er aktiveret for at tilpasse klyngen. s</td></tr>
        <tr><td>Hoved/arbejder</td>
            <td>Angiv noderne (**hoved** eller **arbejder**) under tilpasning scriptet kører. </b>.
        <tr><td>Parametre</td>
            <td>Angiv parametrene, hvis det er nødvendigt ved scriptet.</td></tr>
    </table>

    Tryk på ENTER for at tilføje mere end én scripthandling for at installere flere komponenter på klyngen.

3. Klik på **Vælg** for at gemme script handling konfigurationen og fortsætte med oprettelse af klynge.

## <a name="call-scripts-using-azure-powershell"></a>Ringe til scripts med Azure PowerShell

Denne følgende PowerShell-script viser, hvordan du installerer knallertmotor på Windows-baseret HDInsight klynge.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    
    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.
    
    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"
    
    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    
    #############################################################
    # Connect to Azure
    #############################################################
    
    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID
    
    #############################################################
    # Prepare the dependent components
    #############################################################
    
    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext
    
    #############################################################
    # Create cluster with ApacheSpark
    #############################################################
    
    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey 
                
    
    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `
    
    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


For at installere andre programmer, skal du erstatte scriptfil i script:


Når du bliver bedt om det, Angiv legitimationsoplysningerne for-klyngen. Det kan tage flere minutter, før klyngen oprettes.

## <a name="call-scripts-using-net-sdk"></a>Ringe til scripts med .NET SDK 

I følgende eksempel viser, hvordan du installerer knallertmotor på Windows-baseret HDInsight klynge. Hvis du vil installere andre programmer, skal du erstatte scriptfil i koden.

**Oprette en HDInsight klynge med knallertmotor** 

1. Oprette et C# console program i Visual Studio.
2. Kør følgende kommando fra konsollen Nuget Package Manager.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight

2. Brug følgende ved hjælp af sætninger i filen Program.cs:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;

3. Placere koden i klassen med følgende:

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }


4. Tryk på **F5** for at køre programmet.


## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Understøttelse af open source-software, der bruges på HDInsight klynger
Tjenesten Microsoft Azure HDInsight er en fleksibel platform, som gør det muligt at opbygge stor data-programmer i skyen ved hjælp af et netværk af open source-teknologier udformet omkring Hadoop. Microsoft Azure indeholder et generelt understøttelsesniveau til open source-teknologier, som beskrevet i afsnittet **Understøtter omfang** af <a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure understøtter ofte stillede spørgsmål om websted</a>. Tjenesten HDInsight giver et ekstra niveau af understøttelse af nogle af komponenterne, som beskrevet nedenfor.

Der findes to typer open source-komponenter, der er tilgængelige i tjenesten HDInsight:

- **Indbyggede komponenter** - disse komponenter er forudinstalleret på HDInsight klynger og angive kernefunktioner af klyngen. For eksempel hører GARN Ressourcestyring, forespørgselssprog Hive (HiveQL) og biblioteket Mahout til denne kategori. En komplet liste over klyngekomponenter er tilgængelig i [Nyheder i de Hadoop klynge versioner, der leveres af HDInsight?](hdinsight-component-versioning.md) </a>.
- **Brugerdefineret komponenter** - dig, som bruger af klynge, kan installere eller bruge i din arbejdsbyrde alle komponenter, der er tilgængelige i community'et eller oprettet af dig.

Indbyggede komponenter understøttes fuldt ud, og Microsoft Support hjælper med at isolere og løse problemer i forbindelse med disse komponenter.

> [AZURE.WARNING] Komponenter i HDInsight klynge understøttes fuldt ud, og Microsoft Support hjælper med at isolere og løse problemer i forbindelse med disse komponenter.
>
> Brugerdefinerede komponenter modtager kommercielt begrundet support for at hjælpe dig med at foretage yderligere fejlfinding af problemet. Dette kan medføre løse problemet eller beder dig om at deltage tilgængelige kanaler for de Åbn kilde-teknologier, hvor deep ekspertise i forbindelse med teknologien, der er fundet. For eksempel, der er mange communitywebsteder, der kan bruges, ønsker: [MSDN-forum for HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Også Apache projekter har projektwebsteder på [http://apache.org](http://apache.org), for eksempel: [Hadoop](http://hadoop.apache.org/), [gnister](http://spark.apache.org/).

Tjenesten HDInsight indeholder flere måder at bruge brugerdefinerede komponenter. Uanset hvordan bruges en komponent eller installeret på klyngen, gælder det samme niveau af support. Nedenfor finder du en liste over de mest almindelige måder, brugerdefinerede komponenter, der kan bruges på HDInsight klynger:

1. Jobbet - Hadoop eller andre typer job, udføre eller bruge brugerdefinerede komponenter kan sendes til klyngen.
2. Tilpasning af klynge - under oprettelse af klynge, kan du angive flere indstillinger og brugerdefinerede komponenter, der skal installeres på klyngenoderne.
3. Prøver - For populære brugerdefinerede komponenter, Microsoft og andre muligvis give eksempler på, hvordan disse komponenter kan bruges på HDInsight klynger. Disse eksempler er angivet uden understøttelse af.

## <a name="develop-script-action-scripts"></a>Udvikle scripthandling scripts

Se [udvikle scripthandling scripts til HDInsight][hdinsight-write-script].


## <a name="see-also"></a>Se også

- [Oprette Hadoop klynger i HDInsight] [ hdinsight-provision-cluster] indeholder instruktioner om, hvordan du opretter en HDInsight klynge ved brug af andre brugerdefinerede indstillinger.
- [Udvikle scripthandling scripts til HDInsight][hdinsight-write-script]
- [Installere og bruge knallertmotor på HDInsight klynger][hdinsight-install-spark]
- [Installere og bruge R på HDInsight klynger][hdinsight-install-r]
- [Installation og brug Solr på HDInsight klynger](hdinsight-hadoop-solr-install.md).
- [Installation og brug Giraph på HDInsight klynger](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Faser under oprettelse af klynge"
