<properties
    pageTitle="Administrere Hadoop klynger i HDInsight med .NET SDK | Microsoft Azure"
    description="Lær at udføre administrative opgaver for Hadoop klynger i HDInsight ved hjælp af HDInsight .NET SDK."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Administrere Hadoop klynger i HDInsight ved hjælp af .NET SDK

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Få mere at vide, hvordan du administrerer HDInsight klynger ved hjælp af [HDInsight.NET SDK](https://msdn.microsoft.com/library/mt271028.aspx).


**Forudsætninger**

Før du begynder i denne artikel, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


##<a name="connect-to-azure-hdinsight"></a>Oprette forbindelse til Azure HDInsight

Du skal bruge følgende Nuget-pakker:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.HDInsight

Følgende eksempel-kode viser, hvordan du opretter forbindelse til Azure, før du kan administrere HDInsight klynger under abonnementet Azure.

    using System;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.HDInsight;
    using Microsoft.Azure.Management.HDInsight.Models;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    namespace HDInsightManagement
    {
        class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
            // Replace with your AAD tenant ID if necessary
            private const string TenantId = UserTokenProvider.CommonTenantId; 
            private const string SubscriptionId = "<Your Azure Subscription ID>";
            // This is the GUID for the PowerShell client. Used for interactive logins in this example.
            private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

            static void Main(string[] args)
            {
                // Authenticate and get a token
                var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
                // Flag subscription for HDInsight, if it isn't already.
                EnableHDInsight(authToken);
                // Get an HDInsight management client
                _hdiManagementClient = new HDInsightManagementClient(authToken);

                // insert code here

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
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
        }
    }

Du skal vist meddelelsen, når du kører dette program.  Hvis du ikke vil se bliver bedt om det, kan du se [oprette ikke-interaktiv godkendelse .NET HDInsight programmer](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

##<a name="create-clusters"></a>Oprette klynger

Se [oprette Linux-baserede klynger i HDInsight ved hjælp af .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

##<a name="list-clusters"></a>Listen klynger

Følgende kodestykke vises klynger og visse egenskaber:

    var results = _hdiManagementClient.Clusters.List();
    foreach (var name in results.Clusters) {
        Console.WriteLine("Cluster Name: " + name.Name);
        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
        Console.WriteLine("\t Cluster location: " + name.Location);
        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
    }

##<a name="delete-clusters"></a>Slette klynger

Brug følgende kodestykke til at slette en klynge synkront eller asynkront: 

    _hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
    _hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
            
##<a name="scale-clusters"></a>Skala klynger
Klynge skalering funktion gør det muligt at ændre antallet af knuder, som bruges af en klynge, der kører på Azure HDInsight uden at skulle oprette klyngen.

>[AZURE.NOTE] Kun clusters med HDInsight version 3.1.3 eller højere understøttes. Hvis du er usikker på, hvilken version af din klynge, kan du se siden Egenskaber.  Se [liste og Vis klynger](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

Virkningen af at ændre antallet af dataknuder for hver type af klynge, der understøttes af HDInsight:

- Hadoop

    Du kan øge antallet af knuder arbejder i en Hadoop-klynge, der kører uden at påvirke alle andre ventende eller kører job problemfrit. Nyt job kan også sendes, når handlingen er i gang. Fejl i handlingen skalering håndteres problemfrit, så klyngen er altid tilbage i en funktionel tilstand.

    Når en Hadoop klynge skaleres ved at reducere antallet af dataknuder,, er nogle af tjenesterne i klyngen genstartet. Derved alle, der kører og ventende job mislykkes ved afslutningen af handlingen skalering. Du kan dog indsender job, når handlingen er fuldført.

- HBase

    Du kan problemfrit tilføjer eller fjerner noder til din HBase klynge, mens den kører. Regionale servere afstemmes automatisk i løbet af et par minutter skalering er udført. Du kan også manuelt saldo de regionale servere, ved at logge ind i headnode af klynge og køre følgende kommandoer fra et kommandopromptvindue:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- Storm

    Du kan problemfrit tilføjer eller fjerner data noder til din Storm klynge, mens den kører. Men når installationen er fuldført af handlingen skalering, skal du genoprette topologien.

    Skulle genoprette balancen kan gøres på to måder:

    * Storm web brugergrænseflade
    * Værktøj til kommandolinjen (CLI)

    Se [Apache Storm dokumentation](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) for at få flere oplysninger.

    Webdelen Storm brugergrænseflade findes på HDInsight klyngen:

    ![hdinsight storm skala rebalance](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Her er et eksempel, hvordan du kan bruge kommandoen CLI for at genoprette Storm topologien:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Følgende kodestykke viser, hvordan du kan ændre størrelsen på en klynge synkront eller asynkront:

    _hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    _hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    

##<a name="grantrevoke-access"></a>Giv/revoke access

HDInsight klynger har de følgende HTTP-webtjenester (alle disse tjenester har RESTful slutpunkter):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Disse tjenester er som standard tildelt til access. Du kan revoke/give adgang til. Ophæve:

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = false,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);

Give:

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = enable,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);


>[AZURE.NOTE] Ved at give/tilbagekalde adgang, kan du nulstille klynge brugernavn og din adgangskode.

Dette kan gøres via portalen. Se [Administrere HDInsight ved hjælp af portalen Azure][hdinsight-admin-portal].

##<a name="update-http-user-credentials"></a>Opdatere HTTP brugerlegitimationsoplysninger

Det er den samme fremgangsmåde som [Giv/revoke HTTP adgang](#grant/revoke-access). Hvis klyngen har fået tildelt HTTP-adgang, skal du først fjerne den.  Og derefter tildele adgang med nye HTTP brugerens legitimationsoplysninger.


##<a name="find-the-default-storage-account"></a>Finde lagerplads standardkontoen

Følgende kodestykke viser, hvordan du få kontonavn standard lager og standard lagerplads kontonøgle for en klynge.

    var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
    foreach (var key in results.Configuration.Keys)
    {
        Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
    }


##<a name="submit-jobs"></a>Sende job

**Sende MapReduce job**

Se [køre Hadoop MapReduce eksempler i HDInsight](hdinsight-hadoop-run-samples-linux.md).

**Sende Hive job** 

Se [køre Hive forespørgsler ved hjælp af .NET SDK](hdinsight-hadoop-use-hive-dotnet-sdk.md).

**Sende gris job**

Se [køre gris job ved hjælp af .NET SDK](hdinsight-hadoop-use-pig-dotnet-sdk.md).

**Sende Sqoop job**

Se [Brug Sqoop med HDInsight](hdinsight-hadoop-use-sqoop-dotnet-sdk.md).

**Sende Oozie job**

Se [Brug Oozie med Hadoop til at definere og køre en arbejdsproces i HDInsight](hdinsight-use-oozie-linux-mac.md).

##<a name="upload-data-to-azure-blob-storage"></a>Overføre data til Azure Blob-lager
Se [overføre data til HDInsight][hdinsight-upload-data].


## <a name="see-also"></a>Se også
* [HDInsight .NET SDK referencedokumentation](https://msdn.microsoft.com/library/mt271028.aspx)
* [Administrere HDInsight ved hjælp af portalen Azure][hdinsight-admin-portal]
* [Administrere HDInsight ved hjælp af en kommandolinjen][hdinsight-admin-cli]
* [Oprette HDInsight klynger][hdinsight-provision]
* [Overføre data til HDInsight][hdinsight-upload-data]
* [Introduktion til Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md


