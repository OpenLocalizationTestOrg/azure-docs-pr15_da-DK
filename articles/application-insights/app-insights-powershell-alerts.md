<properties
    pageTitle="Bruge Powershell til at angive beskeder i programmet indsigt"
    description="Automatisere konfigurationen af programmet indsigt at finde mails om metriske ændringer."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/19/2016"
    ms.author="awills"/>

# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Bruge PowerShell til at angive beskeder i programmet indsigt

Du kan automatisere konfigurationen af [beskeder](app-insights-alerts.md) i [Visual Studio programmet indsigt](app-insights-overview.md).

Desuden kan du [angive webhooks til at automatisere dit svar til en besked](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="one-time-setup"></a>Enkeltstående installation

Hvis du ikke har brugt PowerShell med abonnementet Azure før:

Installer Azure Powershell-modulet på den computer, hvor du vil køre scripts.

 * Installere [Microsoft Web Platform Installer (v5 eller nyere)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Bruge det til at installere Microsoft Azure Powershell


## <a name="connect-to-azure"></a>Oprette forbindelse til Azure

Start Azure PowerShell og [oprette forbindelse til dit abonnement](../powershell-install-configure.md):

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


## <a name="get-alerts"></a>Få beskeder

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Tilføje besked


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



## <a name="example-1"></a>Eksempel 1

Sende mig, hvis serverens svar på HTTP-anmodninger og gennemsnit over 5 minutter er langsommere end 1 sekund. Mit program indsigt ressource kaldes IceCreamWebApp, og det er i ressourcegruppe Fabrikam. Jeg er ejeren af Azure-abonnement.

GUID er abonnement-ID (ikke instrumentation tasten af programmet).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Eksempel 2

Jeg har et program, hvor jeg bruge [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) til at rapportere en metrikværdi med navnet "salesPerHour." Sende en mail til mine kolleger, hvis "salesPerHour" falder under 100, gennemsnit over 24 timer.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Den samme regel kan bruges til metrisk rapporteret ved hjælp af [mål parameter](app-insights-api-custom-events-metrics.md#properties) for en anden registrering opkald som TrackEvent eller trackPageView.

## <a name="metric-names"></a>Metriske navne

Metriske navn | Navn på skærm | Beskrivelse
---|---|---
`basicExceptionBrowser.count`|Browser undtagelser|Antallet af ikke-opfanget undtagelser udløst i browseren.
`basicExceptionServer.count`|Server undtagelser|Antallet af ikke-afviklet undtagelser udløst af appen
`clientPerformance.clientProcess.value`|Behandling af klientklokkeslættet|Tid mellem modtager den sidste byte af et dokument, indtil DOM er indlæst. Stadig kan behandling af asynkrone anmodninger.
`clientPerformance.networkConnection.value`|Siden netværk forbinde indlæsningstiden| Tid browseren tager at oprette forbindelse til netværket. Kan være 0, hvis cachelagret.
`clientPerformance.receiveRequest.value`|Modtage svartid| Tid mellem browser sende anmodning til at begynde at modtage svar.
`clientPerformance.sendRequest.value`|Send anmodning om tid| Tid, det tager af browser til at sende anmodning.
`clientPerformance.total.value`|Browser siden indlæsningstiden|Tid fra bruger forespørgslen, indtil DOM, typografiark, scripts og billeder indlæses.
`performanceCounter.available_bytes.value`|Tilgængelig hukommelse|Fysisk hukommelse umiddelbart tilgængelig for en proces eller til brug på systemet.
`performanceCounter.io_data_bytes_per_sec.value`|Processen EY rente|Samlet antal bytes sekundet læses og skrives til filer, netværk og enheder.
`performanceCounter.number_of_exceps_thrown_per_sec`|undtagelse rente|Undtagelser udløst sekundet.
`performanceCounter.percentage_processor_time.value`|Processen CPU|Procentdelen af den forløbne tid for alle procestråde brugt af processor til at udføre instruktioner til programmer processen.
`performanceCounter.percentage_processor_total.value`|Processortid|Procentdelen af tid, som processoren bruger i standby-tråde.
`performanceCounter.process_private_bytes.value`|Proces private byte|Hukommelse udelukkende tildeles overvåget programmets processer.
`performanceCounter.request_execution_time.value`|ASP.NET anmodning kørselstid|Udførelse af klokkeslættet for den seneste anmodning.
`performanceCounter.requests_in_application_queue.value`|ASP.NET-anmodninger i udførelse af kø|Længden af programmet anmodningskøen.
`performanceCounter.requests_per_sec`|ASP.NET anmodning rente|Rente på alle anmodninger om at programmet sekundet fra ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Afhængighed af fejl|Antallet af mislykkede opkald, der er foretaget af serverprogrammet til eksterne ressourcer.
`request.duration`|Serverens svartid|Tid mellem modtager en HTTP-anmodning og afslutte sende svaret.
`request.rate`|Anmode om rente|Rente på alle anmodninger om at programmet sekundet.
`requestFailed.count`|Mislykkede anmodninger|Antallet af HTTP-anmodninger, der udløste en svarkode > = 400
`view.count`|Sidevisninger|Antallet af klienten brugeranmodninger om en webside. Korte trafik filtreres ud.
{dit brugerdefinerede metriske navn}|{Dit metriske navn}|Din metrikværdi rapporteret ved [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) eller i [mål-parameter for et sporing opkald](app-insights-api-custom-events-metrics.md#properties).

Målene sendes af forskellige telemetri moduler:

Metriske gruppe | Indsamler modul
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>få vist | [Browser JavaScript](app-insights-javascript.md)
performanceCounter | [Ydeevne](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Afhængighed](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
Anmod om<br/>requestFailed|[Anmodning om Server](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)

## <a name="webhooks"></a>Webhooks

Du kan [automatisere dit svar til en besked](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure kalder en webadresse på dit valg, når en påmindelse opløftes.

## <a name="see-also"></a>Se også


* [Script til at konfigurere programmet indsigt](app-insights-powershell-script-create-resource.md)
* [Oprette programmet indsigt og web test ressourcer fra skabeloner](app-insights-powershell.md)
* [Automatisere kobling Microsoft Azure diagnosticering til programmet indsigt](app-insights-powershell-azure-diagnostics.md)
* [Automatisere dit svar til en besked](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
