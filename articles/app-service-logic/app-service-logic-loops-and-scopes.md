<properties
   pageTitle="Logik Apps løkker, områder og Debatching | Microsoft Azure"
   description="Logik App løkke, omfang og debatching begreber"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# <a name="logic-apps-loops-scopes-and-debatching"></a>Logik Apps løkker, områder og Debatching
  
>[AZURE.NOTE] Denne version i denne artikel gælder for logik Apps 2016-04-01-preview skemaet og senere.  Begreber er de samme for ældre skemaer, men områder er kun tilgængelig for dette skema eller nyere.
  
## <a name="foreach-loop-and-arrays"></a>ForEach Gentag og matrixer
  
Logik Apps gør det muligt at gentage over et sæt af data og udføre en handling for hvert element.  Dette er muligt via den `foreach` handling.  I designeren, kan du angive for at tilføje en for hver løkke.  Når du har valgt, du vil gentage funktionen i matrixen, kan du begynde at tilføje handlinger.  Du er i øjeblikket er begrænset til kun én handling per foreach løkke, men denne begrænsning vil ophæves i de kommende uger.  Én gang i feltet Gentag kan du begynde at angive, hvad der skal ske på hver værdi i matrixen.

Hvis du bruger visningen programkode, kan du angive en for hver løkke som nedenfor.  Dette er et eksempel på en for hver løkke, der sender en mail for hver mailadresse, der indeholder 'microsoft.com':

```
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  A `foreach` handling kan navigere over matrixer op til 5.000 rækker.  Hver gentagelse kan udføre parallelt, så det kan være nødvendigt at føje meddelelser til en kø, hvis det er nødvendigt flow-styring.
  
## <a name="until-loop"></a>Indtil løkke
  
  Du kan udføre en handling eller en række handlinger, indtil en betingelse opfyldes.  De mest almindelige scenarie for dette er kalder et slutpunkt, indtil du kommer det svar, du leder efter.  I designeren, kan du angive for at tilføje en indtil løkke.  Når du har tilføjet handlinger i feltet Gentag, kan du angive betingelsen Afslut samt løkken begrænsninger.  Der er en 1 minut forsinkelse mellem løkke skifter.
  
  Hvis du bruger visningen programkode, kan du angive en indtil løkke som nedenfor.  Dette er et eksempel på kalder en HTTP-slutpunkt, indtil svar brødteksten indeholder værdien "Afsluttet".  Fuldfører hvornår enten 
  
  * HTTP-svar har status "Afsluttet"
  * Det har forsøgt for 1 time
  * Det har dokumentlinjer 100 gange
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn og Debatching

Nogle gange kan en udløser modtager en matrix med elementer, du vil debatch og starte en arbejdsproces per element.  Dette kan gøres den `spliton` kommandoen.  Som standard, hvis din udløser swagger angiver en data, der er en matrix, en `spliton` tilføjes og starte en Kør per element.  SplitOn kan kun føjes til en udløser.  Dette kan være manuelt konfigureret eller tilsidesat i definition kodevisning.  Aktuelt SplitOn kan debatch matrixer op til 5.000 elementer.  Du kan ikke have en `spliton` og også implementere syncronous svar mønster.  Alle arbejdsprocesser, titlen, der har en `response` handling ud over `spliton` kører asyncronously og sende en øjeblikkelig `202 Accepted` svar.  

SplitOn kan angives i visningen programkode som i følgende eksempel.  Denne godkendelseskontrol en matrix med elementer og debatches på hver række.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequency": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Områder

Det er muligt at gruppere en række handlinger sammen ved hjælp af et omfang.  Dette er særligt nyttige til implementering af håndtering af undtagelser.  Du kan tilføje et nyt område og begynde at tilføje eventuelle handlinger inde i det i designer.  Du kan definere områder i kodevisning ud som følger:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
