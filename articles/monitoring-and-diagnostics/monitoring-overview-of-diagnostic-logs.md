<properties
    pageTitle="Oversigt over Azure diagnosticeringslogfiler | Microsoft Azure"
    description="Få mere at vide, hvad Azure diagnosticeringslogfiler er, og hvordan du kan bruge dem til at forstå hændelser i en Azure ressource."
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
    ms.date="10/12/2016"
    ms.author="johnkem; magoedte"/>

# <a name="overview-of-azure-diagnostic-logs"></a>Oversigt over Azure diagnosticeringslogfiler
**Azure diagnosticeringslogfiler** er logfiler, der udsendes af en ressource, der leverer omfattende og hyppige data om driften af ressourcen. Indholdet af disse logfiler varierer efter ressourcetype (for eksempel Windows-system hændelseslogge er én kategori af Diagnosticeringsloggene for FOS og blob, tabel, og kø logfiler findes kategorier af diagnosticeringslogfiler for lagerplads konti) og adskiller sig fra den [aktivitetslog (tidligere kendt som overvågningsloggen eller funktionsdygtige Log)](monitoring-overview-activity-logs.md), som indeholder indsigt i de handlinger, der er udført af ressourcer i dit abonnement. Ikke alle ressourcer, der understøtter den nye type diagnosticeringslogfiler, der er beskrevet her. Listen over understøttede tjenester nedenfor viser, hvilke ressourcetyper understøtter den nye diagnosticeringslogfiler.

