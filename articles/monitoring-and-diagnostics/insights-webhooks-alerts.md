<properties
    pageTitle="Konfigurere webhooks på Azure metriske beskeder | Microsoft Azure"
    description="Omdirigere Azure beskeder til andre ikke-Azure-systemer."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Konfigurere en webhook på en Azure metriske besked

Webhooks gør det muligt at omdirigere en Azure besked til andre systemer til efterbehandling eller brugerdefinerede handlinger. Du kan bruge en webhook på en besked til at distribuere den til de tjenester, sende SMS, logføre programfejl, et team via chat/messaging services til at give besked eller foretage en række andre handlinger. I denne artikel beskrives, hvordan du angiver en webhook på en Azure metriske besked, og hvordan data for HTTP-POST til en webhook ser ud. Giv besked (sikkerhedsmeddelelsen hændelser) [Se denne side i stedet for](./insights-auditlog-to-webhook-email.md)flere oplysninger om installation og skema til en logfil med Azure aktivitet.

Azure beskeder HTTP POST på beskeder om indholdet i JSON formatere, skema defineret nedenfor, til en webhook URI, du angiver, når du opretter påmindelsen for. Denne URI skal være et gyldigt HTTP eller HTTPS slutpunkt. Azure indlæg én post per anmodningen, når en besked er aktiveret.

## <a name="configuring-webhooks-via-the-portal"></a>Konfiguration af webhooks via portalen

Du kan tilføje eller opdatere webhook URI i skærmbilledet Opret/Opdater beskeder i [portalen](https://portal.azure.com/).

![Tilføje en besked om](./media/insights-webhooks-alerts/Alertwebhook.png)

Du kan også konfigurere en besked til at sende indlæg til en webhook URI ved hjælp af [Azure PowerShell-cmdletter](./insights-powershell-samples.md#create-alert-rules), [På tværs af platforme CLI](./insights-cli-samples.md#work-with-alerts)eller [Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Godkendelse af webhook

Webhook kan godkende ved hjælp af en af følgende metoder:

1. **Token-baseret godkendelse** - webhook URI gemmes sammen med et token-ID, f.eks. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **Grundlæggende godkendelse** - webhook URI gemmes sammen med et brugernavn og adgangskode, f.eks. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Data skema

Handlingen INDLÆG indeholder følgende JSON-data og skema for alle metrisk-baserede beskeder.

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| Felt | Obligatorisk | Faste værdier | Noter |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|Y|"Aktiveret", "Fundet"|Status for den vigtige besked, der er baseret på betingelserne, du har angivet.|
|kontekst| Y | | Beskeder om kontekst.|
|tidsstempel| Y | | Det tidspunkt, hvor påmindelsen blev udløst.|
|id | Y | | Hver besked regel har et entydigt id.|
|Navn               |Y                  |                   | Beskeder om navnet.|
|Beskrivelse        |Y                  |                           |Beskrivelse af beskeden.|
|conditionType      |Y                  |"Metriske", "Hændelse"          |To typer af beskeder understøttes. Én, der er baseret på en metriske betingelse og den anden baseret på en begivenhed i loggen aktivitet. Bruge denne værdi til at kontrollere, hvis den vigtige besked er baseret på metrisk eller en begivenhed.|
|betingelse          |Y                  |                           | De specifikke felter der skal kontrolleres for baseret på conditionType.|
|metricName         |for metriske beskeder  |                           |Navnet på den metrikværdi, der definerer hvad reglen overvåger.|
|metricUnit         |for metriske beskeder  |"Byte", "BytesPerSecond", "Tælle", "CountPerSecond", "Procent", "Sekunder"|     Den enhed, der er tilladt i metrikværdien. [Tilladte værdier er angivet her](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx).|
|metricValue        |for metriske beskeder  |                           |Den faktiske værdi af den metrikværdi, der forårsagede påmindelsen.|
|grænseværdi          |for metriske beskeder  |                           |Grænseværdi, hvormed beskeden er aktiveret.|
|windowSize         |for metriske beskeder  |                           |Tidsrum, der bruges til at overvåge beskeder om aktivitet, der er baseret på grænseværdi. Skal være mellem 5 minutter og 1 dag. ISO 8601 varighed-format.|
|timeAggregation    |for metriske beskeder  |"Middel", "Efternavn", "Maksimum", "Minimum", "Ingen", "samlet" | Hvordan de data, der indsamles skal kombineres med tiden. Standardværdien er gennemsnit. [Tilladte værdier er angivet her](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx).|
|operatoren           |for metriske beskeder  |                           |Den operator, der bruges til at sammenligne de aktuelle metriske data til at angive grænseværdi.|
|subscriptionId     |Y                  |                           |Azure abonnement-ID.|
|resourceGroupName  |Y                  |                           |Navnet på ressourcegruppen for påvirkede ressourcen.|
|resourceName       |Y                  |                           |Ressourcenavnet på den påvirkede ressource.|
|Ressourcetypen       |Y                  |                           |Ressourcetype på den påvirkede ressource.|
|resourceId         |Y                  |                           |Ressource-ID på den påvirkede ressource.|
|resourceRegion     |Y                  |                           |Område eller placeringen af den påvirkede ressource.|
|portalLink         |Y                  |                           |Direkte link til siden portalen ressource oversigt.|
|Egenskaber         |N                  |Valgfri                   |Sæt `<Key, Value>` par (det vil sige `Dictionary<String, String>`), som indeholder oplysninger om begivenheden. Egenskaber for felt er valgfrit. Brugerne kan angive tasten/værdier, der kan overføres via data i en brugerdefineret brugergrænseflade eller logik app-baserede workflow. Den alternative måde at overføre brugerdefinerede egenskaber tilbage til webhook er via webhook uri selve (som forespørgselsparametre)|


>[AZURE.NOTE] Feltet egenskaber kan kun angives ved hjælp af [Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="next-steps"></a>Næste trin

- Lær mere om Azure beskeder og webhooks i sektionen video [Integrere Azure beskeder med PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
- [Udføre Azure automatisering scripts (Runbooks) på Azure beskeder](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Brug logik App til at sende en SMS-besked via Twilio fra en Azure besked](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
- [Brug logik App til at sende en slæk meddelelse fra en Azure besked](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
- [Brug logik App til at sende en meddelelse til en Azure kø fra en Azure besked](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
