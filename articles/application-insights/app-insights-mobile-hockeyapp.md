<properties
    pageTitle="Overvågning af ydeevnen for mobile webapps med udvikler Analytics | Microsoft Azure"
    description="Programmets ydeevne og overvåge brugen for udviklere-mobilappen. , skrivebord, webtjeneste og back end-apps med HockeyApp og programmet indsigt."
    authors="alancameronwills"
    services="application-insights"
    documentationCenter=""
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="awills"/>

# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>Mobile Analytics med HockeyApp og programmet indsigt

Overvåge ydeevnen og brugen af din beta-test og udløst bærbare og stationære apps med [HockeyApp](https://hockeyapp.net/). Overvåge understøttende web-tjenesten og back end-apps med [Visual Studio programmet indsigt](app-insights-overview.md). Analysere data fra både klienten og serveren apps i Analytics, og få vist diagrammer ved siden af hinanden i et Azure-dashboard.

Microsoft udvikler Analytics tilbyder to løsninger til programmet overvågning:

* **HockeyApp til mobile** og -apps.
 * Distribuere testversioner valgte brugere.
 * Ned analyse.
 * Brugerdefinerede telemetri til besøgsanalyse.
* **Programmet indsigt til websteder** og tjenester og back end-programmer.
 * Ydeevnen for og brugen målepunkter og beskeder.
 * Undtagelse rapportering og sporing af diagnosticering.
 * Diagnosticering Søg med filtrering og relaterede links til telemetri.

Begge løsninger tilbyder:

 * Effektiv **[analytisk forespørgselssprog](app-insights-analytics.md)** til diagnosticering og analyse.
 * **[Eksportere data](app-insights-export-telemetry.md)** til din egen lagerplads.
 * **Integreret dashboard** visning af analytiske diagrammer og tabeller.

## <a name="monitor-your-app-components"></a>Overvåge dit app-komponenter

Følg instruktionerne i disse sider for at installere SDK i din kode, og begynd at registrere din app.

### <a name="web-apps---application-insights"></a>Webapps - programmet indsigt

* [ASP.NET WebApp](app-insights-asp-net.md) 
* [Java WebApp](app-insights-java-get-started.md)
* [Node.js online](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Azure-Skytjenester](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>Mobilapps - HockeyApp

* [iOS-app](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Mac OS X-app](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Android-appen](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Universal Windows-app](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Windows Phone 8 og 8.1 app](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Præsentation af Windows Foundation app](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

For Windows-programmer på computeren anbefaler vi HockeyApp. Men du kan også [sende telemetri fra en Windows-app til programmet indsigt](app-insights-windows-desktop.md). Du måske gøre det for at eksperimentere med programmet indsigt.


## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>Analyser og Eksportér til HockeyApp telemetri

Du kan undersøge HockeyApp brugerdefineret og logge telemetri ved hjælp af de analyser og fortløbende eksportere funktioner i programmet indsigt ved [at konfigurere en bro](app-insights-hockeyapp-bridge-app.md).




