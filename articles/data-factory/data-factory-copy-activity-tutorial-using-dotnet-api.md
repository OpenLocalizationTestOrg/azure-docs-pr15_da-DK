<properties 
    pageTitle="Selvstudium: Oprette en rørledning med kopi aktivitet ved hjælp af .NET API | Microsoft Azure" 
    description="I dette selvstudium oprette du en Azure Data Factory rørledning med en kopi aktivitet ved hjælp af .NET API." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="10/27/2016" 
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Selvstudium: Oprette en rørledning med kopi aktivitet ved hjælp af .NET API
> [AZURE.SELECTOR]
- [Oversigt og forudsætninger](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Kopiere guiden](data-factory-copy-data-wizard-tutorial.md)
- [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure ressourcestyring skabelon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Dette selvstudium viser, hvordan du opretter og overvåge en Azure data factory ved hjælp af .NET API. Pipeline i data factory bruger en kopi aktivitet til at kopiere data fra Azure Blob-lager til Azure SQL-Database.

Kopiér aktiviteten udfører på flytning af data i Azure Data Factory. Aktiviteten er drevet af en globalt tilgængelig tjeneste, som kan kopiere data mellem forskellige data butikker i et sikkert, pålideligt og SVG måde. Se [Data bevægelse aktiviteter](data-factory-data-movement-activities.md) artikel for at få mere at vide om den kopi aktivitet.   

> [AZURE.NOTE] 
> I denne artikel omhandler ikke alle Data Factory .NET API. Du kan finde oplysninger om Data Factory .NET SDK i [Data Factory.NET API Reference](https://msdn.microsoft.com/library/mt415893.aspx) . 

## <a name="prerequisites"></a>Forudsætninger
- Gå igennem [selvstudium oversigt og forudsætninger](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) til at få en oversigt over selvstudiet, og Fuldfør trinnene **betingelse** . 
- Visual Studio 2012 eller 2013 eller 2015
- Hent og Installer [Azure.NET SDK](http://azure.microsoft.com/downloads/)
- Azure PowerShell. Følg vejledningen i [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) artikel for at installere Azure PowerShell på din computer. Du kan bruge Azure PowerShell til at oprette et Azure Active Directory-program.

### <a name="create-an-application-in-azure-active-directory"></a>Oprette et program i Azure Active Directory
Oprette et Azure Active Directory-program, oprette en tjeneste hovedstolen for programmet og tildele den til rollen **Data Factory bidragyder** .  

1. Start **PowerShell**. 
1. Kør følgende kommando og angive brugernavn og adgangskode, du bruger til at logge på portalen Azure.
    
        Login-AzureRmAccount   
2. Kør følgende kommando for at få vist alle abonnementer til denne konto.

        Get-AzureRmSubscription 
3. Kør følgende kommando for at markere det abonnement, du vil arbejde med. Erstatte ** &lt;NameOfAzureSubscription** &gt; med navnet på dit Azure-abonnement. 

        Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

    > [AZURE.IMPORTANT] Bemærk ned **SubscriptionId** og **TenantId** fra outputtet af denne kommando. 
4. Opret en Azure ressourcegruppe med navnet **ADFTutorialResourceGroup** ved at køre følgende kommando i PowerShell.  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Hvis ressourcegruppen allerede findes, kan du angive om du vil opdatere den (Y) eller gemme dem som (N). 

    Hvis du bruger en anden ressourcegruppe, skal du bruge navnet på din ressourcegruppe i stedet for ADFTutorialResourceGroup i dette selvstudium.
5. Oprette et Azure Active Directory-program. 

        $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"

    Hvis du får vist følgende fejlmeddelelse, kan du angive en anden URL-adresse og køre kommandoen igen. 

        Another object with the same value for property identifierUris already exists.

6. Opret AD service hovedstolen. 

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

7. Føje service hovedstolen til rollen **Data Factory bidragyder** . 

        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

8. Få program-ID.

        $azureAdApplication

    Bemærk ned program-ID (**applicationID** fra output).

Du skal have følgende fire værdier fra disse trin: 

- Lejer-ID
- Abonnement-ID
- Program-ID 
- Adgangskode (angivet i den første kommando)   

## <a name="walkthrough"></a>Gennemgang
1. Ved hjælp af Visual Studio 2012-2013-2015, oprette et C# .NET console-program.
    1. Start **Visual Studio** 2012-2013-2015.
    2. Klik på **filer**, peg på **Ny**, og klik på **projekt**.
    3. Udvid **skabeloner**, og vælg **Visual C#**. I denne gennemgang skal du bruge C#, men du kan bruge en hvilken som helst .NET-sprog.
    4. Vælg **Console program** på listen over projekttyper i højre side.
    5. Angiv **DataFactoryAPITestApp** for navnet.
    6. Vælg **C:\ADFGetStarted** for placeringen.
    7. Klik på **OK** for at oprette projektet.
2. Klik på **Funktioner**, peg **Nuget Package Manager**, og klik på **Pakke Manager-konsollen**.
3.  Benyt følgende fremgangsmåde i **Pakke Manager-konsollen**: 
    1.  Kør følgende kommando for at installere Data Factory-pakke:`Install-Package Microsoft.Azure.Management.DataFactories`       
    2.  Kør følgende kommando for at installere Azure Active Directory-pakke, (du bruger Active Directory-API i koden):`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
6. Føje afsnittet nedenfor **appSetttings** til **App.config** -filen. Disse indstillinger anvendes af metoden, der hjælper: **GetAuthorizationHeader**. 

    Erstat værdier for ** &lt;program-ID&gt;**, ** &lt;adgangskode&gt;**, ** &lt;abonnement-ID&gt;**, og ** &lt;lejer ID&gt; ** med dine egne værdier. 

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
            </startup>
            <appSettings>
                <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
                <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
                <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

                <add key="ApplicationId" value="your application ID" />
                <add key="Password" value="Password you used while creating the AAD application" />
                <add key="SubscriptionId" value= "Subscription ID" />
                <add key="ActiveDirectoryTenantId" value="Tenant ID" />
            </appSettings>
        </configuration>
6. Tilføj følgende **ved hjælp af** udtalelser til kildefilen (Program.cs) i projektet.

        using System.Threading;
        using System.Configuration;
        using System.Collections.ObjectModel;
        
        using Microsoft.Azure.Management.DataFactories;
        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Common.Models;
        
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure;
6. Tilføj følgende kode, der opretter en forekomst af **DataPipelineManagementClient** klasse til metoden **Main** . Du kan bruge dette objekt til at oprette en data fabrik, en sammenkædet tjeneste, input- og outputområder datasæt og en rørledning. Du kan også bruge dette objekt til at overvåge udsnit i et datasæt på kørselstidspunktet.    

            // create data factory management client
            string resourceGroupName = "ADFTutorialResourceGroup";
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

    > [AZURE.IMPORTANT] 
    > Erstat værdien af **resourceGroupName** med navnet på din Azure ressourcegruppe. 
    > 
    > Opdatere navnet på data factory (**dataFactoryName**) skal være entydig. Navnet på data factory skal være globalt entydig. Se emnet med [Data Factory - navngivning af regler](data-factory-naming-rules.md) for navngivning regler for Data Factory elementer. 

7. Tilføj følgende kode, der opretter en **data factory** til metoden **Main** .

            // create a data factory
            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties() { }
                    }
                }
            );

8. Tilføj følgende kode, der opretter en **Azure-lager sammenkædet tjenesten** med metoden **Main** . 

    > [AZURE.IMPORTANT] Erstatte **storageaccountname** og **accountkey** med navn og tast til kontoen Azure-lager. 

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. Tilføj følgende kode, der opretter en **sammenkædet SQL Azure service** med metoden **Main** .
 
    > [AZURE.IMPORTANT] Erstat **servernavn**, **Databasenavn**, **brugernavn**og **din adgangskode** med navnene på dine Azure SQL server, database, bruger og adgangskode.  

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. Tilføj følgende kode, der opretter **input- og outputområder datasæt** til metoden **Main** . 

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,


            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
                            },

                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

11. Tilføj følgende kode, der **opretter og aktiverer en pipeline** til metoden **Main** . Denne pipeline har **CopyActivity** , der tager **BlobSource** som en kilde og **BlobSink** som en sink.

            // create a pipeline
            Console.WriteLine("Creating a pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipeline";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "BlobToAzureSql",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    TypeProperties = new CopyActivity()
                                    {
                                        Source = new BlobSource(),
                                        Sink = new BlobSink()
                                        {
                                            WriteBatchSize = 10000,
                                            WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                        }
                                    }
                                }
                            },
                        }
                    }
                }); 

12. Tilføj følgende kode til metoden **Main** til Hent status for et data udsnit af output datasættet. Der er kun udsnit forventet i dette eksempel.   
 
            // Pulling status within a timeout threshold
            DateTime start = DateTime.Now;
            bool done = false;

            while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
            {
                Console.WriteLine("Pulling the slice status");
                // wait before the next status check
                Thread.Sleep(1000 * 12);

                var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                    new DataSliceListParameters()
                    {
                        DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                        DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                    });

                foreach (DataSlice slice in datalistResponse.DataSlices)
                {
                    if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                    {
                        Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                        done = true;
                        break;
                    }
                    else
                    {
                        Console.WriteLine("Slice status is: {0}", slice.State);
                    }
                }
            }

14. Tilføj følgende kode for at få køre detaljer for et data udsnit til metoden **Main** .

            Console.WriteLine("Getting run details of a data slice");

            // give it a few minutes for the output slice to be ready
            Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
            Console.ReadKey();

            var datasliceRunListResponse = client.DataSliceRuns.List(
                    resourceGroupName,
                    dataFactoryName,
                    Dataset_Destination,
                    new DataSliceRunListParameters()
                    {
                        DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                    }
                );

            foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
            {
                Console.WriteLine("Status: \t\t{0}", run.Status);
                Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
                Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
                Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
                Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
                Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
                Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
            }

            Console.WriteLine("\nPress any key to exit.");
            Console.ReadKey();

13. Tilføj følgende hjælper metode bruges af metoden **Main** til klassen **Program** .  
 
        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                    result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
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


15. Udvide projektet (**DataFactoryAPITestApp**) i Solution Explorer, højreklik på **referencer**, og klik på **Tilføj Reference**. Markér afkrydsningsfeltet for "**system.Configuration i stor**" samling, og klik på **OK**. 
16. Opbygge console programmet. Klik på **Opret** i menuen og **Opbygge løsning**. 
16. Kontrollér, at der er mindst én fil i beholderen **adftutorial** i dit Azure blob-lager. Hvis ikke, oprette **Emp.txt** fil i Notesblok med følgende indhold, og Overfør den til objektbeholderen adftutorial.

        John, Doe
        Jane, Doe
     
17. Køre eksemplet ved at klikke på **fejlfinding af** -> **Starte fejlfinding** i menuen. Når du ser **få køre oplysninger i et udsnit af data**, vent et par minutter, og tryk på **ENTER**. 
18. Brug portalen Azure til at bekræfte, at data factory **APITutorialFactory** oprettes med følgende elementer: 
    - Sammenkædet service: **LinkedService_AzureStorage** 
    - Datasæt: **DatasetBlobSource** og **DatasetBlobDestination**.
    - Pipeline: **PipelineBlobSample** 
18. Kontrollere, at to medarbejderposterne er oprettet i tabellen "**emp**" i den angivne Azure SQL-database.

## <a name="next-steps"></a>Næste trin

- Læse [Data bevægelse aktiviteter](data-factory-data-movement-activities.md) artikel, der indeholder detaljerede oplysninger om den kopi aktivitet, du har brugt i selvstudiet.
- Du kan finde oplysninger om Data Factory .NET SDK i [Data Factory.NET API Reference](https://msdn.microsoft.com/library/mt415893.aspx) . I denne artikel omhandler ikke alle Data Factory .NET API. 

 
