<properties
    pageTitle="Konfigurere en webhook på Azure aktivitetslog beskeder | Microsoft Azure"
    description="Se, hvordan du bruger aktivitetslog beskeder til at ringe til webhooks. "
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
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-activity-log-alerts"></a>Konfigurere en webhook på en Azure aktivitetslog beskeder

Webhooks gør det muligt at omdirigere en Azure besked til andre systemer til efterbehandling eller brugerdefinerede handlinger. Du kan bruge en webhook på en besked til at distribuere den til de tjenester, sende SMS, logføre programfejl, et team via chat/messaging services til at give besked eller foretage en række andre handlinger. I denne artikel beskrives, hvordan du angiver en webhook på en Azure aktivitetslog besked, og hvordan data for HTTP-POST til en webhook ser ud. Oplysninger om konfiguration og skema for en Azure metriske besked for at [Se denne side i stedet](insights-webhooks-alerts.md). Du kan også konfigurere en aktivitetslog besked til at sende mail når aktiveret.

>[AZURE.NOTE] Denne funktion er i øjeblikket i preview, så forvente variable kvalitet og ydeevne.

Du kan konfigurere en aktivitetslog besked, der bruger [Azure PowerShell-cmdletter](insights-powershell-samples.md#create-alert-rules), [På tværs af platforme CLI](insights-cli-samples.md#work-with-alerts)eller [Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Godkendelse af webhook
TThe webhook kan godkende ved hjælp af en af følgende metoder:

1. **Token-baseret godkendelse** - webhook URI gemmes sammen med et token-ID, f.eks. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **Grundlæggende godkendelse** - webhook URI gemmes sammen med et brugernavn og adgangskode, f.eks. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Data skema
Handlingen INDLÆG indeholder følgende JSON-data og skema for alle aktivitetslog-baserede beskeder. Dette skema ligner den, der bruges af metrisk-baserede beskeder.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|Elementnavn       |Beskrivelse|
|---                |---|
|status             |Bruges til metriske beskeder. Altid angivet til "aktiveret" for aktivitetslog beskeder.|
|kontekst            |Konteksten for begivenheden.|
|resourceProviderName|Ressource-udbyder på den påvirkede ressource.|
|conditionType      |Altid "hændelse".|
|Navn               |Navnet på reglen.|
|id                 |Ressource-ID for påmindelsen.|
|Beskrivelse        |Beskeder om beskrivelse som sæt under oprettelse af beskeden.|
|subscriptionId     |Azure abonnement-ID.|
|tidsstempel          |Tidspunkt, hvor hændelsen blev oprettet af tjenesten Azure, behandlet anmodningen.|
|resourceId         |Ressource-ID på den påvirkede ressource.|
|resourceGroupName  |Navnet på ressourcegruppen for den påvirkede ressource|
|Egenskaber         |Sæt `<Key, Value>` par (det vil sige `Dictionary<String, String>`), som indeholder oplysninger om begivenheden.|
|begivenhed              |Element, der indeholder metadata om begivenheden.|
|godkendelse      |RBAC egenskaberne for begivenheden. Dette omfatter normalt "handlingen", "rolle" og "omfanget."|
|kategori           |Kategori af begivenheden. Understøttede værdier omfatter: Administrative, Giv besked, sikkerhed, ServiceHealth, anbefaling.|
|opkalds             |E-mail-adressen på den bruger, der udføres den handling, UPN-kravet eller SPN krav baseret på tilgængelighed. Kan være null for visse system-opkald.|
|correlationId      |Normalt et GUID i strengformat. Hændelser med correlationId tilhører den samme større handling og normalt dele en correlationId.|
|eventDescription   |Statisk tekstbeskrivelse af begivenheden.|
|eventDataId        |Entydigt id for begivenheden.|
|eventSource        |Navnet på den Azure service eller infrastruktur, der oprettede hændelsen.|
|httpRequest        |Normalt indeholder "clientRequestId", "clientIpAddress" og "metode" (HTTP metode LAGT f.eks.).|
|niveau              |En af følgende værdier: "Kritiske", "Fejl", "Advarsel", "Information" og "Detaljeret".|
|operationId        |Normalt et GUID, deles af de hændelser, der svarer til enkelt handling.|
|operationName      |Navnet på handlingen.|
|Egenskaber         |Egenskaber for begivenheden.|
|status             |En streng. Status for handlingen. Almindelige værdier omfatter: "Startet", "I gang", "Udført", "Mislykkedes", "Aktive", "Fundet".|
|Understatus          |Normalt indeholder HTTP-statuskode af tilsvarende RESTEN opkaldet. Det kan også indeholde andre strenge, der beskriver en understatus. Almindelige understatus værdier omfatter: OK (http-statuskode: 200), oprettede (http-statuskode: 201), accepteret (http-statuskode: 202), ikke indhold (http-statuskode: 204), forkert anmodning (http-statuskode: 400), blev ikke fundet (http-statuskode: 404), konflikt (http-statuskode: 409), intern serverfejl (http-statuskode: 500), tjenesten er ikke tilgængelig (http-statuskode: 503), timeout for gatewayen (http-statuskode: 504)|

## <a name="next-steps"></a>Næste trin
- [Lær mere om aktivitet logfilen](monitoring-overview-activity-logs.md)
- [Udføre Azure automatisering scripts (Runbooks) på Azure beskeder](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Brug logik App til at sende en SMS-besked via Twilio fra en Azure besked](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). I dette eksempel er for metriske beskeder, men kan ændres til at arbejde med en aktivitetslog besked.
- [Brug logik App til at sende en slæk meddelelse fra en Azure besked](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). I dette eksempel er for metriske beskeder, men kan ændres til at arbejde med en aktivitetslog besked.
- [Brug logik App til at sende en meddelelse til en Azure kø fra en Azure besked](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). I dette eksempel er for metriske beskeder, men kan ændres til at arbejde med en aktivitetslog besked.
