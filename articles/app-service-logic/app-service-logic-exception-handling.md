<properties
   pageTitle="Håndtering af logik Apps undtagelse | Microsoft Azure"
   description="Få mere at vide mønstre for fejl og undtagelse, der håndterer med Azure logik Apps"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-error-and-exception-handling"></a>Logisk Apps fejl og håndtering af undtagelse

Logik Apps indeholder et bredt udvalg af funktioner og mønstre for at sikre dine integrationer er robust og tolerant mod fejl.  En af udfordringerne med en hvilken som helst integrationsarkitektur er at sikre, at nedetid eller problemer af afhængige systemer håndteres korrekt.  Logik Apps gør håndtering af fejl en førsteklasses oplevelse, hvorved du får de værktøjer, du har brug for til at handle på undtagelser og fejl i dine arbejdsprocesser.

## <a name="retry-policies"></a>Prøv igen politikker

De mest grundlæggende typer undtagelse og fejlhåndtering er en retry-politik.  Denne politik definerer Hvis handlingen skal prøv igen, hvis indledende anmodning fik timeout eller mislykkedes (enhver anmodning, der udløste en 429 eller 5xx-svar).  Som standard gentagne alle handlinger 4 flere gange over 20 sekunders intervaller.  Så, hvis den første anmodning modtaget en `500 Internal Server Error` svarmeddelelse ved arbejdsproces program afbrydes til 20 sekunder, og forsøg anmodningen igen.  Hvis efter alle gentagne forsøg er svaret stadig en undtagelse eller fejl, arbejdsprocessen fortsætter, og Markér handling status som `Failed`.

