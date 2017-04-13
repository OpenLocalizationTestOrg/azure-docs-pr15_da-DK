<properties
    pageTitle="Oversigt over Azure aktivitet loggen | Microsoft Azure"
    description="Få mere at vide, hvad Azure aktivitet loggen er, og hvordan du kan bruge det for at forstå hændelser i abonnementet Azure."
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
    ms.date="10/25/2016"
    ms.author="johnkem"/>

# <a name="overview-of-the-azure-activity-log"></a>Oversigt over Azure aktivitet logfilen
**Azure aktivitetslog** er en logfil, der giver indblik i de handlinger, der er udført af ressourcer i dit abonnement. Aktivitet loggen tidligere hed "Overvågningslogfiler" eller "Funktionsdygtige logger", da den rapporterer kontrolelement plan begivenheder for dine abonnementer. Bruger aktivitet loggen, kan du bestemme den ' hvad, som, og hvornår ' for et skrive handlinger (læg, INDLÆG, slette) taget på ressourcerne i dit abonnement. Du kan også forstå status for handlingen og andre relevante egenskaber. Aktivitet loggen omfatter ikke læst (Hent) handlinger.

Aktivitet loggen er forskellig fra [Diagnosticeringslogfiler](./monitoring-overview-of-diagnostic-logs.md), som er alle logfiler, der udsendes af en ressource. Disse logfiler indsamler data om den driften af ressourcen i stedet for handlinger på denne ressource.

Du kan hente begivenheder fra din aktivitet logfilen ved hjælp af portalen Azure, CLI, PowerShell-cmdletter og Azure skærm REST-API.

Se denne [video Introduktion til loggen aktivitet](https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz).  

## <a name="what-you-can-do-with-the-activity-log"></a>Hvad du kan gøre med aktivitet logfilen
Her er nogle af de ting, du kan gøre med aktivitet loggen:

- Forespørgsel, og få den vist i **Azure-portalen**.
- Forespørge det via REST-API, PowerShell-Cmdlet eller CLI.
- [Oprette en mail eller webhook besked, der udløser fra en aktivitet loghændelse.](./insights-auditlog-to-webhook-email.md)
- [Gemme den til en **Lagerplads konto** til arkivering eller manuel inspektion](./monitoring-archive-activity-log.md). Du kan angive opbevaring tid (i dage) ved hjælp af **Log profiler**.
- Analysere dem i PowerBI ved hjælp af [**PowerBI indhold pack**](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/).
- [Streame den til en **Begivenhed Hub** ](./monitoring-stream-activity-logs-event-hubs.md) til indtagelse af et tredjepartstjenester eller et brugerdefineret analytics løsning som PowerBI.

## <a name="export-the-activity-log-with-log-profiles"></a>Eksportere aktivitet logfilen med Log profiler
En **Log profil** styrer, hvordan din aktivitetslog eksporteres. Ved hjælp af en Log profil, kan du konfigurere:

- Hvor aktivitet loggen skal sendes (lagerplads konto eller begivenhed Hubs)
- Hvilke begivenhed kategorier (Skriv, Slet handling) skal sendes
- Hvilke områder (placeringer), der skal eksporteres
- Hvor længe aktivitet loggen skal bevares i en lagerplads konto – en opbevaring af nul dage betyder logfiler holdes uendelig. Ellers kan værdien være en hvilken som helst antallet af dage mellem 1 og 2147483647. Hvis opbevaringspolitikker er angivet, men gemme logfiler i en lagerplads konto er deaktiveret (for eksempel, hvis der kun begivenhed hubber eller OMS indstillinger er markeret), har ingen indflydelse på opbevaringspolitikker.

