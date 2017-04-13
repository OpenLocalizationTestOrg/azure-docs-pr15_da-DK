<properties
    pageTitle="RBAC: Indbyggede roller | Microsoft Azure"
    description="Dette emne beskrives det indbyggede i roller for rollebaseret adgangskontrol (RBAC)."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/25/2016"
    ms.author="kgremban"/>

#<a name="rbac-built-in-roles"></a>RBAC: Indbyggede roller

Azure rollebaseret Access kontrolelement (RBAC) leveres med følgende indbyggede roller, der kan være tildelt til brugere, grupper og tjenester. Du kan ikke ændre definitioner af indbyggede roller. Du kan dog oprette [brugerdefinerede roller i Azure RBAC](role-based-access-control-custom-roles.md) så det passer til de specifikke behov for din organisation.

## <a name="roles-in-azure"></a>Roller i Azure

Den følgende tabel indeholder korte beskrivelser af de indbyggede roller. Klik på rollenavnet at se en detaljeret liste over **Handlinger** og **notactions** for rollen. Egenskaben **Handlinger** angiver de tilladte handlinger på Azure ressourcer. Handling strenge kan bruge jokertegn. Egenskaben **notactions** angiver de handlinger, der er udeladt fra de tilladte handlinger.

>[AZURE.NOTE] Azure rolledefinitioner konstant udvikler sig. I denne artikel er opdateret som som muligt, men du kan altid finde de nyeste roller definitioner i Azure PowerShell. Bruge-cmdletter `(get-azurermroledefinition "<role name>").actions` eller `(get-azurermroledefinition "<role name>").notactions` efter behov.

