Der er nogle begrænsninger for antallet af målepunkter og hændelser af hver program (det vil sige, per instrumentation nøgle). 

Begrænsninger for afhænger af den [priser niveau](https://azure.microsoft.com/pricing/details/application-insights/) , du vælger.

**Ressource** | **Standardgrænse** | **Højeste antal**
-------- | ------------- | -------------
Session datapunkter<sup>1, 2</sup> månedsbasis | ubegrænset | 
Samlet datapunkter månedsbasis for anmodning, begivenhed, afhængighed, sporing, undtagelse og visning af siden | 5 millioner | 50 millioner<sup>3</sup>
[Sporing, og Log](../articles/application-insights/app-insights-search-diagnostic-logs.md) data rente | 200 dp/s | 500 dp/s
[Undtagelse](../articles/application-insights/app-insights-asp-net-exceptions.md) data rente | 50 dp/s | 50 dp/s
Samlede data DISKONTO anmodningen, begivenhed, afhængighed og siden visning telemetri | 200 dp/s | 500 dp/s
Rækkedata opbevaring for [søgning](../articles/application-insights/app-insights-diagnostic-search.md) og [analyser](../articles/application-insights/app-insights-analytics.md) | 7 dage
Aggregerede dataopbevaring for [målepunkter explorer](../articles/application-insights/app-insights-metrics-explorer.md) | 90 dage
[Egenskaben](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) navn Tæl | 100 |
Egenskaben navn længde | 150 | 
Egenskaben værdi længde | 8192 | 
Sporing og undtagelse meddelelse længde | 10000 |
[Metrisk](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) navn Tæl | 100 |
Længde på metriske navn |  150 | 
[Tilgængelighed test](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> et datapunkt er en individuel metriske værdi eller en begivenhed med vedhæftede egenskaber og mål.

<sup>2</sup> A session datapunkt logger starten eller slutningen af en session og logger bruger-id.

<sup>3</sup> du kan købe ekstra kapacitet ud over 50 millioner.
 
[Om priser og kvoter for i programmet indsigt](../articles/application-insights/app-insights-pricing.md)
