<properties
 pageTitle="Scheduler udgående godkendelse"
 description="Scheduler udgående godkendelse"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/15/2016"
 ms.author="deli"/>

# <a name="scheduler-outbound-authentication"></a>Scheduler udgående godkendelse

Planlægger-job kan være nødvendigt at foretage et opkald til tjenester, der kræver godkendelse. Denne måde, en kaldet tjeneste kan bestemme Hvis Scheduler jobbet kan få adgang til dens ressourcer. Nogle af disse tjenester omfatter andre Azure services, Salesforce.com, Facebook og sikre brugerdefinerede websteder.

## <a name="adding-and-removing-authentication"></a>Tilføje og fjerne godkendelse

Tilføje godkendelse til en Scheduler sag er enkel – Tilføj et JSON underordnet element `authentication` til den `request` element, når du opretter eller opdatere et job. Hemmeligheder overføres til tjenesten Opgavestyring i en læg, programrettelse eller Send anmodning – som en del af den `authentication` objekt – aldrig returneres i svar. I svar, hemmeligt oplysninger er angivet til null eller har et offentligt token, der repræsenterer den godkendte enhed.

For at fjerne godkendelse, skal du PLACERE eller programrettelse jobbet eksplicit, angive den `authentication` objekt til null. Du se ikke egenskaberne for en hvilken som helst godkendelse tilbage i svar.

På nuværende tidspunkt er de eneste understøttede godkendelsestyper i `ClientCertificate` modellen (til brug af SSL/TLS klientcertifikater), den `Basic` modellen (for basisgodkendelse), og den `ActiveDirectoryOAuth` modellen (for Active Directory OAuth-godkendelse.)

## <a name="request-body-for-clientcertificate-authentication"></a>Anmodningsteksten til ClientCertificate godkendelse

Når du tilføjer godkendelse ved hjælp af den `ClientCertificate` model, skal du angive følgende yderligere elementer i anmodningsteksten.  

|Element|Beskrivelse|
|:---|:---|
|_godkendelse (overordnet element)_|Godkendelse objekt til brug af en klient et SSL-certifikat.|
|_type_|Der er påkrævet. Type godkendelse. For SSL-klientcertifikater, værdien skal være `ClientCertificate`.|
|_PFX_|Der er påkrævet. Base64-kodet indhold af filen PFX.|
|_adgangskode_|Der er påkrævet. Adgangskode for at få adgang til PFX-filen.|


## <a name="response-body-for-clientcertificate-authentication"></a>Svar brødteksten til ClientCertificate godkendelse

Når der sendes en anmodning med oplysninger om godkendelse, indeholder svaret følgende godkendelse-relaterede elementer.

|Element |Beskrivelse |
|:--|:--|
|_godkendelse (overordnet element)_ |Godkendelse objekt til brug af en klient et SSL-certifikat.|
|_type_ |Type godkendelse. Værdien er for SSL-klientcertifikater, `ClientCertificate`.|
|_certificateThumbprint_ |Miniature for certifikatet.|
|_certificateSubjectName_ |Det emne entydige navn for certifikatet.|
|_certificateExpiration_ |Udløbsdatoen for certifikatet.|

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>Eksempel RESTEN anmodning om ClientCertificate godkendelse

```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>Eksempel RESTEN svar til ClientCertificate godkendelse

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="request-body-for-basic-authentication"></a>Anmodningsteksten til basisgodkendelse

Når du tilføjer godkendelse ved hjælp af den `Basic` model, skal du angive følgende yderligere elementer i anmodningsteksten.

|Element|Beskrivelse|
|:--|:--|
|_godkendelse (overordnet element)_ |Godkendelse objekt til brug af grundlæggende godkendelse.|
|_type_ |Der er påkrævet. Type godkendelse. Grundlæggende godkendelse af værdien skal være `Basic`.|
|_brugernavn_ |Der er påkrævet. Brugernavn til at godkende.|
|_adgangskode_ |Der er påkrævet. Adgangskode til at godkende.|

## <a name="response-body-for-basic-authentication"></a>Svar brødteksten til basisgodkendelse

Når der sendes en anmodning med oplysninger om godkendelse, indeholder svaret følgende godkendelse-relaterede elementer.

|Element|Beskrivelse|
|:--|:--|
|_godkendelse (overordnet element)_ |Godkendelse objekt til brug af grundlæggende godkendelse.|
|_type_ |Type godkendelse. Grundlæggende godkendelse af værdien er `Basic`.|
|_brugernavn_ |Den godkendte brugernavn.|

## <a name="sample-rest-request-for-basic-authentication"></a>Eksempel RESTEN anmodning om basisgodkendelse

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>Eksempel RESTEN svar til basisgodkendelse

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>Anmodningsteksten til ActiveDirectoryOAuth godkendelse

Når du tilføjer godkendelse ved hjælp af den `ActiveDirectoryOAuth` model, skal du angive følgende yderligere elementer i anmodningsteksten.

|Element |Beskrivelse |
|:--|:--|
|_godkendelse (overordnet element)_ |Godkendelse objekt for ved hjælp af ActiveDirectoryOAuth godkendelse.|
|_type_ |Der er påkrævet. Type godkendelse. ActiveDirectoryOAuth godkendelse af værdien skal være `ActiveDirectoryOAuth`.|
|_lejer_ |Der er påkrævet. Lejer id for Azure AD-lejer.|
|_målgruppe_ |Der er påkrævet. Dette er angivet til https://management.core.windows.net/.|
|_clientId_ |Der er påkrævet. Give klient-id til Azure AD-programmet.|
|_hemmeligt_ |Der er påkrævet. Hemmeligt af den klient, der anmoder om tokenet.|

### <a name="determining-your-tenant-identifier"></a>Afgøre din lejer-id

Du kan finde lejer id for Azure AD-lejer ved at køre `Get-AzureAccount` i Azure PowerShell.

## <a name="response-body-for-activedirectoryoauth-authentication"></a>Svar brødteksten til ActiveDirectoryOAuth godkendelse

Når der sendes en anmodning med oplysninger om godkendelse, indeholder svaret følgende godkendelse-relaterede elementer.

|Element |Beskrivelse |
|:--|:--|
|_godkendelse (overordnet element)_ |Godkendelse objekt for ved hjælp af ActiveDirectoryOAuth godkendelse.|
|_type_ |Type godkendelse. ActiveDirectoryOAuth godkendelse af værdien er `ActiveDirectoryOAuth`.|
|_lejer_ |Lejer id for Azure AD-lejer. |
|_målgruppe_ |Dette er angivet til https://management.core.windows.net/.|
|_clientId_ |Klient-id for Azure AD-programmet.|

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>Eksempel RESTEN anmodning om ActiveDirectoryOAuth godkendelse

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>Eksempel RESTEN svar til ActiveDirectoryOAuth godkendelse

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>Se også


 [Hvad er Scheduler?](scheduler-intro.md)

 [Azure Scheduler begreber, terminologi og enhed hierarki](scheduler-concepts-terms.md)

 [Introduktion til brug af Scheduler i portalen Azure](scheduler-get-started-portal.md)

 [Planer og fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Azure Scheduler REST-API-reference](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell-cmdletter reference](scheduler-powershell-reference.md)

 [Azure Scheduler høj tilgængelighed og pålidelighed](scheduler-high-availability-reliability.md)

 [Azure Scheduler begrænsninger, standarder og fejlkoder](scheduler-limits-defaults-errors.md)
