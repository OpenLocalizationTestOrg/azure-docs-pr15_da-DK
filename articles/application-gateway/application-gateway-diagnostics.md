<properties 
   pageTitle="Overvåge logfilerne access og ydeevne og målepunkter for Application Gateway | Microsoft Azure"
   description="Lær, hvordan du aktiverer og administrere adgang og ydeevne logfiler for Application Gateway"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="amitsriva" />

# <a name="diagnostics-logging-and-metrics-for-application-gateway"></a>Logføring af diagnosticering og Måleredskaber for Application Gateway

Azure giver mulighed for at overvåge en ressource til logføring og målepunkter

[**Logføring**](#enable-logging-with-powershell) - logføring giver mulighed for ydeevnen, access og andre logfiler, der skal gemmes eller consumed fra en ressource til overvågning.

[**Målepunkter**](#metrics) - Application gateway i øjeblikket har en metrikværdi. Denne metrikværdi måler gennemløb af gatewayen programmet i byte sekundet.

Du kan bruge forskellige typer logfiler i Azure til at administrere og fejlfinding i forbindelse med program-gateways. Nogle af disse logfiler kan åbnes via portalen, og alle logfiler kan udtrækkes fra en Azure blob-lager, og vises i forskellige værktøjer, som [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel og PowerBI. Du kan lære mere om de forskellige typer logge på listen nedenfor:

- **Overvågningslogge:** Du kan bruge [Azure overvågningslogge](../monitoring-and-diagnostics/insights-debugging-with-events.md) (tidligere kendt som funktionsdygtige logfiler) til at få vist alle handlinger, der sendes til abonnementet Azure og deres status. Overvågningslogge er aktiveret som standard, og kan ses i portalen Azure preview.
- **Få adgang til logfiler:** Du kan bruge denne log til at få vist programmet gateway access mønster og analysere vigtige oplysninger, herunder hvem der har IP, URL-adresse, der har skrevet, svar ventetid, returnerer kode, byte ind og ud. Access log indsamles hver 300 sekunder. Denne log indeholder én post hver forekomst af programmet gateway. Programmet gateway forekomst kan blive identificeret efter 'instanceId' egenskab.
- **Ydeevne logfiler:** Du kan bruge denne log til at få vist, hvordan programmet gatewayforekomster klarer sig. Denne log registrerer ydeevneoplysninger på hver forekomst datotype herunder samlede anmodning om served overførselshastighed i byte, samlet antal anmodninger served mislykkede anmodninger Tæl, sund og beskadiget back end-forekomst Tæl. Ydeevnen log indsamles hver 60 sekunder.
- **Firewall logfiler:** Du kan bruge denne log til at få vist de anmodninger, logføres via afsløre eller forebygge tilstand for et program gateway, der er konfigureret med firewall til webprogrammer.

>[AZURE.WARNING] Logfiler er kun tilgængelige for de ressourcer, der er implementeret i implementeringsmodel ressourcestyring. Du kan ikke bruge logfiler til ressourcer i modellen Klassisk installation. Reference til en bedre forståelse af de to modeller, artiklen [om ressourcestyring installation og klassisk installation](../resource-manager-deployment-model.md) .

## <a name="enable-logging-with-powershell"></a>Aktivere logføring af med PowerShell

Overvågning af logføring er automatisk aktiveret for hver ressourcestyring ressource. Du skal aktivere access og ydeevne logføring til at starte med at samle de data, der er tilgængelige via disse logfiler. For at aktivere logføring skal du se følgende trin: 

1. Bemærk, at kontoen lagerplads ressource-ID, hvor dataene i logfilen er gemt. Dette vil være i formatet: /subscriptions/\<subscriptionId\>/resourceGroups/\<gruppe ressourcenavn\>/providers/Microsoft.Storage/storageAccounts/\<kontonavn lager\>. En hvilken som helst lagerplads konto i dit abonnement kan bruges. Du kan bruge portalen preview til at finde disse oplysninger.

    ![Få vist portal - programmet Gateway diagnosticering](./media/application-gateway-diagnostics/diagnostics1.png)

2. Bemærk dit program gateway ressource-ID til hvilke logføring er at være aktiveret. Dette vil være i formatet: /subscriptions/\<subscriptionId\>/resourceGroups/\<gruppe ressourcenavn\>/providers/Microsoft.Network/applicationGateways/\<gateway programnavn\>. Du kan bruge portalen preview til at finde disse oplysninger.

    ![Få vist portal - programmet Gateway diagnosticering](./media/application-gateway-diagnostics/diagnostics2.png)

3. Aktivere logføring af diagnosticering ved hjælp af følgende powershell-cmdlet:

        Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true  

>[AZURE.INFORMATION] overvågningslogge kræver ikke en særskilt opbevaring-konto. Brug af lagerplads til access og ydeevne logføring påløber, gebyrer.

## <a name="enable-logging-with-azure-portal"></a>Aktivere logføring af med Azure-portalen

### <a name="step-1"></a>Trin 1

Gå til din ressource i portalen Azure. Klik på **diagnosticeringslogfiler**. Hvis dette er første gang konfiguration af diagnosticering bladet ser ud som følgende billede:

3 logfiler er tilgængelige for programmet gateway.

- Access Log
- Log af ydeevne
- Firewall-loggen

Klik på **Slå diagnosticering** for at starte indsamling af data.

![diagnosticering indstilling blade][1]

### <a name="step-2"></a>Trin 2

På bladet **diagnosticering indstillinger** indstillingerne for hvordan diagnosticeringslogfilerne er angivet. I dette eksempel bruges Log analytics til at gemme logfiler. Klik på **Konfigurer** under **Log Analytics** til at konfigurere dit arbejdsområde. Begivenhed hubs og en lagerplads konto kan bruges til at gemme loggene diagnosticering.

![diagnosticering blade][2]

### <a name="step-3"></a>Trin 3

Vælge et eksisterende arbejdsområde til OMS eller oprette en ny. I dette eksempel bruges et eksisterende dokument.

![OMS arbejdsområder][3]

### <a name="step-4"></a>Trin 4

Når du er færdig, Bekræft indstillingerne, og klik på **Gem** for at gemme indstillingerne.

![bekræfte valget][4]

## <a name="audit-log"></a>Overvågningslog

Denne log (tidligere kaldet "funktionsdygtige loggen") genereres af Azure som standard.  Loggene bevares for 90 dage i Azures hændelseslogfiler store. Lær mere om disse logfiler ved at læse artiklen [se begivenheder og overvågningsloggen](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="access-log"></a>Access log

Denne log genereres kun, hvis du har aktiveret den på en per Application Gateway datotype som beskrevet i de forrige trin. Data, der er gemt i lagerplads-konto, du angav, da du har aktiveret logføring. Hver access Application Gateway er logget på JSON-format, som det fremgår i følgende eksempel:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayAccess",
        "time": "2016-04-11T04:24:37Z",
        "category": "ApplicationGatewayAccessLog",
        "properties": {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIP":"37.186.113.170",
            "clientPort":"12345",
            "httpMethod":"HEAD",
            "requestUri":"/xyz/portal",
            "requestQuery":"",
            "userAgent":"-",
            "httpStatus":"200",
            "httpVersion":"HTTP/1.0",
            "receivedBytes":"27",
            "sentBytes":"202",
            "timeTaken":"359",
            "sslEnabled":"off"
        }
    }

## <a name="performance-log"></a>Log af ydeevne

Denne log genereres kun, hvis du har aktiveret den på en per Application Gateway datotype som beskrevet i de forrige trin. Data, der er gemt i lagerplads-konto, du angav, da du har aktiveret logføring. Følgende data logføres:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayPerformance",
        "time": "2016-04-09T00:00:00Z",
        "category": "ApplicationGatewayPerformanceLog",
        "properties": 
        {
            "instanceId":"ApplicationGatewayRole_IN_1",
            "healthyHostCount":"4",
            "unHealthyHostCount":"0",
            "requestCount":"185",
            "latency":"0",
            "failedRequestCount":"0",
            "throughput":"119427"
        }
    }


## <a name="firewall-log"></a>Firewall-loggen

Denne log genereres kun, hvis du har aktiveret den på en per programmet gateway datotype som beskrevet i de forrige trin. Denne log kræver også, firewall til webprogrammer er konfigureret på et program-gateway. Data, der er gemt i lagerplads-konto, du angav, da du har aktiveret logføring. Følgende data logføres:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
        "operationName": "ApplicationGatewayFirewall",
        "time": "2016-09-20T00:40:04.9138513Z",
        "category": "ApplicationGatewayFirewallLog",
        "properties":     {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIp":"108.41.16.164",
            "clientPort":1815,
            "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
            "ruleId":"OWASP_973336",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "action":"Logged",
            "site":"Global",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
    }

## <a name="view-and-analyze-the-audit-log"></a>Få vist og analysere overvågningsloggen

Du kan se og analysere overvågningslog logdata ved hjælp af følgende metoder:

- **Azure værktøjer:** Hente oplysninger fra overvågningslogge via Azure PowerShell, Azure Command Line Interface (CLI), Azure REST-API eller portalen Azure preview.  Trinvise instruktioner til hver enkelt metode gennemgås i artiklen [overvågningslog handlinger med ressourcestyring](../resource-group-audit.md) .
- **Power BI:** Hvis du ikke allerede har en [Power BI](https://powerbi.microsoft.com/pricing) -konto, kan du prøve den gratis. Brug af [Azure overvågningslogge indhold pack til Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) kan du analysere dine data med forudkonfigurerede dashboards, der kan bruges som-er, eller Tilpas.

## <a name="view-and-analyze-the-access-performance-and-firewall-log"></a>Få vist og analysere access, ydeevne og firewall logfilen

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) kan indsamle tælleren og hændelseslog filer fra kontoen Blob-lager og omfatter visualiseringer og effektive søgefunktioner til at analysere logfilerne.

Du kan også oprette forbindelse til kontoen lager og hente JSON logposter til access og ydeevne logfiler. Når du henter JSON-filer, kan du konvertere dem til csv- og view i Excel, PowerBI eller andre datavisualisering værktøjer.

>[AZURE.TIP] Hvis du kender til Visual Studio og grundlæggende begreber for at ændre værdier for konstanter og variabler i C#, kan du bruge [log konverteringsprogram værktøjer](https://github.com/Azure-Samples/networking-dotnet-log-converter) tilgængelige fra Github.

## <a name="metrics"></a>Målepunkter

Målepunkter er en funktion for visse Azure ressourcer, hvor du kan få vist tællere i ydeevne på portalen. For Application Gateway er en metrikværdi tilgængelig på tidspunktet for oprettelsen af denne artikel. Denne metrikværdi er overførselshastighed og kan ses på portalen. Gå til et program-gateway, og klik på **målepunkter**.  Vælg overførselshastighed i sektionen **tilgængelige målepunkter** til at få vist værdierne. Du kan se et eksempel med de filtre, som kan bruges til at vise dataene i forskellige tidsintervaller i det følgende billede.

Du finder en liste over aktuelt understøtter målepunkter på [understøttet måleredskaber med Azure skærm](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![metriske visning][5]

## <a name="alert-rules"></a>Regler for påmindelser

Regler for påmindelser kan startes baseret på på basis af en ressource måleenheder. Det betyder, at programmet gatewayen en besked kan ringe til en webhook eller sende en administrator, hvis overførselshastigheden for programmet gateway er over, under eller på en grænseværdi for et angivet tidsrum.

I følgende eksempel fører dig til at oprette en besked om, der sender en mail til en administrator, når en grænseværdi for overførselshastighed er blevet opfyldt.

### <a name="step-1"></a>Trin 1

Klik på **Tilføj metriske besked** til at starte. Denne blade kan også nås fra bladet målepunkter.

![regler for påmindelser blade][6]

### <a name="step-2"></a>Trin 2

Udfylde navnet, betingelse i bladet **Tilføj regel** og besked sektioner, og klik på **OK** , når du er færdig.

Vælgeren **betingelse** giver mulighed for 4 værdier, **større end**, **større end eller lig med**, **mindre end**eller **mindre end eller lig med**.

Vælgeren **periode** giver mulighed for valg af en periode fra 5 minutter til 6 timer.

Ved at vælge **mail ejere, bidragydere, og læsere** kan e-mailen være dynamiske baseret på de brugere, der har adgang til ressourcen. Ellers kan en kommasepareret liste over brugere leveres i tekstfeltet **Yderligere administrator email(s)** .

![Tilføj regel blade][7]

Hvis grænsen er opfyldt, modtager en mail ligner, på følgende billede:

![grænseværdi for brudt mail][8]

Der vises en liste af vigtige beskeder, når en metriske er blevet oprettet og indeholder en oversigt over alle regler for påmindelser.

![få vist reglen][9]

Hvis du vil vide mere om påmindelser skal du besøge [Modtag beskeder om meddelelser](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Du kan finde flere oplysninger om webhooks, og hvordan du kan bruge dem med beskeder, gå til [Konfigurer en webhook på en Azure metriske besked](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="next-steps"></a>Næste trin

- Visualisere tæller og hændelseslogfiler med [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) 
- [Visuelle effekter din Azure overvågningslogge med Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogindlæg.
- [Visning og analysere Azure overvågningslogge i Power BI og meget mere](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogindlæg.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png