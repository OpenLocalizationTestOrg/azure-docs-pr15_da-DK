<properties 
    pageTitle="Konfiguration af programmet indsigt SDK med ApplicationInsights.config eller .xml" 
    description="Aktivere eller deaktivere data samling moduler og føje tællere i ydeevne og andre parametre" 
    services="application-insights"
    documentationCenter="" 
    authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/12/2016" 
    ms.author="awills"/>

# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfiguration af programmet indsigt SDK med ApplicationInsights.config eller .xml

Programmet indsigt .NET SDK består af et antal NuGet pakker. [Core pakken](http://www.nuget.org/packages/Microsoft.ApplicationInsights) indeholder API til at sende telemetri til programmet indsigt. [Yderligere pakker](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) giver telemetri _moduler_ og _initialisering_ for automatisk at spore telemetri fra dit program og dens kontekst. Ved at justere konfigurationsfilen, kan du aktivere eller deaktivere telemetri moduler og initialisering og indstille parametre for nogle af dem.

Konfigurationsfilen hedder `ApplicationInsights.config` eller `ApplicationInsights.xml`, afhængigt af typen af dit program. Det føjes automatisk til dit projekt når du [har installeret de fleste versioner af SDK][start]. Den også føjet til en WebApp [Status skærm på en IIS-server][redfield], eller når du vælger Appplication indsigt [udvidelse til en Azure websted eller VM](app-insights-azure-web-apps.md).

Der ikke er en tilsvarende filen for at kontrollere [SDK på en webside][client].

I dette dokument beskrives de afsnit, du kan se i konfigurationen fil, hvordan de styre komponenterne i SDK, og hvilke NuGet pakker indlæse disse komponenter.

## <a name="telemetry-modules-aspnet"></a>Telemetri moduler (ASP.NET)

Hvert telemetri modul indsamler en bestemt type af data og bruger core API til at sende dataene. Modulerne, der er installeret af forskellige NuGet-pakker, som også tilføjer de nødvendige linjer til .config-filen.

Der findes en node i konfigurationsfil for hvert modul. Hvis du vil deaktivere et modul, Slet noden eller skrive en kommentar ud af.



### <a name="dependency-tracking"></a>Afhængighed registrering

[Afhængighed sporing](app-insights-dependencies.md) indsamler telemetri om opkald din app foretager databaser og eksterne tjenester og -databaser. Hvis du vil tillade dette modul til at arbejde i en IIS-server, skal du [installere Status skærm][redfield]. Bruge den i Azure Onlines eller FOS, [Vælg filtypenavnet programmet indsigt](app-insights-azure-web-apps.md).

Du kan også skrive din egen afhængighed sporing kode ved hjælp af [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency).


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet pakke.

### <a name="performance-collector"></a>Ydeevnen indsamler

[Indsamler system ydeevne tællere](app-insights-performance-counters.md) som CPU, hukommelse og netværk indlæse fra IIS installationer. Du kan angive hvilke tællere til at indsamle, herunder tællere i ydeevne du har konfigureret dig selv.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet pakke.


### <a name="application-insights-diagnostics-telemetry"></a>Programmet indsigt diagnosticering Telemetri

Den `DiagnosticsTelemetryModule` rapporterer fejl i programmet indsigt instrumentation selve koden. Eksempelvis hvis koden ikke kan få adgang til tællere i ydeevne, eller hvis en `ITelemetryInitializer` medfører en undtagelse. Spor telemetri registreres i dette modul vises i feltet [Diagnosticering Søg][diagnostic]. Sender diagnosticering data til dc.services.vsallin.net.
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet pakke. Hvis du kun installere denne pakke, oprettes der ikke automatisk filen ApplicationInsights.config. 

### <a name="developer-mode"></a>Udviklertilstand

`DeveloperModeWithDebuggerAttachedTelemetryModule`Fremtvinger programmet indsigt `TelemetryChannel` til at sende data med det samme, én telemetri element ad gangen, når et fejlfindingsværktøj er knyttet til ansøgningsprocedure. Dette reducerer mængden af tid mellem tidspunktet, når programmet registrerer telemetri, og når det vises på portalen programmet indsigt. Det får betydeligt omkostninger i båndbredde CPU og netværk.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application indsigt Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet pakke

### <a name="web-request-tracking"></a>Webforespørgsel registrering

Rapporter [svar tid og resultat kode](app-insights-asp-net.md) for HTTP-anmodninger. 

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet pakke

### <a name="exception-tracking"></a>Undtagelse registrering

`ExceptionTrackingTelemetryModule`Spor ikke-afviklet undtagelser i WebApp. Se [fejl og undtagelser][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet pakke


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-registrerer [ikke overholdte opgave undtagelser](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`-registrerer ikke-afviklet undtagelser for arbejder roller, windows services og console-programmer.
* [Application indsigt Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet pakke.

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights

Pakken Microsoft.ApplicationInsights indeholder [core API](https://msdn.microsoft.com/library/mt420197.aspx) af SDK. Brug denne indstilling de øvrige telemetri moduler, og du kan også [bruge den til at definere dine egne telemetri](app-insights-api-custom-events-metrics.md).

* Noget i ApplicationInsights.config.
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet pakke. Hvis du lige har installeret denne NuGet, oprettes der ingen .config-fil.

## <a name="telemetry-channel"></a>Telemetri kanal

Salgskanalen telemetri administrerer bufferen og overførslen af telemetri til tjenesten programmet indsigt. 

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`er standard kanalen for tjenester. Det placerer data i hukommelsen.
* `Microsoft.ApplicationInsights.PersistenceChannel`er et alternativ til console-programmer. Det kan gemme unflushed data til fast lager, når din app lukkes, og send, når programmet starter igen.


## <a name="telemetry-initializers-aspnet"></a>Telemetri initialisering (ASP.NET)

Telemetri initialisering angive kontekst egenskaber, der sendes sammen med hver vare telemetri. 

Du kan [skrive din egen initialisering](app-insights-api-filtering-sampling.md#add-properties) for at angive kontekst egenskaber.

Standard initialisering angives alle enten via internettet eller WindowsServer NuGet-pakker:


* `AccountIdTelemetryInitializer`angiver egenskaben AccountId.
* `AuthenticatedUserIdTelemetryInitializer`Egenskaben AuthenticatedUserId er angivet af JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer`opdateringer af `RoleName` og `RoleInstance` egenskaberne for den `Device` kontekst for alle telemetri elementer med oplysningerne fra Azure runtime-miljø.
* `BuildInfoConfigComponentVersionTelemetryInitializer`opdateringer på `Version` egenskab for den `Component` kontekst for alle telemetri elementer med den værdi, der er hentet fra den `BuildInfo.config` fil produceret af MS Build.
* `ClientIpHeaderTelemetryInitializer`opdateringer `Ip` egenskab for den `Location` kontekst for alle telemetri varer baseret på den `X-Forwarded-For` HTTP sidehoved for din anmodning.
* `DeviceTelemetryInitializer`opdaterer følgende egenskaber for den `Device` kontekst for alle telemetri elementer.
 - `Type`er indstillet til "PC"
 - `Id`er angivet til domænenavnet på computeren, hvor webprogrammet kører.
 - `OemName`er indstillet til den værdi, der er hentet fra den `Win32_ComputerSystem.Manufacturer` feltet vha.
 - `Model`er indstillet til den værdi, der er hentet fra den `Win32_ComputerSystem.Model` feltet vha.
 - `NetworkType`er indstillet til den værdi, der er hentet fra den `NetworkInterface`.
 - `Language`er indstillet til navnet på den `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer`opdateringer af `RoleInstance` egenskab for den `Device` kontekst for alle telemetri elementer med domænenavnet på den computer, hvor webprogrammet kører.
* `OperationNameTelemetryInitializer`opdateringer på `Name` egenskab for den `RequestTelemetry` og `Name` egenskab for den `Operation` kontekst for alle telemetri varer baseret på http-metode samt navnene på ASP.NET MVC controller og en handling, der er aktiveret for at behandle anmodningen.
* `OperationIdTelemetryInitializer`eller `OperationCorrelationTelemetryInitializer` opdateringer på `Operation.Id` kontekstegenskab for alle telemetri elementer registreres under håndtering af en anmodning med den automatisk oprettede `RequestTelemetry.Id`.
* `SessionTelemetryInitializer`opdateringer på `Id` egenskab for den `Session` kontekst for alle telemetri elementer med værdi, der er hentet fra den `ai_session` cookie genereret ApplicationInsights JavaScript instrumentation koden kører i brugerens browser. 
* `SyntheticTelemetryInitializer`eller `SyntheticUserAgentTelemetryInitializer` opdateringer på `User`, `Session` og `Operation` kontekster egenskaber for alle telemetri varer registreres når håndtering af en anmodning om fra en korte kilde, som en tilgængelighed teste eller søge program bot. Som standard vises [Målepunkter Explorer](app-insights-metrics-explorer.md) ikke korte telemetri. 

    Den `<Filters>` angive identificere egenskaber for anmodninger.
* `UserAgentTelemetryInitializer`opdateringer på `UserAgent` egenskab for den `User` kontekst for alle telemetri varer baseret på den `User-Agent` HTTP sidehoved for din anmodning.
* `UserTelemetryInitializer`opdateringer af `Id` og `AcquisitionDate` egenskaber for `User` kontekst for alle telemetri elementer med værdier, der er hentet fra den `ai_user` cookie genereres af programmet indsigt JavaScript instrumentation koden kører i brugerens browser.
* `WebTestTelemetryInitializer`Angiver bruger-id, session-id og egenskaber for korte datakilde til HTTP-forespørgsler, der stammer fra [tilgængelighed test](app-insights-monitor-web-app-availability.md).
Den `<Filters>` angive identificere egenskaber for anmodninger.

## <a name="telemetry-processors-aspnet"></a>Telemetri processorer (ASP.NET)

Telemetri processorer kan filtrere og ændre hvert telemetri element, lige før den sendes fra SDK på portalen.

Du kan [skrive din egen telemetri processorer](app-insights-api-filtering-sampling.md#filtering).


#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Tilpasset udvalg telemetri processor (fra 2.0.0-beta3)

Dette er aktiveret som standard. Hvis din app sender en masse telemetri, fjerner denne processor noget af det.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parameteren indeholder destinationen, som algoritmen forsøger at opnå. Hver forekomst af SDK fungerer uafhængigt af hinanden, så hvis serveren er en klynge af flere maskiner, den faktiske mængde telemetri multipliceret i overensstemmelse hermed.

[Lær mere om udvalg](app-insights-sampling.md).



#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Fast rente udvalg telemetri processor (fra 2.0.0-beta1)

Der er også et almindelige [udvalg telemetri processor](app-insights-api-filtering-sampling.md#sampling) (fra 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Kanal parametre (Java)

Disse parametre påvirker, hvordan Java SDK skal gemme og Rens telemetridata, der indsamles.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity

Antallet af telemetri elementer, der kan være gemt i den SDK hukommelseslager. Når dette nummer er nået, telemetri bufferen tømmes – det vil sige, telemetri elementer sendes til programmet indsigt-serveren.

-   Min: 1
-   Maks: 1000
-   Som standard: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds 

Bestemmer, hvor ofte dataene, der er gemt i den hukommelseslager skal ryddes (sendt til programmet indsigt).

-   Min: 1
-   Maks: 300
-   Som standard: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB

Bestemmer den maksimale størrelse i MB, der er afsat til fast lager på den lokale disk. Denne lagerplads bruges til vedvarende telemetri elementer, der ikke kunne sendes til programmet indsigt slutpunkt. Når lagerstørrelse er opfyldt, slettes nye telemetri elementer.

-   Min: 1
-   Maks: 100
-   Som standard: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey

Dette bestemmer programmet indsigt ressourcen, dataene vises. Du opretter typisk en separat ressource med en separat nøgle for hver af dine programmer.

Hvis du vil indstille nøglen dynamisk – for eksempel, hvis du vil sende resultater fra dit program til forskellige ressourcer – kan du udelader tasten fra konfigurationsfilen og angive den kode i stedet.

For at angive URL'en for alle forekomster af TelemetryClient, herunder standard telemetri moduler, du angive en nøgle i TelemetryConfiguration.Active. I en initialiseringsmetode til, som global.aspx.cs i en ASP.NET-tjeneste, kan du gøre følgende:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Hvis du blot ønsker at sende et bestemt sæt af hændelser til en anden ressource, kan du angive nøglen for en bestemt TelemetryClient:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Lær mere om API][api].

At få en ny nøgle, [oprette en ny ressource i portalen programmet indsigt][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