Du kan konfigurere forsøg politikker i **input med værdien** af en bestemt handling.  En retry-politik kan konfigureres til at prøve op til 4 gange over 1 time intervaller.  Detaljerede oplysninger om input egenskaber kan ikke [findes på MSDN][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Hvis du gerne vil have HTTP-handlingen til at prøve 4 gange og vente 10 minutter mellem hvert forsøg på har du ville definitionen af følgende:

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Få vist den [retry-politik i sektionen på MSDN]kan finde flere oplysninger om understøttede syntaksen[retryPolicyMSDN].

## <a name="runafter-property-to-catch-failures"></a>RunAfter egenskab til at opfange mislykkede forsøg

Hver logik app handling erklærer, hvilke handlinger skal fuldføre, før handlingen, der starter.  Du kan betragte dette som rækkefølgen af trinnene i arbejdsprocessen.  I denne rækkefølge, der kendes som den `runAfter` egenskab i definitionen af handling.  Det er et objekt, der beskriver, hvilke handlinger og handling statusser vil udføre handlingen.  Alle handlinger, der tilføjes via designer er som standard angivet til `runAfter` det forrige trin, hvis det foregående trin blev `Succeeded`.  Du kan dog tilpasse denne værdi for at forespørgselsreglen handlinger, når tidligere handlinger er `Failed`, `Skipped`, eller et mulige sæt af disse værdier.  Hvis du vil føje et element til et dedikeret Service Bus emne efter en bestemt handling `Insert_Row` mislykkes, skal du bruge følgende `runAfter` konfiguration:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

Meddelelse om den `runAfter` egenskab er indstillet til forespørgselsreglen, hvis den `Insert_Row` handling er `Failed`.  Til at køre handlingen, hvis handling status er `Succeeded`, `Failed`, eller `Skipped` syntaksen ville være:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

>[AZURE.TIP] Handlinger, der kører efter en foregående handling har mislykkedes og fuldført, markeres som `Succeeded`.  Denne funktionalitet betyder, at hvis du er blevet produkter alle fejl i en arbejdsproces Kør selve er markeret som `Succeeded`.

## <a name="scopes-and-results-to-evaluate-actions"></a>Områder og resultater til at evaluere handlinger

Svarende til, hvordan du kan køre efter individuelle handlinger, du kan også gruppe handlinger sammen i et [område](app-service-logic-loops-and-scopes.md) - der fungerer som en logisk gruppering af handlinger.  Områder er nyttig både til organisering af dine logik app handlinger, og for at udføre Sammenlæg evaluering på status for et omfang.  Omfanget selve, modtager en status, når alle handlinger i et omfang har fuldført.  Omfang status bestemmes med de samme kriterier som en Kør –, hvis den sidste handling i en udførelse af gren er `Failed` eller `Aborted` status er `Failed`.

Du kan `runAfter` et område er markeret `Failed` forespørgselsreglen bestemte handlinger for eventuelle fejl, der er opstået i området.  Kører efter et omfang mislykkes, kan du oprette en enkelt handling for at fange fejl, hvis *alle* handlinger i omfanget mislykkes.

### <a name="getting-the-context-of-failures-with-results"></a>Få konteksten antal mislykkede forsøg resultat

Fangst mislykkede forsøg fra et område er meget nyttig, men du kan også vælge kontekst til at forstå nøjagtigt, hvilke handlinger mislykkedes, og eventuelle fejl eller status-koder, der blev returneret.  Den `@result()` arbejdsproces funktionen giver kontekst til resultatet af alle handlinger i et omfang.

`@result()`indsættes en enkelt parameter, omfang navn, og returnerer en matrix med alle handling resultater fra inden for området.  Disse handling objekter omfatter de samme attributter som den `@actions()` objekt, herunder handling starttidspunktet, sluttidspunktet handling, handling status, handling input, handling korrelations id'er og handling skriver.  Du kan nemt par en `@result()` fungerer sammen med en `runAfter` til at sende konteksten af eventuelle handlinger, der mislykkedes inden for et omfang.

Hvis du vil udføre en handling *for hver* handling i et område, der `Failed`, du kan par `@result()` med handlingen **[Filter matrix](../connectors/connectors-native-query.md)** og en **[ForEach](app-service-logic-loops-and-scopes.md)** løkke.  Dette kan du filtrere af en række resultater til handlinger, der mislykkedes.  Du kan gøre det filtrerede resultat matrix og udføre en handling for hver fejl ved hjælp af **ForEach** løkke.  Her er et eksempel nedenfor, efterfulgt af en detaljeret forklaring.  I dette eksempel sender en anmodning om en HTTP POST med svar brødteksten i eventuelle handlinger, der blev ikke er omfattet `My_Scope`.

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Her er en detaljeret gennemgang af, hvad der sker:

1. **Filter matrix** handling til at filtrere på `@result('My_Scope')` at få resultatet af alle handlinger i`My_Scope`
1. Betingelsen for **Filter matrix** er en `@result()` vare med statussen lig med `Failed`.  Dette filtrerer matrix med alle handling resultater fra `My_Scope` til kun en matrix med mislykkedes handling resultater.
1. Udføre handlingen **For hver** på **Filtreret matrix** output.  Dette vil udføre en handling *for hver* mislykkedes handling resultatet vi filtreret ovenfor.
    - Hvis der var en enkelt handling i det område, der mislykkedes, handlinger i den `foreach` afvikles kun én gang.  Mange mislykkede handlinger medfører en handling per fejl.
1. Send en HTTP-POST på den `foreach` elementet svar brødtekst eller `@item()['outputs']['body']`.  Den `@result()` element figur er den samme som den `@actions()` forme og kan fortolkes på samme måde.
1. Desuden to brugerdefinerede sidehoveder med navnet på mislykkedes handlingen `@item()['name']` og den mislykkedes køre klient sporing ID `@item()['clientTrackingId']`.

Til reference her er et eksempel på en enkelt `@result()` element.  Du kan se den `name`, `body`, og `clientTrackingId` egenskaber parset i det foregående eksempel.  Bemærk, uden for en `foreach`, `@result()` returnerer en matrix med disse objekter.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/foo/bar does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Du kan bruge de ovenstående udtryk til at udføre forskellige undtagelse, der håndterer mønstre.  Du kan vælge at udføre en enkelt undtagelse, der håndterer handling uden for det område, der accepterer hele filtrerede matrix med fejl, og fjern den `foreach`.  Du kan også medtage andre nyttige egenskaber fra den `@result()` svar ovenstående.

## <a name="azure-diagnostics-and-telemetry"></a>Azure diagnosticerings- og telemetri

De mønstre ovenfor er fantastisk måde at håndtere fejl og undtagelser i en serie, men du kan også identificere og reagere på fejl uafhængigt af afkrydsningsfeltet Kør sig selv.  [Azure diagnosticering](app-service-logic-monitor-your-logic-apps.md) indeholder en enkelt metode til at sende alle hændelser i arbejdsprocesser (herunder alle Kør og en handling statusangivelser) til en Azure-lager-konto eller en Azure begivenhed Hub.  Du kan overvåge logfiler og målepunkter eller udgive dem i en hvilken som helst overvågning værktøj, du foretrækker, at evaluere køre statusser.  En potentiel mulighed er at streame alle begivenheder via Azure begivenhed Hub til [Stream analyser](https://azure.microsoft.com/services/stream-analytics/).  Du kan skrive direkte forespørgsler fra en hvilken som helst afvigelser, gennemsnit eller mislykkede fra diagnosticeringslogfilerne i Stream Analytics.  Stream Analytics kan nemt output til andre datakilder som køer, emner, SQL, DocumentDB og Power BI.

## <a name="next-steps"></a>Næste trin
- [Se, hvordan en kunde indbygget robust fejlhåndtering med logik Apps](app-service-logic-scenario-error-and-exception-handling.md)
- [Find flere Apps logik eksempler og scenarier](app-service-logic-examples-and-scenarios.md)
- [Lær at oprette automatiseret installationer af logik apps](app-service-logic-create-deploy-template.md)
- [Designe og implementere logik apps fra Visual Studio](app-service-logic-deploy-from-vs.md)


<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9