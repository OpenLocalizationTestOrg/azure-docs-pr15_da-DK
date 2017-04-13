<properties
   pageTitle="Log Analytics beskeder om REST-API"
   description="Log Analytics beskeder om REST-API kan du oprette og administrere beskeder i handlinger Management pakke (OMS).  Denne artikel indeholder oplysninger om API og flere eksempler for at udføre forskellige handlinger."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="log-analytics-alert-rest-api"></a>Log Analytics beskeder om REST-API

Log Analytics beskeder om REST-API kan du oprette og administrere beskeder i handlinger Management Suite (OMS).  Denne artikel indeholder oplysninger om API og flere eksempler for at udføre forskellige handlinger.

Log Analytics Search REST-API er RESTful og kan åbnes via Azure ressourcestyring REST-API. I dette dokument kan du finde eksempler hvor API åbnes fra en PowerShell-kommandolinjen ved hjælp af [ARMClient](https://github.com/projectkudu/ARMClient), en Åbn kilde kommandolinjeværktøj, der forenkler aktivering Azure ressourcestyring API. Brug af ARMClient og PowerShell er et mange muligheder for at få adgang til API'EN Log Analytics søgning. Med disse værktøjer kan du udnytte RESTful Azure ressourcestyring API for at foretage opkald til OMS arbejdsområder og udføre søgning kommandoer grænserne. API output søgeresultater for dig i JSON-formatet, så du kan bruge søgeresultaterne fra et program på mange forskellige måder.

## <a name="prerequisites"></a>Forudsætninger
Påmindelser kan på nuværende tidspunkt kun oprettes med en gemt søgning i Log Analytics.  Du kan referere til [Log Search REST-API](log-analytics-log-search-api.md) kan finde flere oplysninger.

## <a name="schedules"></a>Tidsplaner
En gemt søgning kan have en eller flere tidsplaner. Planen definerer, hvor ofte søgningen er Kør og et tidsinterval, som kriterierne, der er identificeret.
Tidsplaner har egenskaber i den følgende tabel.

| Egenskaben  | Beskrivelse |
|:--|:--|
| Interval | Hvor ofte kører søgningen. Målt i minutter. |
| QueryTimeSpan | Et tidsinterval, som kriterierne, der evalueres over. Skal være lig med eller større end Interval. Målt i minutter. |
| Version | Den API-version, der bruges.  I øjeblikket skal dette altid være angivet til 1. |

For eksempel overveje forespørgsel om en hændelse med et Interval af 15 minutter og et tidsinterval på 30 minutter. I dette tilfælde forespørgsler vil blive kørt hver 15 minutter, og en besked skal udløses, hvis kriterierne, der fortsat for sand over et tidsinterval på 30 minutter.

### <a name="retrieving-schedules"></a>Hente tidsplaner
Bruge Get-metoden til at hente alle planer for en gemt søgning.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Bruge metoden Get med en tidsplan-ID til at hente en bestemt tidsplan for en gemt søgning.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Følgende er et eksempel svar til en tidsplan.

    {
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
        "Interval": 15,
        "QueryTimeSpan": 15
    }

### <a name="creating-a-schedule"></a>Oprette en tidsplan
Du kan bruge metoden læg med et entydigt tidsplan-ID til at oprette en ny tidsplan.  Bemærk, at to tidsplaner ikke kan have samme-ID, selvom de er tilknyttet forskellige gemte søgninger.  Når du opretter en tidsplan i konsollen OMS, oprettes en GUID for tidsplan-ID.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Redigere en tidsplan
Brug metoden læg med et eksisterende tidsplan ID for den samme gemte søgning til at ændre samme skema.  Brødteksten i anmodningen skal indeholde etag i tidsplanen.

    $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Slette tidsplaner
Brug metoden Delete med en tidsplan-ID til at slette en tidsplan.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Handlinger
En tidsplan kan have flere handlinger. En handling kan definere en eller flere processer til at udføre som at sende en mail eller starte en runbook, eller den kan definere en grænseværdi, der bestemmer, hvornår resultaterne af en søgning opfylder nogle kriterier.  Nogle handlinger, der definerer begge så processer udføres, når grænsen er opfyldt.

Alle handlinger har egenskaber i den følgende tabel.  Forskellige typer beskeder har forskellige yderligere egenskaber, der er beskrevet nedenfor.

| Egenskaben | Beskrivelse |
|:--|:--|
| Type | Type af handlingen.  De mulige værdier er i øjeblikket advarslen, og Webhook. |
| Navn | Vist navn for påmindelsen. |
| Version | Den API-version, der bruges.  I øjeblikket skal dette altid være angivet til 1. |

### <a name="retrieving-actions"></a>Henter handlinger
Bruge Get-metoden til at hente alle handlinger for en plan.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Bruge metoden Get med handlings-ID til at hente en bestemt handling for en plan.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Oprettelse eller redigering af handlinger
Du kan bruge metoden læg med en handlings-ID, der er entydige for tidsplan for at oprette en ny handling.  Når du opretter en handling i konsollen OMS, er en GUID til handling-ID.

Brug metoden læg med en eksisterende handlings-ID for den samme gemte søgning til at ændre samme skema.  Brødteksten i anmodningen skal indeholde etag i tidsplanen.

Anmodning om formatet for at oprette en ny handling varierer efter handlingstype, så disse eksempler er angivet i afsnittene herunder.

### <a name="deleting-actions"></a>Slette handlinger
Brug metoden Delete med handlings-ID til at slette en handling.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Handlinger for beskeder
En tidsplan bør have kun én beskeder om handling.  Handlinger for beskeder har en eller flere sektioner i den følgende tabel.  Hver er beskrevet i yderligere detaljeret nedenfor.

| Sektion | Beskrivelse |
|:--|:--|
| Grænseværdi | Kriterier for når handlingen er udført. |  
| EmailNotification | Sende mail til flere modtagere. |
| Afhjælpning | Starte en runbook i Azure automatisering til at forsøge at løse identificerede problem. |

#### <a name="thresholds"></a>Tærskler
En besked om handling skal have grænseværdi for kun én.  Når resultaterne af en gemt søgning matcher grænseværdi i en handling, der er knyttet til, at søgningen, køres andre processer i denne handling.  En handling kan også indeholde kun en grænseværdi, så den kan bruges sammen med andre typer, der ikke indeholder tærskler handlinger.

Tærskler har egenskaber i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| Operatoren | Operatoren grænseværdi for sammenligning. <br> BT = større end <br> lt = mindre end |
| Værdi | Værdi for grænsen. |

For eksempel overveje forespørgsel om en hændelse med et Interval på 15 minutter, et tidsinterval på 30 minutter og en grænseværdi for større end 10. I dette tilfælde forespørgsler vil blive kørt hver 15 minutter, og en besked skal udløses, hvis den returnerede 10 hændelser, der blev oprettet i løbet af et tidsinterval på 30 minutter.

Følgende er et eksempel svar til en handling med kun en tærskelværdi.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Du kan bruge metoden læg med et entydigt handlings-ID til at oprette en ny grænseværdi handling for en plan.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Brug metoden læg med en eksisterende handlings-ID til at ændre en grænseværdi handling for en plan.  Brødteksten i anmodningen skal indeholde etag af handlingen.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>Mailmeddelelse
Mailbeskeder sende mail til en eller flere modtagere.  De indeholder egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| Modtagere | Liste over e-mail-adresser. |
| Emne | Emnet for e-mailen. |
| Vedhæftet fil | Vedhæftede filer understøttes ikke i øjeblikket, så det altid har værdien "Ingen". |

Følgende er et eksempel svar til en e-mail-meddelelse handling med en grænseværdi.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Du kan bruge metoden læg med et entydigt handlings-ID til at oprette en ny e-mail-handling for en plan.  I følgende eksempel oprettes en mailmeddelelse med en grænseværdi, så e-mailen sendes, når resultaterne af den gemte søgning overskrider grænsen.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

Brug metoden læg med en eksisterende handlings-ID til at ændre en e-mail-handling for en plan.  Brødteksten i anmodningen skal indeholde etag af handlingen.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

#### <a name="remediation-actions"></a>Afhjælpning handlinger
Remediations starte en runbook i Azure automatisering, der forsøger at løse det problem, der er identificeret med beskeden.  Du skal oprette en webhook for runbook bruges i en afhjælpning handling og derefter angive URI i egenskaben WebhookUri.  Når du opretter denne handling ved hjælp af konsollen OMS, oprettes der automatisk en ny webhook for runbook.

Remediations omfatter egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| RunbookName | Navnet på runbook. Dette skal svare til en publiceret runbook i den automatiske konto, der er konfigureret med Automation løsningen i arbejdsområdet OMS. |
| WebhookUri | URI for webhook.
| Udløbet | Udløbsdatoen og klokkeslættet for webhook.  Hvis webhook ikke har en udløbsdato, kan det være en hvilken som helst gyldigt fremtidig dato. |

Følgende er et eksempel svar til en afhjælpning handling med en grænseværdi.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Du kan bruge metoden læg med et entydigt handlings-ID til at oprette en ny afhjælpning handling for en plan.  I følgende eksempel oprettes en afhjælpning med en grænseværdi, så runbook startes, når resultaterne af den gemte søgning overskrider grænsen.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Brug metoden læg med en eksisterende handlings-ID til at ændre en afhjælpning handling for en plan.  Brødteksten i anmodningen skal indeholde etag af handlingen.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Eksempel
Følgende er en komplet eksempel til at oprette en ny besked via mail.  Dette opretter en ny tidsplan sammen med en handling, der indeholder en grænseværdi og mail.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $workspaceId    = "MyWorkspace"
    $searchId       = "51cf0bd9-5c74-6bcb-927e-d1e9080b934e"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule?api-version=2015-03-20 $scheduleJson

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/mythreshold?api-version=2015-03-20 $thresholdJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/myemailaction?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Webhook handlinger
Webhook handlinger starte en proces ved at ringe til en URL-adresse, og du kan også give en data til at blive sendt.  De ligner de afhjælpning handlinger, bortset fra de er beregnet til webhooks, kan aktivere processer end Azure automatisering runbooks.  De indeholder også flere mulighed for at give en data blev leveret til remote processen.

Webhook handlinger har ikke en grænse, men i stedet skal føjes til en plan, har en handling for besked med en grænseværdi.  Du kan tilføje flere Webhook handlinger, som alle køres, når grænseværdi er opfyldt.

Webhook handlinger omfatter egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| WebhookUri | Emnet for e-mailen. |
| CustomPayload | Brugerdefinerede data skal sendes til webhook.  Formatet afhænger af, hvad webhook forventer. |

Følgende er et eksempel svar til webhook handling og en handling for tilknyttede besked med en grænseværdi.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>Oprette eller redigere en webhook handling
Du kan bruge metoden læg med et entydigt handlings-ID til at oprette en ny webhook handling for en plan.  I følgende eksempel oprettes en Webhook handling og en handling for besked med en grænseværdi, så webhook udløses, når resultaterne af den gemte søgning overskrider grænsen.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Brug metoden læg med en eksisterende handlings-ID til at ændre en webhook handling for en plan.  Brødteksten i anmodningen skal indeholde etag af handlingen.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Næste trin

- Bruge [REST-API til at udføre log søgninger](log-analytics-log-search-api.md) i Log analyser.