Disse indstillinger kan konfigureres via indstillingen "Eksportér" i bladet aktivitetslog på portalen. De kan også være konfigureret automatisk [ved hjælp af Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell-cmdlet'er eller CLI. Et abonnement kan kun have én log profil.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Konfigurere log profiler ved hjælp af portalen Azure
Du kan streame aktivitet Logfør i en begivenhed Hub eller gemme dem på en lagerplads konto ved hjælp af indstillingen "Eksportér" i portalen Azure.

1. Gå til bladet **Aktivitetslog** ved hjælp af menuen til venstre på portalen.

    ![Naviger til aktivitet logfil i portalen](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Klik på knappen **Eksportér** på øverst del af bladet.

    ![Eksportere knappen i portalen](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. I bladet, der vises, kan du vælge:  
    - områder, du vil eksportere begivenheder
    - Kontoen lagerplads, du vil gemme begivenheder
    - antallet af dage, du vil beholde disse begivenheder i lagerplads. En indstilling på 0 dage bevarer loggene uendelig.
    - Tjenesten Bus Namespace, hvor du vil have en begivenhed Hub skal have oprettet på at streaming disse begivenheder.

    ![Eksportere aktivitetslog blade](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Klik på **Gem** for at gemme disse indstillinger. Indstillingerne anvendes umiddelbart på dit abonnement.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Konfigurere log profiler ved hjælp af Azure PowerShell-cmdlet'er
#### <a name="get-existing-log-profile"></a>Få eksisterende log profil
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Tilføj en log-profil
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Egenskaben         | Påkrævet | Beskrivelse   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Navn             | Ja      | Navnet på din log profil.                                 |
| StorageAccountId | Nej       | Ressource-ID for kontoen lagerplads, aktivitet loggen skal gemmes.                         |
| serviceBusRuleId | Nej       | Service Bus regel-ID'ET for det Service Bus navneområde, du vil gerne have begivenhed hubs, der er oprettet i. Er en streng med dette format: `{service bus resource ID}/authorizationrules/{key name}`. |
| Placeringer        | Ja      | Kommasepareret liste over områder, du vil indsamle aktivitetslog hændelser.              |
| RetentionInDays  | Ja      | Antallet af dage, for hvilke hændelser skal bevares, mellem 1 og 2147483647. En værdi på nul gemmer logge på ubestemt tid (altid). |
| Kategorier       | Nej       | Kommasepareret liste over kategorier, der indsamles. Mulige værdier er skrive, slette og handling.                                 |

#### <a name="remove-a-log-profile"></a>Fjerne en log-profil
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Konfigurere log profiler ved hjælp af Azure på tværs af platforme CLI
#### <a name="get-existing-log-profile"></a>Få eksisterende log profil
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
Den `name` egenskaben skal være navnet på din log profil.

#### <a name="add-a-log-profile"></a>Tilføj en log-profil
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Egenskaben         | Påkrævet | Beskrivelse   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Navn             | Ja      | Navnet på din log profil.                                 |
| storageId        | Nej       | Ressource-ID for kontoen lagerplads, aktivitet loggen skal gemmes.                         |
| serviceBusRuleId | Nej       | Service Bus regel-ID'ET for det Service Bus navneområde, du vil gerne have begivenhed hubs, der er oprettet i. Er en streng med dette format: `{service bus resource ID}/authorizationrules/{key name}`. |
| placeringer        | Ja      | Kommasepareret liste over områder, du vil indsamle aktivitetslog hændelser.              |
| retentionInDays  | Ja      | Antallet af dage, for hvilke hændelser skal bevares, mellem 1 og 2147483647. En værdi på nul gemmer logge på ubestemt tid (altid).     |
| kategorier       | Nej       | Kommasepareret liste over kategorier, der indsamles. Mulige værdier er skrive, slette og handling.                                 |

#### <a name="remove-a-log-profile"></a>Fjerne en log-profil
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>Begivenhed skema
Hver enkelt hændelse aktivitet log har en JSON blob ligner dette eksempel:

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| Elementnavn         | Beskrivelse             |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| godkendelse        | BLOB af RBAC egenskaber for begivenheden. Normalt indeholder "handling", "rolle" og "omfang" egenskaber.|
| opkalds               | E-mail-adressen på den bruger, der er udført handlingen, UPN-kravet eller SPN krav baseret på tilgængelighed.|
| kanaler             | En af følgende værdier: "Admin", "Handling"|
| correlationId        | Normalt et GUID i strengformatet. Hændelser, der deler en correlationId hører til den samme uber handling.   |
| Beskrivelse          | Statisk tekstbeskrivelse af en begivenhed.                              |
| eventDataId          | Entydigt id for en begivenhed.    |
| eventSource          | Navnet på den Azure service eller infrastruktur, der har oprettet begivenheden.    |
| httpRequest          | BLOB, der beskriver HTTP-anmodningen. Normalt indeholder "clientRequestId", "clientIpAddress" og "metode" (HTTP metode. For example, PLACERE).                            |
| niveau                | Niveau for begivenheden. En af følgende værdier: "Kritiske", "Fejl", "Advarsel", "Information" og "Detaljeret"  |
| resourceGroupName    | Navnet på ressourcegruppen for påvirkede ressourcen.               |
| resourceProviderName | Navnet på den ressource-provider til den påvirkede ressource             |
| resourceUri          | Ressource-id på den påvirkede ressource.                               |
| operationId          | Et GUID, der deles af de hændelser, der svarer til en enkelt handling.         |
| operationName        | Navnet på handlingen.  |
| Egenskaber           | Sæt `<Key, Value>` par (det vil sige, en ordbog), der beskriver oplysningerne om begivenheden.                                |
| status               | En streng, der beskriver status for handlingen. Nogle almindelige værdier er: gang skal i gang, lykkedes, mislykkedes, aktiv, løst.                                |
| Understatus            | Normalt HTTP-statuskode af tilsvarende RESTEN opkald, men kan også indeholde andre strenge, der beskriver en understatus, som disse almindelige værdier: OK (http-statuskode: 200), oprettede (http-statuskode: 201), accepteret (http-statuskode: 202), ikke indhold (http-statuskode: 204), forkert anmodning (http-statuskode: 400), blev ikke fundet (http-statuskode: 404), konflikt (http-statuskode : 409), intern serverfejl (HTTP statuskode: 500), Service ikke tilgængelig (HTTP statuskode: 503), timeout for gatewayen (HTTP statuskode: 504). |
| eventTimestamp       | Tidsstemplet for, hvornår hændelsen blev oprettet af tjenesten Azure behandling af anmodningen, svarer begivenheden.     |
| submissionTimestamp  | Tidsstemplet for, hvornår begivenheden blev tilgængeligt til forespørgsler.             |
| subscriptionId       | Azure abonnement-id.  |
| nextLink             | Fortsættelse token til at hente den næste række resultater, når de er delt op i flere svar. Normalt nødvendigt, når der er mere end 200 poster.     |

## <a name="next-steps"></a>Næste trin
- [Lær mere om aktivitetslog (tidligere overvågningslogge)](../resource-group-audit.md)
- [Streame Azure aktivitet loggen til begivenhed hubber](./monitoring-stream-activity-logs-event-hubs.md)
