<properties 
   pageTitle="Komme i gang med Data sø analyser ved hjælp af REST-API | Microsoft Azure" 
   description="Bruge WebHDFS REST API'er til at udføre handlinger på Data sø Analytics" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>Introduktion til Azure Data sø analyser ved hjælp af REST API'er

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Lær at bruge WebHDFS REST API'er og Data sø Analytics REST API'er til at administrere Data sø Analytics-konti, job og katalog. 

## <a name="prerequisites"></a>Forudsætninger

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

- **Oprette et Azure Active Directory-program**. Du kan bruge Azure AD-programmet til at godkende Data sø Analytics-tjenesteprogram med Azure AD. Der er forskellige måder til at godkende med Azure AD, som er **Slutbrugerlicensaftale godkendelse** eller **tjeneste til godkendelse**. Instruktioner og yderligere oplysninger om, hvordan du godkende under [godkende med Data sø analyser ved hjælp af Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

- [cURL](http://curl.haxx.se/). I denne artikel bruges krøllet til at vise, hvordan du kan foretage REST-API-opkald på en Data sø Analytics-konto.

## <a name="authenticate-with-azure-active-directory"></a>Godkende med Azure Active Directory

Der findes to metoder til at godkende med Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Slutbrugere godkendelse (interaktive)

Bruger denne metode, programmet beder brugeren om at logge på og alle handlinger udføres i konteksten for brugeren. 

Følg disse trin for interaktive godkendelse:

1. Omdirigere brugeren til følgende URL-adressen til dit program:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<OMDIRIGER URI > skal kodes til brug i en URL-adresse. Brug så https://localhost, `https%3A%2F%2Flocalhost`)

    Du kan erstatte pladsholder værdierne i URL-adressen ovenfor og sæt den ind i en webbrowser adresselinjen til dette selvstudium. Du bliver brugeren omdirigeret for at godkende ved hjælp af dit Azure logon. Når du blev logger på, vises svaret i browserens adresselinje. Svaret bliver i følgende format:
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Registrere godkendelseskoden fra svaret. Dette selvstudium skal du kopiere godkendelseskoden til fra adresselinjen i webbrowseren og sende dem i POST-anmodningen til det token slutpunkt, som vist nedenfor:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] I dette tilfælde den \<OMDIRIGER URI > skal ikke kodes.

3. Svaret er et JSON-objekt, der indeholder et adgangstoken (f.eks., `"access_token": "<ACCESS_TOKEN>"`) og et Opdater token (f.eks., `"refresh_token": "<REFRESH_TOKEN>"`). Dit program bruger adgangstoken ved adgang til Azure sø datalager og Opdater tokenet til at få en anden adgangstoken, når et adgangstoken udløber.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Når adgangstoken udløber, kan du anmode om en ny adgangstoken ved hjælp af token opdatering, som vist nedenfor:

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Du kan finde flere oplysninger om interaktive brugergodkendelse, [godkendelseskode give flow](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Tjenesten service-godkendelse (ikke-interaktiv)

Bruger denne metode, indeholder programmet sin egen legitimationsoplysninger for at udføre handlinger. I dette, skal du udstede en POST-anmodning som vist nedenfor: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Output fra mødeindkaldelsen indeholder et godkendelse token (markeret med `access-token` i outputtet nedenfor), du efterfølgende der går med dine REST-API-opkald. Gemme dette godkendelse symbol i en tekstfil Du skal bruge dette senere i denne artikel.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

I denne artikel bruges på **ikke-interaktiv** metode. Du kan finde flere oplysninger om ikke-interaktiv (service-tjenesten opkald), [tjenesten til tjenesten opkald ved hjælp af legitimationsoplysninger](https://msdn.microsoft.com/library/azure/dn645543.aspx).
## <a name="create-a-data-lake-analytics-account"></a>Oprette en Data sø Analytics-konto

Du skal oprette en Azure ressourcegruppe og en sø datalager-konto, før du kan oprette en Data sø Analytics-konto.  Se [oprette en sø datalager-konto](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account).

Følgende kommando for krøllet viser, hvordan du opretter en konto:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

Erstatte \< `REDACTED` \> med token godkendelse \< `AzureSubscriptionID` \> med dit abonnement-ID \< `AzureResourceGroupName` \> med et eksisterende Azure ressourcegruppe navn og \< `NewAzureDataLakeAnalyticsAccountName` \> med et nyt Data sø Analytics-kontonavn. Anmodning om data for denne kommando findes i filen **CreateDatalakeAnalyticsAccountRequest.json** , som er knyttet til den `-d` parameter ovenfor. Indholdet af filen input.json minde om følgende:

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>Listen Data sø Analytics konti i et abonnement

Følgende kommando for krøllet viser, hvordan med listen over konti i et abonnement:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

Erstatte \< `REDACTED` \> med token godkendelse \< `AzureSubscriptionID` \> med dit abonnement-ID. Output ligner:

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Få oplysninger om en Data sø Analytics-konto

Følgende kommando for krøllet viser, hvordan du får en kontooplysninger:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

Erstatte \< `REDACTED` \> med token godkendelse \< `AzureSubscriptionID` \> med dit abonnement-ID \< `AzureResourceGroupName` \> med et eksisterende Azure ressourcegruppe navn og \< `DataLakeAnalyticsAccountName` \> med navnet på en eksisterende Data sø Analytics-konto. Output ligner:

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Listen Data sø butikker for en Data sø Analytics-konto

Følgende kommando for krøllet viser, hvordan til at få vist Data sø butikker på en konto:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

Erstatte \< `REDACTED` \> med token godkendelse \< `AzureSubscriptionID` \> med dit abonnement-ID \< `AzureResourceGroupName` \> med et eksisterende Azure ressourcegruppe navn og \< `DataLakeAnalyticsAccountName` \> med navnet på en eksisterende Data sø Analytics-konto. Output ligner:

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>Sende U-SQL-job

Følgende kommando for krøllet viser hvordan du sender et U-SQL-job:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

Erstatte \< `REDACTED` \> med token godkendelse \< `DataLakeAnalyticsAccountName` \> med navnet på en eksisterende Data sø Analytics-konto. Anmodning om data for denne kommando findes i filen **SubmitADLAJob.json** , som er knyttet til den `-d` parameter ovenfor. Indholdet af filen input.json minde om følgende:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

Output ligner:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>Listen U-SQL-job

Følgende kommando for krøllet viser, hvordan med listen over U-SQL-job:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

Erstatte \< `REDACTED` \> med token godkendelse og \< `DataLakeAnalyticsAccountName` \> med navnet på en eksisterende Data sø Analytics-konto. 


Output ligner:

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>Få katalogelementer

Følgende kommando for krøllet viser, hvordan du får databaserne fra kataloget:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

Output ligner:

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>Se også

- For at se en mere kompleks forespørgsel skal du se [analysér websted logfiler, der bruger Azure Data sø analyser](data-lake-analytics-analyze-weblogs.md).
- For at komme i gang U-SQL udviklingsprogrammer skal du se [udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Få U-SQL under [Introduktion til Azure Data sø Analytics U-SQL-sprog](data-lake-analytics-u-sql-get-started.md).
- Se [administrere Azure Data sø Analytics Azure-portalen](data-lake-analytics-manage-use-portal.md)til af administrationsopgaver.
- For at få et overblik over Data sø Analytics skal du se [Oversigt over Azure Data sø analyser](data-lake-analytics-overview.md).
- For at se det samme selvstudium ved hjælp af andre værktøjer skal du klikke på fanen vælgere øverst på siden.
- Hvis du vil logge diagnosticeringsoplysninger, se [adgang til diagnosticering logfiler for Azure Data sø analyser](data-lake-analytics-diagnostic-logs.md)
