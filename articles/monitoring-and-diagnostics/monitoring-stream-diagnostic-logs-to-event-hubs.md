<properties
    pageTitle="Streame Azure diagnosticeringslogfiler til begivenhed hubber | Microsoft Azure"
    description="Lær at streame Azure diagnosticeringslogfiler til begivenhed hubber."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="johnkem"/>

# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>Streame Azure diagnosticeringslogfiler til begivenhed hubber

**[Azure diagnosticeringslogfiler](monitoring-overview-of-diagnostic-logs.md)** streames i nær realtid til alle programmer, ved hjælp af indstillingen indbyggede "Eksportere til begivenhed Hubs" i portalen eller ved at aktivere tjenesten Bus regel-id'et i en diagnosticering indstilling via Azure PowerShell-cmdlet'er eller Azure CLI.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Hvad du kan gøre med diagnosticering logfiler og begivenhed Hubs
Her er et par måder, du kan bruge streaming muligheden for diagnosticeringslogfiler:

- **Stream logfiler til 3 part logføring og telemetri systemer** – bliver Over tid, begivenhed Hubs streaming ordning pipe din diagnosticeringslogfiler til tredjepart SIEMs og logge analytics løsninger.

- **Få vist tjenestetilstand ved streaming "varmt sti" data til PowerBI** – ved hjælp af begivenhed hubber, Stream analyser og PowerBI, kan du nemt transformere dataene diagnostics til nær realtid indsigt på dine Azure tjenester. [I denne artikel dokumentation giver en god oversigt over, hvordan du konfigurere en begivenhed Hubs, behandle data med Stream analyser og bruge PowerBI som output](../stream-analytics/stream-analytics-power-bi-dashboard.md). Her er nogle tip til at få konfigureret med Diagnosticeringslogge:
    - Hubberne begivenhed til en kategori af diagnosticeringslogfiler oprettes automatisk, når du markere indstillingen i portalen eller aktiverer den via PowerShell, så du vil markere begivenhed hubberne i navneområdet Service Bus med det navn, der starter med "indsigt-"
    - Her er et eksempel Stream Analytics forespørgsel, du kan bruge til at fortolke blot alle dataene i logfilen i til en PowerBI tabel:

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

- **Oprette en brugerdefineret telemetri og logføring platform** – Hvis du allerede har en specialtilpasset telemetri platform eller er lige tænker over opbygning af en, meget SVG udgivelse abonnement art begivenhed Hubs gør muligt at fleksibelt indtager diagnosticeringslogfiler. [Se Dan Rosanovas vejledning til brug af begivenhed Hubs i en global skala telemetri platform her](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Aktivere streaming af logge til diagnosticering
Du kan aktivere streaming af diagnosticeringslogfiler fra et program, via portalen, eller brug af [Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn931943.aspx). Uanset hvilken mulighed, du vælger en tjeneste Bus Namespace, og der oprettes en begivenhed Hubs i navneområdet for hver log kategori, du aktiverer. En diagnosticering **Log kategori** er en type logfil, en ressource kan indsamle. Du kan vælge hvilke log kategorier, du vil have til at samle for en bestemt ressource i portalen Azure under bladet diagnosticering.

![Log kategorier i portalen](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [AZURE.WARNING] Aktivere og streaming diagnosticeringslogfiler fra Beregn ressourcer (for eksempel FOS eller tjeneste strukturen) [kræver en anden række trin](../event-hubs/event-hubs-streaming-azure-diags-data.md).

### <a name="via-powershell-cmdlets"></a>Via PowerShell-cmdlet'er
Hvis du vil aktivere streaming via [Azure PowerShell-cmdletter](insights-powershell-samples.md), kan du bruge den `Set-AzureRmDiagnosticSetting` cmdlet med disse parametre:

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

Tjenesten Bus regel-ID'ET er en streng med dette format: `{service bus resource ID}/authorizationrules/{key name}`, for eksempel `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`.


### <a name="via-azure-cli"></a>Via Azure CLI
Hvis du vil aktivere streaming via [Azure CLI](insights-cli-samples.md), kan du bruge den `insights diagnostic set` kommando således:

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Bruge det samme format til tjenesten Bus regel-ID'ET, som beskrevet for PowerShell-Cmdlet.

###<a name="via-azure-portal"></a>Via Azure-portalen
For at aktivere streaming via Azure-portalen skal du gå til diagnosticeringsindstillingerne for en ressource og vælg 'Export til begivenhed Hub'.

![Eksportere til begivenhed hubber i portalen](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Vælg en eksisterende Service Bus Namespace for at konfigurere den. Navneområdet for den valgte bliver, hvor begivenhed hubberne, der er oprettet (Hvis dette er dit første gang streaming diagnosticeringslogfiler) eller streames til (Hvis der allerede findes ressourcer, der streaming pågældende log kategori til dette navneområde), og politikken definerer de tilladelser, der indeholder den streaming ordning. I dag, kræver streaming til en begivenhed hubber Administrer, Læs og Send tilladelser. Du kan oprette eller ændre Service Bus Namespace delt access politikker i portalen klassisk under fanen "Konfigurer" til din tjeneste Bus Namespace. For at opdatere én af disse indstillinger for diagnosticering, skal klienten have tilladelsen ListKey på reglen Service Bus godkendelse.

##<a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Hvordan optager logdataene fra begivenhed Hubs?
Her er eksempeldata output fra hubberne begivenhed:

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Elementnavn | Beskrivelse                                            |
|--------------|--------------------------------------------------------|
|poster       | En matrix med alle log begivenheder i denne data.            |
|tid          | Tidspunkt, hvor hændelsen indtraf.                      |
|kategori      | Log kategorien for begivenheden.                           |
|resourceId    | Ressource-ID på den ressource, som genererede begivenheden. |
|operationName | Navnet på handlingen.                                 |
|niveau         | Valgfrit. Angiver niveauet log begivenhed.               |
|Egenskaber    | Egenskaber for begivenheden.                               |


Du kan få vist en liste over alle ressource-udbydere, der understøtter streaming til begivenhed Hub [her](monitoring-overview-of-diagnostic-logs.md).

##<a name="next-steps"></a>Næste trin
- [Få mere at vide om Azure diagnosticeringslogfiler](monitoring-overview-of-diagnostic-logs.md)
- [Introduktion til begivenhed Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
