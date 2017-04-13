<properties
    pageTitle="Streame Azure aktivitet loggen til begivenhed hubber | Microsoft Azure"
    description="Lær at streame Azure aktivitet loggen til begivenhed hubber."
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
    ms.date="10/03/2016"
    ms.author="johnkem"/>

# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Streame Azure aktivitet loggen til begivenhed hubber
[**Azure aktivitetslog**](./monitoring-overview-activity-logs.md) streames i nær realtid til alle programmer, ved hjælp af indstillingen "Eksportere" indbyggede i portalen eller ved at aktivere tjenesten Bus regel-id'et i en Log profil via Azure PowerShell-cmdlet'er eller Azure CLI.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Hvad du kan gøre med aktivitetslog og begivenhed Hubs
Her er et par måder, du kan bruge streaming muligheden for aktivitetslog:

- **Stream til fra tredjepart til logføring og telemetri** – bliver Over tid, begivenhed Hubs streaming ordning pipe din aktivitetslog til tredjepart SIEMs og logge analytics løsninger.
- **Oprette en brugerdefineret telemetri og logføring platform** – Hvis du allerede har en specialtilpasset telemetri platform eller er lige tænker over opbygning af en, meget SVG udgivelse abonnement art begivenhed Hubs gør muligt at fleksibelt indtager aktivitet logfilen. [Se Dan Rosanova vejledning til brug af begivenhed Hubs i en global skala telemetri platform her.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Aktivere streaming af aktivitet logfilen
Du kan aktivere streaming af aktivitet loggen, enten fra et program eller via portalen. Uanset hvilken mulighed, du vælger en tjeneste Bus Namespace og en delt adgangspolitik for dette navneområde, og der oprettes en begivenhed Hub i pågældende navneområde, når den første nye aktivitet loghændelse finder sted. Hvis du ikke har en tjeneste Bus Namespace, skal du først oprette en. Hvis du tidligere har streames aktivitetslog hændelser, der skal denne tjeneste Bus Namespace, genbruges hubben begivenhed, der tidligere blev oprettet. Den delte access-politik definerer de tilladelser, der indeholder den streaming ordning. I dag, kræver streaming til en begivenhed hubber **Administrer**, **læse**og **sende** tilladelser. Du kan oprette eller ændre Service Bus Namespace delt access politikker i portalen klassisk under fanen "Konfigurer" til din tjeneste Bus Namespace. Hvis du vil opdatere aktivitetslog log profil for at medtage streaming, skal brugeren foretage ændringen have tilladelsen ListKey på Service Bus godkendelsesreglen.

### <a name="via-azure-portal"></a>Via Azure-portalen 
1. Gå til bladet **Aktivitetslog** ved hjælp af menuen til venstre på portalen.

    ![Naviger til aktivitet logfil i portalen](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Klik på knappen **Eksportér** på øverst del af bladet.

    ![Eksportere knappen i portalen](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Bladet, der vises, kan du markere de områder, hvor du vil gerne stream begivenheder og Service Bus Namespace, hvor du vil have en begivenhed Hub skal have oprettet på at streaming disse begivenheder.

    ![Eksportere aktivitetslog blade](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Klik på **Gem** for at gemme disse indstillinger. Indstillingerne anvendes umiddelbart på dit abonnement.


### <a name="via-powershell-cmdlets"></a>Via PowerShell-cmdlet'er
Hvis der findes allerede en log profil, skal du først fjerne profilen.

1. Brug `Get-AzureRmLogProfile` at identificere, om der findes en log-profil
2. Hvis det er tilfældet, skal du bruge `Remove-AzureRmLogProfile` at fjerne den.
3. Brug `Set-AzureRmLogProfile` til at oprette en profil:

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

Tjenesten Bus regel-ID'ET er en streng med dette format: {service bus ressource-ID} /authorizationrules/ {vigtige name}, for eksempel 

### <a name="via-azure-cli"></a>Via Azure CLI
Hvis der findes allerede en log profil, skal du først fjerne profilen.

1. Brug `azure insights logprofile list` at identificere, om der findes en log-profil
2. Hvis det er tilfældet, skal du bruge `azure insights logprofile delete` at fjerne den.
3. Brug `azure insights logprofile add` til at oprette en profil:

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

Tjenesten Bus regel-ID'ET er en streng med dette format: `{service bus resource ID}/authorizationrules/{key name}`.
 
## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Hvordan optager logdataene fra begivenhed Hubs?
[Skemaet for aktivitet logfilen findes her](./monitoring-overview-activity-logs.md). Hver enkelt hændelse er i en matrix med JSON BLOB kaldet "poster."

## <a name="next-steps"></a>Næste trin
- [Arkivere aktivitet loggen til en lagerplads konto](./monitoring-archive-activity-log.md)
- [Læs oversigt over Azure aktivitet logfilen](./monitoring-overview-activity-logs.md)
- [Konfigurere en besked, der er baseret på en aktivitet loghændelse](./insights-auditlog-to-webhook-email.md)