| Rollenavn | Beskrivelse |
| --------- | ----------- |
| [API Management Service bidragyder](#api-management-service-contributor) | Kan administrere API Management services |
| [Programmet indsigt komponent bidragyder](#application-insights-component-contributor) | Kan administrere programmet indsigt komponenter |
| [Automatisering Operator](#automation-operator) | Kan starte, stoppe, suspendere og genoptage job |
| [BizTalk bidragyder](#biztalk-contributor) | Kan administrere BizTalk-tjenester |
| [ClearDB MySQL DB bidragyder](#cleardb-mysql-db-contributor) | Kan administrere ClearDB MySQL databaser |
| [Bidragyder](#contributor) | Kan administrere alt undtagen adgang. |
| [Data Factory bidragyder](#data-factory-contributor) | Kan oprette og administrere data fabrikker og underordnede ressourcer grænserne. |
| [DevTest øvelser bruger](#devtest-labs-user) | Kan se alt og oprette forbindelse, start, genstart og lukning virtuelle maskiner |
| [DNS-Zone bidragyder](#dns-zone-contributor) | Kan administrere DNS-zoner og poster |
| [DocumentDB konto bidragyder](#documentdb-account-contributor) | Kan administrere DocumentDB konti |
| [Intelligente systemer konto bidragyder](#intelligent-systems-account-contributor) | Kan administrere intelligente systemer konti |
| [Netværk bidragyder](#network-contributor) | Kan administrere alle netværksressourcer |
| [Ny Relic APM konto bidragyder](#new-relic-apm-account-contributor) | Kan administrere nye Relic ydeevne programadministration konti og programmer |
| [Ejer](#owner) | Kan administrere alt, herunder access |
| [Reader](#reader) | Kan se alt, men kan ikke foretage ændringer |
| [Redis Cache bidragyder](#redis-cache-contributor) | Kan administrere Redis cache |
| [Scheduler Job samlinger bidragyder](#scheduler-job-collections-contributor) | Kan administrere scheduler job af websteder |
| [Search-tjenesten bidragyder](#search-service-contributor) | Kan administrere søgning tjenester |
| [Security Manager](#security-manager) | Kan administrere sikkerhedskomponenter, sikkerhedspolitikker og virtuelle maskiner |
| [SQL DB bidragyder](#sql-db-contributor) | Kan administrere SQL-databaser, men ikke deres sikkerhedsrelaterede politikker |
| [SQL Security Manager](#sql-security-manager) | Kan administrere sikkerhedsrelaterede politikkerne for SQL-servere og databaser |
| [SQL Server bidragyder](#sql-server-contributor) | Kan administrere SQL-servere og databaser, men ikke deres sikkerhedsrelaterede politikker |
| [Klassisk lagerplads konto bidragyder](#classic-storage-account-contributor) | Kan administrere klassisk lagerplads konti |
| [Lagerplads konto bidragyder](#storage-account-contributor) | Kan administrere lagerplads konti |
| [Access Brugeradministrator](#user-access-administrator) | Kan administrere brugeradgang til Azure ressourcer |
| [Klassisk virtuelt bidragyder](#classic-virtual-machine-contributor) | Kan administrere klassisk virtuelle maskiner, men ikke den virtuelle netværk eller lagerplads konto som de er forbundet |
| [Virtuelt bidragyder](#virtual-machine-contributor) | Kan administrere virtuelle maskiner, men ikke den virtuelle netværk eller lagerplads konto som de er forbundet |
| [Klassisk netværk bidragyder](#classic-network-contributor) | Kan administrere klassisk virtuelle netværk og reserveret IP'er |
| [Web Plan bidragyder](#web-plan-contributor) | Kan administrere web-planer |
| [Websted bidragyder](#website-contributor) | Kan administrere websteder, men ikke de web-planer, som de er forbundet |

## <a name="role-permissions"></a>Rolletilladelser
I følgende tabel beskrives de bestemte tilladelser, der er tildelt til hver roller. Dette kan omfatte **Handlinger**, som giver tilladelser, og **NotActions**, som begrænser dem.

### <a name="api-management-service-contributor"></a>API Management Service bidragyder
Kan administrere API Management services

| **Handlinger** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | Oprette og administrere API Management Services |
| Microsoft.Authorization/*/read | Læs godkendelse |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs roller og rolletildelinger |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="application-insights-component-contributor"></a>Programmet indsigt komponent bidragyder
Kan administrere programmet indsigt komponenter

| **Handlinger** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolletildelinger |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.Insights/components/* | Oprette og administrere indsigt komponenter |
| Microsoft.Insights/webtests/* | Oprette og administrere web test |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="automation-operator"></a>Automatisering Operator
Kan starte, stoppe, suspendere og genoptage job

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolletildelinger |
| Microsoft.Automation/automationAccounts/jobs/read | Læs automatisering konto job |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Genoptage et job med automation-konto |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Stoppe en automatisering konto job |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Læs automatisering konto job streams |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Annullere et job med automation-konto |
| Microsoft.Automation/automationAccounts/jobs/write | Skrive automatisering konto job |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Læse en automatisering konto job tidsplan |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Læse en automatisering konto job tidsplan |
| Microsoft.Automation/automationAccounts/read | Læs automatisering konti |
| Microsoft.Automation/automationAccounts/runbooks/read | Læs automatisering runbooks |
| Microsoft.Automation/automationAccounts/schedules/read | Læs automatisering konto tidsplaner |
| Microsoft.Automation/automationAccounts/schedules/write | Skrive automatisering konto tidsplaner |
| Microsoft.Insights/components/* | Oprette og administrere indsigt komponenter |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="biztalk-contributor"></a>BizTalk bidragyder
Kan administrere BizTalk-tjenester

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolletildelinger |
| Microsoft.BizTalkServices/BizTalk/* | Oprette og administrere BizTalk-tjenester |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB bidragyder
Kan administrere ClearDB MySQL databaser

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolletildelinger |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Support/* | Oprette og administrere supportbilletter |
| successbricks.cleardb/databases/* | Oprette og administrere ClearDB MySQL databaser |

### <a name="contributor"></a>Bidragyder
Kan administrere alt undtagen access

| **Handlinger** ||
| ------- | ------ |
| * | Oprette og administrere ressourcer af alle typer |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Delete | Kan ikke slette roller og rolletildelinger |  
| Microsoft.Authorization/*/Write | Kan ikke oprette roller og rolletildelinger |

### <a name="data-factory-contributor"></a>Data Factory bidragyder
Oprette og administrere data fabrikker og underordnede ressourcer grænserne.

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolle tildelinger |
| Microsoft.DataFactory/dataFactories/* | Oprette og administrere data fabrikker og underordnede ressourcer grænserne. |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="devtest-labs-user"></a>DevTest øvelser bruger
Kan se alt og oprette forbindelse, start, genstart og lukning virtuelle maskiner

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolle tildelinger |
| Microsoft.Compute/availabilitySets/read | Læs egenskaberne for tilgængelighed sæt |
| Microsoft.Compute/virtualMachines/*/read | Læs egenskaberne for en virtuel maskine (VM størrelser, runtime status, VM filtypenavne osv.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Dealloker virtuelle maskiner |
| Microsoft.Compute/virtualMachines/read | Læs egenskaberne for en virtuel maskine |
| Microsoft.Compute/virtualMachines/restart/action | Genstart virtuelle maskiner |
| Microsoft.Compute/virtualMachines/start/action | Start virtuelle maskiner |
| Microsoft.DevTestLab/*/read | Læs egenskaberne for en øvelse |
| Microsoft.DevTestLab/labs/createEnvironment/action | Oprette et testmiljø |
| Microsoft.DevTestLab/labs/formulas/delete | Slette formler |
| Microsoft.DevTestLab/labs/formulas/read | Læs formler |
| Microsoft.DevTestLab/labs/formulas/write | Tilføje eller redigere formler |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Evaluere øvelse politikker |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Deltage i en Indlæs belastningsjusteringstjenesten back end-adresse samling |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Deltage i en belastningsjustering indgående NAT regel |
| Microsoft.Network/networkInterfaces/*/read | Læs egenskaberne for et netværkskort (for eksempel alle Indlæs balancere, netværksgrænsefladen er en del af) |
| Microsoft.Network/networkInterfaces/join/action | Deltage i et virtuelt til en netværksgrænseflade |
| Microsoft.Network/networkInterfaces/read | Læs netværksgrænseflader |
| Microsoft.Network/networkInterfaces/write | Skrive netværksgrænseflader |
| Microsoft.Network/publicIPAddresses/*/read | Læs egenskaberne for en offentlig IP-adresse |
| Microsoft.Network/publicIPAddresses/join/action | Deltage i en offentlig IP-adresse |
| Microsoft.Network/publicIPAddresses/read | Læs netværk offentlige IP-adresser |
| Microsoft.Network/virtualNetworks/subnets/join/action | Deltage i et virtuelt netværk |
| Microsoft.Resources/deployments/operations/read | Læs installationshandlinger |
| Microsoft.Resources/deployments/read | Læs installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Storage/storageAccounts/listKeys/action | Listen lagerplads konto taster |

### <a name="dns-zone-contributor"></a>DNS-Zone bidragyder
Kan administrere DNS-zoner og poster.

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/\*/læsning | Læs roller og rolletildelinger |
| Microsoft.Insights/alertRules/\* | Oprette og administrere regler for påmindelser |
| Microsoft.Network/dnsZones/\* | Oprette og administrere DNS-zoner og poster |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/\* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Support/\* | Oprette og administrere supportbilletter |


### <a name="documentdb-account-contributor"></a>DocumentDB konto bidragyder
Kan administrere DocumentDB konti

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolle tildelinger |
| Microsoft.DocumentDb/databaseAccounts/* | Oprette og administrere DocumentDB konti |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="intelligent-systems-account-contributor"></a>Intelligente systemer konto bidragyder
Kan administrere intelligente systemer konti

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolle tildelinger |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.IntelligentSystems/accounts/* | Oprette og administrere intelligente systemer konti |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="network-contributor"></a>Netværk bidragyder
Kan administrere alle netværksressourcer

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolle tildelinger |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.Network/* | Oprette og administrere netværk |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="new-relic-apm-account-contributor"></a>Ny Relic APM konto bidragyder
Kan administrere nye Relic ydeevne programadministration konti og programmer

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolle tildelinger |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Support/* | Oprette og administrere supportbilletter |
| NewRelic.APM/accounts/* | Oprette og administrere nye Relic programmet ydeevne management konti |

### <a name="owner"></a>Ejer
Kan administrere alt, herunder access

| **Handlinger** ||
| ------- | ------ |
| * | Oprette og administrere ressourcer af alle typer |

### <a name="reader"></a>Reader
Kan se alt, men kan ikke foretage ændringer

| **Handlinger** ||
| ------- | ------ |
| * / læsning | Læs ressourcer for alle typer, undtagen hemmeligheder. |

### <a name="redis-cache-contributor"></a>Redis Cache bidragyder
Kan administrere Redis cache

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolle tildelinger |
| Microsoft.Cache/redis/* | Oprette og administrere Redis cache |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="scheduler-job-collections-contributor"></a>Scheduler Job samlinger bidragyder
Kan administrere Scheduler job af websteder

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolle tildelinger |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |  
| Microsoft.Scheduler/jobcollections/* | Oprette og administrere job af websteder |
| Microsoft.Support/* | Oprette og administrere supportbilletter  |

### <a name="search-service-contributor"></a>Search-tjenesten bidragyder
Kan administrere søgning tjenester

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolle tildelinger |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |  
| Microsoft.Search/searchServices/* | Oprette og administrere søgning tjenester |
| Microsoft.Support/* | Oprette og administrere supportbilletter  |

### <a name="security-manager"></a>Security Manager
Kan administrere sikkerhedskomponenter, sikkerhedspolitikker og virtuelle maskiner

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolle tildelinger |
| Microsoft.ClassicCompute/*/read | Læse konfigurationsoplysninger klassisk beregne virtuelle maskiner |
| Microsoft.ClassicCompute/virtualMachines/*/write | Skrive konfiguration for virtuelle maskiner |
| Microsoft.ClassicNetwork/*/read | Læse konfigurationsoplysninger om klassisk netværk  |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |  
| Microsoft.Security/* | Oprette og administrere sikkerhedskomponenter og politikker |
| Microsoft.Support/* | Oprette og administrere supportbilletter  |

### <a name="sql-db-contributor"></a>SQL DB bidragyder
Kan administrere SQL-databaser, men ikke deres sikkerhedsrelaterede politikker

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs roller og rolle tildelinger |
| Microsoft.Insights/alertRules/* | Oprette og administrere regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Sql/servers/databases/* | Oprette og administrere SQL-databaser |
| Microsoft.Sql/servers/read | Læs SQL-servere |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Kan ikke redigere overvågningslog politikker |
| Microsoft.Sql/servers/databases/auditingSettings/* | Kan ikke redigere overvågningsindstillinger |
| Microsoft.Sql/servers/databases/auditRecords/read  | Kan ikke læse poster for overvågning |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Kan ikke redigere forbindelse politikker |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Kan ikke redigere data masking politikker |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Kan ikke redigere beskeder om sikkerhedspolitikker |
| Microsoft.Sql/servers/databases/securityMetrics/* | Kan ikke redigere sikkerhed målepunkter |

### <a name="sql-security-manager"></a>SQL Security Manager
Kan administrere sikkerhedsrelaterede politikkerne for SQL-servere og databaser

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs Microsoft godkendelse |
| Microsoft.Insights/alertRules/* | Oprette og administrere indsigt regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Sql/servers/auditingPolicies/* | Oprette og administrere overvågning politikker for SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Oprette og administrere overvågning indstillingen for SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Oprette og administrere overvågning politikker for SQL server-database |
| Microsoft.Sql/servers/databases/auditingSettings/* | Oprette og administrere SQL server-database overvågningsindstillinger |
| Microsoft.Sql/servers/databases/auditRecords/read | Læs overvågningslog poster |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Oprette og administrere politikker for SQL server-database-forbindelse |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Oprette og administrere SQL server-databasedata masking politikker |
| Microsoft.Sql/servers/databases/read | Læs SQL-databaser |
| Microsoft.Sql/servers/databases/schemas/read | Læs SQL server-database ved hjælp af skemaer |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Læs SQL server-database tabelkolonner |
| Microsoft.Sql/servers/databases/schemas/tables/read | Læs SQL server-databasetabeller |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Oprette og administrere SQL server-database beskeder om sikkerhedspolitikker |
| Microsoft.Sql/servers/databases/securityMetrics/* | Oprette og administrere SQL server-database sikkerhed målepunkter |
| Microsoft.Sql/servers/read | Læs SQL-servere |
| Microsoft.Sql/servers/securityAlertPolicies/* | Oprette og administrere SQL server-beskeder om sikkerhedspolitikker |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="sql-server-contributor"></a>SQL Server bidragyder
Kan administrere SQL-servere og databaser, men ikke deres sikkerhedsrelaterede politikker

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs godkendelse|
| Microsoft.Insights/alertRules/* | Oprette og administrere indsigt regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Sql/servers/* | Oprette og administrere SQL-servere |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | Kan ikke redigere overvågning politikker for SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Kan ikke redigere SQL server-overvågningsindstillinger |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Kan ikke redigere overvågning politikker for SQL server-database |
| Microsoft.Sql/servers/databases/auditingSettings/* | Kan ikke redigere SQL server-database overvågningsindstillinger |
| Microsoft.Sql/servers/databases/auditRecords/read  | Kan ikke læse poster for overvågning |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Kan ikke redigere politikker for SQL server-database-forbindelse |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Kan ikke redigere SQL server-databasedata masking politikker |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Kan ikke redigere SQL server-database beskeder om sikkerhedspolitikker |
| Microsoft.Sql/servers/databases/securityMetrics/* | Kan ikke redigere SQL server-database sikkerhed målepunkter |
| Microsoft.Sql/servers/securityAlertPolicies/* | Kan ikke redigere SQL server-beskeder om sikkerhedspolitikker |

### <a name="classic-storage-account-contributor"></a>Klassisk lagerplads konto bidragyder
Kan administrere klassisk lagerplads konti

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs godkendelse |
| Microsoft.ClassicStorage/storageAccounts/* | Opret og administrer lagerplads konti |
| Microsoft.Insights/alertRules/* | Oprette og administrere indsigt regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |  
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="storage-account-contributor"></a>Lagerplads konto bidragyder
Kan administrere lagerplads konti, men ikke få adgang til dem.

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs alle godkendelse |
| Microsoft.Insights/alertRules/* | Oprette og administrere indsigt regler for påmindelser |
| Microsoft.Insights/diagnosticSettings/* | Administrere indstillinger for diagnosticering |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |  
| Microsoft.Storage/storageAccounts/* | Opret og administrer lagerplads konti |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="user-access-administrator"></a>Access Brugeradministrator
Kan administrere brugeradgang til Azure ressourcer

| **Handlinger** ||
| ------- | ------ |
| * / læsning | Læs ressourcer for alle typer, undtagen hemmeligheder. |
| Microsoft.Authorization/* | Administrere godkendelse |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="classic-virtual-machine-contributor"></a>Klassisk virtuelt bidragyder
Kan administrere klassisk virtuelle maskiner, men ikke den virtuelle netværk eller lagerplads konto som de er forbundet

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs godkendelse |
| Microsoft.ClassicCompute/domainNames/* | Oprette og administrere klassisk Beregn domænenavne |
| Microsoft.ClassicCompute/virtualMachines/* | Oprette og administrere virtuelle maskiner |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Deltage i netværk sikkerhedsgrupper |
| Microsoft.ClassicNetwork/reservedIps/link/action | Sammenkæde reserveret IP'er |
| Microsoft.ClassicNetwork/reservedIps/read | Læs reserveret IP-adresser |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Deltage i virtuelle netværk |
| Microsoft.ClassicNetwork/virtualNetworks/read | Læs virtuelle netværk |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Læse lagerplads konto diske |
| Microsoft.ClassicStorage/storageAccounts/images/read | Læs lagerplads konto billeder |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listen lagerplads konto taster |
| Microsoft.ClassicStorage/storageAccounts/read | Læs klassisk lagerplads konti |
| Microsoft.Insights/alertRules/* | Oprette og administrere indsigt regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="virtual-machine-contributor"></a>Virtuelt bidragyder
Kan administrere virtuelle maskiner, men ikke den virtuelle netværk eller lagerplads konto som de er forbundet

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs godkendelse |
| Microsoft.Compute/availabilitySets/* | Oprette og administrere Beregn tilgængelighed sæt |
| Microsoft.Compute/locations/* | Oprette og administrere Beregn placeringer |
| Microsoft.Compute/virtualMachines/* | Oprette og administrere virtuelle maskiner |
| Microsoft.Compute/virtualMachineScaleSets/* | Oprette og administrere virtuelt skala sæt |
| Microsoft.Insights/alertRules/* | Oprette og administrere indsigt regler for påmindelser |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Deltage i netværk programmet gateway back end-adresse grupper |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Deltage i Indlæs belastningsjusteringstjenesten back end-adresse grupper |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Deltage i justering af belastning indgående NAT-grupper |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Deltage i justering af belastning indgående NAT regler |
| Microsoft.Network/loadBalancers/read | Læs Indlæs balancere |
| Microsoft.Network/locations/* | Oprette og administrere netværksplaceringer |
| Microsoft.Network/networkInterfaces/* | Oprette og administrere netværksgrænseflader |
| Microsoft.Network/networkSecurityGroups/join/action | Deltage i netværk sikkerhedsgrupper |
| Microsoft.Network/networkSecurityGroups/read | Læs netværk sikkerhedsgrupper |
| Microsoft.Network/publicIPAddresses/join/action | Deltage i netværk offentlige IP-adresser |
| Microsoft.Network/publicIPAddresses/read | Læs netværk offentlige IP-adresser |
| Microsoft.Network/virtualNetworks/read | Læs virtuelle netværk |
| Microsoft.Network/virtualNetworks/subnets/join/action | Deltage i virtuelt netværksundernet |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |  
| Microsoft.Storage/storageAccounts/listKeys/action | Listen lagerplads konto taster |
| Microsoft.Storage/storageAccounts/read | Læs lagerplads konti |
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="classic-network-contributor"></a>Klassisk netværk bidragyder
Kan administrere klassisk virtuelle netværk og reserveret IP'er

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs godkendelse |
| Microsoft.ClassicNetwork/* | Oprette og administrere klassisk netværk |
| Microsoft.Insights/alertRules/* | Oprette og administrere indsigt regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |  
| Microsoft.Support/* | Oprette og administrere supportbilletter |

### <a name="web-plan-contributor"></a>Web Plan bidragyder
Kan administrere web-planer

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs godkendelse |
| Microsoft.Insights/alertRules/* | Oprette og administrere indsigt regler for påmindelser |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |  
| Microsoft.Support/* | Oprette og administrere supportbilletter |
| Microsoft.Web/serverFarms/* | Oprette og administrere serverfarme |

### <a name="website-contributor"></a>Websted bidragyder
Kan administrere websteder, men ikke de web-planer, som de er forbundet

| **Handlinger** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Læs godkendelse |
| Microsoft.Insights/alertRules/* | Oprette og administrere indsigt regler for påmindelser |
| Microsoft.Insights/components/* | Oprette og administrere indsigt komponenter |
| Microsoft.ResourceHealth/availabilityStatuses/read | Læs tilstanden for ressourcerne |
| Microsoft.Resources/deployments/* | Oprette og administrere ressource gruppe installationer |
| Microsoft.Resources/subscriptions/resourceGroups/read | Læs ressourcegrupper |  
| Microsoft.Support/* | Oprette og administrere supportbilletter |
| Microsoft.Web/certificates/* | Oprette og administrere websted certifikater |
| Microsoft.Web/listSitesAssignedToHostName/read | Læse websteder, der er tildelt et værtsnavn |
| Microsoft.Web/serverFarms/join/action | Deltage i serverfarme |
| Microsoft.Web/serverFarms/read | Læs serverfarme |
| Microsoft.Web/sites/* | Oprette og administrere websteder |

## <a name="see-also"></a>Se også
- [Rollebaseret adgangskontrol](role-based-access-control-configure.md): Introduktion til RBAC i portalen Azure.
- [Brugerdefineret roller i Azure RBAC](role-based-access-control-custom-roles.md): Lær at oprette brugerdefinerede roller, så det passer til dine access-behov.
- [Oprette en access ændre rapporten Oversigt over](role-based-access-control-access-change-history-report.md): Hold styr på ændre rolletildelinger i RBAC.
- [Rollebaseret adgangskontrol fejlfinding](role-based-access-control-troubleshooting.md): få forslag til at løse almindelige problemer.
