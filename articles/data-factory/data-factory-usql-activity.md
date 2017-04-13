<properties 
    pageTitle="Køre U-SQL-script på Azure Data sø Analytics fra Azure Data Factory" 
    description="Lær at behandle data ved at køre U-SQL-scripts på Azure Data sø Analytics Beregn tjeneste." 
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
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="spelluru"/>

# <a name="run-u-sql-script-on-azure-data-lake-analytics-from-azure-data-factory"></a>Køre U-SQL-script på Azure Data sø Analytics fra Azure Data Factory
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Gris](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
[Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
[En lagret Procedure](data-factory-stored-proc-activity.md)
[Data sø Analytics U-SQL](data-factory-usql-activity.md)
[.NET brugerdefineret](data-factory-use-custom-activities.md)
 
En rørledning i en Azure data factory behandler data i sammenkædede lagerplads services ved hjælp af sammenkædede Beregn services. Den indeholder en sekvens af aktiviteter, hvor hver aktivitet udfører bestemte behandling. I denne artikel beskrives **Data sø Analytics U-SQL aktivitet** , der kører et **U-SQL** -script på en **Azure Data sø Analytics** Beregn sammenkædet tjeneste. 

> [AZURE.NOTE] 
> Opret en Azure Data sø Analytics konto før du opretter en rørledning med en Data sø Analytics U-SQL-aktiviteter. For at få mere for at vide om Azure Data sø Analytics, kan du se [Introduktion til Azure Data sø analyser](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> Gennemse [oprette dit første pipeline selvstudium](data-factory-build-your-first-pipeline.md) have en detaljeret vejledning for at oprette en data fabrik, sammenkædede services, datasæt og en rørledning. Brug JSON kodestykker med Data Factory redaktør eller Visual Studio eller Azure PowerShell til at oprette Data Factory objekter.

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data sø Analytics sammenkædet Service
Du opretter en **Azure Data sø Analytics** sammenkædet service for at knytte en Azure Data sø Analytics Beregn tjeneste til en Azure data factory. Data sø Analytics U-SQL-aktivitet i pipeline refererer til denne sammenkædede tjeneste. 

I følgende eksempel indeholder JSON definition for en Azure Data sø Analytics sammenkædet tjeneste. 

    {
        "name": "AzureDataLakeAnalyticsLinkedService",
        "properties": {
            "type": "AzureDataLakeAnalytics",
            "typeProperties": {
                "accountName": "adftestaccount",
                "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
                "authorization": "<authcode>",
                "sessionId": "<session ID>", 
                "subscriptionId": "<subscription id>",
                "resourceGroupName": "<resource group name>"
            }
        }
    }


Den følgende tabel indeholder en beskrivelse for de egenskaber, der bruges i JSON definitionen. 

Egenskaben | Beskrivelse | Påkrævet
-------- | ----------- | --------
Type | Typeegenskaben skal du vælge: **AzureDataLakeAnalytics**. | Ja
Kontonavn | Azure Data sø Analytics firmanavn. | Ja
dataLakeAnalyticsUri | Azure Data sø Analytics URI. |  Nej 
godkendelse | Godkendelseskoden hentes automatisk efter at klikke på **Godkend** knap i Data Factory Editor og fuldfører OAuth logon.  | Ja 
subscriptionId | Azure abonnement-id | Ikke (Hvis der ikke er angivet, abonnement på data factory bruges). 
resourceGroupName | Azure ressource gruppenavn |  Ikke (Hvis der ikke er angivet, ressourcegruppe af data factory bruges).
Session-id | session-id'et fra OAuth godkendelse session. Hvert session-id er entydigt og kan kun bruges én gang. Sessionen Id genereres automatisk i Data Factory Editor. | Ja

Koden tilladelse, du har oprettet ved hjælp af knappen **godkende** udløber efter et stykke tid. Du kan finde udløb tidspunkter for forskellige typer brugerkonti i den følgende tabel. Du muligvis får vist følgende fejlmeddelelse, når godkendelse **token udløber**: legitimationsoplysninger handlingen fejl: invalid_grant - AADSTS70002: fejl i validering af legitimationsoplysninger. AADSTS70008: Medfølgende access Giv er udløbet eller tilbagekaldt. Sporing-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 tidsstempel: 2015-12-15 21:09:31Z

 
| Brugertype | Udløber efter |
| :-------- | :----------- | 
| Brugerkonti ikke administreres af Azure Active Directory (@hotmail.com, @live.com, osv.) | 12 timer |
| Brugerkonti, der administreres af Azure Active Directory (AAD) | 14 dage efter det sidste udsnit køre. <br/><br/>90 dage, hvis et udsnit, der er baseret på OAuth-baserede sammenkædede tjenesten kører mindst én gang hver 14 dage. |

Hvis du vil undgå/Løs fejlen, godkende igen ved hjælp af **godkende** knappen hvornår **token udløber** og genimplementeringen sammenkædede tjenesten. Du kan også oprette værdier for egenskaber for **Session-id-** og **tilladelse** fra et program ved hjælp af kode i følgende afsnit. 

  
### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Til at generere session-id- og godkendelse værdier fra et program 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Se emner i [AzureDataLakeStoreLinkedService klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)og [AuthorizationSessionGetResponse klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) få mere at vide om de Data Factory-klasser, der bruges i koden. Føj en reference til: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll for klassen WindowsFormsWebAuthenticationDialog. 
 
 
## <a name="data-lake-analytics-u-sql-activity"></a>Data sø Analytics U-SQL aktivitet 

Den følgende JSON kodestykke definerer en rørledning med en Data sø Analytics U-SQL-aktiviteter. Aktivitet definitionen har en reference til tjenesten Azure Data sø Analytics sammenkædet, du oprettede tidligere.   
  

    {
        "name": "ComputeEventsByRegionPipeline",
        "properties": {
            "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
            "activities": 
            [
                {
                    "type": "DataLakeAnalyticsU-SQL",
                    "typeProperties": {
                        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                        "scriptLinkedService": "StorageLinkedService",
                        "degreeOfParallelism": 3,
                        "priority": 100,
                        "parameters": {
                            "in": "/datalake/input/SearchLog.tsv",
                            "out": "/datalake/output/Result.tsv"
                        }
                    },
                    "inputs": [
                        {
                            "name": "DataLakeTable"
                        }
                    ],
                    "outputs": 
                    [
                        {
                            "name": "EventsByRegionTable"
                        }
                    ],
                    "policy": {
                        "timeout": "06:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "EventsByRegion",
                    "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
                }
            ],
            "start": "2015-08-08T00:00:00Z",
            "end": "2015-08-08T01:00:00Z",
            "isPaused": false
        }
    }


I følgende tabel beskrives navne og beskrivelser af egenskaber, der er specifikke for denne aktivitet. 

Egenskaben | Beskrivelse | Påkrævet
:-------- | :----------- | :--------
type | Typeegenskaben skal angives til **DataLakeAnalyticsU-SQL**. | Ja
scriptPath | Sti til mappe, der indeholder U-SQL-scriptet. Navnet på filen er store og små bogstaver. | Ingen (Hvis du bruger script)
scriptLinkedService | Sammenkædede tjeneste, der kæder opbevaring, der indeholder scriptet til fabriksindstillingerne data | Ingen (Hvis du bruger script)
script | Angiv indbygget script i stedet for at angive scriptPath og scriptLinkedService. For eksempel: "script": "Opret databasetest". | Ingen (Hvis du bruger scriptPath og scriptLinkedService)
degreeOfParallelism | Det maksimale antal noder samtidigt bruges til at køre jobbet. | Nej
prioritet | Bestemmer, hvilke job af alle, der skal vælges køres først. Nederst tal, jo højere prioritet. | Nej 
parametre | Parametre for U-SQL-script | Nej 

Se [SearchLogProcessing.txt Script definitionen](#script-definition) for definitionen af script. 

## <a name="sample-input-and-output-datasets"></a>Eksempel på input og output-datasæt

### <a name="input-dataset"></a>Input-datasæt
I dette eksempel er inputdataene placeret i en Azure sø Store (SearchLog.tsv-fil i mappen datalake/input). 

    {
        "name": "DataLakeTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/input/",
                "fileName": "SearchLog.tsv",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }   

### <a name="output-dataset"></a>Output datasæt
I dette eksempel er outputdataene produceret af U-SQL-script gemt i en Azure sø Store (datalake/output mappe). 

    {
        "name": "EventsByRegionTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="sample-data-lake-store-linked-service"></a>Eksempel datalager sø sammenkædet Service
Her er definitionen af stikprøvernes Azure datalager sø sammenkædet tjenesten bruges af input/output datasæt. 

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

Se [flytte data til og fra Azure datalager sø](data-factory-azure-datalake-connector.md) artikel beskrivelser af JSON egenskaber. 

## <a name="sample-u-sql-script"></a>Eksempel U-SQL-Script 

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv(nullEscape:"#NULL#");
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start <= DateTime.Parse("2012/02/19");
    
    OUTPUT @rs1   
        TO @out
          USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

Værdierne for **@in** og **@out** parametre i U-SQL-script videresendes dynamisk af ADF ved hjælp af afsnittet 'parameters'. I afsnittet 'parameters' i pipeline definitionen.

Du kan angive andre egenskaber som degreeOfParallelism og prioritet samt i din pipeline definition for de job, der kører på Azure Data sø Analytics-tjenesten.

## <a name="dynamic-parameters"></a>Dynamiske parametre
I eksempel pipeline definitionen tildeles ind og ud parametre med faste værdier. 

    "parameters": {
        "in": "/datalake/input/SearchLog.tsv",
        "out": "/datalake/output/Result.tsv"
    }

Det er muligt at bruge dynamiske parametre i stedet. Eksempel: 

    "parameters": {
        "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
        "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
    }

I dette tilfælde input filer er stadig valgte fra mappen /datalake/input og outputfiler der oprettes i mappen /datalake/output. Filnavnene er dynamisk baseret på starttidspunktet udsnit.  