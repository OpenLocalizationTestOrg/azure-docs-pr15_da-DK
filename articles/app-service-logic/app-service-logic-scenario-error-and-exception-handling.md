<properties
    pageTitle="Logføring og fejlhåndtering i logik Apps | Microsoft Azure"
    description="Få vist en virkelige liv use case til avanceret fejl håndtering af og logger med logik Apps"
    keywords=""
    services="logic-apps"
    authors="hedidin"
    manager="anneta"
    editor=""
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="b-hoedid"/>

# <a name="logging-and-error-handling-in-logic-apps"></a>Logføring og fejlhåndtering i logik Apps

I denne artikel beskrives, hvordan du kan udvide en logik app for at understøtte bedre håndtering af undtagelser. Det er en virkelige liv use case og vores svar på spørgsmålet om, "Logik Apps understøtter undtagelse og fejlhåndtering?"

>[AZURE.NOTE]Den aktuelle version af funktionen logik Apps i Microsoft Azure App Service indeholder en standardskabelon til handling svar.
>Dette omfatter både intern validering og fejlsvar returneres fra en API-app.

## <a name="overview-of-the-use-case-and-scenario"></a>Oversigt over Brug store og små bogstaver og scenarie

Følgende tekstenheden er use case til denne artikel.
En velkendt sundhedssektoren organisation ansat os til at udvikle en Azure løsning, der opretter en patienter portal ved hjælp af Microsoft Dynamics CRM Online. De skal bruges til at sende aftale poster mellem Dynamics CRM Online patienter portalen og Salesforce.  Vi er blevet bedt om at bruge [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) standarden for alle patienter poster.

Projektet havde to overordnede krav:  

 -  En metode til at logge af poster, der sendes fra portalen Dynamics CRM Online
 -  En metode til at få vist eventuelle fejl, der er opstået i arbejdsprocessen


## <a name="how-we-solved-the-problem"></a>Hvordan vi løser problemet

