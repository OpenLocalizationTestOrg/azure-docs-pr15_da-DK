<properties 
    pageTitle="Sådan konfigureres Azure Machine Learning slutpunkter i Stream Analytics | Microsoft Azure" 
    description="Maskine sprog brugerdefinerede funktioner i Stream Analytics"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
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
    ms.author="jeffstok"
/>

# <a name="machine-learning-integration-in-stream-analytics"></a>Machine Learning integration i Stream Analytics

Stream Analytics understøtter brugerdefinerede funktioner, der kalder til Azure Machine Learning slutpunkter. REST-API understøttelse af denne funktion er detaljeret i [Stream Analytics REST-API bibliotek](https://msdn.microsoft.com/library/azure/dn835031.aspx). I denne artikel indeholder supplerende oplysninger, der kræves for vellykket gennemførelse af denne egenskab i Stream Analytics. En selvstudium har også blevet sendt og findes [her](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Oversigt: Azure Machine Learning terminologi

Microsoft Azure Machine Learning indeholder et samarbejde, træk og slip værktøj, du kan bruge til at oprette, teste og implementere løsninger skønnet analyser af dine data. *Azure Machine Learning Studio*kaldes dette værktøj. Studio bruges til at interagere med Machine Learning-ressourcer og nemt oprette, teste og forbedrer dit design. Disse ressourcer samt deres definitioner er under.

- **Arbejdsområde**: *arbejdsområde* er en objektbeholder, der indeholder alle andre maskine undervisningsressourcer sammen i en objektbeholder til administration og styring.
- **Forsøg**: *forsøg* er oprettet af data forskere at udnytte datasæt og træne en machine learning model.
- **Slutpunkt**: *slutpunkter* er objektet Azure maskine kan bruges til at bruge funktioner som input, en bestemt maskine learning model og returnere karakter output.
- **Vundne webtjeneste**: en *vundne webtjeneste* er en samling af slutpunkter, som nævnt ovenfor.

Hvert slutpunkt har API'er til udførelse af batchen og synkron udførelse af. Stream Analytics bruger synkron udførelse af. Bestemte tjenestens navn er en [Anmodning/svar tjeneste](../machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs) i AzureML studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Machine Learning-ressourcer, der kræves for Stream Analytics-job

Er alle nødvendige til vellykket udførelse behandlingen af job, en anmodning/svar slutpunkt, en [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md#get-an-azure-machine-learning-authorization-key)og en swagger definition med henblik på Stream analyser. Stream Analytics har flere ark, der konstruktioner URL-adressen for swagger slutpunkt, søger efter i grænsefladen og returnerer en standard UDF definition til brugeren.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Konfigurere en Stream analyser og Machine Learning UDF via REST-API

Du kan konfigurere tingene for at ringe funktioner Azure maskine sprog ved hjælp af REST API'er. Trinnene er som følger:

1. Oprette et Stream Analytics-job
2. Definere en input
3. Definere en output
4. Oprette en brugerdefineret funktion (UDF)
5. Skrive en Stream Analytics-transformation, der kalder udf-filen
6. Starte jobbet

## <a name="creating-a-udf-with-basic-properties"></a>Oprette en med grundlæggende egenskaber

Som et eksempel opretter følgende eksempelkode en skalarværdi UDF med navnet *newudf* , som binder til Azure Machine Learning ark. Bemærk, at det *første eller sidste ark* (service URI) kan findes på siden API Hjælp for den valgte tjeneste og *apiKey* kan findes på siden Services primære.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Eksempel anmodningsteksten:  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Ring til RetrieveDefaultDefinition slutpunkt for standard UDF

Når skelet UDF oprettes behov fuldført definitionen af udf-filen. RetreiveDefaultDefinition slutpunktet hjælper dig med at få en standard definition for en skalarværdi funktion, der er bundet til et Azure Machine Learning slutpunkt. Nedenstående data skal du få standard UDF definitionen for en skalarværdi funktion, der er bundet til et Azure Machine Learning slutpunkt. Den angive ikke den faktiske slutpunkt, som den allerede har fået under læg anmodning. Stream Analytics kalder slutpunktet indeholdt i anmodningen, hvis den findes eksplicit. Ellers bruges den, der oprindeligt refereres til. De UDF tager en enkelt streng parameter (en sætning) og returnerer en enkelt output af typen streng her, hvilket angiver sætningen "synspunkt" navn.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Eksempel anmodningsteksten:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

En stikprøve output dette ville se noget i stil nedenfor.  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="patch-udf-with-the-response"></a>Programrettelse UDF med svaret 

Nu udf-filen skal være installeret for at udbedre med forrige svaret, som vist nedenfor.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Anmodningsteksten (Output fra RetrieveDefaultDefinition):

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementere Stream Analytics transformation til at ringe til udf-filen

Nu forespørge UDF (her kaldet scoreTweet) efter hver indtastning begivenhed og skrive et svar for hændelsen til output.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