![Logiske placeringen af logge til diagnosticering](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## <a name="what-you-can-do-with-diagnostic-logs"></a>Hvad du kan gøre med diagnosticeringslogfiler
Her er nogle af de ting, du kan gøre med Diagnosticeringslogge:

- Gemme dem i en **Lagerplads konto** for overvågning eller manuel kontrol. Du kan angive opbevaring tid (i dage) ved hjælp af **Indstillinger for diagnosticering**.
- Du kan [streame dem til **Begivenhed hubber** ](monitoring-stream-diagnostic-logs-to-event-hubs.md) for indtagelse af et tredjepartstjenester eller et brugerdefineret analytics løsning som PowerBI.
- Analysere dem med [OMS Log Analytics](../log-analytics/log-analytics-azure-storage-json.md)

## <a name="diagnostic-settings"></a>Indstillinger for diagnosticering
Diagnosticeringslogfiler for ikke-Beregn ressourcer er konfigureret ved hjælp af indstillinger for diagnosticering. **Indstillinger for diagnosticering** for et kontrolelement til ressource:

- Som sendes diagnosticeringslogfiler (lagerplads konto, begivenhed Hubs og/eller OMS Log Analytics).
- Hvilke Log kategorier sendes.
- Hvor længe hver log kategori bør bevares i en lagerplads konto – en opbevaring på nul dage betyder, at logge holdes uendelig. Ellers kan denne værdi ligge fra 1 til 2147483647. Hvis opbevaringspolitikker er angivet, men gemme logfiler i en lagerplads konto er deaktiveret (for eksempel hvis der kun begivenhed hubber eller OMS indstillinger er markeret), opbevaringspolitikker har ingen effekt.

Disse indstillinger er nemt konfigureret via bladet diagnosticering for en ressource på portalen Azure, via Azure PowerShell og CLI kommandoer eller via [Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [AZURE.WARNING] Logge til diagnosticering og målepunkter for Beregn ressourcer (for eksempel FOS eller tjeneste strukturen) kan du bruge [en separat funktion til konfiguration og valg af output](../azure-diagnostics.md).

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Sådan aktiveres samling af logge til diagnosticering
Samling af diagnosticeringslogfiler kan aktiveres som en del af oprettelse af en ressource, eller når en ressource er oprettet via ressourcens blade på portalen. Du kan også aktivere diagnosticeringslogfiler når som helst ved hjælp af Azure PowerShell eller CLI kommandoer eller ved hjælp af Azure skærm REST-API.

> [AZURE.TIP] Disse instruktioner gælder muligvis ikke direkte til hver ressource. Se linkene skema nederst på denne side for at forstå specielle trin, der gælder muligvis for visse ressourcetyper.

[I denne artikel beskrives, hvordan du kan bruge en ressourceskabelon til at aktivere diagnosticeringsindstillinger, når du opretter en ressource](./monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>Aktivere diagnosticeringslogfiler på portalen
Du kan aktivere diagnosticeringslogfiler på portalen Azure, når du opretter Beregn ressourcetyper ved at aktivere filtypenavnet Windows eller Linux Azure diagnosticering:

1.  Gå til **Ny** og vælge den ressource, du er interesseret i.
2.  Vælg **aktiveret** efter konfiguration af de grundlæggende indstillinger og vælge en størrelse i bladet **Indstillinger** under **overvågnings**, og vælg en lagerplads-konto, hvor du vil have til at gemme diagnosticeringslogfilerne. Du betaler normal data satser for lager og transaktioner, når du sender diagnostics til en lagerplads konto.

    ![Aktivere diagnosticeringslogfiler under oprettelse af ressource](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3.  Klik på **OK** , og oprette ressourcen.

Du kan aktivere diagnosticeringslogfiler på portalen Azure for ikke-Beregn ressourcer, når en ressource er blevet oprettet ved at gøre følgende:

1.  Gå til bladet for ressourcen, og Åbn bladet **diagnosticering** .
2.  Klik **på** og vælg en lagerplads firma og/eller begivenhed Hub.

    ![Aktivere diagnosticeringslogfiler efter oprettelse af ressource](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3.  Vælg hvilken **Log kategorier** , du vil have til at indsamle eller streame under **logfiler**.
4.  Klik på **Gem**.

### <a name="enable-diagnostic-logs-via-powershell"></a>Aktivere diagnosticeringslogfiler via PowerShell
Brug følgende kommandoer til at aktivere diagnosticeringslogfiler via Azure PowerShell-cmdlet'er.

Brug denne kommando til at aktivere lagringen af diagnosticeringslogfiler på en lagerplads konto:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true

Lagerplads konto-ID er ressource-id for kontoen lagerplads, du vil sende logfiler.

Brug denne kommando til at aktivere streaming af diagnosticeringslogfiler en begivenhed hub:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

Tjenesten Bus regel-ID'ET er en streng med dette format: `{service bus resource ID}/authorizationrules/{key name}`.

Brug denne kommando til at aktivere afsendelse af logge til diagnosticering til et Log Analytics-arbejdsområde:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [log analytics workspace id] -Enabled $true

> [AZURE.NOTE] Parameteren WorkspaceId er ikke tilgængelige i oktober-versionen. Det vil være tilgængelige i November-versionen.

Du kan få dit Log Analytics arbejdsområde-ID i portalen Azure.

Du kan kombinere disse parametre for at aktivere flere Outputindstillinger.

### <a name="enable-diagnostic-logs-via-cli"></a>Aktivere diagnosticeringslogfiler via CLI
For at aktivere diagnosticeringslogfiler via Azure CLI skal du bruge følgende kommandoer:

Brug denne kommando til at aktivere lagringen af diagnosticeringslogfiler på en lagerplads konto:

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

Lagerplads konto-ID er ressource-id for kontoen lagerplads, du vil sende logfiler.

Brug denne kommando til at aktivere streaming af diagnosticeringslogfiler en begivenhed hub:

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

Tjenesten Bus regel-ID'ET er en streng med dette format: `{service bus resource ID}/authorizationrules/{key name}`.

Brug denne kommando til at aktivere afsendelse af logge til diagnosticering til et Log Analytics-arbejdsområde:

    azure insights diagnostic set --resourceId <resourceId> --workspaceId <workspaceId> --enabled true

> [AZURE.NOTE] Parameteren workspaceId er ikke tilgængelige i oktober-versionen. Det vil være tilgængelige i November-versionen.

Du kan få dit Log Analytics arbejdsområde-ID i portalen Azure.

Du kan kombinere disse parametre for at aktivere flere Outputindstillinger.

### <a name="enable-diagnostic-logs-via-rest-api"></a>Aktivere diagnosticeringslogfiler via REST-API
For at ændre indstillinger for diagnosticering ved hjælp af Azure skærm REST-API skal du se [dette dokument](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-diagnostic-settings-in-the-portal"></a>Administrere indstillinger for diagnosticering i portalen

For at sikre, at alle dine ressourcer er korrekt konfigureret med indstillinger for diagnosticering, kan du gå til bladet **overvågnings** i portalen og åbne bladet **Diagnosticeringslogfiler** .

![Diagnosticering logfiler blade i portalen](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Du skal muligvis klikke på "Flere tjenester" til at finde bladet overvågnings.

I denne blade, kan du få vist og filtrere alle de ressourcer, der understøtter diagnosticeringslogfiler for at se, hvis de har aktiveret diagnosticering og hvilke lagerplads konto, begivenhed hub og/eller Log Analytics arbejdsområde disse logfiler der flyder til.

![Diagnostiske logfiler blade resultater i portalen](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

At klikke på en ressource, vises alle de logfiler, der er gemt på kontoen lager og giver dig mulighed for at deaktivere eller ændre indstillingerne for diagnosticering. Klik på Hent for at hente logfiler for en bestemt periode.

![Diagnosticering logfiler blade én ressource](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [AZURE.NOTE] Diagnosticeringslogfiler kun vises i denne visning og er tilgængelig til hentning, hvis du har konfigureret diagnosticering indstillinger for at gemme dem i en lagerplads konto.

Klikke på linket til **Diagnosticeringsindstillinger** vil få vist bladet diagnosticeringsindstillinger, hvor du kan aktivere, deaktivere eller ændre indstillingerne for diagnosticering for den valgte ressource.

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>Understøttede tjenester og -Skemafiler til diagnosticeringslogfiler
Skemaet for diagnosticeringslogfiler varierer afhængigt af den ressource, og log kategori. Nedenfor finder du de understøttede tjenester og deres skema.

| Tjenesten                       | Skemaet og dokumenter                                                                                                   |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|
|    Software justering af belastning     |    [Log analytics til justering af belastning Azure (Preview)](../load-balancer/load-balancer-monitor-log.md)             |
|    Netværk sikkerhedsgrupper    |    [Log analyser af netværk sikkerhedsgrupper (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)     |
|    Program-Gateways       |    [Logføring af diagnosticering til Application Gateway](../application-gateway/application-gateway-diagnostics.md)     |
|    Vigtige samling                  |    [Azure vigtige samling logføring](../key-vault/key-vault-logging.md)                                                 |
|    Azure søgning               |    [Aktivere og brug af søgning trafik Analytics](../search/search-traffic-analytics.md)                         |
|    Sø datalager            |    [Få adgang til diagnosticeringslogfiler til Azure sø datalager](../data-lake-store/data-lake-store-diagnostic-logs.md) |
|    Data sø Analytics        |    [Få adgang til diagnosticeringslogfiler for Azure Data sø analyser](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
|    Logik Apps                 |    Ingen skema, der er tilgængelige.                                                                                         |
|    Azure batchen                |    [Azure logføring til diagnosticering af batchen](../batch/batch-diagnostics.md)                                              |
|    Azure-automatisering           |    [Log analyser af Azure automatisering](../automation/automation-manage-send-joblogs-log-analytics.md)          |
|    Begivenhed Hub                  |    Ingen skema, der er tilgængelige.                                                                                         |
|    Service Bus                |    Ingen skema, der er tilgængelige.                                                                                         |
|    Stream Analytics           |    Ingen skema, der er tilgængelige.                                                                                         |

## <a name="supported-log-categories-per-resource-type"></a>Understøttede log kategorier per ressourcetype

|Ressourcetype|Kategori|Kategori visningsnavn|
|---|---|---|
|Microsoft.Automation/automationAccounts|JobLogs|Logfiler over job|
|Microsoft.Automation/automationAccounts|JobStreams|Jobbet Streams|
|Microsoft.Batch/batchAccounts|ServiceLog|Registrerer tjenesten|
|Microsoft.DataLakeAnalytics/accounts|Overvågningslog|Overvågningslogge|
|Microsoft.DataLakeAnalytics/accounts|Anmodninger om|Anmode om logfiler|
|Microsoft.DataLakeStore/accounts|Overvågningslog|Overvågningslogge|
|Microsoft.DataLakeStore/accounts|Anmodninger om|Anmode om logfiler|
|Microsoft.EventHub/namespaces|ArchiveLogs|Arkivere logfiler|
|Microsoft.EventHub/namespaces|OperationalLogs|Funktionsdygtige logfiler|
|Microsoft.KeyVault/vaults|AuditEvent|Overvågningslogge|
|Microsoft.Logic/workflows|WorkflowRuntime|Diagnosticering hændelser i arbejdsprocesser runtime|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Netværk sikkerhedsgruppe begivenhed|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Netværk sikkerhedsgruppe regel tæller|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Netværk sikkerhedsgruppe regel Flow begivenhed|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Indlæse belastningsjusteringstjenesten beskeder om begivenheder|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Indlæse belastningsjusteringstjenesten efterprøvning af af sundhedsstatus|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Programmets Gateway Access logfil|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Programmets Gateway ydeevne logfil|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Programmets Gateway Firewall logfil|
|Microsoft.Search/searchServices|OperationLogs|Handlingen logfiler|
|Microsoft.ServerManagement/nodes|RequestLogs|Anmode om logfiler|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Funktionsdygtige logfiler|
|Microsoft.StreamAnalytics/streamingjobs|Udførelse af|Udførelse af|
|Microsoft.StreamAnalytics/streamingjobs|Redigering|Redigering|

## <a name="next-steps"></a>Næste trin
- [Stream diagnosticeringslogfiler til **begivenhed hubber**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Ændre indstillinger for diagnosticering ved hjælp af Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn931931.aspx)
- [Analysere loggene med OMS Log Analytics](../log-analytics/log-analytics-azure-storage-json.md)
