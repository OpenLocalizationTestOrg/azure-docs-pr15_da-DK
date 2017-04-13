<properties
    pageTitle="Administration af .NET SDK til Stream Analytics | Microsoft Azure"
    description="Introduktion til Stream Analytics Management .NET SDK. Lær, hvordan du konfigurerer og køre analytics job: oprette et projekt, input, output og transformationer."
    keywords=".NET SDK, analytics API"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Administration af .NET SDK: Konfigurere og køre analytics job med Azure Stream Analytics API til .NET

Lær, hvordan du konfigurerer en køre analytics-job ved hjælp af Stream Analytics API til .NET ved hjælp af administration .NET SDK. Konfigurere et projekt, oprette input- og outputområder kilder, transformationer og start og stop job. Du kan streame data fra Blob-lager eller fra en hændelse hub for din analytics-job.

Se [management referencedokumentation for Stream Analytics-API til .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics er en komplet administreret tjeneste give lav ventetid meget tilgængelige SVG, og kompleks begivenhed behandling over streaming data i skyen. Stream Analytics giver mulighed for at konfigurere streaming job til at analysere datastreams, og gør muligt at drive i realtid analyser.  


## <a name="prerequisites"></a>Forudsætninger
Før du begynder i denne artikel, skal du have følgende:

- Installer Visual Studio 2012 eller 2013.
- Hent og Installer [Azure.NET SDK](https://azure.microsoft.com/downloads/).
- Oprette en Azure ressourcegruppe i dit abonnement. Følgende er et eksempel Azure PowerShell-script. Azure PowerShell kan finde oplysninger i [installere og konfigurere Azure PowerShell](../powershell-install-configure.md);  


        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


-   Konfigurere en inputkilde og output destinationswebsteder til at bruge. Til kan se instruktioner [Tilføje input](stream-analytics-add-inputs.md) til konfiguration af en stikprøve input og [Tilføje lagrer](stream-analytics-add-outputs.md) du konfigurerer et eksempel på output.


## <a name="set-up-a-project"></a>Konfigurere et projekt

Hvis du vil oprette bruger et analytics-job API'EN Stream Analytics for .NET, først konfigurere dit projekt.

1. Oprette et Visual Studio C# .NET console-program.
2. Kør følgende kommandoer for at installere NuGet pakkerne i pakke Manager-konsollen. Den første del er Azure Stream Analytics Management .NET SDK. Den anden opgave er den Azure Active Directory-klient, der skal bruges til godkendelse.

        Install-Package Microsoft.Azure.Management.StreamAnalytics
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Føje afsnittet nedenfor **appSettings** til App.config-filen:

        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>


    Erstatte værdier for **SubscriptionId** og **ActiveDirectoryTenantId** med abonnementet Azure og lejer id'er. Du kan få disse værdier ved at køre følgende Azure PowerShell-cmdlet:

        Get-AzureAccount

5. Tilføj følgende **ved hjælp af** udtalelser til kildefilen (Program.cs) i projektet:

        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.  Tilføje en godkendelsesmetode hjælper:

        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(
                        ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                        ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AsaClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  


## <a name="create-a-stream-analytics-management-client"></a>Oprette en Stream Analytics management-klient

Et **StreamAnalyticsManagementClient** objekt tillader dig at administrere jobbet samt job components, som input, output og transformation.

Føj følgende kode til starten af metoden **Main** :

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

Variablen **resourceGroupName** værdien skal være den samme som navnet på den ressourcegruppe, du har oprettet eller valgte i de nødvendige trin.

Referere til [godkendelse af en tjeneste sikkerhedskonto med Azure ressourcestyring](../resource-group-authenticate-service-principal.md)for at automatisere legitimationsoplysninger præsentation aspekter af oprettelse af jobbet.

De resterende afsnit i denne artikel forudsætter, at denne kode er i starten af metoden **Main** .

## <a name="create-a-stream-analytics-job"></a>Oprette et Stream Analytics-job

Følgende kode opretter et Stream Analytics job under ressourcegruppen, du har defineret. Du føjer en input, output og transformation til jobbet senere.

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>Oprette en Stream Analytics inputkilde

Følgende kode opretter en Stream Analytics inputkilde med blob inputkilde type og CSV-serialisering. Hvis du vil oprette en begivenhed hub inputkilde, skal du bruge **EventHubStreamInputDataSource** i stedet for **BlobStreamInputDataSource**. På samme måde, kan du tilpasse serialiseringstype af inputkilden.

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Inputkilder, er fra en Blob-lager eller en begivenhed hub bundet til et bestemt job. For at bruge den samme inputkilde til forskellige job, skal du ringe metoden igen og angive et andet jobnavn.


## <a name="test-a-stream-analytics-input-source"></a>Teste en Stream Analytics inputkilde

Metoden **TestConnection** tester, om Stream Analytics jobbet er kunne oprette forbindelse til inputkilden samt andre aspekter, der er specifikke for typen inputkilde. For eksempel i blob inputkilden du har oprettet i et tidligere trin, kontrollerer metoden, at den kontonavn lager og en nøgle kan bruges til at oprette forbindelse til kontoen lagerplads samt Kontrollér, at den angivne beholder findes.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Oprette et Stream Analytics output mål

Oprette et output mål er meget svarer til at oprette en Stream Analytics inputkilde. Sådan inputkilder, er output destinationer bundet til et bestemt job. Hvis du vil bruge den samme output destination for forskellige job, skal du ringe metoden igen og angive et andet jobnavn.

Følgende kode opretter et output mål (Azure SQL-database). Du kan tilpasse output destinationens datatype og/eller serialiseringstype.

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>Teste en Stream Analytics output destination

En Stream Analytics output destination har også metoden **TestConnection** til test forbindelser.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Oprette en Stream Analytics-transformation

Følgende kode opretter en Stream Analytics-transformation med forespørgslen "Vælg * fra Input" og angiver, at tildele én streaming enhed til Stream Analytics jobbet. Du kan finde flere oplysninger om at justere streaming enheder, [skala Azure Stream Analytics job](stream-analytics-scale-jobs.md).


    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Som input og output, er en transformationer også knyttet til det specifikke Stream Analytics-job det blev oprettet under.

## <a name="start-a-stream-analytics-job"></a>Starte et Stream Analytics-job
Når du har oprettet et Stream Analytics-job og dens input(s), output(s) og transformation, kan du starte jobbet ved at ringe til metoden **Start** .

Følgende eksempel kode starter en Stream Analytics sag med et brugerdefineret output starttidspunkt, der er angivet til 12 December 2012 12:12:12 UTC:

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## <a name="stop-a-stream-analytics-job"></a>Standse et Stream Analytics-job
Du kan stoppe en Stream Analytics-job, der kører ved at ringe til metoden **stoppe** .

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Slette et Stream Analytics-job
Metoden **slette** sletter jobbet samt de underliggende underordnede ressourcer, herunder input(s), output(s) og transformation af jobbet.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## <a name="get-support"></a>Få support
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forummet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Næste trin

Du har lære grundlæggende om brug af en .NET SDK til at oprette og køre analytics job. Hvis du vil vide mere, skal du se følgende:

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Management .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
