<properties 
    pageTitle="Overvåge brugen og ydeevne til Windows-programmer" 
    description="Analysere brugen og ydeevnen af din Windows-programmet med HockeyApp og programmet indsigt." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="awills"/>

# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Overvåge brugen og ydeevne i Windows-skrivebord apps

*Programmet indsigt er i Vis udskrift.*

[Visual Studio programmet indsigt](app-insights-overview.md) og [HockeyApp](https://hockeyapp.net) kan du overvåge udløst programmet for brugen og ydeevne.

> [AZURE.IMPORTANT] Vi anbefaler [HockeyApp](https://hockeyapp.net) til at distribuere og overvåge skrivebord og enhed apps. Med HockeyApp, kan du administrere fordeling, direkte test og feedback fra brugere, samt overvåge brugen og nedbrud rapporter. Du kan også [eksportere og forespørge din telemetri med analyser](app-insights-hockeyapp-bridge-app.md).

> Selvom telemetri kan sendes til programmet indsigt fra et desktopprogram, er det sænkes anvendeligt til fejlfinding og forsøg.


## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Sende telemetri til programmet indsigt fra et Windows-program

1. I [Azure-portalen](https://portal.azure.com), [oprette en programmet indsigt ressource](app-insights-create-new-resource.md). Vælg ASP.NET-app til programtype.
2. Tage en kopi af tasten Instrumentation. Finde nøglen i Essentials-rullemenuen på den nye ressource, du lige har oprettet. 
3. Rediger NuGet pakker af projektet app i Visual Studio, og Tilføj Microsoft.ApplicationInsights.WindowsServer. (Eller vælge Microsoft.ApplicationInsights, hvis du blot ønsker den mest API uden standard telemetri samling moduler.)
4. Indstille nøglen instrumentation enten i din kode:

    `TelemetryConfiguration.Active.InstrumentationKey = "`*din nøgle*`";` 

    eller i ApplicationInsights.config (Hvis du har installeret en af de almindelige telemetri pakker):
 
    `<InstrumentationKey>`*din nøgle*`</InstrumentationKey>` 

    Hvis du bruger ApplicationInsights.config, skal du sørge for dens egenskaber i Solution Explorer er sat til **opbygge handling = indhold, Kopiér til Output Directory = kopi**.
5. [Brug API](app-insights-api-custom-events-metrics.md) til at sende telemetri.
6. Køre din app, og se telemetri i den ressource, du oprettede i portalen Azure.

## <a name="telemetry"></a>Eksempler på kode

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Næste trin

* [Oprette et dashboard](app-insights-dashboards.md)
* [Diagnosticering søgning](app-insights-diagnostic-search.md)
* [Udforske målepunkter](app-insights-metrics-explorer.md)
* [Skrive Analytics-forespørgsler](app-insights-analytics.md)
 
