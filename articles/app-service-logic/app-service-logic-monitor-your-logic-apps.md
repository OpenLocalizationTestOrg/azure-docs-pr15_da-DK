<properties 
    pageTitle="Overvåge dine logik apps i Azure App Service | Microsoft Azure" 
    description="Hvordan du kan se, hvad din logik apps har foretaget" 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="monitor-your-logic-apps"></a>Overvåge dine logik apps

Når du [opretter en logik app](app-service-logic-create-a-logic-app.md), kan du se den fulde historik for kørslen i portalen Azure.  Du kan også konfigurere tjenester som Azure diagnosticering og Azure beskeder til at overvåge hændelser i realtid og besked om begivenheder som "Når mere end 5 kører ikke inden for en time."

## <a name="monitor-in-the-azure-portal"></a>Skærm i portalen Azure

Vælg **Gennemse**for at få vist oversigten, og vælg **Logik Apps**. Der vises en liste over alle logik apps i dit abonnement.  Vælg den ønskede til at overvåge logik app.  Du får vist en liste over alle handlinger og udløsere, der er opstået for denne logik app.

![Oversigt](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Der er et par sektioner på denne blade, der er nyttige:

- **Oversigt** viser en liste over **alle kører** og **Udløser historik**
    - **Alle kører** listen seneste logik app'en kører.  Du kan klikke på en hvilken som helst række få mere at vide under kørsel, eller klik på feltet med listen over flere kører.
    - **Oversigt over udløser** viser alle udløser aktivitet for denne logik app.  Udløser aktivitet kunne være "sprunget over" Tjek til nye data (fx vil du se, hvis en ny fil blev føjet til FTP), "Udført" hvilket betyder, at der blev returneret data forespørgselsreglen en logik app, eller "Mislykkedes" svarer fejl i konfigurationen.
- **Diagnosticering** gør det muligt at få vist runtime detaljer og begivenheder og abonnere på [Azure beskeder](#adding-azure-alerts)

>[AZURE.NOTE] Alle runtime detaljer og hændelser, der er krypteret på resten i tjenesten logik App. De er kun dekrypteres på en visning anmodning fra en bruger. Adgang til disse hændelser kan også styres ved Azure Role-Based Access kontrolelement (RBAC).

### <a name="view-the-run-details"></a>Få vist køre oplysninger

Denne liste over kører viser **Status**, **Starttidspunkt**og **varighed** for en bestemt Kør. Vælg en hvilken som helst række kunne se oplysninger om på, der kører.

Visningen overvågning viser hvert trin i feltet Kør, input og output, og eventuelle fejlmeddelelser, der kan have occurre.

![Kør og handlinger](./media/app-service-logic-monitor-your-logic-apps/monitor-view.png)

Hvis du har brug for eventuelle yderligere oplysninger som køre **Korrelations-ID'ET** (der kan bruges til REST-API), kan du klikke på knappen **Kør detaljer** .  Dette omfatter alle trinnene, status og input/afgang for Kør.

## <a name="azure-diagnostics-and-alerts"></a>Azure diagnosticering og beskeder

Foruden de oplysninger, som Azure portalen og REST-API ovenfor, kan du konfigurere din logik app for at bruge Azure diagnosticering til mere omfattende detaljer og fejlfinding.

1. Klik på **diagnosticering** del af bladet logik app
1. Klik for at konfigurere **Indstillinger for diagnosticering**
1. Konfigurere en begivenhed Hub eller lagerplads konto for at sende data til

    ![Indstillinger for Azure diagnosticering](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

### <a name="adding-azure-alerts"></a>Tilføje Azure beskeder

Når diagnosticering er konfigureret, kan du tilføje Azure beskeder forespørgselsreglen, når visse tærskler er krydset.  Vælg **beskeder** felt og **Tilføj påmindelse**i bladet **diagnosticering** .  Dette fører dig gennem konfigurationen af en besked, der er baseret på en række tærskler og målepunkter.

![Azure beskeder om målepunkter](./media/app-service-logic-monitor-your-logic-apps/alerts.png)

Du kan konfigurere den **betingelse**, **grænseværdi**og **periode** efter behov.  Til sidst skal kan du konfigurere en e-mail-adresse for at sende en meddelelse til, eller du kan konfigurere en webhook.  Du kan bruge [anmodning udløser](../connectors/connectors-native-reqres.md) i en logik app til at køre på en besked samt (til at gøre ting som [stil slæk](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app), [sende en tekst](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)eller [tilføje en meddelelse til en kø](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)).

### <a name="azure-diagnostics-settings"></a>Indstillinger for Azure diagnosticering

Hver af disse hændelser indeholder oplysninger om logik app og begivenhed som status.  Her er et eksempel på en *ActionCompleted* begivenhed:

```javascript
{
            "time": "2016-07-09T17:09:54.4773148Z",
            "workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
            "resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-06-01",
                "startTime": "2016-07-09T17:09:53.4336305Z",
                "endTime": "2016-07-09T17:09:53.5430281Z",
                "status": "Succeeded",
                "code": "OK",
                "resource": {
                    "subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
                    "resourceGroupName": "MyResourceGroup",
                    "workflowId": "cff00d5458f944d5a766f2f9ad142553",
                    "workflowName": "MyLogicApp",
                    "runId": "08587361146922712057",
                    "location": "eastus",
                    "actionName": "Http"
                },
                "correlation": {
                    "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
                    "clientTrackingId": "my-custom-tracking-id"
                },
                "trackedProperties": {
                    "myProperty": "<value>"
                }
            }
        }
```

De to egenskaber, der er særligt nyttigt for overvågning og registrering er *clientTrackingId* og *trackedProperties*.  

#### <a name="client-tracking-id"></a>Klient-ID registrering

Klienten sporing ID er en værdi, der koordinere begivenheder på tværs af en logik app kører, herunder indlejrede arbejdsprocesser kaldet som en del af en logik app.  Dette ID skal være automatisk oprettede Hvis ikke leveres, men du kan manuelt angive klienten sporing ID fra en udløser ved at overføre en `x-ms-client-tracking-id` sidehoved med id-værdien i udløser anmodningen (anmodning udløser, HTTP udløser eller webhook udløser).

#### <a name="tracked-properties"></a>Registrerede egenskaber

Registrerede egenskaber kan tilføjes til handlinger i Arbejdsprocesdefinitionen til at registrere input eller output i diagnosticering data.  Det kan være nyttigt, hvis du vil registrere data som en "ordre-ID" i din telemetri.  Hvis du vil tilføje en registrerede egenskab, skal du medtage den `trackedProperties` egenskaben i en handling.  Registrerede egenskaber kan kun registrere en enkelt handlinger input og output, men du kan bruge den `correlation` egenskaber for hændelserne, der koordinere på tværs af handlinger i en serie.

```javascript
{
    "myAction": {
        "type": "http",
        "inputs": {
            "uri": "http://uri",
            "headers": {
                "Content-Type": "application/json"
            },
            "body": "@triggerBody()"
        },
        "trackedProperties":{
            "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
            "myActionHTTPValue": "@action()['outputs']['body']['foo']",
            "transactionId": "@action()['inputs']['body']['bar']"
        }
    }
}
```

### <a name="extending-your-solutions"></a>Hvis du vil udvide dine løsninger

Du kan udnytte denne telemetri fra den begivenhed Hub eller lager til andre tjenester som [Handlinger Management-pakken](https://www.microsoft.com/cloud-platform/operations-management-suite), [Azure Stream analyser](https://azure.microsoft.com/services/stream-analytics/)og [Power BI](https://powerbi.com) have realtid overvågning af dine integration af arbejdsprocesser.

## <a name="next-steps"></a>Næste trin
- [Almindelige eksempler og scenarier, hvor logik apps](app-service-logic-examples-and-scenarios.md)
- [Oprette en skabelon til distribution af logik App](app-service-logic-create-deploy-template.md)
- [Enterprise funktioner for klientintegration](app-service-logic-enterprise-integration-overview.md)
