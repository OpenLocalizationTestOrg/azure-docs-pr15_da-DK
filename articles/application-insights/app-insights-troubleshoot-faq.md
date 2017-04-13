<properties 
    pageTitle="Fejlfinding og spørgsmål om programmet indsigt" 
    description="Noget i Visual Studio programmet indsigt uklare eller fungerer ikke? Prøv her." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="questions---application-insights-for-aspnet"></a>Spørgsmål - programmet indsigt til ASP.NET

## <a name="configuration-problems"></a>Konfigurationsproblemer

*Jeg har problemer med at min:*

* [.NET-app](app-insights-asp-net-troubleshoot-no-data.md)
* [Overvågning af en allerede kører app](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Azure diagnosticering](app-insights-azure-diagnostics.md)
* [Java WebApp](app-insights-java-troubleshoot.md)
* [Andre platforme](app-insights-platforms.md)

*Jeg får ikke data fra serveren*

* [Angive undtagelser](app-insights-ip-addresses.md)
* [Konfigurere en ASP.NET-server](app-insights-monitor-performance-live-website-now.md)
* [Konfigurere en Java-server](app-insights-java-agent.md)


## <a name="can-i-use-application-insights-with-"></a>Kan jeg bruge programmet indsigt med...?

[Se platforme][platforms]


## <a name="is-it-free"></a>Er det gratis?

* Ja, hvis du vælger den gratis [priser niveau](app-insights-pricing.md). Du får de fleste funktioner og en bredt kvote af data. 
* Du skal angive dataene kreditkort til at registrere hos Microsoft Azure, men ingen gebyrer foretages, medmindre du bruger en anden betalt til Azure service, eller du eksplicit har opgraderet til et udbetalende trin.
* Hvis din app sender flere data end kvoten for månedlige for det gratis niveau, holder den bliver logget. Hvis det sker, kan du enten vælge for at begynde betaling eller vente, indtil kvoten nulstilles i slutningen af måneden.
* Grundlæggende brugen og session data er ikke underlagt en kvote.
* Der er også en gratis 30-dages prøveversion, hvor du får funktionerne betalt gratis.
* Hvert program ressource har en separat kvote, og du angive dens priser niveau uafhængigt af andre.

#### <a name="what-do-i-get-if-i-pay"></a>Hvad får jeg, hvis jeg betale?

* En større [månedlige kvote af data](https://azure.microsoft.com/pricing/details/application-insights/).
* Indstilling til at betale 'forældede' for at fortsætte med at indsamle data via kvoten for månedlige. Hvis dine data handler om kvote, er du betale hver Mb.
* [Fortløbende eksportere](app-insights-export-telemetry.md).


## <a name="q14"></a>Hvad ændrer programmet indsigt i projektet?

Oplysninger, afhænger af typen projekt. For et webprogram:


+ Føjer filerne til dit projekt:

 + ApplicationInsights.config. 
 + AI.js


+ Installationer pakkerne NuGet:

 -  *Programmet indsigt API* - core API

 -  *Programmet indsigt API til webprogrammer* - bruges til at sende telemetri fra serveren

 -  *Programmet indsigt API til JavaScript programmer* - bruges til at sende telemetri fra klienten på computeren

    Pakkerne omfatter disse enheder:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Indsætter elementer til:

 - Web.config

 - Packages.config

+ (Nye projekter kun -, hvis du [tilføje programmet indsigt til et eksisterende projekt][start], du skal gøre dette manuelt.) Indsætter kodestykker i klienten og serveren koden initialiseret dem med programmet indsigt ressource-ID. For eksempel i en MVC app er koden indsat i mastersiden Views/Shared/_Layout.cshtml


## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hvordan opgraderer jeg fra ældre versioner af SDK?

Du kan se den [Produktbemærkninger](app-insights-release-notes.md) til SDK relevant at typen af programmet. 



## <a name="update"></a>Hvordan kan jeg ændre hvilken Azure ressource mit projekt sender data til?

I Solution Explorer, skal du højreklikke på `ApplicationInsights.config` , og vælg **Opdater programmet indsigt**. Du kan sende dataene til en eksisterende eller nye ressource i Azure. Guiden Opgrader ændres nøglen instrumentation i ApplicationInsights.config, som bestemmer, hvor serveren SDK sender dine data. Medmindre du fravælger "Opdater alt", kan det også ændre den nøgle, hvor det vises på dine websider.


#### <a name="data"></a>Hvor længe bevares data i portalen? Er det sikkert?

Se nærmere på [dataopbevaring og beskyttelse af personlige oplysninger][data].

## <a name="logging"></a>Logføring

#### <a name="post"></a>Hvordan kan jeg se INDLÆG data i diagnosticering søgning?

Vi ikke kan logge INDLÆG data automatisk, men du kan bruge et TrackTrace opkald: dataene i parameteren meddelelse. Dette er en længere størrelsesbegrænsning end begrænsningerne på strengegenskaber, selvom du ikke kan filtrere på den. 

## <a name="security"></a>Sikkerhed

#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>Er mine data sikre i portalen? Hvor længe bevares den?

Se [Data opbevaring og beskyttelse af personlige oplysninger][data].


## <a name="q17"></a>Har jeg aktiveret alt i programmet indsigt?

<table border="1">
<tr><th>Hvad skal du se afsnittet</th><th>Sådan henter du det</th><th>Hvorfor du vil.</th></tr>
<tr><td>Tilgængeligheden af diagrammer</td><td><a href="../app-insights-monitor-web-app-availability/">Web-test</a></td><td>Ved din online er op</td></tr>
<tr><td>Server app performance: svar gange...
</td><td><a href="../app-insights-asp-net/">Føje programmet indsigt til projektet</a><br/>eller <br/><a href="../app-insights-monitor-performance-live-website-now/">Installere AI Status skærm på server</a> (eller skrive din egen programkode til at <a href="../app-insights-api-custom-events-metrics/#track-dependency">spore afhængigheder</a>)</td><td>Finde performance problemer</td></tr>
<tr><td>Afhængighed telemetri</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installere AI Status skærm på server</a></td><td>Diagnosticere problemer med databaser eller andre eksterne komponenter</td></tr>
<tr><td>Få staksporinger fra undtagelser</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Indsætte TrackException opkald i din kode</a> (men nogle rapporteres automatisk)</td><td>Registrere og diagnosticere undtagelser</td></tr>
<tr><td>Søg log sporinger</td><td><a href="../app-insights-search-diagnostic-logs/">Tilføje en logføring adapter</a></td><td>Diagnosticere undtagelser, performance problemer</td></tr>
<tr><td>Grundlæggende om brugen af klienten: sidevisninger, sessioner,...</td><td><a href="../app-insights-javascript/">JavaScript initialisering på websider</a></td><td>Brugsstatistik</td></tr>
<tr><td>Klient brugerdefinerede målepunkter</td><td><a href="../app-insights-api-custom-events-metrics/">Sporing ringer på websider</a></td><td>Forbedre brugeroplevelsen</td></tr>
<tr><td>Server brugerdefinerede målepunkter</td><td><a href="../app-insights-api-custom-events-metrics/">Sporing opkald i server kode</a></td><td>Business intelligence</td></tr>
</table>


## <a name="automation"></a>Automatisering

Du kan [skrive PowerShell-scripts,](app-insights-powershell.md) for at oprette og opdatere programmet indsigt ressourcer.

## <a name="more-answers"></a>Flere svar

* [Programmet indsigt forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)


<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 