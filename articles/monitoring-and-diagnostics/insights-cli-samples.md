<properties
    pageTitle="Azure eksempler på skærm CLI Hurtig start. | Microsoft Azure"
    description="Eksempel CLI-kommandoer til Azure skærm funktioner. Azure skærm er en Microsoft Azure-tjeneste, hvor du kan sende beskeder om meddelelser, skal du ringe URL-webadresser, der er baseret på værdier for konfigurerede telemetridata og Autoskalering Cloud Services, virtuelle maskiner og Web Apps."
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
    ms.date="09/08/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor--cross-platform-cli-quick-start-samples"></a>Eksempler på Azure skærm på tværs af platforme CLI Hurtig start

I denne artikel beskrives eksempel kommandolinjen (CLI) indeholder kommandoer til at hjælpe dig med at få adgang til Azure skærm funktioner. Azure skærm giver dig mulighed for at Autoskalering Cloud Services, virtuelle maskiner og Web Apps og til at sende påmindelser eller ringe URL-webadresser, der er baseret på værdierne af konfigurerede telemetridata.

>[AZURE.NOTE] Azure skærm er det nye navn til Hvad hedder "Azure indsigt" indtil Sept 25 2016. Navneområder og dermed kommandoerne nedenfor indeholder stadig "viden".


## <a name="prerequisites"></a>Forudsætninger

Hvis du ikke har allerede installeret Azure CLI, kan du se [installere Azure CLI](../xplat-cli-install.md). Hvis du er bekendt med Azure CLI, du kan læse mere om den på [Brug Azure CLI til Mac, Linux, og Windows Azure Resource Manager](../xplat-cli-azure-resource-manager.md).


I Windows, skal du installere npm fra [Node.js websted](https://nodejs.org/). Når du gennemføre installationen ved hjælp af CMD.exe med Kør som Administrator rettigheder, kan du udføre følgende fra den mappe, hvor npm er installeret:

```console
npm install azure-cli --global
```

Gå derefter til en hvilken som helst mappe/placering, du vil, og skriv på kommandolinjen:

```console
azure help
```

## <a name="log-in-to-azure"></a>Log på Azure

Det første trin er at logge på din Azure-konto.

```console
azure login
```

Når du kører denne kommando, skal du logge på via vejledningen på skærmen. Bagefter, du kan se din konto, TenantId og standard abonnement-id. Alle kommandoer fungerer i konteksten for din standard-abonnement.

Brug følgende kommando for at få vist oplysningerne for dit aktuelle abonnement.

```console
azure account show
```

Hvis du vil ændre arbejde kontekst til et andet abonnement, skal du bruge følgende kommando.

```console
azure account set "subscription ID or subscription name"
```

Hvis du vil bruge kommandoerne Azure ressourcestyring og Azure skærm, skal du være i Azure ressourcestyring tilstand.

```console
azure config mode arm
```

Følg denne fremgangsmåde for at få vist en liste over alle understøttede Azure skærm-kommandoer.

```console
azure insights
```

## <a name="view-audit-logs-for-a-subscription"></a>Vis overvågningslogge til et abonnement

Følg denne fremgangsmåde for at få vist en liste over overvågningslogge.

```console
azure insights logs list [options]
```

Kan du prøve følgende for at få vist alle tilgængelige indstillinger.

```console
azure insights logs list -help
```

Her er et eksempel på listen logge af en resourceGroup

```console
azure insights logs list --resourceGroup "myrg1"
```

Eksempel på listen logge af kalderen

```console
azure insights logs list --caller "myname@company.com"
```

Eksempel på liste logger ved opkalds på en ressourcetype, inden for en start- og slutdato for

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Arbejde med beskeder
Du kan bruge oplysningerne i sektionen til at arbejde med beskeder.

### <a name="get-alert-rules-in-a-resource-group"></a>Få besked om regler i en ressourcegruppe

```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Oprette en metriske beskeder om regel

```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-a-log-alert-rule"></a>Oprette en logfil beskeder om regel

```console
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### <a name="create-webtest-alert-rule"></a>Oprette webtest reglen

```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Slette en besked om

```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Log profiler
Brug oplysningerne i dette afsnit til at arbejde med log profiler.

### <a name="get-a-log-profile"></a>Få en log-profil

```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Tilføj en log profil uden opbevaring

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Fjerne en log-profil

```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Tilføje en log profil med opbevaring

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Tilføj en log profil med opbevaring og EventHub

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnosticering
Brug oplysningerne i dette afsnit til at arbejde med indstillinger for diagnosticering.

### <a name="get-a-diagnostic-setting"></a>Få en diagnosticering til at

```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Deaktivere en diagnosticering indstilling

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Aktivere en diagnosticering indstilling uden opbevaring

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Autoskalering
Brug oplysningerne i dette afsnit til at arbejde med Autoskalering indstillinger. Du vil ændre disse eksempler.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Hent Autoskalering indstillinger for en ressourcegruppe

```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Få Autoskalering indstillinger ved navn i en ressourcegruppe

```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Angive indstillinger for auotoscale

```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```