>[AZURE.TIP] Du kan få vist en overordnet video af projektet [Integration brugergruppe](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Integration af bruger gruppe").

Vi valgte [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/ "Azure DocumentDB") som et lager for log og fejloplysninger posterne (DocumentDB refererer til poster som dokumenter). Da logik Apps har en standardskabelon for alle svar, vil vi ikke behøver at oprette et brugerdefineret skema. Vi kunne oprette en API-app til at **indsætte** og **forespørgsel** for både fejl og log poster. Et skema kan for hver API App også angive.  

Et andet krav blev at tømme poster efter en bestemt dato. DocumentDB har en egenskab, kaldet [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Time to Live") (TTL), hvilket er tilladt os til at konfigurere en **Time to Live** værdi for hver post eller af websteder. Dette er slettet skal manuelt slette poster i DocumentDB.

### <a name="creation-of-the-logic-app"></a>Oprettelse af appen logik

Det første trin er at oprette appen logik og indlæse den i designer. I dette eksempel bruger vi overordnet / underordnet logik apps. Lad os antage, at vi allerede har oprettet overordnet og vil oprette et underordnet logik app.

Da vi vil logføring den post, der kommer ud af Dynamics CRM Online, Lad os starte øverst. Vi skal bruge en anmodning om udløser, fordi den overordnede logik app udløser dette barn.

> [AZURE.IMPORTANT] For at fuldføre dette selvstudium, skal du oprette en DocumentDB database og to af websteder (logføring og fejl).

### <a name="logic-app-trigger"></a>Logik app udløser

Vi bruger en anmodning om udløser som vist i følgende eksempel.

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


### <a name="steps"></a>Trin

Vi skal logge kilden (anmodning) til posten patienter fra portalen Dynamics CRM Online.

1. Vi har brug at få en ny aftalepost fra Dynamics CRM Online.
    Udløser kommer fra CRM giver os den **CRM PatentId**, **posttype**, **nyt eller opdateret post** (nye eller opdatere boolesk værdi), og **SalesforceId**. **SalesforceId** kan være null, fordi den bruges kun til en opdatering.
    Vi får CRM-post ved hjælp af CRM **PatientID** og **Record Type**.
1. Vi skal nu føje vores DocumentDB API app **InsertLogEntry** handling, som vist i følgende tallene.


#### <a name="insert-log-entry-designer-view"></a>Indsætte log visningen i Forespørgselsdesigner med posten

![Indsætte en post i logfil](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### <a name="insert-error-entry-designer-view"></a>Indsætte fejl visningen i Forespørgselsdesigner med posten
![Indsætte en post i logfil](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### <a name="check-for-create-record-failure"></a>Kontrollér, om Opret post fejl

![Betingelse](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)


## <a name="logic-app-source-code"></a>Logik app-kildekode

>[AZURE.NOTE]  Følgende er kun eksempler. Da dette selvstudium er baseret på en implementering i øjeblikket fremstilling, vises værdien af en **Kilde Node** muligvis ikke egenskaber, der er relateret til planlægning af en aftale.

### <a name="logging"></a>Logføring
I følgende eksempel-logik app kode viser, hvordan du håndterer logføring.

#### <a name="log-entry"></a>Post i logfilen
Dette er logik app-kildekoden til at indsætte en post i logfilen.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Log anmodning

Dette er sendt til appen API log anmodningsmeddelelsen.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{\"Pragma\":\"no-cache\",\"x-ms-request-id\":\"e750c9a9-bd48-44c4-bbba-1688b6f8a132\",\"OData-Version\":\"4.0\",\"Cache-Control\":\"no-cache\",\"Date\":\"Fri, 10 Jun 2016 22:31:56 GMT\",\"Set-Cookie\":\"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1\",\"Server\":\"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0\",\"X-AspNet-Version\":\"4.0.30319\",\"X-Powered-By\":\"ASP.NET\",\"Content-Length\":\"1935\",\"Content-Type\":\"application/json; odata.metadata=minimal; odata.streaming=true\",\"Expires\":\"-1\"}"
        }
    }

```


#### <a name="log-response"></a>Log svar

Dette er svarmeddelelse log fra API app.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "\"0400fc2f-0000-0000-0000-575b3ff00000\"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{\"Pragma\":\"no-cache\",\"x-ms-request-id\":\"e750c9a9-bd48-44c4-bbba-1688b6f8a132\",\"OData-Version\":\"4.0\",\"Cache-Control\":\"no-cache\",\"Date\":\"Fri, 10 Jun 2016 22:31:56 GMT\",\"Set-Cookie\":\"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1\",\"Server\":\"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0\",\"X-AspNet-Version\":\"4.0.30319\",\"X-Powered-By\":\"ASP.NET\",\"Content-Length\":\"1935\",\"Content-Type\":\"application/json; odata.metadata=minimal; odata.streaming=true\",\"Expires\":\"-1\"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Nu Lad os se på fejlhåndtering trin.


### <a name="error-handling"></a>Håndtering af fejl

Følgende eksempel-logik Apps kode viser, hvordan du kan implementere fejlhåndtering.

#### <a name="create-error-record"></a>Oprette fejl post

Dette er kildekode logik Apps for at oprette fejlposten en.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}          
```

#### <a name="insert-error-into-documentdb--request"></a>Indsætte fejl i DocumentDB - anmodning

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{\"Account_Class_vod__c\":\"PRAC\",\"Account_Status_MED__c\":\"I\",\"CRM_HUB_ID__c\":\"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0\",\"Credentials_vod__c\",\"DTC_ID_MED__c\":\"\",\"Fax\":\"\",\"FirstName\":\"A\",\"Gender_vod__c\":\"\",\"IMS_ID__c\":\"\",\"LastName\":\"BAILEY\",\"MasterID_mp__c\":\"\",\"C_ID_MED__c\":\"851588\",\"Middle_vod__c\":\"\",\"NPI_vod__c\":\"\",\"PDRP_MED__c\":false,\"PersonDoNotCall\":false,\"PersonEmail\":\"\",\"PersonHasOptedOutOfEmail\":false,\"PersonHasOptedOutOfFax\":false,\"PersonMobilePhone\":\"\",\"Phone\":\"\",\"Practicing_Specialty__c\":\"FM - FAMILY MEDICINE\",\"Primary_City__c\":\"\",\"Primary_State__c\":\"\",\"Primary_Street_Line2__c\":\"\",\"Primary_Street__c\":\"\",\"Primary_Zip__c\":\"\",\"RecordTypeId\":\"012U0000000JaPWIA0\",\"Request_Date__c\":\"2016-06-10T22:31:55.9647467Z\",\"ONY_ID__c\":\"\",\"Specialty_1_vod__c\":\"\",\"Suffix_vod__c\":\"\",\"Website\":\"\"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-documentdb--response"></a>Indsætte fejl i DocumentDB - svar


``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "\"0c00eaac-0000-0000-0000-575b3fdc0000\"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{\"Account_Class_vod__c\":\"PRAC\",\"Account_Status_MED__c\":\"I\",\"CRM_HUB_ID__c\":\"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0\",\"Credentials_vod__c\":\"DO - Degree level is DO\",\"DTC_ID_MED__c\":\"\",\"Fax\":\"\",\"FirstName\":\"A\",\"Gender_vod__c\":\"\",\"IMS_ID__c\":\"\",\"LastName\":\"BAILEY\",\"MterID_mp__c\":\"\",\"Medicis_ID_MED__c\":\"851588\",\"Middle_vod__c\":\"\",\"NPI_vod__c\":\"\",\"PDRP_MED__c\":false,\"PersonDoNotCall\":false,\"PersonEmail\":\"\",\"PersonHasOptedOutOfEmail\":false,\"PersonHasOptedOutOfFax\":false,\"PersonMobilePhone\":\"\",\"Phone\":\"\",\"Practicing_Specialty__c\":\"FM - FAMILY MEDICINE\",\"Primary_City__c\":\"\",\"Primary_State__c\":\"\",\"Primary_Street_Line2__c\":\"\",\"Primary_Street__c\":\"\",\"Primary_Zip__c\":\"\",\"RecordTypeId\":\"012U0000000JaPWIA0\",\"Request_Date__c\":\"2016-06-10T22:31:55.9647467Z\",\"XXXXXXX\":\"\",\"Specialty_1_vod__c\":\"\",\"Suffix_vod__c\":\"\",\"Website\":\"\"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Salesforce fejlsvar

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="returning-the-response-back-to-the-parent-logic-app"></a>Returnerer svar tilbage til den overordnede logik app

Når du har svaret, kan du sende det tilbage til den overordnede logik app.

#### <a name="return-success-response-to-the-parent-logic-app"></a>Returnere succes svar til den overordnede logik app

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "   Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-the-parent-logic-app"></a>Returnere fejlsvar til den overordnede logik app

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="documentdb-repository-and-portal"></a>DocumentDB lager og portal

Vores løsning tilføjes ekstra funktioner med [DocumentDB](https://azure.microsoft.com/services/documentdb).

### <a name="error-management-portal"></a>Fejl management portal

For at få vist fejlene, kan du oprette en MVC WebApp for at få vist fejlposter fra DocumentDB. **Liste**, **Detaljer**, **redigere**og **slette** handlinger er inkluderet i den aktuelle version.

> [AZURE.NOTE]Redigere handling: DocumentDB indeholder en Erstat af hele dokumentet.
> De poster, der vises på **listen** og **detaljerede** visninger er kun eksempler. De er ikke faktisk patienter aftale poster.

Følgende er nogle eksempler på vores MVC app-oplysninger, der er oprettet med den beskrevne tidligere tilgang.

#### <a name="error-management-list"></a>På listen over fejl administration

![På listen over fejl](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Fejl management detaljevisning

![Flere oplysninger om fejlen](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Log management portal

For at få vist logfilerne, oprettet vi også en MVC WebApp.  Følgende er nogle eksempler på vores MVC app-oplysninger, der er oprettet med den beskrevne tidligere tilgang.

#### <a name="sample-log-detail-view"></a>Eksempel log detaljevisning

![Log detaljevisning](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>API app-oplysninger

#### <a name="logic-apps-exception-management-api"></a>Logik Apps undtagelse management API

Vores open source-logik Apps undtagelse management API app indeholder følgende funktioner.

Der er to enheder:

- **ErrorController** indsætter en fejl post (dokument) i en DocumentDB af websteder.
- **LogController** Indsætter en telefonlogpost (dokument) i en DocumentDB af websteder.

> [AZURE.TIP] Brug af begge enheder `async Task<dynamic>` handlinger. Dette giver mulighed for handlinger løses på kørselstidspunktet, så vi kan oprette DocumentDB skemaet i brødteksten i handlingen.

Alle dokumenter i DocumentDB skal have et entydigt ID. Vi bruger `PatientId` og tilføje et tidsstempel, der er konverteret til en Unix tidsstempel værdi (dobbelt). Vi afkorte den for at fjerne den procentvise værdi.

Du kan få vist kildekode vores fejl domænecontrolleren API [fra GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Vi kalder API fra en logik app ved hjælp af følgende syntaks.

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Udtrykket i det foregående eksempel-kode kontrollerer, om der *Create_NewPatientRecord* status for **mislykkedes**.

## <a name="summary"></a>Oversigt

- Du kan nemt implementere logføring og fejlhåndtering i en logik app.
- Du kan bruge DocumentDB som lageret for log og fejloplysninger poster (dokumenter).
- Du kan bruge MVC til at oprette en portal for at få vist log og fejloplysninger poster.

### <a name="source-code"></a>Kildekode
Kildekode for logik Apps undtagelse ledelsen API programmet findes i denne [GitHub lager](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Logik App undtagelse Management API").


## <a name="next-steps"></a>Næste trin
- [Få vist flere logik Apps eksempler og scenarier](app-service-logic-examples-and-scenarios.md)
- [Få mere at vide om logik Apps overvågning værktøjer](app-service-logic-monitor-your-logic-apps.md)
- [Oprette en logik App automatiseret udrulning skabelon](app-service-logic-create-deploy-template.md)
